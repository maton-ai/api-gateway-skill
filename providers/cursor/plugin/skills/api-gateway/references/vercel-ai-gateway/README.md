# Vercel AI Gateway Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

> **Cost:** Every successful call to `/v1/chat/completions`, `/v1/responses`, `/v1/messages`, or `/v1/embeddings` bills real money against the connected Vercel account. Treat inference requests as write operations: confirm the model and approximate request volume with the user before running them in a loop, over a batch, or with a high `max_tokens`. Check `pricing` via `/v1/models/{creator}/{model}` first — rates vary by more than 1000x across the catalog.

> **Data handling:** Prompt and completion content is forwarded to the selected upstream provider and retained in AI Gateway's usage records, where it is visible in the Vercel dashboard. Do not place secrets in prompts. Treat model output as untrusted input — never execute or interpolate it into commands without validation.

**App name:** `vercel-ai-gateway`
**Base URL proxied:** `ai-gateway.vercel.sh`

Not to be confused with `vercel` (`api.vercel.com`), which covers projects, deployments, and domains. This app is the inference gateway only.

## API Path Pattern

```
/vercel-ai-gateway/v1/{resource}
```

The `/v1` prefix is mandatory. Paths without it return `404` with an **HTML** body rather than JSON.

## Common Endpoints

### Models

#### List Models
```bash
GET /vercel-ai-gateway/v1/models
```

Returns the entire catalog (315 models from 34 providers at time of testing) as `{"object": "list", "data": [...]}`.

**`limit` and `type` query parameters are silently ignored** — they return `200` with the full catalog. Filter client-side.

#### Get Model
```bash
GET /vercel-ai-gateway/v1/models/{creator}/{model}
```

Example: `GET /vercel-ai-gateway/v1/models/anthropic/claude-haiku-4.5`

Not in the published REST reference, but works.

#### List Model Endpoints
```bash
GET /vercel-ai-gateway/v1/models/{creator}/{model}/endpoints
```

Per-provider pricing, context limits, uptime, and latency for a model. Works for all model types. `data` is an **object** here, unlike the array returned by `/v1/models`.

Provider counts vary: `anthropic/claude-opus-5` → 4 providers, `openai/gpt-4o-mini` → 2, `alibaba/qwen-3-14b` → 1.

### Credits & Usage

#### Get Credit Balance
```bash
GET /vercel-ai-gateway/v1/credits
```

```json
{ "balance": "4.99999992", "total_used": "0.00000008" }
```

Values are decimal **strings** in USD, not numbers, and carry sub-cent precision (8 decimals observed) — never round them for accounting. A `"balance": "0"` alongside a `403` on inference is the signature of the card gate, not exhausted credits.

#### Get Generation Usage
```bash
GET /vercel-ai-gateway/v1/generation?id=gen_{ulid}
```

Cost and token usage for one completed request. The ID comes from the `id` field of a chat completion response (or the first streaming chunk).

**Usage events are ingested asynchronously** — an immediate lookup returns `404 Usage event not found`. Measured delay was **~9s** (404 at 0/3/6s, 200 at 9s), so retry with backoff instead of treating the first 404 as failure.

This route uses **different field names** from the inline `usage` on an inference response: `tokens_prompt`/`tokens_completion` (plus `native_tokens_*` for provider-reported counts), `total_cost`, `provider_name`, `latency`, `streamed`, and `finish_reason`. Costs here are numbers, unlike the strings from `/v1/credits`.

#### Get Spend Report
```bash
GET /vercel-ai-gateway/v1/report?start_date=2026-08-01&end_date=2026-08-04
```

**Requires a paid Vercel plan — a separate gate from having a card on file.** On an account with a valid card and positive balance, where every other endpoint returns `200`, this still returns `403 forbidden`. The plan check precedes parameter validation, so its error says nothing about your query string. This is the only endpoint here whose success shape is unverified; use `/v1/generation` or `/v1/credits` for cost data instead.

### Inference

All inference routes are OpenAI/Anthropic-compatible. Model IDs are always `{creator}/{model}`.

#### Chat Completions
```bash
POST /vercel-ai-gateway/v1/chat/completions
Content-Type: application/json

{
  "model": "anthropic/claude-haiku-4.5",
  "messages": [
    { "role": "user", "content": "Say hello in five words." }
  ],
  "max_tokens": 100
}
```

Add `"stream": true` for a `text/event-stream` of `data: {...}` chunks ending in `data: [DONE]`. The first chunk's `delta` carries only `{"role": "assistant"}`; `usage`, `provider_metadata`, and `generationId` arrive only on the final chunk (the one with `finish_reason`). `data: [DONE]` is a bare sentinel, not JSON.

Responses carry `id` (a `gen_<ulid>`), `choices[].message.content`, `usage`, and a gateway `provider_metadata` block. `provider_metadata.gateway.routing.finalProvider` names the upstream that actually served the request — the only way to attribute a response when several providers serve one model. `generationId` appears both at the top level and under `provider_metadata.gateway`.

