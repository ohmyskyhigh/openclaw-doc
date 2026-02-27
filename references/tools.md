# Tools


---

<!-- Page: Tools Index -->

## Tools

**Source:** https://docs.openclaw.ai/tools/index

[Skip to main content](https://docs.openclaw.ai/tools/index#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Overview

Tools

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Tools (OpenClaw)](https://docs.openclaw.ai/tools/index#tools-openclaw)
- [Disabling tools](https://docs.openclaw.ai/tools/index#disabling-tools)
- [Tool profiles (base allowlist)](https://docs.openclaw.ai/tools/index#tool-profiles-base-allowlist)
- [Provider-specific tool policy](https://docs.openclaw.ai/tools/index#provider-specific-tool-policy)
- [Tool groups (shorthands)](https://docs.openclaw.ai/tools/index#tool-groups-shorthands)
- [Plugins + tools](https://docs.openclaw.ai/tools/index#plugins-%2B-tools)
- [Tool inventory](https://docs.openclaw.ai/tools/index#tool-inventory)
- [apply\_patch](https://docs.openclaw.ai/tools/index#apply_patch)
- [exec](https://docs.openclaw.ai/tools/index#exec)
- [process](https://docs.openclaw.ai/tools/index#process)
- [loop-detection (tool-call loop guardrails)](https://docs.openclaw.ai/tools/index#loop-detection-tool-call-loop-guardrails)
- [web\_search](https://docs.openclaw.ai/tools/index#web_search)
- [web\_fetch](https://docs.openclaw.ai/tools/index#web_fetch)
- [browser](https://docs.openclaw.ai/tools/index#browser)
- [canvas](https://docs.openclaw.ai/tools/index#canvas)
- [nodes](https://docs.openclaw.ai/tools/index#nodes)
- [image](https://docs.openclaw.ai/tools/index#image)
- [message](https://docs.openclaw.ai/tools/index#message)
- [cron](https://docs.openclaw.ai/tools/index#cron)
- [gateway](https://docs.openclaw.ai/tools/index#gateway)
- [sessions\_list / sessions\_history / sessions\_send / sessions\_spawn / session\_status](https://docs.openclaw.ai/tools/index#sessions_list-%2F-sessions_history-%2F-sessions_send-%2F-sessions_spawn-%2F-session_status)
- [agents\_list](https://docs.openclaw.ai/tools/index#agents_list)
- [Parameters (common)](https://docs.openclaw.ai/tools/index#parameters-common)
- [Recommended agent flows](https://docs.openclaw.ai/tools/index#recommended-agent-flows)
- [Safety](https://docs.openclaw.ai/tools/index#safety)
- [How tools are presented to the agent](https://docs.openclaw.ai/tools/index#how-tools-are-presented-to-the-agent)

# [​](https://docs.openclaw.ai/tools/index\#tools-openclaw)  Tools (OpenClaw)

OpenClaw exposes **first-class agent tools** for browser, canvas, nodes, and cron.
These replace the old `openclaw-*` skills: the tools are typed, no shelling,
and the agent should rely on them directly.

## [​](https://docs.openclaw.ai/tools/index\#disabling-tools)  Disabling tools

You can globally allow/deny tools via `tools.allow` / `tools.deny` in `openclaw.json`
(deny wins). This prevents disallowed tools from being sent to model providers.

Copy

```
{
  tools: { deny: ["browser"] },
}
```

Notes:

- Matching is case-insensitive.
- `*` wildcards are supported (`"*"` means all tools).
- If `tools.allow` only references unknown or unloaded plugin tool names, OpenClaw logs a warning and ignores the allowlist so core tools stay available.

## [​](https://docs.openclaw.ai/tools/index\#tool-profiles-base-allowlist)  Tool profiles (base allowlist)

`tools.profile` sets a **base tool allowlist** before `tools.allow`/`tools.deny`.
Per-agent override: `agents.list[].tools.profile`.Profiles:

- `minimal`: `session_status` only
- `coding`: `group:fs`, `group:runtime`, `group:sessions`, `group:memory`, `image`
- `messaging`: `group:messaging`, `sessions_list`, `sessions_history`, `sessions_send`, `session_status`
- `full`: no restriction (same as unset)

Example (messaging-only by default, allow Slack + Discord tools too):

Copy

```
{
  tools: {
    profile: "messaging",
    allow: ["slack", "discord"],
  },
}
```

Example (coding profile, but deny exec/process everywhere):

Copy

```
{
  tools: {
    profile: "coding",
    deny: ["group:runtime"],
  },
}
```

Example (global coding profile, messaging-only support agent):

Copy

```
{
  tools: { profile: "coding" },
  agents: {
    list: [\
      {\
        id: "support",\
        tools: { profile: "messaging", allow: ["slack"] },\
      },\
    ],
  },
}
```

## [​](https://docs.openclaw.ai/tools/index\#provider-specific-tool-policy)  Provider-specific tool policy

Use `tools.byProvider` to **further restrict** tools for specific providers
(or a single `provider/model`) without changing your global defaults.
Per-agent override: `agents.list[].tools.byProvider`.This is applied **after** the base tool profile and **before** allow/deny lists,
so it can only narrow the tool set.
Provider keys accept either `provider` (e.g. `google-antigravity`) or
`provider/model` (e.g. `openai/gpt-5.2`).Example (keep global coding profile, but minimal tools for Google Antigravity):

Copy

```
{
  tools: {
    profile: "coding",
    byProvider: {
      "google-antigravity": { profile: "minimal" },
    },
  },
}
```

Example (provider/model-specific allowlist for a flaky endpoint):

Copy

```
{
  tools: {
    allow: ["group:fs", "group:runtime", "sessions_list"],
    byProvider: {
      "openai/gpt-5.2": { allow: ["group:fs", "sessions_list"] },
    },
  },
}
```

Example (agent-specific override for a single provider):

Copy

```
{
  agents: {
    list: [\
      {\
        id: "support",\
        tools: {\
          byProvider: {\
            "google-antigravity": { allow: ["message", "sessions_list"] },\
          },\
        },\
      },\
    ],
  },
}
```

## [​](https://docs.openclaw.ai/tools/index\#tool-groups-shorthands)  Tool groups (shorthands)

Tool policies (global, agent, sandbox) support `group:*` entries that expand to multiple tools.
Use these in `tools.allow` / `tools.deny`.Available groups:

- `group:runtime`: `exec`, `bash`, `process`
- `group:fs`: `read`, `write`, `edit`, `apply_patch`
- `group:sessions`: `sessions_list`, `sessions_history`, `sessions_send`, `sessions_spawn`, `session_status`
- `group:memory`: `memory_search`, `memory_get`
- `group:web`: `web_search`, `web_fetch`
- `group:ui`: `browser`, `canvas`
- `group:automation`: `cron`, `gateway`
- `group:messaging`: `message`
- `group:nodes`: `nodes`
- `group:openclaw`: all built-in OpenClaw tools (excludes provider plugins)

Example (allow only file tools + browser):

Copy

```
{
  tools: {
    allow: ["group:fs", "browser"],
  },
}
```

## [​](https://docs.openclaw.ai/tools/index\#plugins-+-tools)  Plugins + tools

Plugins can register **additional tools** (and CLI commands) beyond the core set.
See [Plugins](https://docs.openclaw.ai/tools/plugin) for install + config, and [Skills](https://docs.openclaw.ai/tools/skills) for how
tool usage guidance is injected into prompts. Some plugins ship their own skills
alongside tools (for example, the voice-call plugin).Optional plugin tools:

- [Lobster](https://docs.openclaw.ai/tools/lobster): typed workflow runtime with resumable approvals (requires the Lobster CLI on the gateway host).
- [LLM Task](https://docs.openclaw.ai/tools/llm-task): JSON-only LLM step for structured workflow output (optional schema validation).

## [​](https://docs.openclaw.ai/tools/index\#tool-inventory)  Tool inventory

### [​](https://docs.openclaw.ai/tools/index\#apply_patch)  `apply_patch`

Apply structured patches across one or more files. Use for multi-hunk edits.
Experimental: enable via `tools.exec.applyPatch.enabled` (OpenAI models only).
`tools.exec.applyPatch.workspaceOnly` defaults to `true` (workspace-contained). Set it to `false` only if you intentionally want `apply_patch` to write/delete outside the workspace directory.

### [​](https://docs.openclaw.ai/tools/index\#exec)  `exec`

Run shell commands in the workspace.Core parameters:

- `command` (required)
- `yieldMs` (auto-background after timeout, default 10000)
- `background` (immediate background)
- `timeout` (seconds; kills the process if exceeded, default 1800)
- `elevated` (bool; run on host if elevated mode is enabled/allowed; only changes behavior when the agent is sandboxed)
- `host` (`sandbox | gateway | node`)
- `security` (`deny | allowlist | full`)
- `ask` (`off | on-miss | always`)
- `node` (node id/name for `host=node`)
- Need a real TTY? Set `pty: true`.

Notes:

- Returns `status: "running"` with a `sessionId` when backgrounded.
- Use `process` to poll/log/write/kill/clear background sessions.
- If `process` is disallowed, `exec` runs synchronously and ignores `yieldMs`/`background`.
- `elevated` is gated by `tools.elevated` plus any `agents.list[].tools.elevated` override (both must allow) and is an alias for `host=gateway` \+ `security=full`.
- `elevated` only changes behavior when the agent is sandboxed (otherwise it’s a no-op).
- `host=node` can target a macOS companion app or a headless node host (`openclaw node run`).
- gateway/node approvals and allowlists: [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals).

### [​](https://docs.openclaw.ai/tools/index\#process)  `process`

Manage background exec sessions.Core actions:

- `list`, `poll`, `log`, `write`, `kill`, `clear`, `remove`

Notes:

- `poll` returns new output and exit status when complete.
- `log` supports line-based `offset`/`limit` (omit `offset` to grab the last N lines).
- `process` is scoped per agent; sessions from other agents are not visible.

### [​](https://docs.openclaw.ai/tools/index\#loop-detection-tool-call-loop-guardrails)  `loop-detection` (tool-call loop guardrails)

OpenClaw tracks recent tool-call history and blocks or warns when it detects repetitive no-progress loops.
Enable with `tools.loopDetection.enabled: true` (default is `false`).

Copy

```
{
  tools: {
    loopDetection: {
      enabled: true,
      warningThreshold: 10,
      criticalThreshold: 20,
      globalCircuitBreakerThreshold: 30,
      historySize: 30,
      detectors: {
        genericRepeat: true,
        knownPollNoProgress: true,
        pingPong: true,
      },
    },
  },
}
```

- `genericRepeat`: repeated same tool + same params call pattern.
- `knownPollNoProgress`: repeating poll-like tools with identical outputs.
- `pingPong`: alternating `A/B/A/B` no-progress patterns.
- Per-agent override: `agents.list[].tools.loopDetection`.

### [​](https://docs.openclaw.ai/tools/index\#web_search)  `web_search`

Search the web using Brave Search API.Core parameters:

- `query` (required)
- `count` (1–10; default from `tools.web.search.maxResults`)

Notes:

- Requires a Brave API key (recommended: `openclaw configure --section web`, or set `BRAVE_API_KEY`).
- Enable via `tools.web.search.enabled`.
- Responses are cached (default 15 min).
- See [Web tools](https://docs.openclaw.ai/tools/web) for setup.

### [​](https://docs.openclaw.ai/tools/index\#web_fetch)  `web_fetch`

Fetch and extract readable content from a URL (HTML → markdown/text).Core parameters:

- `url` (required)
- `extractMode` (`markdown` \| `text`)
- `maxChars` (truncate long pages)

Notes:

- Enable via `tools.web.fetch.enabled`.
- `maxChars` is clamped by `tools.web.fetch.maxCharsCap` (default 50000).
- Responses are cached (default 15 min).
- For JS-heavy sites, prefer the browser tool.
- See [Web tools](https://docs.openclaw.ai/tools/web) for setup.
- See [Firecrawl](https://docs.openclaw.ai/tools/firecrawl) for the optional anti-bot fallback.

### [​](https://docs.openclaw.ai/tools/index\#browser)  `browser`

Control the dedicated OpenClaw-managed browser.Core actions:

- `status`, `start`, `stop`, `tabs`, `open`, `focus`, `close`
- `snapshot` (aria/ai)
- `screenshot` (returns image block + `MEDIA:<path>`)
- `act` (UI actions: click/type/press/hover/drag/select/fill/resize/wait/evaluate)
- `navigate`, `console`, `pdf`, `upload`, `dialog`

Profile management:

- `profiles` — list all browser profiles with status
- `create-profile` — create new profile with auto-allocated port (or `cdpUrl`)
- `delete-profile` — stop browser, delete user data, remove from config (local only)
- `reset-profile` — kill orphan process on profile’s port (local only)

Common parameters:

- `profile` (optional; defaults to `browser.defaultProfile`)
- `target` (`sandbox` \| `host` \| `node`)
- `node` (optional; picks a specific node id/name)
Notes:
- Requires `browser.enabled=true` (default is `true`; set `false` to disable).
- All actions accept optional `profile` parameter for multi-instance support.
- When `profile` is omitted, uses `browser.defaultProfile` (defaults to “chrome”).
- Profile names: lowercase alphanumeric + hyphens only (max 64 chars).
- Port range: 18800-18899 (~100 profiles max).
- Remote profiles are attach-only (no start/stop/reset).
- If a browser-capable node is connected, the tool may auto-route to it (unless you pin `target`).
- `snapshot` defaults to `ai` when Playwright is installed; use `aria` for the accessibility tree.
- `snapshot` also supports role-snapshot options (`interactive`, `compact`, `depth`, `selector`) which return refs like `e12`.
- `act` requires `ref` from `snapshot` (numeric `12` from AI snapshots, or `e12` from role snapshots); use `evaluate` for rare CSS selector needs.
- Avoid `act` → `wait` by default; use it only in exceptional cases (no reliable UI state to wait on).
- `upload` can optionally pass a `ref` to auto-click after arming.
- `upload` also supports `inputRef` (aria ref) or `element` (CSS selector) to set `<input type="file">` directly.

### [​](https://docs.openclaw.ai/tools/index\#canvas)  `canvas`

Drive the node Canvas (present, eval, snapshot, A2UI).Core actions:

- `present`, `hide`, `navigate`, `eval`
- `snapshot` (returns image block + `MEDIA:<path>`)
- `a2ui_push`, `a2ui_reset`

Notes:

- Uses gateway `node.invoke` under the hood.
- If no `node` is provided, the tool picks a default (single connected node or local mac node).
- A2UI is v0.8 only (no `createSurface`); the CLI rejects v0.9 JSONL with line errors.
- Quick smoke: `openclaw nodes canvas a2ui push --node <id> --text "Hello from A2UI"`.

### [​](https://docs.openclaw.ai/tools/index\#nodes)  `nodes`

Discover and target paired nodes; send notifications; capture camera/screen.Core actions:

- `status`, `describe`
- `pending`, `approve`, `reject` (pairing)
- `notify` (macOS `system.notify`)
- `run` (macOS `system.run`)
- `camera_snap`, `camera_clip`, `screen_record`
- `location_get`

Notes:

- Camera/screen commands require the node app to be foregrounded.
- Images return image blocks + `MEDIA:<path>`.
- Videos return `FILE:<path>` (mp4).
- Location returns a JSON payload (lat/lon/accuracy/timestamp).
- `run` params: `command` argv array; optional `cwd`, `env` (`KEY=VAL`), `commandTimeoutMs`, `invokeTimeoutMs`, `needsScreenRecording`.

Example (`run`):

Copy

```
{
  "action": "run",
  "node": "office-mac",
  "command": ["echo", "Hello"],
  "env": ["FOO=bar"],
  "commandTimeoutMs": 12000,
  "invokeTimeoutMs": 45000,
  "needsScreenRecording": false
}
```

### [​](https://docs.openclaw.ai/tools/index\#image)  `image`

Analyze an image with the configured image model.Core parameters:

- `image` (required path or URL)
- `prompt` (optional; defaults to “Describe the image.”)
- `model` (optional override)
- `maxBytesMb` (optional size cap)

Notes:

- Only available when `agents.defaults.imageModel` is configured (primary or fallbacks), or when an implicit image model can be inferred from your default model + configured auth (best-effort pairing).
- Uses the image model directly (independent of the main chat model).

### [​](https://docs.openclaw.ai/tools/index\#message)  `message`

Send messages and channel actions across Discord/Google Chat/Slack/Telegram/WhatsApp/Signal/iMessage/MS Teams.Core actions:

- `send` (text + optional media; MS Teams also supports `card` for Adaptive Cards)
- `poll` (WhatsApp/Discord/MS Teams polls)
- `react` / `reactions` / `read` / `edit` / `delete`
- `pin` / `unpin` / `list-pins`
- `permissions`
- `thread-create` / `thread-list` / `thread-reply`
- `search`
- `sticker`
- `member-info` / `role-info`
- `emoji-list` / `emoji-upload` / `sticker-upload`
- `role-add` / `role-remove`
- `channel-info` / `channel-list`
- `voice-status`
- `event-list` / `event-create`
- `timeout` / `kick` / `ban`

Notes:

- `send` routes WhatsApp via the Gateway; other channels go direct.
- `poll` uses the Gateway for WhatsApp and MS Teams; Discord polls go direct.
- When a message tool call is bound to an active chat session, sends are constrained to that session’s target to avoid cross-context leaks.

### [​](https://docs.openclaw.ai/tools/index\#cron)  `cron`

Manage Gateway cron jobs and wakeups.Core actions:

- `status`, `list`
- `add`, `update`, `remove`, `run`, `runs`
- `wake` (enqueue system event + optional immediate heartbeat)

Notes:

- `add` expects a full cron job object (same schema as `cron.add` RPC).
- `update` uses `{ jobId, patch }` (`id` accepted for compatibility).

### [​](https://docs.openclaw.ai/tools/index\#gateway)  `gateway`

Restart or apply updates to the running Gateway process (in-place).Core actions:

- `restart` (authorizes + sends `SIGUSR1` for in-process restart; `openclaw gateway` restart in-place)
- `config.get` / `config.schema`
- `config.apply` (validate + write config + restart + wake)
- `config.patch` (merge partial update + restart + wake)
- `update.run` (run update + restart + wake)

Notes:

- Use `delayMs` (defaults to 2000) to avoid interrupting an in-flight reply.
- `restart` is enabled by default; set `commands.restart: false` to disable it.

### [​](https://docs.openclaw.ai/tools/index\#sessions_list-/-sessions_history-/-sessions_send-/-sessions_spawn-/-session_status)  `sessions_list` / `sessions_history` / `sessions_send` / `sessions_spawn` / `session_status`

List sessions, inspect transcript history, or send to another session.Core parameters:

- `sessions_list`: `kinds?`, `limit?`, `activeMinutes?`, `messageLimit?` (0 = none)
- `sessions_history`: `sessionKey` (or `sessionId`), `limit?`, `includeTools?`
- `sessions_send`: `sessionKey` (or `sessionId`), `message`, `timeoutSeconds?` (0 = fire-and-forget)
- `sessions_spawn`: `task`, `label?`, `runtime?`, `agentId?`, `model?`, `thinking?`, `cwd?`, `runTimeoutSeconds?`, `thread?`, `mode?`, `cleanup?`
- `session_status`: `sessionKey?` (default current; accepts `sessionId`), `model?` (`default` clears override)

Notes:

- `main` is the canonical direct-chat key; global/unknown are hidden.
- `messageLimit > 0` fetches last N messages per session (tool messages filtered).
- Session targeting is controlled by `tools.sessions.visibility` (default `tree`: current session + spawned subagent sessions). If you run a shared agent for multiple users, consider setting `tools.sessions.visibility: "self"` to prevent cross-session browsing.
- `sessions_send` waits for final completion when `timeoutSeconds > 0`.
- Delivery/announce happens after completion and is best-effort; `status: "ok"` confirms the agent run finished, not that the announce was delivered.
- `sessions_spawn` supports `runtime: "subagent" | "acp"` (`subagent` default). For ACP runtime behavior, see [ACP Agents](https://docs.openclaw.ai/tools/acp-agents).
- `sessions_spawn`starts a sub-agent run and posts an announce reply back to the requester chat.

  - Supports one-shot mode (`mode: "run"`) and persistent thread-bound mode (`mode: "session"` with `thread: true`).
  - If `thread: true` and `mode` is omitted, mode defaults to `session`.
  - `mode: "session"` requires `thread: true`.
  - If `runTimeoutSeconds` is omitted, OpenClaw uses `agents.defaults.subagents.runTimeoutSeconds` when set; otherwise timeout defaults to `0` (no timeout).
  - Discord thread-bound flows depend on `session.threadBindings.*` and `channels.discord.threadBindings.*`.
  - Reply format includes `Status`, `Result`, and compact stats.
  - `Result` is the assistant completion text; if missing, the latest `toolResult` is used as fallback.
- Manual completion-mode spawns send directly first, with queue fallback and retry on transient failures (`status: "ok"` means run finished, not that announce delivered).
- `sessions_spawn` is non-blocking and returns `status: "accepted"` immediately.
- `sessions_send` runs a reply‑back ping‑pong (reply `REPLY_SKIP` to stop; max turns via `session.agentToAgent.maxPingPongTurns`, 0–5).
- After the ping‑pong, the target agent runs an **announce step**; reply `ANNOUNCE_SKIP` to suppress the announcement.
- Sandbox clamp: when the current session is sandboxed and `agents.defaults.sandbox.sessionToolsVisibility: "spawned"`, OpenClaw clamps `tools.sessions.visibility` to `tree`.

### [​](https://docs.openclaw.ai/tools/index\#agents_list)  `agents_list`

List agent ids that the current session may target with `sessions_spawn`.Notes:

- Result is restricted to per-agent allowlists (`agents.list[].subagents.allowAgents`).
- When `["*"]` is configured, the tool includes all configured agents and marks `allowAny: true`.

## [​](https://docs.openclaw.ai/tools/index\#parameters-common)  Parameters (common)

Gateway-backed tools (`canvas`, `nodes`, `cron`):

- `gatewayUrl` (default `ws://127.0.0.1:18789`)
- `gatewayToken` (if auth enabled)
- `timeoutMs`

Note: when `gatewayUrl` is set, include `gatewayToken` explicitly. Tools do not inherit config
or environment credentials for overrides, and missing explicit credentials is an error.Browser tool:

- `profile` (optional; defaults to `browser.defaultProfile`)
- `target` (`sandbox` \| `host` \| `node`)
- `node` (optional; pin a specific node id/name)

## [​](https://docs.openclaw.ai/tools/index\#recommended-agent-flows)  Recommended agent flows

Browser automation:

1. `browser` → `status` / `start`
2. `snapshot` (ai or aria)
3. `act` (click/type/press)
4. `screenshot` if you need visual confirmation

Canvas render:

1. `canvas` → `present`
2. `a2ui_push` (optional)
3. `snapshot`

Node targeting:

1. `nodes` → `status`
2. `describe` on the chosen node
3. `notify` / `run` / `camera_snap` / `screen_record`

## [​](https://docs.openclaw.ai/tools/index\#safety)  Safety

- Avoid direct `system.run`; use `nodes` → `run` only with explicit user consent.
- Respect user consent for camera/screen capture.
- Use `status/describe` to ensure permissions before invoking media commands.

## [​](https://docs.openclaw.ai/tools/index\#how-tools-are-presented-to-the-agent)  How tools are presented to the agent

Tools are exposed in two parallel channels:

1. **System prompt text**: a human-readable list + guidance.
2. **Tool schema**: the structured function definitions sent to the model API.

That means the agent sees both “what tools exist” and “how to call them.” If a tool
doesn’t appear in the system prompt or the schema, the model cannot call it.

[Lobster](https://docs.openclaw.ai/tools/lobster)

Ctrl+I

---

---

<!-- Page: Slash Commands -->

## Slash Commands

**Source:** https://docs.openclaw.ai/tools/slash-commands

[Skip to main content](https://docs.openclaw.ai/tools/slash-commands#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Skills

Slash Commands

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Slash commands](https://docs.openclaw.ai/tools/slash-commands#slash-commands)
- [Config](https://docs.openclaw.ai/tools/slash-commands#config)
- [Command list](https://docs.openclaw.ai/tools/slash-commands#command-list)
- [Usage surfaces (what shows where)](https://docs.openclaw.ai/tools/slash-commands#usage-surfaces-what-shows-where)
- [Model selection (/model)](https://docs.openclaw.ai/tools/slash-commands#model-selection-%2Fmodel)
- [Debug overrides](https://docs.openclaw.ai/tools/slash-commands#debug-overrides)
- [Config updates](https://docs.openclaw.ai/tools/slash-commands#config-updates)
- [Surface notes](https://docs.openclaw.ai/tools/slash-commands#surface-notes)

# [​](https://docs.openclaw.ai/tools/slash-commands\#slash-commands)  Slash commands

Commands are handled by the Gateway. Most commands must be sent as a **standalone** message that starts with `/`.
The host-only bash chat command uses `! <cmd>` (with `/bash <cmd>` as an alias).There are two related systems:

- **Commands**: standalone `/...` messages.
- **Directives**: `/think`, `/verbose`, `/reasoning`, `/elevated`, `/exec`, `/model`, `/queue`.

  - Directives are stripped from the message before the model sees it.
  - In normal chat messages (not directive-only), they are treated as “inline hints” and do **not** persist session settings.
  - In directive-only messages (the message contains only directives), they persist to the session and reply with an acknowledgement.
  - Directives are only applied for **authorized senders**. If `commands.allowFrom` is set, it is the only
    allowlist used; otherwise authorization comes from channel allowlists/pairing plus `commands.useAccessGroups`.
    Unauthorized senders see directives treated as plain text.

There are also a few **inline shortcuts** (allowlisted/authorized senders only): `/help`, `/commands`, `/status`, `/whoami` (`/id`).
They run immediately, are stripped before the model sees the message, and the remaining text continues through the normal flow.

## [​](https://docs.openclaw.ai/tools/slash-commands\#config)  Config

Copy

```
{
  commands: {
    native: "auto",
    nativeSkills: "auto",
    text: true,
    bash: false,
    bashForegroundMs: 2000,
    config: false,
    debug: false,
    restart: false,
    allowFrom: {
      "*": ["user1"],
      discord: ["user:123"],
    },
    useAccessGroups: true,
  },
}
```

- `commands.text` (default `true`) enables parsing `/...` in chat messages.

  - On surfaces without native commands (WhatsApp/WebChat/Signal/iMessage/Google Chat/MS Teams), text commands still work even if you set this to `false`.
- `commands.native` (default `"auto"`) registers native commands.

  - Auto: on for Discord/Telegram; off for Slack (until you add slash commands); ignored for providers without native support.
  - Set `channels.discord.commands.native`, `channels.telegram.commands.native`, or `channels.slack.commands.native` to override per provider (bool or `"auto"`).
  - `false` clears previously registered commands on Discord/Telegram at startup. Slack commands are managed in the Slack app and are not removed automatically.
- `commands.nativeSkills` (default `"auto"`) registers **skill** commands natively when supported.

  - Auto: on for Discord/Telegram; off for Slack (Slack requires creating a slash command per skill).
  - Set `channels.discord.commands.nativeSkills`, `channels.telegram.commands.nativeSkills`, or `channels.slack.commands.nativeSkills` to override per provider (bool or `"auto"`).
- `commands.bash` (default `false`) enables `! <cmd>` to run host shell commands (`/bash <cmd>` is an alias; requires `tools.elevated` allowlists).
- `commands.bashForegroundMs` (default `2000`) controls how long bash waits before switching to background mode (`0` backgrounds immediately).
- `commands.config` (default `false`) enables `/config` (reads/writes `openclaw.json`).
- `commands.debug` (default `false`) enables `/debug` (runtime-only overrides).
- `commands.allowFrom` (optional) sets a per-provider allowlist for command authorization. When configured, it is the
only authorization source for commands and directives (channel allowlists/pairing and `commands.useAccessGroups`
are ignored). Use `"*"` for a global default; provider-specific keys override it.
- `commands.useAccessGroups` (default `true`) enforces allowlists/policies for commands when `commands.allowFrom` is not set.

## [​](https://docs.openclaw.ai/tools/slash-commands\#command-list)  Command list

Text + native (when enabled):

- `/help`
- `/commands`
- `/skill <name> [input]` (run a skill by name)
- `/status` (show current status; includes provider usage/quota for the current model provider when available)
- `/allowlist` (list/add/remove allowlist entries)
- `/approve <id> allow-once|allow-always|deny` (resolve exec approval prompts)
- `/context [list|detail|json]` (explain “context”; `detail` shows per-file + per-tool + per-skill + system prompt size)
- `/export-session [path]` (alias: `/export`) (export current session to HTML with full system prompt)
- `/whoami` (show your sender id; alias: `/id`)
- `/session ttl <duration|off>` (manage session-level settings, such as TTL)
- `/subagents list|kill|log|info|send|steer|spawn` (inspect, control, or spawn sub-agent runs for the current session)
- `/agents` (list thread-bound agents for this session)
- `/focus <target>` (Discord: bind this thread, or a new thread, to a session/subagent target)
- `/unfocus` (Discord: remove the current thread binding)
- `/kill <id|#|all>` (immediately abort one or all running sub-agents for this session; no confirmation message)
- `/steer <id|#> <message>` (steer a running sub-agent immediately: in-run when possible, otherwise abort current work and restart on the steer message)
- `/tell <id|#> <message>` (alias for `/steer`)
- `/config show|get|set|unset` (persist config to disk, owner-only; requires `commands.config: true`)
- `/debug show|set|unset|reset` (runtime overrides, owner-only; requires `commands.debug: true`)
- `/usage off|tokens|full|cost` (per-response usage footer or local cost summary)
- `/tts off|always|inbound|tagged|status|provider|limit|summary|audio` (control TTS; see [/tts](https://docs.openclaw.ai/tts))

  - Discord: native command is `/voice` (Discord reserves `/tts`); text `/tts` still works.
- `/stop`
- `/restart`
- `/dock-telegram` (alias: `/dock_telegram`) (switch replies to Telegram)
- `/dock-discord` (alias: `/dock_discord`) (switch replies to Discord)
- `/dock-slack` (alias: `/dock_slack`) (switch replies to Slack)
- `/activation mention|always` (groups only)
- `/send on|off|inherit` (owner-only)
- `/reset` or `/new [model]` (optional model hint; remainder is passed through)
- `/think <off|minimal|low|medium|high|xhigh>` (dynamic choices by model/provider; aliases: `/thinking`, `/t`)
- `/verbose on|full|off` (alias: `/v`)
- `/reasoning on|off|stream` (alias: `/reason`; when on, sends a separate message prefixed `Reasoning:`; `stream` = Telegram draft only)
- `/elevated on|off|ask|full` (alias: `/elev`; `full` skips exec approvals)
- `/exec host=<sandbox|gateway|node> security=<deny|allowlist|full> ask=<off|on-miss|always> node=<id>` (send `/exec` to show current)
- `/model <name>` (alias: `/models`; or `/<alias>` from `agents.defaults.models.*.alias`)
- `/queue <mode>` (plus options like `debounce:2s cap:25 drop:summarize`; send `/queue` to see current settings)
- `/bash <command>` (host-only; alias for `! <command>`; requires `commands.bash: true` \+ `tools.elevated` allowlists)

Text-only:

- `/compact [instructions]` (see [/concepts/compaction](https://docs.openclaw.ai/concepts/compaction))
- `! <command>` (host-only; one at a time; use `!poll` \+ `!stop` for long-running jobs)
- `!poll` (check output / status; accepts optional `sessionId`; `/bash poll` also works)
- `!stop` (stop the running bash job; accepts optional `sessionId`; `/bash stop` also works)

Notes:

- Commands accept an optional `:` between the command and args (e.g. `/think: high`, `/send: on`, `/help:`).
- `/new <model>` accepts a model alias, `provider/model`, or a provider name (fuzzy match); if no match, the text is treated as the message body.
- For full provider usage breakdown, use `openclaw status --usage`.
- `/allowlist add|remove` requires `commands.config=true` and honors channel `configWrites`.
- `/usage` controls the per-response usage footer; `/usage cost` prints a local cost summary from OpenClaw session logs.
- `/restart` is enabled by default; set `commands.restart: false` to disable it.
- Discord-only native command: `/vc join|leave|status` controls voice channels (requires `channels.discord.voice` and native commands; not available as text).
- Discord thread-binding commands (`/focus`, `/unfocus`, `/agents`, `/session ttl`) require effective thread bindings to be enabled (`session.threadBindings.enabled` and/or `channels.discord.threadBindings.enabled`).
- `/verbose` is meant for debugging and extra visibility; keep it **off** in normal use.
- Tool failure summaries are still shown when relevant, but detailed failure text is only included when `/verbose` is `on` or `full`.
- `/reasoning` (and `/verbose`) are risky in group settings: they may reveal internal reasoning or tool output you did not intend to expose. Prefer leaving them off, especially in group chats.
- **Fast path:** command-only messages from allowlisted senders are handled immediately (bypass queue + model).
- **Group mention gating:** command-only messages from allowlisted senders bypass mention requirements.
- **Inline shortcuts (allowlisted senders only):**certain commands also work when embedded in a normal message and are stripped before the model sees the remaining text.

  - Example: `hey /status` triggers a status reply, and the remaining text continues through the normal flow.
- Currently: `/help`, `/commands`, `/status`, `/whoami` (`/id`).
- Unauthorized command-only messages are silently ignored, and inline `/...` tokens are treated as plain text.
- **Skill commands:**`user-invocable` skills are exposed as slash commands. Names are sanitized to `a-z0-9_` (max 32 chars); collisions get numeric suffixes (e.g. `_2`).

  - `/skill <name> [input]` runs a skill by name (useful when native command limits prevent per-skill commands).
  - By default, skill commands are forwarded to the model as a normal request.
  - Skills may optionally declare `command-dispatch: tool` to route the command directly to a tool (deterministic, no model).
  - Example: `/prose` (OpenProse plugin) — see [OpenProse](https://docs.openclaw.ai/prose).
- **Native command arguments:** Discord uses autocomplete for dynamic options (and button menus when you omit required args). Telegram and Slack show a button menu when a command supports choices and you omit the arg.

## [​](https://docs.openclaw.ai/tools/slash-commands\#usage-surfaces-what-shows-where)  Usage surfaces (what shows where)

- **Provider usage/quota** (example: “Claude 80% left”) shows up in `/status` for the current model provider when usage tracking is enabled.
- **Per-response tokens/cost** is controlled by `/usage off|tokens|full` (appended to normal replies).
- `/model status` is about **models/auth/endpoints**, not usage.

## [​](https://docs.openclaw.ai/tools/slash-commands\#model-selection-/model)  Model selection (`/model`)

`/model` is implemented as a directive.Examples:

Copy

```
/model
/model list
/model 3
/model openai/gpt-5.2
/model opus@anthropic:default
/model status
```

Notes:

- `/model` and `/model list` show a compact, numbered picker (model family + available providers).
- On Discord, `/model` and `/models` open an interactive picker with provider and model dropdowns plus a Submit step.
- `/model <#>` selects from that picker (and prefers the current provider when possible).
- `/model status` shows the detailed view, including configured provider endpoint (`baseUrl`) and API mode (`api`) when available.

## [​](https://docs.openclaw.ai/tools/slash-commands\#debug-overrides)  Debug overrides

`/debug` lets you set **runtime-only** config overrides (memory, not disk). Owner-only. Disabled by default; enable with `commands.debug: true`.Examples:

Copy

```
/debug show
/debug set messages.responsePrefix="[openclaw]"
/debug set channels.whatsapp.allowFrom=["+1555","+4477"]
/debug unset messages.responsePrefix
/debug reset
```

Notes:

- Overrides apply immediately to new config reads, but do **not** write to `openclaw.json`.
- Use `/debug reset` to clear all overrides and return to the on-disk config.

## [​](https://docs.openclaw.ai/tools/slash-commands\#config-updates)  Config updates

`/config` writes to your on-disk config (`openclaw.json`). Owner-only. Disabled by default; enable with `commands.config: true`.Examples:

Copy

```
/config show
/config show messages.responsePrefix
/config get messages.responsePrefix
/config set messages.responsePrefix="[openclaw]"
/config unset messages.responsePrefix
```

Notes:

- Config is validated before write; invalid changes are rejected.
- `/config` updates persist across restarts.

## [​](https://docs.openclaw.ai/tools/slash-commands\#surface-notes)  Surface notes

- **Text commands** run in the normal chat session (DMs share `main`, groups have their own session).
- **Native commands**use isolated sessions:

  - Discord: `agent:<agentId>:discord:slash:<userId>`
  - Slack: `agent:<agentId>:slack:slash:<userId>` (prefix configurable via `channels.slack.slashCommand.sessionPrefix`)
  - Telegram: `telegram:slash:<userId>` (targets the chat session via `CommandTargetSessionKey`)
- **`/stop`** targets the active chat session so it can abort the current run.
- **Slack:**`channels.slack.slashCommand` is still supported for a single `/openclaw`-style command. If you enable `commands.native`, you must create one Slack slash command per built-in command (same names as `/help`). Command argument menus for Slack are delivered as ephemeral Block Kit buttons.

[Multi-Agent Sandbox & Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools) [Skills](https://docs.openclaw.ai/tools/skills)

Ctrl+I

---

---

<!-- Page: Reactions -->

## Reactions

**Source:** https://docs.openclaw.ai/tools/reactions

[Skip to main content](https://docs.openclaw.ai/tools/reactions#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Built-in tools

Reactions

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Reaction tooling](https://docs.openclaw.ai/tools/reactions#reaction-tooling)

# [​](https://docs.openclaw.ai/tools/reactions\#reaction-tooling)  Reaction tooling

Shared reaction semantics across channels:

- `emoji` is required when adding a reaction.
- `emoji=""` removes the bot’s reaction(s) when supported.
- `remove: true` removes the specified emoji when supported (requires `emoji`).

Channel notes:

- **Discord/Slack**: empty `emoji` removes all of the bot’s reactions on the message; `remove: true` removes just that emoji.
- **Google Chat**: empty `emoji` removes the app’s reactions on the message; `remove: true` removes just that emoji.
- **Telegram**: empty `emoji` removes the bot’s reactions; `remove: true` also removes reactions but still requires a non-empty `emoji` for tool validation.
- **WhatsApp**: empty `emoji` removes the bot reaction; `remove: true` maps to empty emoji (still requires `emoji`).
- **Signal**: inbound reaction notifications emit system events when `channels.signal.reactionNotifications` is enabled.

[Thinking Levels](https://docs.openclaw.ai/tools/thinking) [Browser (OpenClaw-managed)](https://docs.openclaw.ai/tools/browser)

Ctrl+I

---

---

<!-- Page: Browser Login -->

## Browser Login

**Source:** https://docs.openclaw.ai/tools/browser-login

[Skip to main content](https://docs.openclaw.ai/tools/browser-login#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Browser

Browser Login

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Browser login + X/Twitter posting](https://docs.openclaw.ai/tools/browser-login#browser-login-%2B-x%2Ftwitter-posting)
- [Manual login (recommended)](https://docs.openclaw.ai/tools/browser-login#manual-login-recommended)
- [Which Chrome profile is used?](https://docs.openclaw.ai/tools/browser-login#which-chrome-profile-is-used)
- [X/Twitter: recommended flow](https://docs.openclaw.ai/tools/browser-login#x%2Ftwitter-recommended-flow)
- [Sandboxing + host browser access](https://docs.openclaw.ai/tools/browser-login#sandboxing-%2B-host-browser-access)

# [​](https://docs.openclaw.ai/tools/browser-login\#browser-login-+-x/twitter-posting)  Browser login + X/Twitter posting

## [​](https://docs.openclaw.ai/tools/browser-login\#manual-login-recommended)  Manual login (recommended)

When a site requires login, **sign in manually** in the **host** browser profile (the openclaw browser).Do **not** give the model your credentials. Automated logins often trigger anti‑bot defenses and can lock the account.Back to the main browser docs: [Browser](https://docs.openclaw.ai/tools/browser).

## [​](https://docs.openclaw.ai/tools/browser-login\#which-chrome-profile-is-used)  Which Chrome profile is used?

OpenClaw controls a **dedicated Chrome profile** (named `openclaw`, orange‑tinted UI). This is separate from your daily browser profile.Two easy ways to access it:

1. **Ask the agent to open the browser** and then log in yourself.
2. **Open it via CLI**:

Copy

```
openclaw browser start
openclaw browser open https://x.com
```

If you have multiple profiles, pass `--browser-profile <name>` (the default is `openclaw`).

## [​](https://docs.openclaw.ai/tools/browser-login\#x/twitter-recommended-flow)  X/Twitter: recommended flow

- **Read/search/threads:** use the **host** browser (manual login).
- **Post updates:** use the **host** browser (manual login).

## [​](https://docs.openclaw.ai/tools/browser-login\#sandboxing-+-host-browser-access)  Sandboxing + host browser access

Sandboxed browser sessions are **more likely** to trigger bot detection. For X/Twitter (and other strict sites), prefer the **host** browser.If the agent is sandboxed, the browser tool defaults to the sandbox. To allow host control:

Copy

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",
        browser: {
          allowHostControl: true,
        },
      },
    },
  },
}
```

Then target the host browser:

Copy

```
openclaw browser open https://x.com --browser-profile openclaw --target host
```

Or disable sandboxing for the agent that posts updates.

[Browser (OpenClaw-managed)](https://docs.openclaw.ai/tools/browser) [Chrome Extension](https://docs.openclaw.ai/tools/chrome-extension)

Ctrl+I

---

---

<!-- Page: ClawHub -->

## clawhub.md

**Source:** https://docs.openclaw.ai/tools/clawhub.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# ClawHub

# ClawHub

ClawHub is the **public skill registry for OpenClaw**. It is a free service: all skills are public, open, and visible to everyone for sharing and reuse. A skill is just a folder with a `SKILL.md` file (plus supporting text files). You can browse skills in the web app or use the CLI to search, install, update, and publish skills.

Site: [clawhub.ai](https://clawhub.ai)

## What ClawHub is

* A public registry for OpenClaw skills.
* A versioned store of skill bundles and metadata.
* A discovery surface for search, tags, and usage signals.

## How it works

1. A user publishes a skill bundle (files + metadata).
2. ClawHub stores the bundle, parses metadata, and assigns a version.
3. The registry indexes the skill for search and discovery.
4. Users browse, download, and install skills in OpenClaw.

## What you can do

* Publish new skills and new versions of existing skills.
* Discover skills by name, tags, or search.
* Download skill bundles and inspect their files.
* Report skills that are abusive or unsafe.
* If you are a moderator, hide, unhide, delete, or ban.

## Who this is for (beginner-friendly)

If you want to add new capabilities to your OpenClaw agent, ClawHub is the easiest way to find and install skills. You do not need to know how the backend works. You can:

* Search for skills by plain language.
* Install a skill into your workspace.
* Update skills later with one command.
* Back up your own skills by publishing them.

## Quick start (non-technical)

1. Install the CLI (see next section).
2. Search for something you need:
   * `clawhub search "calendar"`
3. Install a skill:
   * `clawhub install <skill-slug>`
4. Start a new OpenClaw session so it picks up the new skill.

## Install the CLI

Pick one:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
npm i -g clawhub
```

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
pnpm add -g clawhub
```

## How it fits into OpenClaw

By default, the CLI installs skills into `./skills` under your current working directory. If a OpenClaw workspace is configured, `clawhub` falls back to that workspace unless you override `--workdir` (or `CLAWHUB_WORKDIR`). OpenClaw loads workspace skills from `<workspace>/skills` and will pick them up in the **next** session. If you already use `~/.openclaw/skills` or bundled skills, workspace skills take precedence.

For more detail on how skills are loaded, shared, and gated, see
[Skills](/tools/skills).

## Skill system overview

A skill is a versioned bundle of files that teaches OpenClaw how to perform a
specific task. Each publish creates a new version, and the registry keeps a
history of versions so users can audit changes.

A typical skill includes:

* A `SKILL.md` file with the primary description and usage.
* Optional configs, scripts, or supporting files used by the skill.
* Metadata such as tags, summary, and install requirements.

ClawHub uses metadata to power discovery and safely expose skill capabilities.
The registry also tracks usage signals (such as stars and downloads) to improve
ranking and visibility.

## What the service provides (features)

* **Public browsing** of skills and their `SKILL.md` content.
* **Search** powered by embeddings (vector search), not just keywords.
* **Versioning** with semver, changelogs, and tags (including `latest`).
* **Downloads** as a zip per version.
* **Stars and comments** for community feedback.
* **Moderation** hooks for approvals and audits.
* **CLI-friendly API** for automation and scripting.

## Security and moderation

ClawHub is open by default. Anyone can upload skills, but a GitHub account must
be at least one week old to publish. This helps slow down abuse without blocking
legitimate contributors.

Reporting and moderation:

* Any signed in user can report a skill.
* Report reasons are required and recorded.
* Each user can have up to 20 active reports at a time.
* Skills with more than 3 unique reports are auto hidden by default.
* Moderators can view hidden skills, unhide them, delete them, or ban users.
* Abusing the report feature can result in account bans.

Interested in becoming a moderator? Ask in the OpenClaw Discord and contact a
moderator or maintainer.

## CLI commands and parameters

Global options (apply to all commands):

* `--workdir <dir>`: Working directory (default: current dir; falls back to OpenClaw workspace).
* `--dir <dir>`: Skills directory, relative to workdir (default: `skills`).
* `--site <url>`: Site base URL (browser login).
* `--registry <url>`: Registry API base URL.
* `--no-input`: Disable prompts (non-interactive).
* `-V, --cli-version`: Print CLI version.

Auth:

* `clawhub login` (browser flow) or `clawhub login --token <token>`
* `clawhub logout`
* `clawhub whoami`

Options:

* `--token <token>`: Paste an API token.
* `--label <label>`: Label stored for browser login tokens (default: `CLI token`).
* `--no-browser`: Do not open a browser (requires `--token`).

Search:

* `clawhub search "query"`
* `--limit <n>`: Max results.

Install:

* `clawhub install <slug>`
* `--version <version>`: Install a specific version.
* `--force`: Overwrite if the folder already exists.

Update:

* `clawhub update <slug>`
* `clawhub update --all`
* `--version <version>`: Update to a specific version (single slug only).
* `--force`: Overwrite when local files do not match any published version.

List:

* `clawhub list` (reads `.clawhub/lock.json`)

Publish:

* `clawhub publish <path>`
* `--slug <slug>`: Skill slug.
* `--name <name>`: Display name.
* `--version <version>`: Semver version.
* `--changelog <text>`: Changelog text (can be empty).
* `--tags <tags>`: Comma-separated tags (default: `latest`).

Delete/undelete (owner/admin only):

* `clawhub delete <slug> --yes`
* `clawhub undelete <slug> --yes`

Sync (scan local skills + publish new/updated):

* `clawhub sync`
* `--root <dir...>`: Extra scan roots.
* `--all`: Upload everything without prompts.
* `--dry-run`: Show what would be uploaded.
* `--bump <type>`: `patch|minor|major` for updates (default: `patch`).
* `--changelog <text>`: Changelog for non-interactive updates.
* `--tags <tags>`: Comma-separated tags (default: `latest`).
* `--concurrency <n>`: Registry checks (default: 4).

## Common workflows for agents

### Search for skills

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
clawhub search "postgres backups"
```

### Download new skills

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
clawhub install my-skill-pack
```

### Update installed skills

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
clawhub update --all
```

### Back up your skills (publish or sync)

For a single skill folder:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
clawhub publish ./my-skill --slug my-skill --name "My Skill" --version 1.0.0 --tags latest
```

To scan and back up many skills at once:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
clawhub sync --all
```

## Advanced details (technical)

### Versioning and tags

* Each publish creates a new **semver** `SkillVersion`.
* Tags (like `latest`) point to a version; moving tags lets you roll back.
* Changelogs are attached per version and can be empty when syncing or publishing updates.

### Local changes vs registry versions

Updates compare the local skill contents to registry versions using a content hash. If local files do not match any published version, the CLI asks before overwriting (or requires `--force` in non-interactive runs).

### Sync scanning and fallback roots

`clawhub sync` scans your current workdir first. If no skills are found, it falls back to known legacy locations (for example `~/openclaw/skills` and `~/.openclaw/skills`). This is designed to find older skill installs without extra flags.

### Storage and lockfile

* Installed skills are recorded in `.clawhub/lock.json` under your workdir.
* Auth tokens are stored in the ClawHub CLI config file (override via `CLAWHUB_CONFIG_PATH`).

### Telemetry (install counts)

When you run `clawhub sync` while logged in, the CLI sends a minimal snapshot to compute install counts. You can disable this entirely:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export CLAWHUB_DISABLE_TELEMETRY=1
```

## Environment variables

* `CLAWHUB_SITE`: Override the site URL.
* `CLAWHUB_REGISTRY`: Override the registry API URL.
* `CLAWHUB_CONFIG_PATH`: Override where the CLI stores the token/config.
* `CLAWHUB_WORKDIR`: Override the default workdir.
* `CLAWHUB_DISABLE_TELEMETRY=1`: Disable telemetry on `sync`.
````

---

---

<!-- Page: LLM Task -->

## LLM Task

**Source:** https://docs.openclaw.ai/tools/llm-task

[Skip to main content](https://docs.openclaw.ai/tools/llm-task#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Built-in tools

LLM Task

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [LLM Task](https://docs.openclaw.ai/tools/llm-task#llm-task)
- [Enable the plugin](https://docs.openclaw.ai/tools/llm-task#enable-the-plugin)
- [Config (optional)](https://docs.openclaw.ai/tools/llm-task#config-optional)
- [Tool parameters](https://docs.openclaw.ai/tools/llm-task#tool-parameters)
- [Output](https://docs.openclaw.ai/tools/llm-task#output)
- [Example: Lobster workflow step](https://docs.openclaw.ai/tools/llm-task#example-lobster-workflow-step)
- [Safety notes](https://docs.openclaw.ai/tools/llm-task#safety-notes)

# [​](https://docs.openclaw.ai/tools/llm-task\#llm-task)  LLM Task

`llm-task` is an **optional plugin tool** that runs a JSON-only LLM task and
returns structured output (optionally validated against JSON Schema).This is ideal for workflow engines like Lobster: you can add a single LLM step
without writing custom OpenClaw code for each workflow.

## [​](https://docs.openclaw.ai/tools/llm-task\#enable-the-plugin)  Enable the plugin

1. Enable the plugin:

Copy

```
{
  "plugins": {
    "entries": {
      "llm-task": { "enabled": true }
    }
  }
}
```

2. Allowlist the tool (it is registered with `optional: true`):

Copy

```
{
  "agents": {
    "list": [\
      {\
        "id": "main",\
        "tools": { "allow": ["llm-task"] }\
      }\
    ]
  }
}
```

## [​](https://docs.openclaw.ai/tools/llm-task\#config-optional)  Config (optional)

Copy

```
{
  "plugins": {
    "entries": {
      "llm-task": {
        "enabled": true,
        "config": {
          "defaultProvider": "openai-codex",
          "defaultModel": "gpt-5.2",
          "defaultAuthProfileId": "main",
          "allowedModels": ["openai-codex/gpt-5.3-codex"],
          "maxTokens": 800,
          "timeoutMs": 30000
        }
      }
    }
  }
}
```

`allowedModels` is an allowlist of `provider/model` strings. If set, any request
outside the list is rejected.

## [​](https://docs.openclaw.ai/tools/llm-task\#tool-parameters)  Tool parameters

- `prompt` (string, required)
- `input` (any, optional)
- `schema` (object, optional JSON Schema)
- `provider` (string, optional)
- `model` (string, optional)
- `authProfileId` (string, optional)
- `temperature` (number, optional)
- `maxTokens` (number, optional)
- `timeoutMs` (number, optional)

## [​](https://docs.openclaw.ai/tools/llm-task\#output)  Output

Returns `details.json` containing the parsed JSON (and validates against
`schema` when provided).

## [​](https://docs.openclaw.ai/tools/llm-task\#example-lobster-workflow-step)  Example: Lobster workflow step

Copy

```
openclaw.invoke --tool llm-task --action json --args-json '{
  "prompt": "Given the input email, return intent and draft.",
  "input": {
    "subject": "Hello",
    "body": "Can you help?"
  },
  "schema": {
    "type": "object",
    "properties": {
      "intent": { "type": "string" },
      "draft": { "type": "string" }
    },
    "required": ["intent", "draft"],
    "additionalProperties": false
  }
}'
```

## [​](https://docs.openclaw.ai/tools/llm-task\#safety-notes)  Safety notes

- The tool is **JSON-only** and instructs the model to output only JSON (no
code fences, no commentary).
- No tools are exposed to the model for this run.
- Treat output as untrusted unless you validate with `schema`.
- Put approvals before any side-effecting step (send, post, exec).

[Lobster](https://docs.openclaw.ai/tools/lobster) [Exec Tool](https://docs.openclaw.ai/tools/exec)

Ctrl+I

---
