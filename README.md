# Maton API Gateway

API gateway for calling third-party APIs with managed auth.

Call native API endpoints directly with a single API key.

## Quick Start

```bash
# Send a Slack message
curl -s -X POST 'https://gateway.maton.ai/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d '{"channel": "C0123456", "text": "Hello from gateway!"}'
```

## Getting Your API Key

1. Sign in or create an account at [maton.ai](https://maton.ai)
2. Go to [maton.ai/settings](https://maton.ai/settings)
3. Click the copy button to copy your API key

```bash
export MATON_API_KEY="YOUR_API_KEY"
```

## Installation

### Skills CLI

Install the skill directly into any supported agent:

```bash
npx skills add maton-ai/api-gateway-skill
```

### Plugins

We ship plugins that bundle the api-gateway skill for popular agent harnesses.

**Claude Code** — add this repo as a plugin marketplace, then install the plugin:

```bash
claude plugin marketplace add maton-ai/api-gateway-skill
claude plugin install api-gateway@maton-plugins
```

**Cursor** — install the plugin directly from this repo:

```bash
/add-plugin maton-ai/api-gateway-skill
```

**Codex** — add this repo as a plugin marketplace, then install the plugin:

```bash
codex plugin marketplace add maton-ai/api-gateway-skill
codex plugin add api-gateway@maton-plugins
```

**Kiro** — shipped as a [Power](https://kiro.dev/docs/powers/). Kiro has no install CLI;
import it from GitHub inside the IDE:

1. Open the **Powers** panel (the Ghosty icon with the lightning bolt).
2. Choose **Add Custom Power → Import power from GitHub**.
3. Paste the power folder URL:
   `https://github.com/maton-ai/api-gateway-skill/tree/main/providers/kiro/.kiro/powers/api-gateway`
4. Click **Install**. Kiro reads `POWER.md` (and its local `references/`) and activates the
   power on demand when your request matches its keywords.

**ClawHub** — install using the clawhub CLI:

```bash
clawhub install @byungkyu/api-gateway
```

## Network Access

The skill requires outbound network access to `maton.ai`.

### Claude Desktop / Claude Cowork

**Free / Pro / Max plans**

Package manager egress is on by default, but allowlisting arbitrary domains is not allowed (as of July 2026). Instead, we recommend using the [Maton MCP server](https://github.com/maton-ai/agent-toolkit), which doesn't require network allowlisting.

**Team / Enterprise plans**

Network egress is disabled by default. An organization owner must enable it:

1. Open **Settings → Capabilities → "Code execution and file creation."**
2. Select **"Allow network egress to package managers and specific domains."**
3. Add `maton.ai` to the allowed-domains list.

Refer to Anthropic's [network access in Claude](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude#h_6b7e833898) for more details.

## Repository Layout

- `SKILL.md` + `references/` — the canonical skill (single source of truth).
- `providers/{claude,cursor,codex}/plugin/` — per-harness plugins, each with a copy of the skill under `skills/api-gateway/`.
- `providers/kiro/.kiro/powers/api-gateway/` — the skill packaged as a Kiro Power (`POWER.md` + local `references/`).