Reasoning models add `message.reasoning` and `message.reasoning_details`.

#### Responses
```bash
POST /vercel-ai-gateway/v1/responses
Content-Type: application/json

{
  "model": "openai/gpt-4o-mini",
  "input": "Say hello in five words."
}
```

`input` is required (string or structured array).

Returns `output` as an **array of typed items**, not a single message — reasoning models emit a `type: "reasoning"` item before the `type: "message"` item, so filter by `type` rather than indexing `output[0]`. Note that `error` is present but **`null` on success**: check the value, not key presence.

#### Messages (Anthropic-shaped)
```bash
POST /vercel-ai-gateway/v1/messages
Content-Type: application/json

{
  "model": "anthropic/claude-haiku-4.5",
  "max_tokens": 100,
  "messages": [
    { "role": "user", "content": "Say hello in five words." }
  ]
}
```

`max_tokens` is **required** here, unlike on `/v1/chat/completions`. Uses Anthropic's error envelope: `{"type": "error", "error": {...}}`.

Two differences from Anthropic's native API: the `content` array returns the `text` block **before** the `thinking` block (the reverse of native ordering, so filter by `type`), and `id` is a Vercel `gen_<ulid>` rather than an Anthropic `msg_...` ID.

#### Embeddings
```bash
POST /vercel-ai-gateway/v1/embeddings
Content-Type: application/json

{
  "model": "openai/text-embedding-3-small",
  "input": ["first string", "second string"]
}
```

Only models with `"type": "embedding"` (26 in the catalog) work here. One `data` entry per input string, ordered by `index`; `openai/text-embedding-3-small` returns 1536 dimensions. This route has **no top-level `id`** — the generation ID is only under `providerMetadata.gateway.generationId`.

## Model Types

`/v1/models` returns eight `type` values. The last three are absent from the published docs, so switch on `type` defensively:

| Type | Count | Example |
|------|-------|---------|
| `language` | 208 | `anthropic/claude-haiku-4.5` |
| `image` | 32 | `bfl/flux-2-flex` |
| `video` | 30 | `alibaba/wan-v2.6-t2v` |
| `embedding` | 26 | `openai/text-embedding-3-small` |
| `realtime` | 6 | `openai/gpt-realtime-2` |
| `reranking` | 5 | `cohere/rerank-v3.5` |
| `transcription` | 5 | `openai/whisper-1` |
| `speech` | 3 | `openai/tts-1` |

## Pricing Shapes

`pricing` is always present, but **its keys depend on model type** and per-token `input`/`output` are not universal. Reading `pricing.input` unconditionally raises on 65 of 315 models:

| Type | Typical keys |
|------|--------------|
| `language` | `input`, `output` (205/208); also `input_cache_read`, `web_search`, `regional`, `input_tiers`/`output_tiers` |
| `embedding` | `input` (all) |
| `image` | `image` or `image_dimension_quality_pricing`; only 5 have `input` |
| `video` | `video_duration_pricing` or `video_token_pricing` — **none** have `input`/`output` |
| `transcription` | `input`, `transcription_duration_cost_per_second` |
| `speech` | `input`, `speech_input_character_cost` |
| `realtime` | `input`/`output`, `audio_input_token_cost`, `realtime_session_duration_cost_per_second` |
| `reranking` | `input` (2 of 5) |

The three `perplexity/sonar*` models have an **empty** `pricing` object. Always check key membership before arithmetic.

All prices are USD per token as decimal strings — multiply by 1e6 for per-million figures.

## Conditional Fields

Only these are present on all 315 models: `id`, `object`, `created`, `released`, `owned_by`, `name`, `description`, `type`, `supported_specifications`, `modalities`, `pricing`.

Everything else is conditional: `context_window`/`max_tokens` (307), `tags` (248), `supported_parameters`/`temperature` (214), `knowledge` (149), `reasoning_options` (110), `regions` (45), `video_capabilities` (30), `interleaved` (13), `deprecated_at` (1). Use safe accessors.

## Account States Affecting Inference

Inference passes through three account-state gates, each with a different error type:

| State | Status | `type` | Meaning |
|-------|--------|--------|---------|
| No card on file | 403 | `customer_verification_required` | Inference blocked entirely |
| Card on file, free credits | 429 | `rate_limit_exceeded` | Works, but throttled |
| Paid credits | 200 | — | Unrestricted |

**No card on file** returns `403` after auth and routing succeed (`AI Gateway requires a valid credit card on file to service requests...`). This is upstream Vercel account state, not a gateway or connection fault — `GET /v1/models` and `GET /v1/credits` still return `200` over the same connection. Free models (priced `"0"`) are gated too; nothing bypasses the card requirement. Do not recreate the connection for this error. After adding a card, the unlock takes ~15–30s to propagate (`balance` goes `"0"` → `"5"`), so retry before concluding failure.

