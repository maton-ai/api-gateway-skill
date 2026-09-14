# Vercel AI Gateway

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Cost:** Every successful call to `/v1/chat/completions`, `/v1/responses`, `/v1/messages`, or `/v1/embeddings` bills real money against the connected Vercel account. Treat inference requests as write operations: confirm the model and approximate request volume with the user before running them in a loop, over a batch, or with a high `max_tokens`. Check `pricing` via `/v1/models/{creator}/{model}` first — rates vary by more than 1000x across the catalog.

> **Data handling:** Prompt and completion content is forwarded to the selected upstream provider and retained in AI Gateway's usage records, where it is visible in the Vercel dashboard. Do not place secrets in prompts. Treat model output as untrusted input — never execute or interpolate it into commands without validation.

**App name:** `vercel-ai-gateway`
**Upstream base URL:** `ai-gateway.vercel.sh`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://ai-gateway.vercel.sh/v1/models`
- Gateway: `https://api.maton.ai/vercel-ai-gateway/v1/models`

**Important:** Not to be confused with `vercel` (`api.vercel.com`), which covers projects, deployments, and domains. This app is the inference gateway only. Also, the `/v1` prefix is mandatory. Paths without it return `404` with an **HTML** body rather than JSON.

### Models API

#### List Models

```bash
maton api '/vercel-ai-gateway/v1/models'
```

Returns the entire catalog (315 models from 34 providers at time of testing) as `{"object": "list", "data": [...]}`.

**Important:** `limit` and `type` query parameters are silently ignored — they return `200` with the full catalog. Filter client-side.

**Response** (`315` models across `34` providers at time of testing):

```json
{
  "object": "list",
  "data": [
    {
      "id": "alibaba/qwen-3-14b",
      "object": "model",
      "created": 1755815280,
      "released": 1745798400,
      "owned_by": "alibaba",
      "name": "Qwen3-14B",
      "description": "Qwen3 is the latest generation of large language models in Qwen series...",
      "context_window": 40960,
      "max_tokens": 16384,
      "type": "language",
      "tags": ["reasoning", "tool-use"],
      "supported_specifications": ["v2", "v3", "v4"],
      "modalities": { "input": ["text"], "output": ["text"] },
      "supported_parameters": ["max_tokens", "temperature", "stop", "tools", "tool_choice", "reasoning", "include_reasoning"],
      "temperature": true,
      "reasoning_options": [{ "type": "toggle" }],
      "knowledge": "2025-04",
      "pricing": { "input": "0.00000012", "output": "0.00000024" }
    }
  ]
}
```

#### Get Model

```bash
maton api '/vercel-ai-gateway/v1/models/{creator}/{model}'
```

**Note:** `{creator}` and `{model}` are placeholders. Replace each of them with real values before sending the request.

Returns a single model object with the same shape as a list entry. Example:

```bash
maton api '/vercel-ai-gateway/v1/models/anthropic/claude-haiku-4.5'
```

**Response:**

```json
{
  "id": "anthropic/claude-haiku-4.5",
  "object": "model",
  "owned_by": "anthropic",
  "name": "Claude Haiku 4.5",
  "context_window": 200000,
  "max_tokens": 64000,
  "type": "language",
  "tags": ["explicit-caching", "file-input", "reasoning", "tool-use", "vision", "web-search"],
  "regions": ["eu", "us"],
  "modalities": { "input": ["text", "image", "pdf"], "output": ["text"] },
  "reasoning_options": [{ "type": "toggle" }, { "type": "budget_tokens", "min": 1024 }],
  "knowledge": "2025-02-28",
  "interleaved": true,
  "pricing": {
    "input": "0.000001",
    "output": "0.000005",
    "input_cache_read": "0.0000001",
    "input_cache_write": "0.00000125",
    "regional": {
      "us": { "input": "0.0000011", "output": "0.0000055", "input_cache_read": "0.00000011", "input_cache_write": "0.000001375" },
      "eu": { "input": "0.0000011", "output": "0.0000055", "input_cache_read": "0.00000011", "input_cache_write": "0.000001375" }
    }
  }
}
```

