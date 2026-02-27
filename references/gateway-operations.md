# Gateway Operations


---

<!-- Page: Heartbeat -->

## Heartbeat

**Source:** https://docs.openclaw.ai/gateway/heartbeat

[Skip to main content](https://docs.openclaw.ai/gateway/heartbeat#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration and operations

Heartbeat

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Heartbeat (Gateway)](https://docs.openclaw.ai/gateway/heartbeat#heartbeat-gateway)
- [Quick start (beginner)](https://docs.openclaw.ai/gateway/heartbeat#quick-start-beginner)
- [Defaults](https://docs.openclaw.ai/gateway/heartbeat#defaults)
- [What the heartbeat prompt is for](https://docs.openclaw.ai/gateway/heartbeat#what-the-heartbeat-prompt-is-for)
- [Response contract](https://docs.openclaw.ai/gateway/heartbeat#response-contract)
- [Config](https://docs.openclaw.ai/gateway/heartbeat#config)
- [Scope and precedence](https://docs.openclaw.ai/gateway/heartbeat#scope-and-precedence)
- [Per-agent heartbeats](https://docs.openclaw.ai/gateway/heartbeat#per-agent-heartbeats)
- [Active hours example](https://docs.openclaw.ai/gateway/heartbeat#active-hours-example)
- [24/7 setup](https://docs.openclaw.ai/gateway/heartbeat#24%2F7-setup)
- [Multi account example](https://docs.openclaw.ai/gateway/heartbeat#multi-account-example)
- [Field notes](https://docs.openclaw.ai/gateway/heartbeat#field-notes)
- [Delivery behavior](https://docs.openclaw.ai/gateway/heartbeat#delivery-behavior)
- [Visibility controls](https://docs.openclaw.ai/gateway/heartbeat#visibility-controls)
- [What each flag does](https://docs.openclaw.ai/gateway/heartbeat#what-each-flag-does)
- [Per-channel vs per-account examples](https://docs.openclaw.ai/gateway/heartbeat#per-channel-vs-per-account-examples)
- [Common patterns](https://docs.openclaw.ai/gateway/heartbeat#common-patterns)
- [HEARTBEAT.md (optional)](https://docs.openclaw.ai/gateway/heartbeat#heartbeat-md-optional)
- [Can the agent update HEARTBEAT.md?](https://docs.openclaw.ai/gateway/heartbeat#can-the-agent-update-heartbeat-md)
- [Manual wake (on-demand)](https://docs.openclaw.ai/gateway/heartbeat#manual-wake-on-demand)
- [Reasoning delivery (optional)](https://docs.openclaw.ai/gateway/heartbeat#reasoning-delivery-optional)
- [Cost awareness](https://docs.openclaw.ai/gateway/heartbeat#cost-awareness)

# [​](https://docs.openclaw.ai/gateway/heartbeat\#heartbeat-gateway)  Heartbeat (Gateway)

> **Heartbeat vs Cron?** See [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat) for guidance on when to use each.

Heartbeat runs **periodic agent turns** in the main session so the model can
surface anything that needs attention without spamming you.Troubleshooting: [/automation/troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)

## [​](https://docs.openclaw.ai/gateway/heartbeat\#quick-start-beginner)  Quick start (beginner)

1. Leave heartbeats enabled (default is `30m`, or `1h` for Anthropic OAuth/setup-token) or set your own cadence.
2. Create a tiny `HEARTBEAT.md` checklist in the agent workspace (optional but recommended).
3. Decide where heartbeat messages should go (`target: "none"` is the default; set `target: "last"` to route to the last contact).
4. Optional: enable heartbeat reasoning delivery for transparency.
5. Optional: restrict heartbeats to active hours (local time).

Example config:

Copy

```
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",
        target: "last", // explicit delivery to last contact (default is "none")
        directPolicy: "allow", // default: allow direct/DM targets; set "block" to suppress
        // activeHours: { start: "08:00", end: "24:00" },
        // includeReasoning: true, // optional: send separate `Reasoning:` message too
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/gateway/heartbeat\#defaults)  Defaults

- Interval: `30m` (or `1h` when Anthropic OAuth/setup-token is the detected auth mode). Set `agents.defaults.heartbeat.every` or per-agent `agents.list[].heartbeat.every`; use `0m` to disable.
- Prompt body (configurable via `agents.defaults.heartbeat.prompt`):
`Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.`
- The heartbeat prompt is sent **verbatim** as the user message. The system
prompt includes a “Heartbeat” section and the run is flagged internally.
- Active hours (`heartbeat.activeHours`) are checked in the configured timezone.
Outside the window, heartbeats are skipped until the next tick inside the window.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#what-the-heartbeat-prompt-is-for)  What the heartbeat prompt is for

The default prompt is intentionally broad:

- **Background tasks**: “Consider outstanding tasks” nudges the agent to review
follow-ups (inbox, calendar, reminders, queued work) and surface anything urgent.
- **Human check-in**: “Checkup sometimes on your human during day time” nudges an
occasional lightweight “anything you need?” message, but avoids night-time spam
by using your configured local timezone (see [/concepts/timezone](https://docs.openclaw.ai/concepts/timezone)).

If you want a heartbeat to do something very specific (e.g. “check Gmail PubSub
stats” or “verify gateway health”), set `agents.defaults.heartbeat.prompt` (or
`agents.list[].heartbeat.prompt`) to a custom body (sent verbatim).

## [​](https://docs.openclaw.ai/gateway/heartbeat\#response-contract)  Response contract

- If nothing needs attention, reply with **`HEARTBEAT_OK`**.
- During heartbeat runs, OpenClaw treats `HEARTBEAT_OK` as an ack when it appears
at the **start or end** of the reply. The token is stripped and the reply is
dropped if the remaining content is **≤ `ackMaxChars`** (default: 300).
- If `HEARTBEAT_OK` appears in the **middle** of a reply, it is not treated
specially.
- For alerts, **do not** include `HEARTBEAT_OK`; return only the alert text.

Outside heartbeats, stray `HEARTBEAT_OK` at the start/end of a message is stripped
and logged; a message that is only `HEARTBEAT_OK` is dropped.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#config)  Config

Copy

```
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m", // default: 30m (0m disables)
        model: "anthropic/claude-opus-4-6",
        includeReasoning: false, // default: false (deliver separate Reasoning: message when available)
        target: "last", // default: none | options: last | none | <channel id> (core or plugin, e.g. "bluebubbles")
        to: "+15551234567", // optional channel-specific override
        accountId: "ops-bot", // optional multi-account channel id
        prompt: "Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.",
        ackMaxChars: 300, // max chars allowed after HEARTBEAT_OK
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/heartbeat\#scope-and-precedence)  Scope and precedence

- `agents.defaults.heartbeat` sets global heartbeat behavior.
- `agents.list[].heartbeat` merges on top; if any agent has a `heartbeat` block, **only those agents** run heartbeats.
- `channels.defaults.heartbeat` sets visibility defaults for all channels.
- `channels.<channel>.heartbeat` overrides channel defaults.
- `channels.<channel>.accounts.<id>.heartbeat` (multi-account channels) overrides per-channel settings.

### [​](https://docs.openclaw.ai/gateway/heartbeat\#per-agent-heartbeats)  Per-agent heartbeats

If any `agents.list[]` entry includes a `heartbeat` block, **only those agents**
run heartbeats. The per-agent block merges on top of `agents.defaults.heartbeat`
(so you can set shared defaults once and override per agent).Example: two agents, only the second agent runs heartbeats.

Copy

```
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",
        target: "last", // explicit delivery to last contact (default is "none")
      },
    },
    list: [\
      { id: "main", default: true },\
      {\
        id: "ops",\
        heartbeat: {\
          every: "1h",\
          target: "whatsapp",\
          to: "+15551234567",\
          prompt: "Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.",\
        },\
      },\
    ],
  },
}
```

### [​](https://docs.openclaw.ai/gateway/heartbeat\#active-hours-example)  Active hours example

Restrict heartbeats to business hours in a specific timezone:

Copy

```
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",
        target: "last", // explicit delivery to last contact (default is "none")
        activeHours: {
          start: "09:00",
          end: "22:00",
          timezone: "America/New_York", // optional; uses your userTimezone if set, otherwise host tz
        },
      },
    },
  },
}
```

Outside this window (before 9am or after 10pm Eastern), heartbeats are skipped. The next scheduled tick inside the window will run normally.

### [​](https://docs.openclaw.ai/gateway/heartbeat\#24/7-setup)  24/7 setup

If you want heartbeats to run all day, use one of these patterns:

- Omit `activeHours` entirely (no time-window restriction; this is the default behavior).
- Set a full-day window: `activeHours: { start: "00:00", end: "24:00" }`.

Do not set the same `start` and `end` time (for example `08:00` to `08:00`).
That is treated as a zero-width window, so heartbeats are always skipped.

### [​](https://docs.openclaw.ai/gateway/heartbeat\#multi-account-example)  Multi account example

Use `accountId` to target a specific account on multi-account channels like Telegram:

Copy

```
{
  agents: {
    list: [\
      {\
        id: "ops",\
        heartbeat: {\
          every: "1h",\
          target: "telegram",\
          to: "12345678:topic:42", // optional: route to a specific topic/thread\
          accountId: "ops-bot",\
        },\
      },\
    ],
  },
  channels: {
    telegram: {
      accounts: {
        "ops-bot": { botToken: "YOUR_TELEGRAM_BOT_TOKEN" },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/heartbeat\#field-notes)  Field notes

- `every`: heartbeat interval (duration string; default unit = minutes).
- `model`: optional model override for heartbeat runs (`provider/model`).
- `includeReasoning`: when enabled, also deliver the separate `Reasoning:` message when available (same shape as `/reasoning on`).
- `session`: optional session key for heartbeat runs.

  - `main` (default): agent main session.
  - Explicit session key (copy from `openclaw sessions --json` or the [sessions CLI](https://docs.openclaw.ai/cli/sessions)).
  - Session key formats: see [Sessions](https://docs.openclaw.ai/concepts/session) and [Groups](https://docs.openclaw.ai/channels/groups).
- `target`:

  - `last`: deliver to the last used external channel.
  - explicit channel: `whatsapp` / `telegram` / `discord` / `googlechat` / `slack` / `msteams` / `signal` / `imessage`.
  - `none` (default): run the heartbeat but **do not deliver** externally.
- `directPolicy`: controls direct/DM delivery behavior:

  - `allow` (default): allow direct/DM heartbeat delivery.
  - `block`: suppress direct/DM delivery (`reason=dm-blocked`).
- `to`: optional recipient override (channel-specific id, e.g. E.164 for WhatsApp or a Telegram chat id). For Telegram topics/threads, use `<chatId>:topic:<messageThreadId>`.
- `accountId`: optional account id for multi-account channels. When `target: "last"`, the account id applies to the resolved last channel if it supports accounts; otherwise it is ignored. If the account id does not match a configured account for the resolved channel, delivery is skipped.
- `prompt`: overrides the default prompt body (not merged).
- `ackMaxChars`: max chars allowed after `HEARTBEAT_OK` before delivery.
- `suppressToolErrorWarnings`: when true, suppresses tool error warning payloads during heartbeat runs.
- `activeHours`: restricts heartbeat runs to a time window. Object with `start` (HH:MM, inclusive; use `00:00` for start-of-day), `end` (HH:MM exclusive; `24:00` allowed for end-of-day), and optional `timezone`.

  - Omitted or `"user"`: uses your `agents.defaults.userTimezone` if set, otherwise falls back to the host system timezone.
  - `"local"`: always uses the host system timezone.
  - Any IANA identifier (e.g. `America/New_York`): used directly; if invalid, falls back to the `"user"` behavior above.
  - `start` and `end` must not be equal for an active window; equal values are treated as zero-width (always outside the window).
  - Outside the active window, heartbeats are skipped until the next tick inside the window.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#delivery-behavior)  Delivery behavior

- Heartbeats run in the agent’s main session by default (`agent:<id>:<mainKey>`),
or `global` when `session.scope = "global"`. Set `session` to override to a
specific channel session (Discord/WhatsApp/etc.).
- `session` only affects the run context; delivery is controlled by `target` and `to`.
- To deliver to a specific channel/recipient, set `target` \+ `to`. With
`target: "last"`, delivery uses the last external channel for that session.
- Heartbeat deliveries allow direct/DM targets by default. Set `directPolicy: "block"` to suppress direct-target sends while still running the heartbeat turn.
- If the main queue is busy, the heartbeat is skipped and retried later.
- If `target` resolves to no external destination, the run still happens but no
outbound message is sent.
- Heartbeat-only replies do **not** keep the session alive; the last `updatedAt`
is restored so idle expiry behaves normally.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#visibility-controls)  Visibility controls

By default, `HEARTBEAT_OK` acknowledgments are suppressed while alert content is
delivered. You can adjust this per channel or per account:

Copy

```
channels:
  defaults:
    heartbeat:
      showOk: false # Hide HEARTBEAT_OK (default)
      showAlerts: true # Show alert messages (default)
      useIndicator: true # Emit indicator events (default)
  telegram:
    heartbeat:
      showOk: true # Show OK acknowledgments on Telegram
  whatsapp:
    accounts:
      work:
        heartbeat:
          showAlerts: false # Suppress alert delivery for this account
```

Precedence: per-account → per-channel → channel defaults → built-in defaults.

### [​](https://docs.openclaw.ai/gateway/heartbeat\#what-each-flag-does)  What each flag does

- `showOk`: sends a `HEARTBEAT_OK` acknowledgment when the model returns an OK-only reply.
- `showAlerts`: sends the alert content when the model returns a non-OK reply.
- `useIndicator`: emits indicator events for UI status surfaces.

If **all three** are false, OpenClaw skips the heartbeat run entirely (no model call).

### [​](https://docs.openclaw.ai/gateway/heartbeat\#per-channel-vs-per-account-examples)  Per-channel vs per-account examples

Copy

```
channels:
  defaults:
    heartbeat:
      showOk: false
      showAlerts: true
      useIndicator: true
  slack:
    heartbeat:
      showOk: true # all Slack accounts
    accounts:
      ops:
        heartbeat:
          showAlerts: false # suppress alerts for the ops account only
  telegram:
    heartbeat:
      showOk: true
```

### [​](https://docs.openclaw.ai/gateway/heartbeat\#common-patterns)  Common patterns

| Goal | Config |
| --- | --- |
| Default behavior (silent OKs, alerts on) | _(no config needed)_ |
| Fully silent (no messages, no indicator) | `channels.defaults.heartbeat: { showOk: false, showAlerts: false, useIndicator: false }` |
| Indicator-only (no messages) | `channels.defaults.heartbeat: { showOk: false, showAlerts: false, useIndicator: true }` |
| OKs in one channel only | `channels.telegram.heartbeat: { showOk: true }` |

## [​](https://docs.openclaw.ai/gateway/heartbeat\#heartbeat-md-optional)  HEARTBEAT.md (optional)

If a `HEARTBEAT.md` file exists in the workspace, the default prompt tells the
agent to read it. Think of it as your “heartbeat checklist”: small, stable, and
safe to include every 30 minutes.If `HEARTBEAT.md` exists but is effectively empty (only blank lines and markdown
headers like `# Heading`), OpenClaw skips the heartbeat run to save API calls.
If the file is missing, the heartbeat still runs and the model decides what to do.Keep it tiny (short checklist or reminders) to avoid prompt bloat.Example `HEARTBEAT.md`:

Copy

```
# Heartbeat checklist

- Quick scan: anything urgent in inboxes?
- If it’s daytime, do a lightweight check-in if nothing else is pending.
- If a task is blocked, write down _what is missing_ and ask Peter next time.
```

### [​](https://docs.openclaw.ai/gateway/heartbeat\#can-the-agent-update-heartbeat-md)  Can the agent update HEARTBEAT.md?

Yes — if you ask it to.`HEARTBEAT.md` is just a normal file in the agent workspace, so you can tell the
agent (in a normal chat) something like:

- “Update `HEARTBEAT.md` to add a daily calendar check.”
- “Rewrite `HEARTBEAT.md` so it’s shorter and focused on inbox follow-ups.”

If you want this to happen proactively, you can also include an explicit line in
your heartbeat prompt like: “If the checklist becomes stale, update HEARTBEAT.md
with a better one.”Safety note: don’t put secrets (API keys, phone numbers, private tokens) into
`HEARTBEAT.md` — it becomes part of the prompt context.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#manual-wake-on-demand)  Manual wake (on-demand)

You can enqueue a system event and trigger an immediate heartbeat with:

Copy

```
openclaw system event --text "Check for urgent follow-ups" --mode now
```

If multiple agents have `heartbeat` configured, a manual wake runs each of those
agent heartbeats immediately.Use `--mode next-heartbeat` to wait for the next scheduled tick.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#reasoning-delivery-optional)  Reasoning delivery (optional)

By default, heartbeats deliver only the final “answer” payload.If you want transparency, enable:

- `agents.defaults.heartbeat.includeReasoning: true`

When enabled, heartbeats will also deliver a separate message prefixed
`Reasoning:` (same shape as `/reasoning on`). This can be useful when the agent
is managing multiple sessions/codexes and you want to see why it decided to ping
you — but it can also leak more internal detail than you want. Prefer keeping it
off in group chats.

## [​](https://docs.openclaw.ai/gateway/heartbeat\#cost-awareness)  Cost awareness

Heartbeats run full agent turns. Shorter intervals burn more tokens. Keep
`HEARTBEAT.md` small and consider a cheaper `model` or `target: "none"` if you
only want internal state updates.

[Health Checks](https://docs.openclaw.ai/gateway/health) [Doctor](https://docs.openclaw.ai/gateway/doctor)

Ctrl+I

---

---

<!-- Page: Secrets Management -->

## Secrets Management

**Source:** https://docs.openclaw.ai/gateway/secrets

[Skip to main content](https://docs.openclaw.ai/gateway/secrets#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration and operations

Secrets Management

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Secrets management](https://docs.openclaw.ai/gateway/secrets#secrets-management)
- [Goals and runtime model](https://docs.openclaw.ai/gateway/secrets#goals-and-runtime-model)
- [Onboarding reference preflight](https://docs.openclaw.ai/gateway/secrets#onboarding-reference-preflight)
- [SecretRef contract](https://docs.openclaw.ai/gateway/secrets#secretref-contract)
- [source: "env"](https://docs.openclaw.ai/gateway/secrets#source-env)
- [source: "file"](https://docs.openclaw.ai/gateway/secrets#source-file)
- [source: "exec"](https://docs.openclaw.ai/gateway/secrets#source-exec)
- [Provider config](https://docs.openclaw.ai/gateway/secrets#provider-config)
- [Env provider](https://docs.openclaw.ai/gateway/secrets#env-provider)
- [File provider](https://docs.openclaw.ai/gateway/secrets#file-provider)
- [Exec provider](https://docs.openclaw.ai/gateway/secrets#exec-provider)
- [Exec integration examples](https://docs.openclaw.ai/gateway/secrets#exec-integration-examples)
- [1Password CLI](https://docs.openclaw.ai/gateway/secrets#1password-cli)
- [HashiCorp Vault CLI](https://docs.openclaw.ai/gateway/secrets#hashicorp-vault-cli)
- [sops](https://docs.openclaw.ai/gateway/secrets#sops)
- [In-scope fields (v1)](https://docs.openclaw.ai/gateway/secrets#in-scope-fields-v1)
- [~/.openclaw/openclaw.json](https://docs.openclaw.ai/gateway/secrets#%2F-openclaw%2Fopenclaw-json)
- [~/.openclaw/agents/<agentId>/agent/auth-profiles.json](https://docs.openclaw.ai/gateway/secrets#%2F-openclaw%2Fagents%2F%3Cagentid%3E%2Fagent%2Fauth-profiles-json)
- [Required behavior and precedence](https://docs.openclaw.ai/gateway/secrets#required-behavior-and-precedence)
- [Activation triggers](https://docs.openclaw.ai/gateway/secrets#activation-triggers)
- [Degraded and recovered operator signals](https://docs.openclaw.ai/gateway/secrets#degraded-and-recovered-operator-signals)
- [Audit and configure workflow](https://docs.openclaw.ai/gateway/secrets#audit-and-configure-workflow)
- [secrets audit](https://docs.openclaw.ai/gateway/secrets#secrets-audit)
- [secrets configure](https://docs.openclaw.ai/gateway/secrets#secrets-configure)
- [secrets apply](https://docs.openclaw.ai/gateway/secrets#secrets-apply)
- [One-way safety policy](https://docs.openclaw.ai/gateway/secrets#one-way-safety-policy)
- [auth.json compatibility notes](https://docs.openclaw.ai/gateway/secrets#auth-json-compatibility-notes)
- [Related docs](https://docs.openclaw.ai/gateway/secrets#related-docs)

# [​](https://docs.openclaw.ai/gateway/secrets\#secrets-management)  Secrets management

OpenClaw supports additive secret references so credentials do not need to be stored as plaintext in config files.Plaintext still works. Secret refs are optional.

## [​](https://docs.openclaw.ai/gateway/secrets\#goals-and-runtime-model)  Goals and runtime model

Secrets are resolved into an in-memory runtime snapshot.

- Resolution is eager during activation, not lazy on request paths.
- Startup fails fast if any referenced credential cannot be resolved.
- Reload uses atomic swap: full success or keep last-known-good.
- Runtime requests read from the active in-memory snapshot.

This keeps secret-provider outages off the hot request path.

## [​](https://docs.openclaw.ai/gateway/secrets\#onboarding-reference-preflight)  Onboarding reference preflight

When onboarding runs in interactive mode and you choose secret reference storage, OpenClaw performs a fast preflight check before saving:

- Env refs: validates env var name and confirms a non-empty value is visible during onboarding.
- Provider refs (`file` or `exec`): validates the selected provider, resolves the provided `id`, and checks value type.

If validation fails, onboarding shows the error and lets you retry.

## [​](https://docs.openclaw.ai/gateway/secrets\#secretref-contract)  SecretRef contract

Use one object shape everywhere:

Copy

```
{ source: "env" | "file" | "exec", provider: "default", id: "..." }
```

### [​](https://docs.openclaw.ai/gateway/secrets\#source-env)  `source: "env"`

Copy

```
{ source: "env", provider: "default", id: "OPENAI_API_KEY" }
```

Validation:

- `provider` must match `^[a-z][a-z0-9_-]{0,63}$`
- `id` must match `^[A-Z][A-Z0-9_]{0,127}$`

### [​](https://docs.openclaw.ai/gateway/secrets\#source-file)  `source: "file"`

Copy

```
{ source: "file", provider: "filemain", id: "/providers/openai/apiKey" }
```

Validation:

- `provider` must match `^[a-z][a-z0-9_-]{0,63}$`
- `id` must be an absolute JSON pointer (`/...`)
- RFC6901 escaping in segments: `~` =\> `~0`, `/` =\> `~1`

### [​](https://docs.openclaw.ai/gateway/secrets\#source-exec)  `source: "exec"`

Copy

```
{ source: "exec", provider: "vault", id: "providers/openai/apiKey" }
```

Validation:

- `provider` must match `^[a-z][a-z0-9_-]{0,63}$`
- `id` must match `^[A-Za-z0-9][A-Za-z0-9._:/-]{0,255}$`

## [​](https://docs.openclaw.ai/gateway/secrets\#provider-config)  Provider config

Define providers under `secrets.providers`:

Copy

```
{
  secrets: {
    providers: {
      default: { source: "env" },
      filemain: {
        source: "file",
        path: "~/.openclaw/secrets.json",
        mode: "json", // or "singleValue"
      },
      vault: {
        source: "exec",
        command: "/usr/local/bin/openclaw-vault-resolver",
        args: ["--profile", "prod"],
        passEnv: ["PATH", "VAULT_ADDR"],
        jsonOnly: true,
      },
    },
    defaults: {
      env: "default",
      file: "filemain",
      exec: "vault",
    },
    resolution: {
      maxProviderConcurrency: 4,
      maxRefsPerProvider: 512,
      maxBatchBytes: 262144,
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/secrets\#env-provider)  Env provider

- Optional allowlist via `allowlist`.
- Missing/empty env values fail resolution.

### [​](https://docs.openclaw.ai/gateway/secrets\#file-provider)  File provider

- Reads local file from `path`.
- `mode: "json"` expects JSON object payload and resolves `id` as pointer.
- `mode: "singleValue"` expects ref id `"value"` and returns file contents.
- Path must pass ownership/permission checks.

### [​](https://docs.openclaw.ai/gateway/secrets\#exec-provider)  Exec provider

- Runs configured absolute binary path, no shell.
- By default, `command` must point to a regular file (not a symlink).
- Set `allowSymlinkCommand: true` to allow symlink command paths (for example Homebrew shims). OpenClaw validates the resolved target path.
- Enable `allowSymlinkCommand` only when required for trusted package-manager paths, and pair it with `trustedDirs` (for example `["/opt/homebrew"]`).
- When `trustedDirs` is set, checks apply to the resolved target path.
- Supports timeout, no-output timeout, output byte limits, env allowlist, and trusted dirs.
- Request payload (stdin):

Copy

```
{ "protocolVersion": 1, "provider": "vault", "ids": ["providers/openai/apiKey"] }
```

- Response payload (stdout):

Copy

```
{ "protocolVersion": 1, "values": { "providers/openai/apiKey": "sk-..." } }
```

Optional per-id errors:

Copy

```
{
  "protocolVersion": 1,
  "values": {},
  "errors": { "providers/openai/apiKey": { "message": "not found" } }
}
```

## [​](https://docs.openclaw.ai/gateway/secrets\#exec-integration-examples)  Exec integration examples

### [​](https://docs.openclaw.ai/gateway/secrets\#1password-cli)  1Password CLI

Copy

```
{
  secrets: {
    providers: {
      onepassword_openai: {
        source: "exec",
        command: "/opt/homebrew/bin/op",
        allowSymlinkCommand: true, // required for Homebrew symlinked binaries
        trustedDirs: ["/opt/homebrew"],
        args: ["read", "op://Personal/OpenClaw QA API Key/password"],
        passEnv: ["HOME"],
        jsonOnly: false,
      },
    },
  },
  models: {
    providers: {
      openai: {
        baseUrl: "https://api.openai.com/v1",
        models: [{ id: "gpt-5", name: "gpt-5" }],
        apiKey: { source: "exec", provider: "onepassword_openai", id: "value" },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/secrets\#hashicorp-vault-cli)  HashiCorp Vault CLI

Copy

```
{
  secrets: {
    providers: {
      vault_openai: {
        source: "exec",
        command: "/opt/homebrew/bin/vault",
        allowSymlinkCommand: true, // required for Homebrew symlinked binaries
        trustedDirs: ["/opt/homebrew"],
        args: ["kv", "get", "-field=OPENAI_API_KEY", "secret/openclaw"],
        passEnv: ["VAULT_ADDR", "VAULT_TOKEN"],
        jsonOnly: false,
      },
    },
  },
  models: {
    providers: {
      openai: {
        baseUrl: "https://api.openai.com/v1",
        models: [{ id: "gpt-5", name: "gpt-5" }],
        apiKey: { source: "exec", provider: "vault_openai", id: "value" },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/secrets\#sops)  `sops`

Copy

```
{
  secrets: {
    providers: {
      sops_openai: {
        source: "exec",
        command: "/opt/homebrew/bin/sops",
        allowSymlinkCommand: true, // required for Homebrew symlinked binaries
        trustedDirs: ["/opt/homebrew"],
        args: ["-d", "--extract", '["providers"]["openai"]["apiKey"]', "/path/to/secrets.enc.json"],
        passEnv: ["SOPS_AGE_KEY_FILE"],
        jsonOnly: false,
      },
    },
  },
  models: {
    providers: {
      openai: {
        baseUrl: "https://api.openai.com/v1",
        models: [{ id: "gpt-5", name: "gpt-5" }],
        apiKey: { source: "exec", provider: "sops_openai", id: "value" },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/gateway/secrets\#in-scope-fields-v1)  In-scope fields (v1)

### [​](https://docs.openclaw.ai/gateway/secrets\#/-openclaw/openclaw-json)  `~/.openclaw/openclaw.json`

- `models.providers.<provider>.apiKey`
- `skills.entries.<skillKey>.apiKey`
- `channels.googlechat.serviceAccount`
- `channels.googlechat.serviceAccountRef`
- `channels.googlechat.accounts.<accountId>.serviceAccount`
- `channels.googlechat.accounts.<accountId>.serviceAccountRef`

### [​](https://docs.openclaw.ai/gateway/secrets\#/-openclaw/agents/%3Cagentid%3E/agent/auth-profiles-json)  `~/.openclaw/agents/<agentId>/agent/auth-profiles.json`

- `profiles.<profileId>.keyRef` for `type: "api_key"`
- `profiles.<profileId>.tokenRef` for `type: "token"`

OAuth credential storage changes are out of scope.

## [​](https://docs.openclaw.ai/gateway/secrets\#required-behavior-and-precedence)  Required behavior and precedence

- Field without ref: unchanged.
- Field with ref: required at activation time.
- If plaintext and ref both exist, ref wins at runtime and plaintext is ignored.

Warning code:

- `SECRETS_REF_OVERRIDES_PLAINTEXT`

## [​](https://docs.openclaw.ai/gateway/secrets\#activation-triggers)  Activation triggers

Secret activation is attempted on:

- Startup (preflight plus final activation)
- Config reload hot-apply path
- Config reload restart-check path
- Manual reload via `secrets.reload`

Activation contract:

- Success swaps the snapshot atomically.
- Startup failure aborts gateway startup.
- Runtime reload failure keeps last-known-good snapshot.

## [​](https://docs.openclaw.ai/gateway/secrets\#degraded-and-recovered-operator-signals)  Degraded and recovered operator signals

When reload-time activation fails after a healthy state, OpenClaw enters degraded secrets state.One-shot system event and log codes:

- `SECRETS_RELOADER_DEGRADED`
- `SECRETS_RELOADER_RECOVERED`

Behavior:

- Degraded: runtime keeps last-known-good snapshot.
- Recovered: emitted once after a successful activation.
- Repeated failures while already degraded log warnings but do not spam events.
- Startup fail-fast does not emit degraded events because no runtime snapshot exists yet.

## [​](https://docs.openclaw.ai/gateway/secrets\#audit-and-configure-workflow)  Audit and configure workflow

Use this default operator flow:

Copy

```
openclaw secrets audit --check
openclaw secrets configure
openclaw secrets audit --check
```

Migration completeness:

- Include `skills.entries.<skillKey>.apiKey` targets when those skills use API keys.
- If `audit --check` still reports plaintext findings after a partial migration, migrate the remaining reported paths and rerun audit.

### [​](https://docs.openclaw.ai/gateway/secrets\#secrets-audit)  `secrets audit`

Findings include:

- plaintext values at rest (`openclaw.json`, `auth-profiles.json`, `.env`)
- unresolved refs
- precedence shadowing (`auth-profiles` taking priority over config refs)
- legacy residues (`auth.json`, OAuth out-of-scope reminders)

### [​](https://docs.openclaw.ai/gateway/secrets\#secrets-configure)  `secrets configure`

Interactive helper that:

- configures `secrets.providers` first (`env`/`file`/`exec`, add/edit/remove)
- lets you select secret-bearing fields in `openclaw.json`
- captures SecretRef details (`source`, `provider`, `id`)
- runs preflight resolution
- can apply immediately

Helpful modes:

- `openclaw secrets configure --providers-only`
- `openclaw secrets configure --skip-provider-setup`

`configure` apply defaults to:

- scrub matching static creds from `auth-profiles.json` for targeted providers
- scrub legacy static `api_key` entries from `auth.json`
- scrub matching known secret lines from `<config-dir>/.env`

### [​](https://docs.openclaw.ai/gateway/secrets\#secrets-apply)  `secrets apply`

Apply a saved plan:

Copy

```
openclaw secrets apply --from /tmp/openclaw-secrets-plan.json
openclaw secrets apply --from /tmp/openclaw-secrets-plan.json --dry-run
```

For strict target/path contract details and exact rejection rules, see:

- [Secrets Apply Plan Contract](https://docs.openclaw.ai/gateway/secrets-plan-contract)

## [​](https://docs.openclaw.ai/gateway/secrets\#one-way-safety-policy)  One-way safety policy

OpenClaw intentionally does **not** write rollback backups that contain pre-migration plaintext secret values.Safety model:

- preflight must succeed before write mode
- runtime activation is validated before commit
- apply updates files using atomic file replacement and best-effort in-memory restore on failure

## [​](https://docs.openclaw.ai/gateway/secrets\#auth-json-compatibility-notes)  `auth.json` compatibility notes

For static credentials, OpenClaw runtime no longer depends on plaintext `auth.json`.

- Runtime credential source is the resolved in-memory snapshot.
- Legacy `auth.json` static `api_key` entries are scrubbed when discovered.
- OAuth-related legacy compatibility behavior remains separate.

## [​](https://docs.openclaw.ai/gateway/secrets\#related-docs)  Related docs

- CLI commands: [secrets](https://docs.openclaw.ai/cli/secrets)
- Plan contract details: [Secrets Apply Plan Contract](https://docs.openclaw.ai/gateway/secrets-plan-contract)
- Auth setup: [Authentication](https://docs.openclaw.ai/gateway/authentication)
- Security posture: [Security](https://docs.openclaw.ai/gateway/security)
- Environment precedence: [Environment Variables](https://docs.openclaw.ai/help/environment)

[Authentication](https://docs.openclaw.ai/gateway/authentication) [Secrets Apply Plan Contract](https://docs.openclaw.ai/gateway/secrets-plan-contract)

Ctrl+I

---