**Free-tier credits are rate-limited** (`429 rate_limit_exceeded`, "Free tier requests on this model are rate-limited"). Despite the wording the limit is **account-wide, not per-model** — switching free models returns the same error. **No `Retry-After` header is sent**; back off manually, as the window took several minutes to clear under light testing.

**Schema validation runs before the billing check**, so malformed bodies return `400` even on a blocked account. But model resolution runs *after* it — an unknown model, a bare model ID with no `{creator}/` prefix, and a type mismatch all surface as the same `403`. Validate model IDs against `/v1/models`, which is free and does resolve them.

Free models make testing effectively free: a full sweep of every endpoint here cost **$0.00000008**.

## Notes

- Model IDs are always `{creator}/{model}`. A bare `claude-haiku-4.5` will not resolve.
- **No endpoint is paginated.** No response contains `next`, `cursor`, `offset`, or `has_more`, and no `Link` header is returned.
- Model pricing uses `input`/`output`; endpoint pricing under `/endpoints` uses `prompt`/`completion` for the same values.
- Video capabilities are keyed `video_capabilities` on a model object but `capabilities` on the `/endpoints` response.
- Error envelopes are inconsistent: most routes use `{"error": {"message", "type", "param", "code"}}`, `/v1/messages` uses Anthropic's `{"type": "error", "error": {...}}`, `/v1/generation` uses a flat `{"error": "string"}`, and `/v1/responses` returns `"error": null` **on success** — check the value, not key presence.
- **Gateway metadata key casing differs by route**: `provider_metadata` on `/v1/chat/completions` and `/v1/messages`, `providerMetadata` on `/v1/embeddings` and at the top level of `/v1/responses` and error bodies.
- **Token usage field names differ across three routes**: `prompt_tokens`/`completion_tokens` on `/v1/chat/completions`, `input_tokens`/`output_tokens` on `/v1/responses` and `/v1/messages`, `tokens_prompt`/`tokens_completion` on `/v1/generation`.
- **Reasoning output is named differently on every route**: `message.reasoning` on `/v1/chat/completions`, an `output[]` item of `type: "reasoning"` on `/v1/responses`, a `content[]` block of `type: "thinking"` on `/v1/messages`.
- `usage.cost` is a number while `provider_metadata.gateway.cost` and the `/v1/credits` fields are strings.
- Wrong-method requests return `405` with an **empty body** (`POST`/`DELETE /v1/models`, `GET /v1/chat/completions`). Do not parse the response.
- Uptime, `latency_last_1h`, and `throughput_last_1h` under `/endpoints` are live metrics that change between calls — do not snapshot them as facts.
- A model served by multiple providers can have different context limits and pricing per provider. Check `/endpoints` rather than trusting the catalog's top-level `context_window`.
- Natively, AI Gateway accepts an AI Gateway API key or a Vercel OIDC token; through Maton the credential is injected from the connection (method `API_KEY`, no OAuth browser step).
- An unknown `Maton-Connection` returns a Maton-shaped `404` (`{"message": "Connection ... not found", "type": "Not Found", "code": 404}`), not an AI Gateway error.
- No endpoint takes bracketed query parameters, so `curl -g` is not normally needed here.

## Error Handling

| Status | Meaning |
|--------|---------|
| 400 | Invalid request body or query — missing `messages`/`input`/`max_tokens`, malformed JSON, bad generation ID format |
| 401 | Invalid, missing, or expired Maton credential |
| 403 | `customer_verification_required` (inference needs a card on file, or the model ID does not resolve) or `forbidden` (`/v1/report` needs a paid **plan**) |
| 404 | Unknown model (`model_not_found`), unknown path under `/v1` (`not_found_error`), missing `/v1` prefix (HTML body), unknown `Maton-Connection`, or a generation not yet ingested |
| 405 | Wrong method for the route — empty body |
| 429 | `rate_limit_exceeded` — free-tier credits throttled account-wide; no `Retry-After` header |
| 4xx/5xx | Passthrough error from the Vercel AI Gateway API |

## Resources

- [Vercel AI Gateway REST API](https://vercel.com/docs/ai-gateway/sdks-and-apis/rest-api)
- [AI Gateway Overview](https://vercel.com/docs/ai-gateway)
- [Model Catalog](https://vercel.com/ai-gateway/models)
- [Models and Providers](https://vercel.com/docs/ai-gateway/models-and-providers)
- [OpenAI Chat Completions API](https://vercel.com/docs/ai-gateway/sdks-and-apis/openai-chat-completions)
- [Responses API](https://vercel.com/docs/ai-gateway/sdks-and-apis/responses)
- [Authentication](https://vercel.com/docs/ai-gateway/authentication-and-byok/authentication)
- [Pricing and Credits](https://vercel.com/docs/ai-gateway/pricing)
- [Observability](https://vercel.com/docs/ai-gateway/observability-and-spend/observability)