**Note:** This single-model route is not in the published REST reference but works.

**Note:** Only these fields are present on every model (verified across all 315): `id`, `object`, `created`, `released`, `owned_by`, `name`, `description`, `type`, `supported_specifications`, `modalities`, `pricing`. Everything else is conditional — read it with `.get()`:

| Field | Coverage | Absent when |
|-------|----------|-------------|
| `context_window`, `max_tokens` | 307/315 | `transcription` and `speech` models |
| `tags` | 248/315 | model has no feature tags |
| `supported_parameters`, `temperature` | 214/315 | non-text model types |
| `knowledge` | 149/315 | training cutoff not published |
| `reasoning_options` | 110/315 | model has no reasoning mode |
| `regions` | 45/315 | no regional routing |
| `video_capabilities` | 30/315 | not a `video` model |
| `interleaved` | 13/315 | no interleaved thinking |
| `deprecated_at` | 1/315 | model is not scheduled for removal |

`deprecated_at` is a millisecond epoch. Check for it before pinning a model in production code — exactly one model in the catalog carried it during testing (`openai/gpt-5.3-chat`).

`video` models carry a `video_capabilities` object describing supported operations, resolutions, aspect ratios, durations, fps, and input limits:

```json
{
  "video_capabilities": {
    "supported_operations": ["text-to-video"],
    "supported_resolutions": ["480p", "720p", "1080p"],
    "supported_aspect_ratios": ["16:9", "9:16", "1:1", "4:3", "3:4"],
    "supported_durations_seconds": [5, 10],
    "generate_audio": true,
    "supported_fps": [24],
    "input_limits": { "text": { "max_chars": 1500 } }
  }
}
```

#### List Model Endpoints

```bash
maton api '/vercel-ai-gateway/v1/models/{creator}/{model}/endpoints'
```

**Note:** `{creator}` and `{model}` are placeholders. Replace each of them with real values before sending the request.

Shows every upstream provider that can serve a model, with per-provider pricing, limits, uptime, and latency — this is how you tell why two providers of the "same" model behave differently.

```json
{
  "data": {
    "id": "anthropic/claude-haiku-4.5",
    "name": "Claude Haiku 4.5",
    "architecture": {
      "tokenizer": null,
      "instruct_type": null,
      "modality": "text+image+file→text",
      "input_modalities": ["text", "image", "file"],
      "output_modalities": ["text"]
    },
    "reasoning": { "mandatory": false, "supports_max_tokens": true },
    "endpoints": [
      {
        "name": "anthropic | anthropic/claude-haiku-4.5",
        "model_name": "Claude Haiku 4.5",
        "provider_name": "anthropic",
        "context_length": 200000,
        "max_completion_tokens": 64000,
        "max_prompt_tokens": null,
        "quantization": null,
        "supported_parameters": ["max_tokens", "temperature", "stop", "tools", "tool_choice", "reasoning", "include_reasoning"],
        "tags": ["explicit-caching", "file-input", "reasoning", "tool-use", "vision", "web-search"],
        "pricing": {
          "prompt": "0.000001",
          "completion": "0.000005",
          "request": "0",
          "image": "0",
          "image_output": "0",
          "web_search": "0",
          "internal_reasoning": "0",
          "input_cache_read": "0.0000001",
          "input_cache_write": "0.00000125",
          "discount": 0
        },
        "status": 0,
        "supports_implicit_caching": false,
        "uptime_last_15m": 99.8486,
        "uptime_last_1h": 99.8806,
        "uptime_last_1d": 99.9745,
        "latency_last_1h": { "p50": 817, "p95": 2305.85 },
        "throughput_last_1h": { "p50": 93.5, "p95": 105.05 }
      }
    ]
  }
}
```

Shape differences from `/v1/models` to watch for:

- `data` is an **object** here, not an array.
- Endpoint pricing uses `prompt`/`completion`; model pricing uses `input`/`output` for the same values.
- Video capabilities are under `capabilities`, not `video_capabilities`.
- `reasoning` is present only for models with a reasoning mode (`{"mandatory": bool, "supports_max_tokens": bool}` — `supports_max_tokens` itself is conditional). It is absent for models like `anthropic/claude-opus-5` and `openai/gpt-4o-mini`.
- Per-endpoint, `context_length`, `tags`, `max_completion_tokens`, and `inference_regions` are conditional; the rest of the endpoint object is always present.

`latency_last_1h` (ms) and `throughput_last_1h` (tokens/sec) each carry `p50` and `p95`. Together with the uptime fields these are live operational metrics — good for provider selection, but they change between calls, so do not snapshot them as facts.

This route works for **every** model type, not just `language` — embedding, image, video, speech, transcription, reranking, and realtime models all return endpoint lists.

Provider counts vary widely, which is the point of the route: `anthropic/claude-opus-5` is served by 4 (`anthropic`, `bedrock`, `claudeaws`, `vertexAnthropic`), `openai/gpt-4o-mini` by 2 (`azure`, `openai`), and `alibaba/qwen-3-14b` by 1 (`deepinfra`).

An unknown model returns `404` with `model_not_found`:

```bash
maton api '/vercel-ai-gateway/v1/models/nosuch/nomodel/endpoints'

# 404 {"error": {"message": "...", "type": "model_not_found"}}
```

### Usage API

#### Get Credit Balance

```bash
maton api '/vercel-ai-gateway/v1/credits'
```

```json
{ "balance": "4.99999992", "total_used": "0.00000008" }
```

Values are decimal **strings** in USD, not numbers, and carry sub-cent precision (8 decimals observed) — never round them for accounting. A `"balance": "0"` alongside a `403` on inference is the signature of the card gate, not exhausted credits.

Both values are decimal **strings** in USD, not numbers — parse before comparing (`float(r["balance"])`). They carry full sub-cent precision (8 decimal places observed), so never round them for accounting.

`balance` is `"0"` on an account with no card on file, and jumps to the free-credit grant (`"5"`) once one is added. A `"0"` balance alongside a `403` on inference is the signature of the card gate, not of exhausted credits.

#### Get Generation Usage

```bash
maton api '/vercel-ai-gateway/v1/generation?id=gen_{ulid}'
```

Cost and token usage for one completed request. The ID comes from the `id` field of a chat completion response (or the first streaming chunk).

**Usage events are ingested asynchronously** — an immediate lookup returns `404 Usage event not found`. Measured delay was **~9s** (404 at 0/3/6s, 200 at 9s), so retry with backoff instead of treating the first 404 as failure.

This route uses **different field names** from the inline `usage` on an inference response: `tokens_prompt`/`tokens_completion` (plus `native_tokens_*` for provider-reported counts), `total_cost`, `provider_name`, `latency`, `streamed`, and `finish_reason`. Costs here are numbers, unlike the strings from `/v1/credits`.

Looks up cost and token usage for one completed request. Generation IDs have the form `gen_<ulid>` and come from:

- the `id` field on a `/v1/chat/completions`, `/v1/responses`, or `/v1/messages` response
- the top-level `generationId` on the same responses
- the `id` on every chunk of a streaming response
- `provider_metadata.gateway.generationId` (or `providerMetadata...` on `/v1/embeddings` and `/v1/responses`)

**Response:**

```json
{
  "data": {
    "id": "gen_01KZ7DPJ3VGJXG6NF1WEJWE03M",
    "created_at": "2026-08-04T22:18:25.000Z",
    "model": "inclusionai/ling-3.0-flash-free",
    "provider_name": "novita",
    "streamed": false,
    "finish_reason": "length",
    "total_cost": 0,
    "upstream_inference_cost": 0,
    "usage": 0,
    "is_byok": false,
    "latency": 963,
    "generation_time": 963,
    "tokens_prompt": 21,
    "tokens_completion": 10,
    "native_tokens_prompt": 21,
    "native_tokens_completion": 10,
    "native_tokens_reasoning": 0,
    "native_tokens_cached": 0,
    "native_tokens_cache_creation": 0,
    "billable_web_search_calls": 0
  }
}
```

