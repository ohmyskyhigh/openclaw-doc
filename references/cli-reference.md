# Cli Reference


---

<!-- Page: browser -->

## browser

**Source:** https://docs.openclaw.ai/cli/browser

[Skip to main content](https://docs.openclaw.ai/cli/browser#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

browser

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw browser](https://docs.openclaw.ai/cli/browser#openclaw-browser)
- [Common flags](https://docs.openclaw.ai/cli/browser#common-flags)
- [Quick start (local)](https://docs.openclaw.ai/cli/browser#quick-start-local)
- [Profiles](https://docs.openclaw.ai/cli/browser#profiles)
- [Tabs](https://docs.openclaw.ai/cli/browser#tabs)
- [Snapshot / screenshot / actions](https://docs.openclaw.ai/cli/browser#snapshot-%2F-screenshot-%2F-actions)
- [Chrome extension relay (attach via toolbar button)](https://docs.openclaw.ai/cli/browser#chrome-extension-relay-attach-via-toolbar-button)
- [Remote browser control (node host proxy)](https://docs.openclaw.ai/cli/browser#remote-browser-control-node-host-proxy)

# [​](https://docs.openclaw.ai/cli/browser\#openclaw-browser)  `openclaw browser`

Manage OpenClaw’s browser control server and run browser actions (tabs, snapshots, screenshots, navigation, clicks, typing).Related:

- Browser tool + API: [Browser tool](https://docs.openclaw.ai/tools/browser)
- Chrome extension relay: [Chrome extension](https://docs.openclaw.ai/tools/chrome-extension)

## [​](https://docs.openclaw.ai/cli/browser\#common-flags)  Common flags

- `--url <gatewayWsUrl>`: Gateway WebSocket URL (defaults to config).
- `--token <token>`: Gateway token (if required).
- `--timeout <ms>`: request timeout (ms).
- `--browser-profile <name>`: choose a browser profile (default from config).
- `--json`: machine-readable output (where supported).

## [​](https://docs.openclaw.ai/cli/browser\#quick-start-local)  Quick start (local)

Copy

```
openclaw browser --browser-profile chrome tabs
openclaw browser --browser-profile openclaw start
openclaw browser --browser-profile openclaw open https://example.com
openclaw browser --browser-profile openclaw snapshot
```

## [​](https://docs.openclaw.ai/cli/browser\#profiles)  Profiles

Profiles are named browser routing configs. In practice:

- `openclaw`: launches/attaches to a dedicated OpenClaw-managed Chrome instance (isolated user data dir).
- `chrome`: controls your existing Chrome tab(s) via the Chrome extension relay.

Copy

```
openclaw browser profiles
openclaw browser create-profile --name work --color "#FF5A36"
openclaw browser delete-profile --name work
```

Use a specific profile:

Copy

```
openclaw browser --browser-profile work tabs
```

## [​](https://docs.openclaw.ai/cli/browser\#tabs)  Tabs

Copy

```
openclaw browser tabs
openclaw browser open https://docs.openclaw.ai
openclaw browser focus <targetId>
openclaw browser close <targetId>
```

## [​](https://docs.openclaw.ai/cli/browser\#snapshot-/-screenshot-/-actions)  Snapshot / screenshot / actions

Snapshot:

Copy

```
openclaw browser snapshot
```

Screenshot:

Copy

```
openclaw browser screenshot
```

Navigate/click/type (ref-based UI automation):

Copy

```
openclaw browser navigate https://example.com
openclaw browser click <ref>
openclaw browser type <ref> "hello"
```

## [​](https://docs.openclaw.ai/cli/browser\#chrome-extension-relay-attach-via-toolbar-button)  Chrome extension relay (attach via toolbar button)

This mode lets the agent control an existing Chrome tab that you attach manually (it does not auto-attach).Install the unpacked extension to a stable path:

Copy

```
openclaw browser extension install
openclaw browser extension path
```

Then Chrome → `chrome://extensions` → enable “Developer mode” → “Load unpacked” → select the printed folder.Full guide: [Chrome extension](https://docs.openclaw.ai/tools/chrome-extension)

## [​](https://docs.openclaw.ai/cli/browser\#remote-browser-control-node-host-proxy)  Remote browser control (node host proxy)

If the Gateway runs on a different machine than the browser, run a **node host** on the machine that has Chrome/Brave/Edge/Chromium. The Gateway will proxy browser actions to that node (no separate browser control server required).Use `gateway.nodes.browser.mode` to control auto-routing and `gateway.nodes.browser.node` to pin a specific node if multiple are connected.Security + remote setup: [Browser tool](https://docs.openclaw.ai/tools/browser), [Remote access](https://docs.openclaw.ai/gateway/remote), [Tailscale](https://docs.openclaw.ai/gateway/tailscale), [Security](https://docs.openclaw.ai/gateway/security)

[approvals](https://docs.openclaw.ai/cli/approvals) [channels](https://docs.openclaw.ai/cli/channels)

Ctrl+I

---

---

<!-- Page: completion -->

## completion

**Source:** https://docs.openclaw.ai/cli/completion

[Skip to main content](https://docs.openclaw.ai/cli/completion#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

completion

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw completion](https://docs.openclaw.ai/cli/completion#openclaw-completion)
- [Usage](https://docs.openclaw.ai/cli/completion#usage)
- [Options](https://docs.openclaw.ai/cli/completion#options)
- [Notes](https://docs.openclaw.ai/cli/completion#notes)

# [​](https://docs.openclaw.ai/cli/completion\#openclaw-completion)  `openclaw completion`

Generate shell completion scripts and optionally install them into your shell profile.

## [​](https://docs.openclaw.ai/cli/completion\#usage)  Usage

Copy

```
openclaw completion
openclaw completion --shell zsh
openclaw completion --install
openclaw completion --shell fish --install
openclaw completion --write-state
openclaw completion --shell bash --write-state
```

## [​](https://docs.openclaw.ai/cli/completion\#options)  Options

- `-s, --shell <shell>`: shell target (`zsh`, `bash`, `powershell`, `fish`; default: `zsh`)
- `-i, --install`: install completion by adding a source line to your shell profile
- `--write-state`: write completion script(s) to `$OPENCLAW_STATE_DIR/completions` without printing to stdout
- `-y, --yes`: skip install confirmation prompts

## [​](https://docs.openclaw.ai/cli/completion\#notes)  Notes

- `--install` writes a small “OpenClaw Completion” block into your shell profile and points it at the cached script.
- Without `--install` or `--write-state`, the command prints the script to stdout.
- Completion generation eagerly loads command trees so nested subcommands are included.

[clawbot](https://docs.openclaw.ai/cli/clawbot) [config](https://docs.openclaw.ai/cli/config)

Ctrl+I

---

---

<!-- Page: dashboard -->

## dashboard

**Source:** https://docs.openclaw.ai/cli/dashboard

[Skip to main content](https://docs.openclaw.ai/cli/dashboard#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

dashboard

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw dashboard](https://docs.openclaw.ai/cli/dashboard#openclaw-dashboard)

# [​](https://docs.openclaw.ai/cli/dashboard\#openclaw-dashboard)  `openclaw dashboard`

Open the Control UI using your current auth.

Copy

```
openclaw dashboard
openclaw dashboard --no-open
```

[daemon](https://docs.openclaw.ai/cli/daemon) [devices](https://docs.openclaw.ai/cli/devices)

Ctrl+I

---

---

<!-- Page: logs -->

## logs.md

**Source:** https://docs.openclaw.ai/cli/logs.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# logs

# `openclaw logs`

Tail Gateway file logs over RPC (works in remote mode).

Related:

* Logging overview: [Logging](/logging)

## Examples

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
openclaw logs
openclaw logs --follow
openclaw logs --json
openclaw logs --limit 500
openclaw logs --local-time
openclaw logs --follow --local-time
```

Use `--local-time` to render timestamps in your local timezone.
````

---

---

<!-- Page: memory -->

## memory

**Source:** https://docs.openclaw.ai/cli/memory

[Skip to main content](https://docs.openclaw.ai/cli/memory#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

memory

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw memory](https://docs.openclaw.ai/cli/memory#openclaw-memory)
- [Examples](https://docs.openclaw.ai/cli/memory#examples)
- [Options](https://docs.openclaw.ai/cli/memory#options)

# [​](https://docs.openclaw.ai/cli/memory\#openclaw-memory)  `openclaw memory`

Manage semantic memory indexing and search.
Provided by the active memory plugin (default: `memory-core`; set `plugins.slots.memory = "none"` to disable).Related:

- Memory concept: [Memory](https://docs.openclaw.ai/concepts/memory)
- Plugins: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/cli/memory\#examples)  Examples

Copy

```
openclaw memory status
openclaw memory status --deep
openclaw memory status --deep --index
openclaw memory status --deep --index --verbose
openclaw memory index
openclaw memory index --verbose
openclaw memory search "release checklist"
openclaw memory search --query "release checklist"
openclaw memory status --agent main
openclaw memory index --agent main --verbose
```

## [​](https://docs.openclaw.ai/cli/memory\#options)  Options

Common:

- `--agent <id>`: scope to a single agent (default: all configured agents).
- `--verbose`: emit detailed logs during probes and indexing.

`memory search`:

- Query input: pass either positional `[query]` or `--query <text>`.
- If both are provided, `--query` wins.
- If neither is provided, the command exits with an error.

Notes:

- `memory status --deep` probes vector + embedding availability.
- `memory status --deep --index` runs a reindex if the store is dirty.
- `memory index --verbose` prints per-phase details (provider, model, sources, batch activity).
- `memory status` includes any extra paths configured via `memorySearch.extraPaths`.

[logs](https://docs.openclaw.ai/cli/logs) [message](https://docs.openclaw.ai/cli/message)

Ctrl+I

---

---

<!-- Page: models -->

## models

**Source:** https://docs.openclaw.ai/cli/models

[Skip to main content](https://docs.openclaw.ai/cli/models#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

models

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw models](https://docs.openclaw.ai/cli/models#openclaw-models)
- [Common commands](https://docs.openclaw.ai/cli/models#common-commands)
- [models status](https://docs.openclaw.ai/cli/models#models-status)
- [Aliases + fallbacks](https://docs.openclaw.ai/cli/models#aliases-%2B-fallbacks)
- [Auth profiles](https://docs.openclaw.ai/cli/models#auth-profiles)

# [​](https://docs.openclaw.ai/cli/models\#openclaw-models)  `openclaw models`

Model discovery, scanning, and configuration (default model, fallbacks, auth profiles).Related:

- Providers + models: [Models](https://docs.openclaw.ai/providers/models)
- Provider auth setup: [Getting started](https://docs.openclaw.ai/start/getting-started)

## [​](https://docs.openclaw.ai/cli/models\#common-commands)  Common commands

Copy

```
openclaw models status
openclaw models list
openclaw models set <model-or-alias>
openclaw models scan
```

`openclaw models status` shows the resolved default/fallbacks plus an auth overview.
When provider usage snapshots are available, the OAuth/token status section includes
provider usage headers.
Add `--probe` to run live auth probes against each configured provider profile.
Probes are real requests (may consume tokens and trigger rate limits).
Use `--agent <id>` to inspect a configured agent’s model/auth state. When omitted,
the command uses `OPENCLAW_AGENT_DIR`/`PI_CODING_AGENT_DIR` if set, otherwise the
configured default agent.Notes:

- `models set <model-or-alias>` accepts `provider/model` or an alias.
- Model refs are parsed by splitting on the **first**`/`. If the model ID includes `/` (OpenRouter-style), include the provider prefix (example: `openrouter/moonshotai/kimi-k2`).
- If you omit the provider, OpenClaw treats the input as an alias or a model for the **default provider** (only works when there is no `/` in the model ID).

### [​](https://docs.openclaw.ai/cli/models\#models-status)  `models status`

Options:

- `--json`
- `--plain`
- `--check` (exit 1=expired/missing, 2=expiring)
- `--probe` (live probe of configured auth profiles)
- `--probe-provider <name>` (probe one provider)
- `--probe-profile <id>` (repeat or comma-separated profile ids)
- `--probe-timeout <ms>`
- `--probe-concurrency <n>`
- `--probe-max-tokens <n>`
- `--agent <id>` (configured agent id; overrides `OPENCLAW_AGENT_DIR`/`PI_CODING_AGENT_DIR`)

## [​](https://docs.openclaw.ai/cli/models\#aliases-+-fallbacks)  Aliases + fallbacks

Copy

```
openclaw models aliases list
openclaw models fallbacks list
```

## [​](https://docs.openclaw.ai/cli/models\#auth-profiles)  Auth profiles

Copy

```
openclaw models auth add
openclaw models auth login --provider <id>
openclaw models auth setup-token
openclaw models auth paste-token
```

`models auth login` runs a provider plugin’s auth flow (OAuth/API key). Use
`openclaw plugins list` to see which providers are installed.Notes:

- `setup-token` prompts for a setup-token value (generate it with `claude setup-token` on any machine).
- `paste-token` accepts a token string generated elsewhere or from automation.

[message](https://docs.openclaw.ai/cli/message) [node](https://docs.openclaw.ai/cli/node)

Ctrl+I

---

---

<!-- Page: nodes -->

## nodes.md

**Source:** https://docs.openclaw.ai/cli/nodes.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# nodes

# `openclaw nodes`

Manage paired nodes (devices) and invoke node capabilities.

Related:

* Nodes overview: [Nodes](/nodes)
* Camera: [Camera nodes](/nodes/camera)
* Images: [Image nodes](/nodes/images)

Common options:

* `--url`, `--token`, `--timeout`, `--json`

## Common commands

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
openclaw nodes list
openclaw nodes list --connected
openclaw nodes list --last-connected 24h
openclaw nodes pending
openclaw nodes approve <requestId>
openclaw nodes status
openclaw nodes status --connected
openclaw nodes status --last-connected 24h
```

`nodes list` prints pending/paired tables. Paired rows include the most recent connect age (Last Connect).
Use `--connected` to only show currently-connected nodes. Use `--last-connected <duration>` to
filter to nodes that connected within a duration (e.g. `24h`, `7d`).

## Invoke / run

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
openclaw nodes invoke --node <id|name|ip> --command <command> --params <json>
openclaw nodes run --node <id|name|ip> <command...>
openclaw nodes run --raw "git status"
openclaw nodes run --agent main --node <id|name|ip> --raw "git status"
```

Invoke flags:

* `--params <json>`: JSON object string (default `{}`).
* `--invoke-timeout <ms>`: node invoke timeout (default `15000`).
* `--idempotency-key <key>`: optional idempotency key.

### Exec-style defaults

`nodes run` mirrors the model’s exec behavior (defaults + approvals):

* Reads `tools.exec.*` (plus `agents.list[].tools.exec.*` overrides).
* Uses exec approvals (`exec.approval.request`) before invoking `system.run`.
* `--node` can be omitted when `tools.exec.node` is set.
* Requires a node that advertises `system.run` (macOS companion app or headless node host).

Flags:

* `--cwd <path>`: working directory.
* `--env <key=val>`: env override (repeatable). Note: node hosts ignore `PATH` overrides (and `tools.exec.pathPrepend` is not applied to node hosts).
* `--command-timeout <ms>`: command timeout.
* `--invoke-timeout <ms>`: node invoke timeout (default `30000`).
* `--needs-screen-recording`: require screen recording permission.
* `--raw <command>`: run a shell string (`/bin/sh -lc` or `cmd.exe /c`).
  In allowlist mode on Windows node hosts, `cmd.exe /c` shell-wrapper runs require approval
  (allowlist entry alone does not auto-allow the wrapper form).
* `--agent <id>`: agent-scoped approvals/allowlists (defaults to configured agent).
* `--ask <off|on-miss|always>`, `--security <deny|allowlist|full>`: overrides.
````

---

---

<!-- Page: status -->

## status

**Source:** https://docs.openclaw.ai/cli/status

[Skip to main content](https://docs.openclaw.ai/cli/status#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

CLI commands

status

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [openclaw status](https://docs.openclaw.ai/cli/status#openclaw-status)

# [​](https://docs.openclaw.ai/cli/status\#openclaw-status)  `openclaw status`

Diagnostics for channels + sessions.

Copy

```
openclaw status
openclaw status --all
openclaw status --deep
openclaw status --usage
```

Notes:

- `--deep` runs live probes (WhatsApp Web + Telegram + Discord + Google Chat + Slack + Signal).
- Output includes per-agent session stores when multiple agents are configured.
- Overview includes Gateway + node host service install/runtime status when available.
- Overview includes update channel + git SHA (for source checkouts).
- Update info surfaces in the Overview; if an update is available, status prints a hint to run `openclaw update` (see [Updating](https://docs.openclaw.ai/install/updating)).

[skills](https://docs.openclaw.ai/cli/skills) [system](https://docs.openclaw.ai/cli/system)

Ctrl+I

---

---

<!-- Page: system -->

## system.md

**Source:** https://docs.openclaw.ai/cli/system.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# system

# `openclaw system`

System-level helpers for the Gateway: enqueue system events, control heartbeats,
and view presence.

## Common commands

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
openclaw system event --text "Check for urgent follow-ups" --mode now
openclaw system heartbeat enable
openclaw system heartbeat last
openclaw system presence
```

## `system event`

Enqueue a system event on the **main** session. The next heartbeat will inject
it as a `System:` line in the prompt. Use `--mode now` to trigger the heartbeat
immediately; `next-heartbeat` waits for the next scheduled tick.

Flags:

* `--text <text>`: required system event text.
* `--mode <mode>`: `now` or `next-heartbeat` (default).
* `--json`: machine-readable output.

## `system heartbeat last|enable|disable`

Heartbeat controls:

* `last`: show the last heartbeat event.
* `enable`: turn heartbeats back on (use this if they were disabled).
* `disable`: pause heartbeats.

Flags:

* `--json`: machine-readable output.

## `system presence`

List the current system presence entries the Gateway knows about (nodes,
instances, and similar status lines).

Flags:

* `--json`: machine-readable output.

## Notes

* Requires a running Gateway reachable by your current config (local or remote).
* System events are ephemeral and not persisted across restarts.
````

---
