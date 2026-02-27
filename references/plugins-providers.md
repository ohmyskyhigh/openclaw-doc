# Plugins Providers


---

<!-- Page: Plugin Agent Tools -->

## Plugin Agent Tools

**Source:** https://docs.openclaw.ai/plugins/agent-tools

[Skip to main content](https://docs.openclaw.ai/plugins/agent-tools#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Plugin Agent Tools

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Plugin agent tools](https://docs.openclaw.ai/plugins/agent-tools#plugin-agent-tools)
- [Basic tool](https://docs.openclaw.ai/plugins/agent-tools#basic-tool)
- [Optional tool (opt‑in)](https://docs.openclaw.ai/plugins/agent-tools#optional-tool-opt%E2%80%91in)
- [Rules + tips](https://docs.openclaw.ai/plugins/agent-tools#rules-%2B-tips)

# [​](https://docs.openclaw.ai/plugins/agent-tools\#plugin-agent-tools)  Plugin agent tools

OpenClaw plugins can register **agent tools** (JSON‑schema functions) that are exposed
to the LLM during agent runs. Tools can be **required** (always available) or
**optional** (opt‑in).Agent tools are configured under `tools` in the main config, or per‑agent under
`agents.list[].tools`. The allowlist/denylist policy controls which tools the agent
can call.

## [​](https://docs.openclaw.ai/plugins/agent-tools\#basic-tool)  Basic tool

Copy

```
import { Type } from "@sinclair/typebox";

export default function (api) {
  api.registerTool({
    name: "my_tool",
    description: "Do a thing",
    parameters: Type.Object({
      input: Type.String(),
    }),
    async execute(_id, params) {
      return { content: [{ type: "text", text: params.input }] };
    },
  });
}
```

## [​](https://docs.openclaw.ai/plugins/agent-tools\#optional-tool-opt%E2%80%91in)  Optional tool (opt‑in)

Optional tools are **never** auto‑enabled. Users must add them to an agent
allowlist.

Copy

```
export default function (api) {
  api.registerTool(
    {
      name: "workflow_tool",
      description: "Run a local workflow",
      parameters: {
        type: "object",
        properties: {
          pipeline: { type: "string" },
        },
        required: ["pipeline"],
      },
      async execute(_id, params) {
        return { content: [{ type: "text", text: params.pipeline }] };
      },
    },
    { optional: true },
  );
}
```

Enable optional tools in `agents.list[].tools.allow` (or global `tools.allow`):

Copy

```
{
  agents: {
    list: [\
      {\
        id: "main",\
        tools: {\
          allow: [\
            "workflow_tool", // specific tool name\
            "workflow", // plugin id (enables all tools from that plugin)\
            "group:plugins", // all plugin tools\
          ],\
        },\
      },\
    ],
  },
}
```

Other config knobs that affect tool availability:

- Allowlists that only name plugin tools are treated as plugin opt-ins; core tools remain
enabled unless you also include core tools or groups in the allowlist.
- `tools.profile` / `agents.list[].tools.profile` (base allowlist)
- `tools.byProvider` / `agents.list[].tools.byProvider` (provider‑specific allow/deny)
- `tools.sandbox.tools.*` (sandbox tool policy when sandboxed)

## [​](https://docs.openclaw.ai/plugins/agent-tools\#rules-+-tips)  Rules + tips

- Tool names must **not** clash with core tool names; conflicting tools are skipped.
- Plugin ids used in allowlists must not clash with core tool names.
- Prefer `optional: true` for tools that trigger side effects or require extra
binaries/credentials.

Ctrl+I

---

---

<!-- Page: OpenAI Provider -->

## OpenAI

**Source:** https://docs.openclaw.ai/providers/openai

[Skip to main content](https://docs.openclaw.ai/providers/openai#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Providers

OpenAI

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [OpenAI](https://docs.openclaw.ai/providers/openai#openai)
- [Option A: OpenAI API key (OpenAI Platform)](https://docs.openclaw.ai/providers/openai#option-a-openai-api-key-openai-platform)
- [CLI setup](https://docs.openclaw.ai/providers/openai#cli-setup)
- [Config snippet](https://docs.openclaw.ai/providers/openai#config-snippet)
- [Option B: OpenAI Code (Codex) subscription](https://docs.openclaw.ai/providers/openai#option-b-openai-code-codex-subscription)
- [CLI setup (Codex OAuth)](https://docs.openclaw.ai/providers/openai#cli-setup-codex-oauth)
- [Config snippet (Codex subscription)](https://docs.openclaw.ai/providers/openai#config-snippet-codex-subscription)
- [Codex transport default](https://docs.openclaw.ai/providers/openai#codex-transport-default)
- [Notes](https://docs.openclaw.ai/providers/openai#notes)

# [​](https://docs.openclaw.ai/providers/openai\#openai)  OpenAI

OpenAI provides developer APIs for GPT models. Codex supports **ChatGPT sign-in** for subscription
access or **API key** sign-in for usage-based access. Codex cloud requires ChatGPT sign-in.

## [​](https://docs.openclaw.ai/providers/openai\#option-a-openai-api-key-openai-platform)  Option A: OpenAI API key (OpenAI Platform)

**Best for:** direct API access and usage-based billing.
Get your API key from the OpenAI dashboard.

### [​](https://docs.openclaw.ai/providers/openai\#cli-setup)  CLI setup

Copy

```
openclaw onboard --auth-choice openai-api-key
# or non-interactive
openclaw onboard --openai-api-key "$OPENAI_API_KEY"
```

### [​](https://docs.openclaw.ai/providers/openai\#config-snippet)  Config snippet

Copy

```
{
  env: { OPENAI_API_KEY: "sk-..." },
  agents: { defaults: { model: { primary: "openai/gpt-5.1-codex" } } },
}
```

## [​](https://docs.openclaw.ai/providers/openai\#option-b-openai-code-codex-subscription)  Option B: OpenAI Code (Codex) subscription

**Best for:** using ChatGPT/Codex subscription access instead of an API key.
Codex cloud requires ChatGPT sign-in, while the Codex CLI supports ChatGPT or API key sign-in.

### [​](https://docs.openclaw.ai/providers/openai\#cli-setup-codex-oauth)  CLI setup (Codex OAuth)

Copy

```
# Run Codex OAuth in the wizard
openclaw onboard --auth-choice openai-codex

# Or run OAuth directly
openclaw models auth login --provider openai-codex
```

### [​](https://docs.openclaw.ai/providers/openai\#config-snippet-codex-subscription)  Config snippet (Codex subscription)

Copy

```
{
  agents: { defaults: { model: { primary: "openai-codex/gpt-5.3-codex" } } },
}
```

### [​](https://docs.openclaw.ai/providers/openai\#codex-transport-default)  Codex transport default

OpenClaw uses `pi-ai` for model streaming. For `openai-codex/*` models you can set
`agents.defaults.models.<provider/model>.params.transport` to select transport:

- Default is `"auto"` (WebSocket-first, then SSE fallback).
- `"sse"`: force SSE
- `"websocket"`: force WebSocket
- `"auto"`: try WebSocket, then fall back to SSE

Copy

```
{
  agents: {
    defaults: {
      model: { primary: "openai-codex/gpt-5.3-codex" },
      models: {
        "openai-codex/gpt-5.3-codex": {
          params: {
            transport: "auto",
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/openai\#notes)  Notes

- Model refs always use `provider/model` (see [/concepts/models](https://docs.openclaw.ai/concepts/models)).
- Auth details + reuse rules are in [/concepts/oauth](https://docs.openclaw.ai/concepts/oauth).

[Anthropic](https://docs.openclaw.ai/providers/anthropic) [OpenRouter](https://docs.openclaw.ai/providers/openrouter)

Ctrl+I

---