This route uses different field names from the inline `usage` object on an inference response — `tokens_prompt`/`tokens_completion`, plus `native_tokens_*` for provider-reported counts (see [Cross-Route Differences](#cross-route-differences)). Costs here are numbers, unlike the strings from `/v1/credits`.

**Usage events are ingested asynchronously.** Immediately after a request, this endpoint returns `404 Usage event not found` — that is expected, not an error. Measured ingestion delay on a non-streamed completion was **~9 seconds** (404 at 0s, 3s, and 6s; 200 at 9s), so poll with a short backoff rather than treating the first 404 as failure:

Error responses on this route are **flat** (`{"error": "..."}`), unlike the nested `{"error": {"message", "type"}}` used elsewhere:

| Request | Status | Body |
|---------|--------|------|
| valid-format ID, no event yet | 404 | `{"error":"Usage event not found","id":"gen_...","message":"No usage event found with ID gen_..."}` |
| `id` omitted | 400 | `{"error":"id is required"}` |
| `id=notaulid` | 400 | `{"error":"Invalid generation ID format. Expected format: gen_<ulid>"}` |

#### Get Spend Report

```bash
maton api '/vercel-ai-gateway/v1/report?start_date=2026-08-01&end_date=2026-08-04'
```

**Requires a paid Vercel plan — a separate gate from having a card on file.** On an account with a valid card and positive balance, where every other endpoint returns `200`, this still returns `403 forbidden`. The plan check precedes parameter validation, so its error says nothing about your query string. This is the only endpoint here whose success shape is unverified; use `/v1/generation` or `/v1/credits` for cost data instead.

Aggregated spend over a date range.

**Requires a paid Vercel plan — this is a separate gate from having a credit card on file.** On an account with a valid card and a positive credit balance, where all four inference routes and every other endpoint return `200`, this route still returns `403`:

```json
{ "error": { "message": "Spend report access requires a paid plan. Please upgrade your plan to use this feature.", "type": "forbidden" } }
```

The plan check runs before parameter validation, so an error from this route says nothing about your query string. This is the one endpoint in this document whose success payload has not been observed; treat its response shape as unverified.

For per-request cost data without a paid plan, use `/v1/generation` (per generation) or `/v1/credits` (running total) instead.

### Inference API

All inference routes are OpenAI/Anthropic-compatible. Model IDs are always `{creator}/{model}`.

#### Cross-Route Differences

The same data is named differently on each route. Check this before writing parsing code shared across them:

| | `/chat/completions` | `/responses` | `/messages` | `/embeddings` | `/generation` |
|---|---|---|---|---|---|
| **Token usage** | `prompt_tokens`, `completion_tokens` | `input_tokens`, `output_tokens` | `input_tokens`, `output_tokens` | `prompt_tokens`, `total_tokens` | `tokens_prompt`, `tokens_completion` |
| **Reasoning** | `message.reasoning` | `output[]` item `type: "reasoning"` | `content[]` block `type: "thinking"` | — | `native_tokens_reasoning` |
| **Metadata key** | `provider_metadata` | `providerMetadata` | `provider_metadata` | `providerMetadata` | — |
| **Generation ID** | `id` + `generationId` | `id` | `id` | metadata only | `data.id` |
| **Error envelope** | `{error: {...}}` | `error: null` on success | `{type: "error", error: {...}}` | `{error: {...}}` | `{error: "string"}` |

Two further traps:

- `usage.cost` is a **number** while `provider_metadata.gateway.cost` and the `/v1/credits` fields are **strings**.
- `provider_metadata.gateway.routing` reveals which upstream actually served a request (`resolvedProvider`, `finalProvider`, plus a per-attempt log with upstream status codes) — the only way to attribute a response when several providers serve one model.

#### Chat Completions

```bash
maton api -X POST '/vercel-ai-gateway/v1/chat/completions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "model": "anthropic/claude-haiku-4.5",
  "messages": [
    { "role": "user", "content": "Say hello in five words." }
  ],
  "max_tokens": 100
}
JSON
```

Add `"stream": true` for a `text/event-stream` of `data: {...}` chunks ending in `data: [DONE]`. The first chunk's `delta` carries only `{"role": "assistant"}`; `usage`, `provider_metadata`, and `generationId` arrive only on the final chunk (the one with `finish_reason`). `data: [DONE]` is a bare sentinel, not JSON.

Responses carry `id` (a `gen_<ulid>`), `choices[].message.content`, `usage`, and a gateway `provider_metadata` block. `provider_metadata.gateway.routing.finalProvider` names the upstream that actually served the request — the only way to attribute a response when several providers serve one model. `generationId` appears both at the top level and under `provider_metadata.gateway`.

Reasoning models add `message.reasoning` and `message.reasoning_details`.

**Response** (trimmed — `provider_metadata` is large):

```json
{
  "id": "gen_01KZ7DP24DBPWN0TR4VAG1P9X6",
  "object": "chat.completion",
  "created": 1785881890,
  "model": "inclusionai/ling-3.0-flash-free",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Hello there, how are you?",
        "reasoning": "The user is asking me to say hello in five words...",
        "reasoning_details": [
          { "type": "reasoning.text", "text": "...", "format": "unknown", "index": 0 }
        ],
        "provider_metadata": {
          "gateway": {
            "routing": {
              "originalModelId": "inclusionai/ling-3.0-flash-free",
              "resolvedProvider": "novita",
              "finalProvider": "novita",
              "fallbacksAvailable": [],
              "modelAttempts": [ { "success": true, "providerAttempts": [ { "provider": "novita", "credentialType": "system", "statusCode": 200 } ] } ]
            },
            "cost": "0",
            "marketCost": "0",
            "gatewayCost": "0",
            "generationId": "gen_01KZ7DP24DBPWN0TR4VAG1P9X6"
          }
        }
      },
      "logprobs": null,
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 26,
    "completion_tokens": 36,
    "total_tokens": 62,
    "cost": 0,
    "is_byok": false,
    "completion_tokens_details": { "reasoning_tokens": 30, "reasoning_tokens_estimated": true, "image_tokens": 0 },
    "prompt_tokens_details": { "cached_tokens": 0, "audio_tokens": 0, "video_tokens": 0 },
    "cost_details": { "upstream_inference_cost": null, "upstream_inference_prompt_cost": 0, "upstream_inference_completions_cost": 0 },
    "cache_creation_input_tokens": 0,
    "market_cost": 0
  },
  "system_fingerprint": "fp_yyoa7nu1k5",
  "generationId": "gen_01KZ7DP24DBPWN0TR4VAG1P9X6"
}
```

Beyond the standard OpenAI fields:

- **`generationId` appears twice** — at the top level and under `choices[].message.provider_metadata.gateway`. Both equal `id`. Any of the three works for `/v1/generation`.
- `provider_metadata.gateway.routing` shows which upstream actually served the request (`resolvedProvider`, `finalProvider`), what fallbacks existed, and a per-attempt log with upstream status codes — this is how you tell *which* provider produced a given answer.
- Reasoning models add `message.reasoning` and `message.reasoning_details` alongside `content`. `usage.completion_tokens_details.reasoning_tokens` may carry `reasoning_tokens_estimated: true`, meaning the count is inferred rather than reported by the provider.
- `usage.cost` is a **number** here, while `provider_metadata.gateway.cost` is a **string**.

#### Responses

```bash
maton api -X POST '/vercel-ai-gateway/v1/responses' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "model": "openai/gpt-4o-mini",
  "input": "Say hello in five words."
}
JSON
```

`input` is required (string or structured array).

Returns `output` as an **array of typed items**, not a single message — reasoning models emit a `type: "reasoning"` item before the `type: "message"` item, so filter by `type` rather than indexing `output[0]`. Note that `error` is present but **`null` on success**: check the value, not key presence.

OpenAI Responses API shape. `input` is required and accepts a string or a structured array.

**Response** (trimmed — the full object carries ~35 top-level fields):

```json
{
  "id": "gen_01KZ7E72711DTG68QKAB0KWV2B",
  "object": "response",
  "status": "completed",
  "created_at": 1785882447,
  "completed_at": 1785882447,
  "error": null,
  "incomplete_details": null,
  "output": [
    {
      "type": "reasoning",
      "id": "rs_1785882447953_oj4ccjeuzzg",
      "summary": [],
      "content": [ { "type": "reasoning_text", "text": "The user wants me to say hello in exactly five words..." } ]
    },
    {
      "type": "message",
      "id": "msg_1785882447953_5p6p388zyoh",
      "status": "completed",
      "role": "assistant",
      "content": [ { "type": "output_text", "text": "Hello, nice to meet you!", "annotations": [], "logprobs": [] } ]
    }
  ],
  "usage": {
    "input_tokens": 26,
    "output_tokens": 251,
    "total_tokens": 277,
    "input_tokens_details": { "cached_tokens": 0 },
    "output_tokens_details": { "reasoning_tokens": 0 }
  },
  "model": "inclusionai/ling-3.0-flash-free",
  "text": { "format": { "type": "text" } },
  "temperature": 1,
  "top_p": 1,
  "tool_choice": "auto",
  "tools": [],
  "truncation": "disabled",
  "service_tier": "auto",
  "status": "completed"
}
```

Traps in this shape:

- **`error` is present but `null` on success.** `body["error"]["type"]` after a check on key *presence* will crash — check the value.
- Output is an **array of typed items**, not a single message. Reasoning models emit a `reasoning` item before the `message` item, so the assistant text is not reliably `output[0]` — filter by `type == "message"`, then read `content[].text` where `type == "output_text"`.
- `usage.output_tokens_details.reasoning_tokens` was `0` even for a response with a visible reasoning item, while `output_tokens` (251) far exceeded the visible text — do not rely on it to bill reasoning.

#### Messages (Anthropic-shaped)

```bash
maton api -X POST '/vercel-ai-gateway/v1/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "model": "anthropic/claude-haiku-4.5",
  "max_tokens": 100,
  "messages": [
    { "role": "user", "content": "Say hello in five words." }
  ]
}
JSON
```

`max_tokens` is **required** here, unlike on `/v1/chat/completions`. Uses Anthropic's error envelope: `{"type": "error", "error": {...}}`.

Two differences from Anthropic's native API: the `content` array returns the `text` block **before** the `thinking` block (the reverse of native ordering, so filter by `type`), and `id` is a Vercel `gen_<ulid>` rather than an Anthropic `msg_...` ID.

Anthropic Messages API shape, including its distinct error envelope (`{"type": "error", "error": {...}}`). `max_tokens` is **required** here, unlike on `/v1/chat/completions`.

**Response:**

```json
{
  "id": "gen_01KZ7EGR3JRF0GM4H07G0DG3CQ",
  "type": "message",
  "role": "assistant",
  "content": [
    { "type": "text", "text": "Hello there, how are you?" },
    { "type": "thinking", "thinking": "The user is asking me to say hello in five words..." }
  ],
  "model": "inclusionai/ling-3.0-flash-free",
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "usage": { "input_tokens": 26, "output_tokens": 36 },
  "provider_metadata": { "gateway": { "routing": { "..." : "..." }, "generationId": "gen_01KZ7EGR3JRF0GM4H07G0DG3CQ" } }
}
```

Differences from Anthropic's native API worth noting:

- **`content` ordering is inverted.** The `text` block comes **before** the `thinking` block, whereas Anthropic natively emits `thinking` first. Never assume `content[0]` is the reasoning block — filter by `type`.
- `usage` carries only `input_tokens` and `output_tokens` — no cache or reasoning breakdown.
- `id` is a Vercel `gen_<ulid>`, not an Anthropic `msg_...` ID, and it works with `/v1/generation`.
- A gateway-specific `provider_metadata` key is added, which native Anthropic responses do not have.

#### Embeddings

```bash
maton api -X POST '/vercel-ai-gateway/v1/embeddings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "model": "openai/text-embedding-3-small",
  "input": ["first string", "second string"]
}
JSON
```

Only models with `"type": "embedding"` (26 in the catalog) work here. One `data` entry per input string, ordered by `index`; `openai/text-embedding-3-small` returns 1536 dimensions. This route has **no top-level `id`** — the generation ID is only under `providerMetadata.gateway.generationId`.

`input` is required and accepts a string or an array of strings. Only models with `"type": "embedding"` work here (26 in the catalog).

**Response:**

```json
{
  "object": "list",
  "data": [
    { "object": "embedding", "index": 0, "embedding": [0.0121002197265625, -0.022552490234375, 0.02215576171875, "..."] },
    { "object": "embedding", "index": 1, "embedding": ["..."] }
  ],
  "model": "openai/text-embedding-3-small",
  "usage": { "prompt_tokens": 4, "total_tokens": 4 },
  "providerMetadata": { "gateway": { "..." : "..." } }
}
```

- One `data` entry per input string, ordered by `index`. `openai/text-embedding-3-small` returns **1536** dimensions.
- **There is no top-level `id`** on this route, so no generation ID is available in the response body to pass to `/v1/generation` — it appears only under `providerMetadata.gateway.generationId`.
- `usage` has `prompt_tokens` and `total_tokens` but **no** `completion_tokens`.

### Model Types

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

### Pricing Shapes

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

### Conditional Fields

Only these are present on all 315 models: `id`, `object`, `created`, `released`, `owned_by`, `name`, `description`, `type`, `supported_specifications`, `modalities`, `pricing`.

Everything else is conditional: `context_window`/`max_tokens` (307), `tags` (248), `supported_parameters`/`temperature` (214), `knowledge` (149), `reasoning_options` (110), `regions` (45), `video_capabilities` (30), `interleaved` (13), `deprecated_at` (1). Use safe accessors.

### Account States Affecting Inference

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

### Notes

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

### Error Handling

| Status | Meaning |
|--------|---------|
| 400 | Invalid request body or query — missing `messages`/`input`/`max_tokens`, malformed JSON, bad generation ID format |
| 401 | Invalid, missing, or expired Maton credential |
| 403 | `customer_verification_required` (inference needs a card on file, or the model ID does not resolve) or `forbidden` (`/v1/report` needs a paid **plan**) |
| 404 | Unknown model (`model_not_found`), unknown path under `/v1` (`not_found_error`), missing `/v1` prefix (HTML body), unknown `Maton-Connection`, or a generation not yet ingested |
| 405 | Wrong method for the route — empty body |
| 429 | `rate_limit_exceeded` — free-tier credits throttled account-wide; no `Retry-After` header |
| 4xx/5xx | Passthrough error from the Vercel AI Gateway API |

### Resources

- [Vercel AI Gateway REST API](https://vercel.com/docs/ai-gateway/sdks-and-apis/rest-api)
- [AI Gateway Overview](https://vercel.com/docs/ai-gateway)
- [Model Catalog](https://vercel.com/ai-gateway/models)
- [Models and Providers](https://vercel.com/docs/ai-gateway/models-and-providers)
- [OpenAI Chat Completions API](https://vercel.com/docs/ai-gateway/sdks-and-apis/openai-chat-completions)
- [Responses API](https://vercel.com/docs/ai-gateway/sdks-and-apis/responses)
- [Authentication](https://vercel.com/docs/ai-gateway/authentication-and-byok/authentication)
- [Pricing and Credits](https://vercel.com/docs/ai-gateway/pricing)
- [Observability](https://vercel.com/docs/ai-gateway/observability-and-spend/observability)
- [Maton CLI Manual](https://cli.maton.ai/manual)
