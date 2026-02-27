# Core Concepts


---

<!-- Page: Agent Runtime -->

## Agent Runtime

**Source:** https://docs.openclaw.ai/concepts/agent

[Skip to main content](https://docs.openclaw.ai/concepts/agent#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Fundamentals

Agent Runtime

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Agent Runtime 🤖](https://docs.openclaw.ai/concepts/agent#agent-runtime-)
- [Workspace (required)](https://docs.openclaw.ai/concepts/agent#workspace-required)
- [Bootstrap files (injected)](https://docs.openclaw.ai/concepts/agent#bootstrap-files-injected)
- [Built-in tools](https://docs.openclaw.ai/concepts/agent#built-in-tools)
- [Skills](https://docs.openclaw.ai/concepts/agent#skills)
- [pi-mono integration](https://docs.openclaw.ai/concepts/agent#pi-mono-integration)
- [Sessions](https://docs.openclaw.ai/concepts/agent#sessions)
- [Steering while streaming](https://docs.openclaw.ai/concepts/agent#steering-while-streaming)
- [Model refs](https://docs.openclaw.ai/concepts/agent#model-refs)
- [Configuration (minimal)](https://docs.openclaw.ai/concepts/agent#configuration-minimal)

# [​](https://docs.openclaw.ai/concepts/agent\#agent-runtime-)  Agent Runtime 🤖

OpenClaw runs a single embedded agent runtime derived from **pi-mono**.

## [​](https://docs.openclaw.ai/concepts/agent\#workspace-required)  Workspace (required)

OpenClaw uses a single agent workspace directory (`agents.defaults.workspace`) as the agent’s **only** working directory (`cwd`) for tools and context.Recommended: use `openclaw setup` to create `~/.openclaw/openclaw.json` if missing and initialize the workspace files.Full workspace layout + backup guide: [Agent workspace](https://docs.openclaw.ai/concepts/agent-workspace)If `agents.defaults.sandbox` is enabled, non-main sessions can override this with
per-session workspaces under `agents.defaults.sandbox.workspaceRoot` (see
[Gateway configuration](https://docs.openclaw.ai/gateway/configuration)).

## [​](https://docs.openclaw.ai/concepts/agent\#bootstrap-files-injected)  Bootstrap files (injected)

Inside `agents.defaults.workspace`, OpenClaw expects these user-editable files:

- `AGENTS.md` — operating instructions + “memory”
- `SOUL.md` — persona, boundaries, tone
- `TOOLS.md` — user-maintained tool notes (e.g. `imsg`, `sag`, conventions)
- `BOOTSTRAP.md` — one-time first-run ritual (deleted after completion)
- `IDENTITY.md` — agent name/vibe/emoji
- `USER.md` — user profile + preferred address

On the first turn of a new session, OpenClaw injects the contents of these files directly into the agent context.Blank files are skipped. Large files are trimmed and truncated with a marker so prompts stay lean (read the file for full content).If a file is missing, OpenClaw injects a single “missing file” marker line (and `openclaw setup` will create a safe default template).`BOOTSTRAP.md` is only created for a **brand new workspace** (no other bootstrap files present). If you delete it after completing the ritual, it should not be recreated on later restarts.To disable bootstrap file creation entirely (for pre-seeded workspaces), set:

Copy

```
{ agent: { skipBootstrap: true } }
```

## [​](https://docs.openclaw.ai/concepts/agent\#built-in-tools)  Built-in tools

Core tools (read/exec/edit/write and related system tools) are always available,
subject to tool policy. `apply_patch` is optional and gated by
`tools.exec.applyPatch`. `TOOLS.md` does **not** control which tools exist; it’s
guidance for how _you_ want them used.

## [​](https://docs.openclaw.ai/concepts/agent\#skills)  Skills

OpenClaw loads skills from three locations (workspace wins on name conflict):

- Bundled (shipped with the install)
- Managed/local: `~/.openclaw/skills`
- Workspace: `<workspace>/skills`

Skills can be gated by config/env (see `skills` in [Gateway configuration](https://docs.openclaw.ai/gateway/configuration)).

## [​](https://docs.openclaw.ai/concepts/agent\#pi-mono-integration)  pi-mono integration

OpenClaw reuses pieces of the pi-mono codebase (models/tools), but **session management, discovery, and tool wiring are OpenClaw-owned**.

- No pi-coding agent runtime.
- No `~/.pi/agent` or `<workspace>/.pi` settings are consulted.

## [​](https://docs.openclaw.ai/concepts/agent\#sessions)  Sessions

Session transcripts are stored as JSONL at:

- `~/.openclaw/agents/<agentId>/sessions/<SessionId>.jsonl`

The session ID is stable and chosen by OpenClaw.
Legacy Pi/Tau session folders are **not** read.

## [​](https://docs.openclaw.ai/concepts/agent\#steering-while-streaming)  Steering while streaming

When queue mode is `steer`, inbound messages are injected into the current run.
The queue is checked **after each tool call**; if a queued message is present,
remaining tool calls from the current assistant message are skipped (error tool
results with “Skipped due to queued user message.”), then the queued user
message is injected before the next assistant response.When queue mode is `followup` or `collect`, inbound messages are held until the
current turn ends, then a new agent turn starts with the queued payloads. See
[Queue](https://docs.openclaw.ai/concepts/queue) for mode + debounce/cap behavior.Block streaming sends completed assistant blocks as soon as they finish; it is
**off by default** (`agents.defaults.blockStreamingDefault: "off"`).
Tune the boundary via `agents.defaults.blockStreamingBreak` (`text_end` vs `message_end`; defaults to text\_end).
Control soft block chunking with `agents.defaults.blockStreamingChunk` (defaults to
800–1200 chars; prefers paragraph breaks, then newlines; sentences last).
Coalesce streamed chunks with `agents.defaults.blockStreamingCoalesce` to reduce
single-line spam (idle-based merging before send). Non-Telegram channels require
explicit `*.blockStreaming: true` to enable block replies.
Verbose tool summaries are emitted at tool start (no debounce); Control UI
streams tool output via agent events when available.
More details: [Streaming + chunking](https://docs.openclaw.ai/concepts/streaming).

## [​](https://docs.openclaw.ai/concepts/agent\#model-refs)  Model refs

Model refs in config (for example `agents.defaults.model` and `agents.defaults.models`) are parsed by splitting on the **first**`/`.

- Use `provider/model` when configuring models.
- If the model ID itself contains `/` (OpenRouter-style), include the provider prefix (example: `openrouter/moonshotai/kimi-k2`).
- If you omit the provider, OpenClaw treats the input as an alias or a model for the **default provider** (only works when there is no `/` in the model ID).

## [​](https://docs.openclaw.ai/concepts/agent\#configuration-minimal)  Configuration (minimal)

At minimum, set:

- `agents.defaults.workspace`
- `channels.whatsapp.allowFrom` (strongly recommended)

* * *

_Next: [Group Chats](https://docs.openclaw.ai/channels/group-messages)_ 🦞

[Gateway Architecture](https://docs.openclaw.ai/concepts/architecture) [Agent Loop](https://docs.openclaw.ai/concepts/agent-loop)

Ctrl+I

---

---

<!-- Page: Agent Workspace -->

## agent-workspace.md

**Source:** https://docs.openclaw.ai/concepts/agent-workspace.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Agent Workspace

# Agent workspace

The workspace is the agent's home. It is the only working directory used for
file tools and for workspace context. Keep it private and treat it as memory.

This is separate from `~/.openclaw/`, which stores config, credentials, and
sessions.

**Important:** the workspace is the **default cwd**, not a hard sandbox. Tools
resolve relative paths against the workspace, but absolute paths can still reach
elsewhere on the host unless sandboxing is enabled. If you need isolation, use
[`agents.defaults.sandbox`](/gateway/sandboxing) (and/or per‑agent sandbox config).
When sandboxing is enabled and `workspaceAccess` is not `"rw"`, tools operate
inside a sandbox workspace under `~/.openclaw/sandboxes`, not your host workspace.

## Default location

* Default: `~/.openclaw/workspace`
* If `OPENCLAW_PROFILE` is set and not `"default"`, the default becomes
  `~/.openclaw/workspace-<profile>`.
* Override in `~/.openclaw/openclaw.json`:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agent: {
    workspace: "~/.openclaw/workspace",
  },
}
```

`openclaw onboard`, `openclaw configure`, or `openclaw setup` will create the
workspace and seed the bootstrap files if they are missing.

If you already manage the workspace files yourself, you can disable bootstrap
file creation:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{ agent: { skipBootstrap: true } }
```

## Extra workspace folders

Older installs may have created `~/openclaw`. Keeping multiple workspace
directories around can cause confusing auth or state drift, because only one
workspace is active at a time.

**Recommendation:** keep a single active workspace. If you no longer use the
extra folders, archive or move them to Trash (for example `trash ~/openclaw`).
If you intentionally keep multiple workspaces, make sure
`agents.defaults.workspace` points to the active one.

`openclaw doctor` warns when it detects extra workspace directories.

## Workspace file map (what each file means)

These are the standard files OpenClaw expects inside the workspace:

* `AGENTS.md`
  * Operating instructions for the agent and how it should use memory.
  * Loaded at the start of every session.
  * Good place for rules, priorities, and "how to behave" details.

* `SOUL.md`
  * Persona, tone, and boundaries.
  * Loaded every session.

* `USER.md`
  * Who the user is and how to address them.
  * Loaded every session.

* `IDENTITY.md`
  * The agent's name, vibe, and emoji.
  * Created/updated during the bootstrap ritual.

* `TOOLS.md`
  * Notes about your local tools and conventions.
  * Does not control tool availability; it is only guidance.

* `HEARTBEAT.md`
  * Optional tiny checklist for heartbeat runs.
  * Keep it short to avoid token burn.

* `BOOT.md`
  * Optional startup checklist executed on gateway restart when internal hooks are enabled.
  * Keep it short; use the message tool for outbound sends.

* `BOOTSTRAP.md`
  * One-time first-run ritual.
  * Only created for a brand-new workspace.
  * Delete it after the ritual is complete.

* `memory/YYYY-MM-DD.md`
  * Daily memory log (one file per day).
  * Recommended to read today + yesterday on session start.

* `MEMORY.md` (optional)
  * Curated long-term memory.
  * Only load in the main, private session (not shared/group contexts).

See [Memory](/concepts/memory) for the workflow and automatic memory flush.

* `skills/` (optional)
  * Workspace-specific skills.
  * Overrides managed/bundled skills when names collide.

* `canvas/` (optional)
  * Canvas UI files for node displays (for example `canvas/index.html`).

If any bootstrap file is missing, OpenClaw injects a "missing file" marker into
the session and continues. Large bootstrap files are truncated when injected;
adjust limits with `agents.defaults.bootstrapMaxChars` (default: 20000) and
`agents.defaults.bootstrapTotalMaxChars` (default: 150000).
`openclaw setup` can recreate missing defaults without overwriting existing
files.

## What is NOT in the workspace

These live under `~/.openclaw/` and should NOT be committed to the workspace repo:

* `~/.openclaw/openclaw.json` (config)
* `~/.openclaw/credentials/` (OAuth tokens, API keys)
* `~/.openclaw/agents/<agentId>/sessions/` (session transcripts + metadata)
* `~/.openclaw/skills/` (managed skills)

If you need to migrate sessions or config, copy them separately and keep them
out of version control.

## Git backup (recommended, private)

Treat the workspace as private memory. Put it in a **private** git repo so it is
backed up and recoverable.

Run these steps on the machine where the Gateway runs (that is where the
workspace lives).

### 1) Initialize the repo

If git is installed, brand-new workspaces are initialized automatically. If this
workspace is not already a repo, run:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
cd ~/.openclaw/workspace
git init
git add AGENTS.md SOUL.md TOOLS.md IDENTITY.md USER.md HEARTBEAT.md memory/
git commit -m "Add agent workspace"
```

### 2) Add a private remote (beginner-friendly options)

Option A: GitHub web UI

1. Create a new **private** repository on GitHub.
2. Do not initialize with a README (avoids merge conflicts).
3. Copy the HTTPS remote URL.
4. Add the remote and push:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
git branch -M main
git remote add origin <https-url>
git push -u origin main
```

Option B: GitHub CLI (`gh`)

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
gh auth login
gh repo create openclaw-workspace --private --source . --remote origin --push
```

Option C: GitLab web UI

1. Create a new **private** repository on GitLab.
2. Do not initialize with a README (avoids merge conflicts).
3. Copy the HTTPS remote URL.
4. Add the remote and push:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
git branch -M main
git remote add origin <https-url>
git push -u origin main
```

### 3) Ongoing updates

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
git status
git add .
git commit -m "Update memory"
git push
```

## Do not commit secrets

Even in a private repo, avoid storing secrets in the workspace:

* API keys, OAuth tokens, passwords, or private credentials.
* Anything under `~/.openclaw/`.
* Raw dumps of chats or sensitive attachments.

If you must store sensitive references, use placeholders and keep the real
secret elsewhere (password manager, environment variables, or `~/.openclaw/`).

Suggested `.gitignore` starter:

```gitignore  theme={"theme":{"light":"min-light","dark":"min-dark"}}
.DS_Store
.env
**/*.key
**/*.pem
**/secrets*
```

## Moving the workspace to a new machine

1. Clone the repo to the desired path (default `~/.openclaw/workspace`).
2. Set `agents.defaults.workspace` to that path in `~/.openclaw/openclaw.json`.
3. Run `openclaw setup --workspace <path>` to seed any missing files.
4. If you need sessions, copy `~/.openclaw/agents/<agentId>/sessions/` from the
   old machine separately.

## Advanced notes

* Multi-agent routing can use different workspaces per agent. See
  [Channel routing](/channels/channel-routing) for routing configuration.
* If `agents.defaults.sandbox` is enabled, non-main sessions can use per-session sandbox
  workspaces under `agents.defaults.sandbox.workspaceRoot`.
````

---

---

<!-- Page: System Prompt -->

## System Prompt

**Source:** https://docs.openclaw.ai/concepts/system-prompt

[Skip to main content](https://docs.openclaw.ai/concepts/system-prompt#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Fundamentals

System Prompt

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [System Prompt](https://docs.openclaw.ai/concepts/system-prompt#system-prompt)
- [Structure](https://docs.openclaw.ai/concepts/system-prompt#structure)
- [Prompt modes](https://docs.openclaw.ai/concepts/system-prompt#prompt-modes)
- [Workspace bootstrap injection](https://docs.openclaw.ai/concepts/system-prompt#workspace-bootstrap-injection)
- [Time handling](https://docs.openclaw.ai/concepts/system-prompt#time-handling)
- [Skills](https://docs.openclaw.ai/concepts/system-prompt#skills)
- [Documentation](https://docs.openclaw.ai/concepts/system-prompt#documentation)

# [​](https://docs.openclaw.ai/concepts/system-prompt\#system-prompt)  System Prompt

OpenClaw builds a custom system prompt for every agent run. The prompt is **OpenClaw-owned** and does not use the pi-coding-agent default prompt.The prompt is assembled by OpenClaw and injected into each agent run.

## [​](https://docs.openclaw.ai/concepts/system-prompt\#structure)  Structure

The prompt is intentionally compact and uses fixed sections:

- **Tooling**: current tool list + short descriptions.
- **Safety**: short guardrail reminder to avoid power-seeking behavior or bypassing oversight.
- **Skills** (when available): tells the model how to load skill instructions on demand.
- **OpenClaw Self-Update**: how to run `config.apply` and `update.run`.
- **Workspace**: working directory (`agents.defaults.workspace`).
- **Documentation**: local path to OpenClaw docs (repo or npm package) and when to read them.
- **Workspace Files (injected)**: indicates bootstrap files are included below.
- **Sandbox** (when enabled): indicates sandboxed runtime, sandbox paths, and whether elevated exec is available.
- **Current Date & Time**: user-local time, timezone, and time format.
- **Reply Tags**: optional reply tag syntax for supported providers.
- **Heartbeats**: heartbeat prompt and ack behavior.
- **Runtime**: host, OS, node, model, repo root (when detected), thinking level (one line).
- **Reasoning**: current visibility level + /reasoning toggle hint.

Safety guardrails in the system prompt are advisory. They guide model behavior but do not enforce policy. Use tool policy, exec approvals, sandboxing, and channel allowlists for hard enforcement; operators can disable these by design.

## [​](https://docs.openclaw.ai/concepts/system-prompt\#prompt-modes)  Prompt modes

OpenClaw can render smaller system prompts for sub-agents. The runtime sets a
`promptMode` for each run (not a user-facing config):

- `full` (default): includes all sections above.
- `minimal`: used for sub-agents; omits **Skills**, **Memory Recall**, **OpenClaw**
**Self-Update**, **Model Aliases**, **User Identity**, **Reply Tags**,
**Messaging**, **Silent Replies**, and **Heartbeats**. Tooling, **Safety**,
Workspace, Sandbox, Current Date & Time (when known), Runtime, and injected
context stay available.
- `none`: returns only the base identity line.

When `promptMode=minimal`, extra injected prompts are labeled **Subagent**
**Context** instead of **Group Chat Context**.

## [​](https://docs.openclaw.ai/concepts/system-prompt\#workspace-bootstrap-injection)  Workspace bootstrap injection

Bootstrap files are trimmed and appended under **Project Context** so the model sees identity and profile context without needing explicit reads:

- `AGENTS.md`
- `SOUL.md`
- `TOOLS.md`
- `IDENTITY.md`
- `USER.md`
- `HEARTBEAT.md`
- `BOOTSTRAP.md` (only on brand-new workspaces)
- `MEMORY.md` and/or `memory.md` (when present in the workspace; either or both may be injected)

All of these files are **injected into the context window** on every turn, which
means they consume tokens. Keep them concise — especially `MEMORY.md`, which can
grow over time and lead to unexpectedly high context usage and more frequent
compaction.

> **Note:**`memory/*.md` daily files are **not** injected automatically. They
> are accessed on demand via the `memory_search` and `memory_get` tools, so they
> do not count against the context window unless the model explicitly reads them.

Large files are truncated with a marker. The max per-file size is controlled by
`agents.defaults.bootstrapMaxChars` (default: 20000). Total injected bootstrap
content across files is capped by `agents.defaults.bootstrapTotalMaxChars`
(default: 150000). Missing files inject a short missing-file marker.Sub-agent sessions only inject `AGENTS.md` and `TOOLS.md` (other bootstrap files
are filtered out to keep the sub-agent context small).Internal hooks can intercept this step via `agent:bootstrap` to mutate or replace
the injected bootstrap files (for example swapping `SOUL.md` for an alternate persona).To inspect how much each injected file contributes (raw vs injected, truncation, plus tool schema overhead), use `/context list` or `/context detail`. See [Context](https://docs.openclaw.ai/concepts/context).

## [​](https://docs.openclaw.ai/concepts/system-prompt\#time-handling)  Time handling

The system prompt includes a dedicated **Current Date & Time** section when the
user timezone is known. To keep the prompt cache-stable, it now only includes
the **time zone** (no dynamic clock or time format).Use `session_status` when the agent needs the current time; the status card
includes a timestamp line.Configure with:

- `agents.defaults.userTimezone`
- `agents.defaults.timeFormat` (`auto` \| `12` \| `24`)

See [Date & Time](https://docs.openclaw.ai/date-time) for full behavior details.

## [​](https://docs.openclaw.ai/concepts/system-prompt\#skills)  Skills

When eligible skills exist, OpenClaw injects a compact **available skills list**
(`formatSkillsForPrompt`) that includes the **file path** for each skill. The
prompt instructs the model to use `read` to load the SKILL.md at the listed
location (workspace, managed, or bundled). If no skills are eligible, the
Skills section is omitted.

Copy

```
<available_skills>
  <skill>
    <name>...</name>
    <description>...</description>
    <location>...</location>
  </skill>
</available_skills>
```

This keeps the base prompt small while still enabling targeted skill usage.

## [​](https://docs.openclaw.ai/concepts/system-prompt\#documentation)  Documentation

When available, the system prompt includes a **Documentation** section that points to the
local OpenClaw docs directory (either `docs/` in the repo workspace or the bundled npm
package docs) and also notes the public mirror, source repo, community Discord, and
ClawHub ( [https://clawhub.com](https://clawhub.com/)) for skills discovery. The prompt instructs the model to consult local docs first
for OpenClaw behavior, commands, configuration, or architecture, and to run
`openclaw status` itself when possible (asking the user only when it lacks access).

[Agent Loop](https://docs.openclaw.ai/concepts/agent-loop) [Context](https://docs.openclaw.ai/concepts/context)

Ctrl+I

---

---

<!-- Page: Compaction -->

## Compaction

**Source:** https://docs.openclaw.ai/concepts/compaction

[Skip to main content](https://docs.openclaw.ai/concepts/compaction#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Sessions and memory

Compaction

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Context Window & Compaction](https://docs.openclaw.ai/concepts/compaction#context-window-%26-compaction)
- [What compaction is](https://docs.openclaw.ai/concepts/compaction#what-compaction-is)
- [Configuration](https://docs.openclaw.ai/concepts/compaction#configuration)
- [Auto-compaction (default on)](https://docs.openclaw.ai/concepts/compaction#auto-compaction-default-on)
- [Manual compaction](https://docs.openclaw.ai/concepts/compaction#manual-compaction)
- [Context window source](https://docs.openclaw.ai/concepts/compaction#context-window-source)
- [Compaction vs pruning](https://docs.openclaw.ai/concepts/compaction#compaction-vs-pruning)
- [Tips](https://docs.openclaw.ai/concepts/compaction#tips)

# [​](https://docs.openclaw.ai/concepts/compaction\#context-window-&-compaction)  Context Window & Compaction

Every model has a **context window** (max tokens it can see). Long-running chats accumulate messages and tool results; once the window is tight, OpenClaw **compacts** older history to stay within limits.

## [​](https://docs.openclaw.ai/concepts/compaction\#what-compaction-is)  What compaction is

Compaction **summarizes older conversation** into a compact summary entry and keeps recent messages intact. The summary is stored in the session history, so future requests use:

- The compaction summary
- Recent messages after the compaction point

Compaction **persists** in the session’s JSONL history.

## [​](https://docs.openclaw.ai/concepts/compaction\#configuration)  Configuration

Use the `agents.defaults.compaction` setting in your `openclaw.json` to configure compaction behavior (mode, target tokens, etc.).

## [​](https://docs.openclaw.ai/concepts/compaction\#auto-compaction-default-on)  Auto-compaction (default on)

When a session nears or exceeds the model’s context window, OpenClaw triggers auto-compaction and may retry the original request using the compacted context.You’ll see:

- `🧹 Auto-compaction complete` in verbose mode
- `/status` showing `🧹 Compactions: <count>`

Before compaction, OpenClaw can run a **silent memory flush** turn to store
durable notes to disk. See [Memory](https://docs.openclaw.ai/concepts/memory) for details and config.

## [​](https://docs.openclaw.ai/concepts/compaction\#manual-compaction)  Manual compaction

Use `/compact` (optionally with instructions) to force a compaction pass:

Copy

```
/compact Focus on decisions and open questions
```

## [​](https://docs.openclaw.ai/concepts/compaction\#context-window-source)  Context window source

Context window is model-specific. OpenClaw uses the model definition from the configured provider catalog to determine limits.

## [​](https://docs.openclaw.ai/concepts/compaction\#compaction-vs-pruning)  Compaction vs pruning

- **Compaction**: summarises and **persists** in JSONL.
- **Session pruning**: trims old **tool results** only, **in-memory**, per request.

See [/concepts/session-pruning](https://docs.openclaw.ai/concepts/session-pruning) for pruning details.

## [​](https://docs.openclaw.ai/concepts/compaction\#tips)  Tips

- Use `/compact` when sessions feel stale or context is bloated.
- Large tool outputs are already truncated; pruning can further reduce tool-result buildup.
- If you need a fresh slate, `/new` or `/reset` starts a new session id.

[Memory](https://docs.openclaw.ai/concepts/memory) [Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)

Ctrl+I

---

---

<!-- Page: Retry Policy -->

## retry.md

**Source:** https://docs.openclaw.ai/concepts/retry.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Retry Policy

# Retry policy

## Goals

* Retry per HTTP request, not per multi-step flow.
* Preserve ordering by retrying only the current step.
* Avoid duplicating non-idempotent operations.

## Defaults

* Attempts: 3
* Max delay cap: 30000 ms
* Jitter: 0.1 (10 percent)
* Provider defaults:
  * Telegram min delay: 400 ms
  * Discord min delay: 500 ms

## Behavior

### Discord

* Retries only on rate-limit errors (HTTP 429).
* Uses Discord `retry_after` when available, otherwise exponential backoff.

### Telegram

* Retries on transient errors (429, timeout, connect/reset/closed, temporarily unavailable).
* Uses `retry_after` when available, otherwise exponential backoff.
* Markdown parse errors are not retried; they fall back to plain text.

## Configuration

Set retry policy per provider in `~/.openclaw/openclaw.json`:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  channels: {
    telegram: {
      retry: {
        attempts: 3,
        minDelayMs: 400,
        maxDelayMs: 30000,
        jitter: 0.1,
      },
    },
    discord: {
      retry: {
        attempts: 3,
        minDelayMs: 500,
        maxDelayMs: 30000,
        jitter: 0.1,
      },
    },
  },
}
```

## Notes

* Retries apply per request (message send, media upload, reaction, poll, sticker).
* Composite flows do not retry completed steps.
````

---

---

<!-- Page: Memory v2 Research -->

## Workspace Memory Research

**Source:** https://docs.openclaw.ai/experiments/research/memory

[Skip to main content](https://docs.openclaw.ai/experiments/research/memory#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Experiments

Workspace Memory Research

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Workspace Memory v2 (offline): research notes](https://docs.openclaw.ai/experiments/research/memory#workspace-memory-v2-offline--research-notes)
- [Why change?](https://docs.openclaw.ai/experiments/research/memory#why-change)
- [Design goals](https://docs.openclaw.ai/experiments/research/memory#design-goals)
- [North star model (Hindsight × Letta)](https://docs.openclaw.ai/experiments/research/memory#north-star-model-hindsight-%C3%97-letta)
- [Proposed architecture (Markdown source-of-truth + derived index)](https://docs.openclaw.ai/experiments/research/memory#proposed-architecture-markdown-source-of-truth-%2B-derived-index)
- [Canonical store (git-friendly)](https://docs.openclaw.ai/experiments/research/memory#canonical-store-git-friendly)
- [Derived store (machine recall)](https://docs.openclaw.ai/experiments/research/memory#derived-store-machine-recall)
- [Retain / Recall / Reflect (operational loop)](https://docs.openclaw.ai/experiments/research/memory#retain-%2F-recall-%2F-reflect-operational-loop)
- [Retain: normalize daily logs into “facts”](https://docs.openclaw.ai/experiments/research/memory#retain-normalize-daily-logs-into-%E2%80%9Cfacts%E2%80%9D)
- [Recall: queries over the derived index](https://docs.openclaw.ai/experiments/research/memory#recall-queries-over-the-derived-index)
- [Reflect: produce stable pages + update beliefs](https://docs.openclaw.ai/experiments/research/memory#reflect-produce-stable-pages-%2B-update-beliefs)
- [CLI integration: standalone vs deep integration](https://docs.openclaw.ai/experiments/research/memory#cli-integration-standalone-vs-deep-integration)
- [Why integrate into OpenClaw?](https://docs.openclaw.ai/experiments/research/memory#why-integrate-into-openclaw)
- [Why still split a library?](https://docs.openclaw.ai/experiments/research/memory#why-still-split-a-library)
- [“S-Collide” / SuCo: when to use it (research)](https://docs.openclaw.ai/experiments/research/memory#%E2%80%9Cs-collide%E2%80%9D-%2F-suco-when-to-use-it-research)
- [Smallest useful pilot](https://docs.openclaw.ai/experiments/research/memory#smallest-useful-pilot)
- [References](https://docs.openclaw.ai/experiments/research/memory#references)

# [​](https://docs.openclaw.ai/experiments/research/memory\#workspace-memory-v2-offline--research-notes)  Workspace Memory v2 (offline): research notes

Target: Clawd-style workspace (`agents.defaults.workspace`, default `~/.openclaw/workspace`) where “memory” is stored as one Markdown file per day (`memory/YYYY-MM-DD.md`) plus a small set of stable files (e.g. `memory.md`, `SOUL.md`).This doc proposes an **offline-first** memory architecture that keeps Markdown as the canonical, reviewable source of truth, but adds **structured recall** (search, entity summaries, confidence updates) via a derived index.

## [​](https://docs.openclaw.ai/experiments/research/memory\#why-change)  Why change?

The current setup (one file per day) is excellent for:

- “append-only” journaling
- human editing
- git-backed durability + auditability
- low-friction capture (“just write it down”)

It’s weak for:

- high-recall retrieval (“what did we decide about X?”, “last time we tried Y?”)
- entity-centric answers (“tell me about Alice / The Castle / warelay”) without rereading many files
- opinion/preference stability (and evidence when it changes)
- time constraints (“what was true during Nov 2025?”) and conflict resolution

## [​](https://docs.openclaw.ai/experiments/research/memory\#design-goals)  Design goals

- **Offline**: works without network; can run on laptop/Castle; no cloud dependency.
- **Explainable**: retrieved items should be attributable (file + location) and separable from inference.
- **Low ceremony**: daily logging stays Markdown, no heavy schema work.
- **Incremental**: v1 is useful with FTS only; semantic/vector and graphs are optional upgrades.
- **Agent-friendly**: makes “recall within token budgets” easy (return small bundles of facts).

## [​](https://docs.openclaw.ai/experiments/research/memory\#north-star-model-hindsight-%C3%97-letta)  North star model (Hindsight × Letta)

Two pieces to blend:

1. **Letta/MemGPT-style control loop**

- keep a small “core” always in context (persona + key user facts)
- everything else is out-of-context and retrieved via tools
- memory writes are explicit tool calls (append/replace/insert), persisted, then re-injected next turn

2. **Hindsight-style memory substrate**

- separate what’s observed vs what’s believed vs what’s summarized
- support retain/recall/reflect
- confidence-bearing opinions that can evolve with evidence
- entity-aware retrieval + temporal queries (even without full knowledge graphs)

## [​](https://docs.openclaw.ai/experiments/research/memory\#proposed-architecture-markdown-source-of-truth-+-derived-index)  Proposed architecture (Markdown source-of-truth + derived index)

### [​](https://docs.openclaw.ai/experiments/research/memory\#canonical-store-git-friendly)  Canonical store (git-friendly)

Keep `~/.openclaw/workspace` as canonical human-readable memory.Suggested workspace layout:

Copy

```
~/.openclaw/workspace/
  memory.md                    # small: durable facts + preferences (core-ish)
  memory/
    YYYY-MM-DD.md              # daily log (append; narrative)
  bank/                        # “typed” memory pages (stable, reviewable)
    world.md                   # objective facts about the world
    experience.md              # what the agent did (first-person)
    opinions.md                # subjective prefs/judgments + confidence + evidence pointers
    entities/
      Peter.md
      The-Castle.md
      warelay.md
      ...
```

Notes:

- **Daily log stays daily log**. No need to turn it into JSON.
- The `bank/` files are **curated**, produced by reflection jobs, and can still be edited by hand.
- `memory.md` remains “small + core-ish”: the things you want Clawd to see every session.

### [​](https://docs.openclaw.ai/experiments/research/memory\#derived-store-machine-recall)  Derived store (machine recall)

Add a derived index under the workspace (not necessarily git tracked):

Copy

```
~/.openclaw/workspace/.memory/index.sqlite
```

Back it with:

- SQLite schema for facts + entity links + opinion metadata
- SQLite **FTS5** for lexical recall (fast, tiny, offline)
- optional embeddings table for semantic recall (still offline)

The index is always **rebuildable from Markdown**.

## [​](https://docs.openclaw.ai/experiments/research/memory\#retain-/-recall-/-reflect-operational-loop)  Retain / Recall / Reflect (operational loop)

### [​](https://docs.openclaw.ai/experiments/research/memory\#retain-normalize-daily-logs-into-%E2%80%9Cfacts%E2%80%9D)  Retain: normalize daily logs into “facts”

Hindsight’s key insight that matters here: store **narrative, self-contained facts**, not tiny snippets.Practical rule for `memory/YYYY-MM-DD.md`:

- at end of day (or during), add a `## Retain` section with 2–5 bullets that are:

  - narrative (cross-turn context preserved)
  - self-contained (standalone makes sense later)
  - tagged with type + entity mentions

Example:

Copy

```
## Retain
- W @Peter: Currently in Marrakech (Nov 27–Dec 1, 2025) for Andy’s birthday.
- B @warelay: I fixed the Baileys WS crash by wrapping connection.update handlers in try/catch (see memory/2025-11-27.md).
- O(c=0.95) @Peter: Prefers concise replies (&lt;1500 chars) on WhatsApp; long content goes into files.
```

Minimal parsing:

- Type prefix: `W` (world), `B` (experience/biographical), `O` (opinion), `S` (observation/summary; usually generated)
- Entities: `@Peter`, `@warelay`, etc (slugs map to `bank/entities/*.md`)
- Opinion confidence: `O(c=0.0..1.0)` optional

If you don’t want authors to think about it: the reflect job can infer these bullets from the rest of the log, but having an explicit `## Retain` section is the easiest “quality lever”.

### [​](https://docs.openclaw.ai/experiments/research/memory\#recall-queries-over-the-derived-index)  Recall: queries over the derived index

Recall should support:

- **lexical**: “find exact terms / names / commands” (FTS5)
- **entity**: “tell me about X” (entity pages + entity-linked facts)
- **temporal**: “what happened around Nov 27” / “since last week”
- **opinion**: “what does Peter prefer?” (with confidence + evidence)

Return format should be agent-friendly and cite sources:

- `kind` (`world|experience|opinion|observation`)
- `timestamp` (source day, or extracted time range if present)
- `entities` (`["Peter","warelay"]`)
- `content` (the narrative fact)
- `source` (`memory/2025-11-27.md#L12` etc)

### [​](https://docs.openclaw.ai/experiments/research/memory\#reflect-produce-stable-pages-+-update-beliefs)  Reflect: produce stable pages + update beliefs

Reflection is a scheduled job (daily or heartbeat `ultrathink`) that:

- updates `bank/entities/*.md` from recent facts (entity summaries)
- updates `bank/opinions.md` confidence based on reinforcement/contradiction
- optionally proposes edits to `memory.md` (“core-ish” durable facts)

Opinion evolution (simple, explainable):

- each opinion has:
  - statement
  - confidence `c ∈ [0,1]`
  - last\_updated
  - evidence links (supporting + contradicting fact IDs)
- when new facts arrive:
  - find candidate opinions by entity overlap + similarity (FTS first, embeddings later)
  - update confidence by small deltas; big jumps require strong contradiction + repeated evidence

## [​](https://docs.openclaw.ai/experiments/research/memory\#cli-integration-standalone-vs-deep-integration)  CLI integration: standalone vs deep integration

Recommendation: **deep integration in OpenClaw**, but keep a separable core library.

### [​](https://docs.openclaw.ai/experiments/research/memory\#why-integrate-into-openclaw)  Why integrate into OpenClaw?

- OpenClaw already knows:
  - the workspace path (`agents.defaults.workspace`)
  - the session model + heartbeats
  - logging + troubleshooting patterns
- You want the agent itself to call the tools:
  - `openclaw memory recall "…" --k 25 --since 30d`
  - `openclaw memory reflect --since 7d`

### [​](https://docs.openclaw.ai/experiments/research/memory\#why-still-split-a-library)  Why still split a library?

- keep memory logic testable without gateway/runtime
- reuse from other contexts (local scripts, future desktop app, etc.)

Shape:
The memory tooling is intended to be a small CLI + library layer, but this is exploratory only.

## [​](https://docs.openclaw.ai/experiments/research/memory\#%E2%80%9Cs-collide%E2%80%9D-/-suco-when-to-use-it-research)  “S-Collide” / SuCo: when to use it (research)

If “S-Collide” refers to **SuCo (Subspace Collision)**: it’s an ANN retrieval approach that targets strong recall/latency tradeoffs by using learned/structured collisions in subspaces (paper: arXiv 2411.14754, 2024).Pragmatic take for `~/.openclaw/workspace`:

- **don’t start** with SuCo.
- start with SQLite FTS + (optional) simple embeddings; you’ll get most UX wins immediately.
- consider SuCo/HNSW/ScaNN-class solutions only once:
  - corpus is big (tens/hundreds of thousands of chunks)
  - brute-force embedding search becomes too slow
  - recall quality is meaningfully bottlenecked by lexical search

Offline-friendly alternatives (in increasing complexity):

- SQLite FTS5 + metadata filters (zero ML)
- Embeddings + brute force (works surprisingly far if chunk count is low)
- HNSW index (common, robust; needs a library binding)
- SuCo (research-grade; attractive if there’s a solid implementation you can embed)

Open question:

- what’s the **best** offline embedding model for “personal assistant memory” on your machines (laptop + desktop)?

  - if you already have Ollama: embed with a local model; otherwise ship a small embedding model in the toolchain.

## [​](https://docs.openclaw.ai/experiments/research/memory\#smallest-useful-pilot)  Smallest useful pilot

If you want a minimal, still-useful version:

- Add `bank/` entity pages and a `## Retain` section in daily logs.
- Use SQLite FTS for recall with citations (path + line numbers).
- Add embeddings only if recall quality or scale demands it.

## [​](https://docs.openclaw.ai/experiments/research/memory\#references)  References

- Letta / MemGPT concepts: “core memory blocks” + “archival memory” + tool-driven self-editing memory.
- Hindsight Technical Report: “retain / recall / reflect”, four-network memory, narrative fact extraction, opinion confidence evolution.
- SuCo: arXiv 2411.14754 (2024): “Subspace Collision” approximate nearest neighbor retrieval.

[Onboarding and Config Protocol](https://docs.openclaw.ai/experiments/onboarding-config-protocol) [Model Config Exploration](https://docs.openclaw.ai/experiments/proposals/model-config)

Ctrl+I

---
