# Automation


---

<!-- Page: Cron Jobs -->

## Cron Jobs

**Source:** https://docs.openclaw.ai/automation/cron-jobs

[Skip to main content](https://docs.openclaw.ai/automation/cron-jobs#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Cron Jobs

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Cron jobs (Gateway scheduler)](https://docs.openclaw.ai/automation/cron-jobs#cron-jobs-gateway-scheduler)
- [TL;DR](https://docs.openclaw.ai/automation/cron-jobs#tldr)
- [Quick start (actionable)](https://docs.openclaw.ai/automation/cron-jobs#quick-start-actionable)
- [Tool-call equivalents (Gateway cron tool)](https://docs.openclaw.ai/automation/cron-jobs#tool-call-equivalents-gateway-cron-tool)
- [Where cron jobs are stored](https://docs.openclaw.ai/automation/cron-jobs#where-cron-jobs-are-stored)
- [Beginner-friendly overview](https://docs.openclaw.ai/automation/cron-jobs#beginner-friendly-overview)
- [Concepts](https://docs.openclaw.ai/automation/cron-jobs#concepts)
- [Jobs](https://docs.openclaw.ai/automation/cron-jobs#jobs)
- [Schedules](https://docs.openclaw.ai/automation/cron-jobs#schedules)
- [Main vs isolated execution](https://docs.openclaw.ai/automation/cron-jobs#main-vs-isolated-execution)
- [Main session jobs (system events)](https://docs.openclaw.ai/automation/cron-jobs#main-session-jobs-system-events)
- [Isolated jobs (dedicated cron sessions)](https://docs.openclaw.ai/automation/cron-jobs#isolated-jobs-dedicated-cron-sessions)
- [Payload shapes (what runs)](https://docs.openclaw.ai/automation/cron-jobs#payload-shapes-what-runs)
- [Announce delivery flow](https://docs.openclaw.ai/automation/cron-jobs#announce-delivery-flow)
- [Webhook delivery flow](https://docs.openclaw.ai/automation/cron-jobs#webhook-delivery-flow)
- [Model and thinking overrides](https://docs.openclaw.ai/automation/cron-jobs#model-and-thinking-overrides)
- [Delivery (channel + target)](https://docs.openclaw.ai/automation/cron-jobs#delivery-channel-%2B-target)
- [Telegram delivery targets (topics / forum threads)](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivery-targets-topics-%2F-forum-threads)
- [JSON schema for tool calls](https://docs.openclaw.ai/automation/cron-jobs#json-schema-for-tool-calls)
- [cron.add params](https://docs.openclaw.ai/automation/cron-jobs#cron-add-params)
- [cron.update params](https://docs.openclaw.ai/automation/cron-jobs#cron-update-params)
- [cron.run and cron.remove params](https://docs.openclaw.ai/automation/cron-jobs#cron-run-and-cron-remove-params)
- [Storage & history](https://docs.openclaw.ai/automation/cron-jobs#storage-%26-history)
- [Configuration](https://docs.openclaw.ai/automation/cron-jobs#configuration)
- [Maintenance](https://docs.openclaw.ai/automation/cron-jobs#maintenance)
- [Defaults](https://docs.openclaw.ai/automation/cron-jobs#defaults)
- [How it works](https://docs.openclaw.ai/automation/cron-jobs#how-it-works)
- [Performance caveat for high volume schedulers](https://docs.openclaw.ai/automation/cron-jobs#performance-caveat-for-high-volume-schedulers)
- [Customize examples](https://docs.openclaw.ai/automation/cron-jobs#customize-examples)
- [CLI quickstart](https://docs.openclaw.ai/automation/cron-jobs#cli-quickstart)
- [Gateway API surface](https://docs.openclaw.ai/automation/cron-jobs#gateway-api-surface)
- [Troubleshooting](https://docs.openclaw.ai/automation/cron-jobs#troubleshooting)
- [“Nothing runs”](https://docs.openclaw.ai/automation/cron-jobs#%E2%80%9Cnothing-runs%E2%80%9D)
- [A recurring job keeps delaying after failures](https://docs.openclaw.ai/automation/cron-jobs#a-recurring-job-keeps-delaying-after-failures)
- [Telegram delivers to the wrong place](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivers-to-the-wrong-place)
- [Subagent announce delivery retries](https://docs.openclaw.ai/automation/cron-jobs#subagent-announce-delivery-retries)

# [​](https://docs.openclaw.ai/automation/cron-jobs\#cron-jobs-gateway-scheduler)  Cron jobs (Gateway scheduler)

> **Cron vs Heartbeat?** See [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat) for guidance on when to use each.

Cron is the Gateway’s built-in scheduler. It persists jobs, wakes the agent at
the right time, and can optionally deliver output back to a chat.If you want _“run this every morning”_ or _“poke the agent in 20 minutes”_,
cron is the mechanism.Troubleshooting: [/automation/troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)

## [​](https://docs.openclaw.ai/automation/cron-jobs\#tldr)  TL;DR

- Cron runs **inside the Gateway** (not inside the model).
- Jobs persist under `~/.openclaw/cron/` so restarts don’t lose schedules.
- Two execution styles:
  - **Main session**: enqueue a system event, then run on the next heartbeat.
  - **Isolated**: run a dedicated agent turn in `cron:<jobId>`, with delivery (announce by default or none).
- Wakeups are first-class: a job can request “wake now” vs “next heartbeat”.
- Webhook posting is per job via `delivery.mode = "webhook"` \+ `delivery.to = "<url>"`.
- Legacy fallback remains for stored jobs with `notify: true` when `cron.webhook` is set, migrate those jobs to webhook delivery mode.

## [​](https://docs.openclaw.ai/automation/cron-jobs\#quick-start-actionable)  Quick start (actionable)

Create a one-shot reminder, verify it exists, and run it immediately:

Copy

```
openclaw cron add \
  --name "Reminder" \
  --at "2026-02-01T16:00:00Z" \
  --session main \
  --system-event "Reminder: check the cron docs draft" \
  --wake now \
  --delete-after-run

openclaw cron list
openclaw cron run <job-id>
openclaw cron runs --id <job-id>
```

Schedule a recurring isolated job with delivery:

Copy

```
openclaw cron add \
  --name "Morning brief" \
  --cron "0 7 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize overnight updates." \
  --announce \
  --channel slack \
  --to "channel:C1234567890"
```

## [​](https://docs.openclaw.ai/automation/cron-jobs\#tool-call-equivalents-gateway-cron-tool)  Tool-call equivalents (Gateway cron tool)

For the canonical JSON shapes and examples, see [JSON schema for tool calls](https://docs.openclaw.ai/automation/cron-jobs#json-schema-for-tool-calls).

## [​](https://docs.openclaw.ai/automation/cron-jobs\#where-cron-jobs-are-stored)  Where cron jobs are stored

Cron jobs are persisted on the Gateway host at `~/.openclaw/cron/jobs.json` by default.
The Gateway loads the file into memory and writes it back on changes, so manual edits
are only safe when the Gateway is stopped. Prefer `openclaw cron add/edit` or the cron
tool call API for changes.

## [​](https://docs.openclaw.ai/automation/cron-jobs\#beginner-friendly-overview)  Beginner-friendly overview

Think of a cron job as: **when** to run + **what** to do.

1. **Choose a schedule**   - One-shot reminder → `schedule.kind = "at"` (CLI: `--at`)
   - Repeating job → `schedule.kind = "every"` or `schedule.kind = "cron"`
   - If your ISO timestamp omits a timezone, it is treated as **UTC**.
2. **Choose where it runs**   - `sessionTarget: "main"` → run during the next heartbeat with main context.
   - `sessionTarget: "isolated"` → run a dedicated agent turn in `cron:<jobId>`.
3. **Choose the payload**   - Main session → `payload.kind = "systemEvent"`
   - Isolated session → `payload.kind = "agentTurn"`

Optional: one-shot jobs (`schedule.kind = "at"`) delete after success by default. Set
`deleteAfterRun: false` to keep them (they will disable after success).

## [​](https://docs.openclaw.ai/automation/cron-jobs\#concepts)  Concepts

### [​](https://docs.openclaw.ai/automation/cron-jobs\#jobs)  Jobs

A cron job is a stored record with:

- a **schedule** (when it should run),
- a **payload** (what it should do),
- optional **delivery mode** (`announce`, `webhook`, or `none`).
- optional **agent binding** (`agentId`): run the job under a specific agent; if
missing or unknown, the gateway falls back to the default agent.

Jobs are identified by a stable `jobId` (used by CLI/Gateway APIs).
In agent tool calls, `jobId` is canonical; legacy `id` is accepted for compatibility.
One-shot jobs auto-delete after success by default; set `deleteAfterRun: false` to keep them.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#schedules)  Schedules

Cron supports three schedule kinds:

- `at`: one-shot timestamp via `schedule.at` (ISO 8601).
- `every`: fixed interval (ms).
- `cron`: 5-field cron expression (or 6-field with seconds) with optional IANA timezone.

Cron expressions use `croner`. If a timezone is omitted, the Gateway host’s
local timezone is used.To reduce top-of-hour load spikes across many gateways, OpenClaw applies a
deterministic per-job stagger window of up to 5 minutes for recurring
top-of-hour expressions (for example `0 * * * *`, `0 */2 * * *`). Fixed-hour
expressions such as `0 7 * * *` remain exact.For any cron schedule, you can set an explicit stagger window with `schedule.staggerMs`
(`0` keeps exact timing). CLI shortcuts:

- `--stagger 30s` (or `1m`, `5m`) to set an explicit stagger window.
- `--exact` to force `staggerMs = 0`.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#main-vs-isolated-execution)  Main vs isolated execution

#### [​](https://docs.openclaw.ai/automation/cron-jobs\#main-session-jobs-system-events)  Main session jobs (system events)

Main jobs enqueue a system event and optionally wake the heartbeat runner.
They must use `payload.kind = "systemEvent"`.

- `wakeMode: "now"` (default): event triggers an immediate heartbeat run.
- `wakeMode: "next-heartbeat"`: event waits for the next scheduled heartbeat.

This is the best fit when you want the normal heartbeat prompt + main-session context.
See [Heartbeat](https://docs.openclaw.ai/gateway/heartbeat).

#### [​](https://docs.openclaw.ai/automation/cron-jobs\#isolated-jobs-dedicated-cron-sessions)  Isolated jobs (dedicated cron sessions)

Isolated jobs run a dedicated agent turn in session `cron:<jobId>`.Key behaviors:

- Prompt is prefixed with `[cron:<jobId> <job name>]` for traceability.
- Each run starts a **fresh session id** (no prior conversation carry-over).
- Default behavior: if `delivery` is omitted, isolated jobs announce a summary (`delivery.mode = "announce"`).
- `delivery.mode`chooses what happens:

  - `announce`: deliver a summary to the target channel and post a brief summary to the main session.
  - `webhook`: POST the finished event payload to `delivery.to` when the finished event includes a summary.
  - `none`: internal only (no delivery, no main-session summary).
- `wakeMode`controls when the main-session summary posts:

  - `now`: immediate heartbeat.
  - `next-heartbeat`: waits for the next scheduled heartbeat.

Use isolated jobs for noisy, frequent, or “background chores” that shouldn’t spam
your main chat history.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#payload-shapes-what-runs)  Payload shapes (what runs)

Two payload kinds are supported:

- `systemEvent`: main-session only, routed through the heartbeat prompt.
- `agentTurn`: isolated-session only, runs a dedicated agent turn.

Common `agentTurn` fields:

- `message`: required text prompt.
- `model` / `thinking`: optional overrides (see below).
- `timeoutSeconds`: optional timeout override.

Delivery config:

- `delivery.mode`: `none` \| `announce` \| `webhook`.
- `delivery.channel`: `last` or a specific channel.
- `delivery.to`: channel-specific target (announce) or webhook URL (webhook mode).
- `delivery.bestEffort`: avoid failing the job if announce delivery fails.

Announce delivery suppresses messaging tool sends for the run; use `delivery.channel`/`delivery.to`
to target the chat instead. When `delivery.mode = "none"`, no summary is posted to the main session.If `delivery` is omitted for isolated jobs, OpenClaw defaults to `announce`.

#### [​](https://docs.openclaw.ai/automation/cron-jobs\#announce-delivery-flow)  Announce delivery flow

When `delivery.mode = "announce"`, cron delivers directly via the outbound channel adapters.
The main agent is not spun up to craft or forward the message.Behavior details:

- Content: delivery uses the isolated run’s outbound payloads (text/media) with normal chunking and
channel formatting.
- Heartbeat-only responses (`HEARTBEAT_OK` with no real content) are not delivered.
- If the isolated run already sent a message to the same target via the message tool, delivery is
skipped to avoid duplicates.
- Missing or invalid delivery targets fail the job unless `delivery.bestEffort = true`.
- A short summary is posted to the main session only when `delivery.mode = "announce"`.
- The main-session summary respects `wakeMode`: `now` triggers an immediate heartbeat and
`next-heartbeat` waits for the next scheduled heartbeat.

#### [​](https://docs.openclaw.ai/automation/cron-jobs\#webhook-delivery-flow)  Webhook delivery flow

When `delivery.mode = "webhook"`, cron posts the finished event payload to `delivery.to` when the finished event includes a summary.Behavior details:

- The endpoint must be a valid HTTP(S) URL.
- No channel delivery is attempted in webhook mode.
- No main-session summary is posted in webhook mode.
- If `cron.webhookToken` is set, auth header is `Authorization: Bearer <cron.webhookToken>`.
- Deprecated fallback: stored legacy jobs with `notify: true` still post to `cron.webhook` (if configured), with a warning so you can migrate to `delivery.mode = "webhook"`.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#model-and-thinking-overrides)  Model and thinking overrides

Isolated jobs (`agentTurn`) can override the model and thinking level:

- `model`: Provider/model string (e.g., `anthropic/claude-sonnet-4-20250514`) or alias (e.g., `opus`)
- `thinking`: Thinking level (`off`, `minimal`, `low`, `medium`, `high`, `xhigh`; GPT-5.2 + Codex models only)

Note: You can set `model` on main-session jobs too, but it changes the shared main
session model. We recommend model overrides only for isolated jobs to avoid
unexpected context shifts.Resolution priority:

1. Job payload override (highest)
2. Hook-specific defaults (e.g., `hooks.gmail.model`)
3. Agent config default

### [​](https://docs.openclaw.ai/automation/cron-jobs\#delivery-channel-+-target)  Delivery (channel + target)

Isolated jobs can deliver output to a channel via the top-level `delivery` config:

- `delivery.mode`: `announce` (channel delivery), `webhook` (HTTP POST), or `none`.
- `delivery.channel`: `whatsapp` / `telegram` / `discord` / `slack` / `mattermost` (plugin) / `signal` / `imessage` / `last`.
- `delivery.to`: channel-specific recipient target.

`announce` delivery is only valid for isolated jobs (`sessionTarget: "isolated"`).
`webhook` delivery is valid for both main and isolated jobs.If `delivery.channel` or `delivery.to` is omitted, cron can fall back to the main session’s
“last route” (the last place the agent replied).Target format reminders:

- Slack/Discord/Mattermost (plugin) targets should use explicit prefixes (e.g. `channel:<id>`, `user:<id>`) to avoid ambiguity.
- Telegram topics should use the `:topic:` form (see below).

#### [​](https://docs.openclaw.ai/automation/cron-jobs\#telegram-delivery-targets-topics-/-forum-threads)  Telegram delivery targets (topics / forum threads)

Telegram supports forum topics via `message_thread_id`. For cron delivery, you can encode
the topic/thread into the `to` field:

- `-1001234567890` (chat id only)
- `-1001234567890:topic:123` (preferred: explicit topic marker)
- `-1001234567890:123` (shorthand: numeric suffix)

Prefixed targets like `telegram:...` / `telegram:group:...` are also accepted:

- `telegram:group:-1001234567890:topic:123`

## [​](https://docs.openclaw.ai/automation/cron-jobs\#json-schema-for-tool-calls)  JSON schema for tool calls

Use these shapes when calling Gateway `cron.*` tools directly (agent tool calls or RPC).
CLI flags accept human durations like `20m`, but tool calls should use an ISO 8601 string
for `schedule.at` and milliseconds for `schedule.everyMs`.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#cron-add-params)  cron.add params

One-shot, main session job (system event):

Copy

```
{
  "name": "Reminder",
  "schedule": { "kind": "at", "at": "2026-02-01T16:00:00Z" },
  "sessionTarget": "main",
  "wakeMode": "now",
  "payload": { "kind": "systemEvent", "text": "Reminder text" },
  "deleteAfterRun": true
}
```

Recurring, isolated job with delivery:

Copy

```
{
  "name": "Morning brief",
  "schedule": { "kind": "cron", "expr": "0 7 * * *", "tz": "America/Los_Angeles" },
  "sessionTarget": "isolated",
  "wakeMode": "next-heartbeat",
  "payload": {
    "kind": "agentTurn",
    "message": "Summarize overnight updates."
  },
  "delivery": {
    "mode": "announce",
    "channel": "slack",
    "to": "channel:C1234567890",
    "bestEffort": true
  }
}
```

Notes:

- `schedule.kind`: `at` (`at`), `every` (`everyMs`), or `cron` (`expr`, optional `tz`).
- `schedule.at` accepts ISO 8601 (timezone optional; treated as UTC when omitted).
- `everyMs` is milliseconds.
- `sessionTarget` must be `"main"` or `"isolated"` and must match `payload.kind`.
- Optional fields: `agentId`, `description`, `enabled`, `deleteAfterRun` (defaults to true for `at`),
`delivery`.
- `wakeMode` defaults to `"now"` when omitted.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#cron-update-params)  cron.update params

Copy

```
{
  "jobId": "job-123",
  "patch": {
    "enabled": false,
    "schedule": { "kind": "every", "everyMs": 3600000 }
  }
}
```

Notes:

- `jobId` is canonical; `id` is accepted for compatibility.
- Use `agentId: null` in the patch to clear an agent binding.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#cron-run-and-cron-remove-params)  cron.run and cron.remove params

Copy

```
{ "jobId": "job-123", "mode": "force" }
```

Copy

```
{ "jobId": "job-123" }
```

## [​](https://docs.openclaw.ai/automation/cron-jobs\#storage-&-history)  Storage & history

- Job store: `~/.openclaw/cron/jobs.json` (Gateway-managed JSON).
- Run history: `~/.openclaw/cron/runs/<jobId>.jsonl` (JSONL, auto-pruned by size and line count).
- Isolated cron run sessions in `sessions.json` are pruned by `cron.sessionRetention` (default `24h`; set `false` to disable).
- Override store path: `cron.store` in config.

## [​](https://docs.openclaw.ai/automation/cron-jobs\#configuration)  Configuration

Copy

```
{
  cron: {
    enabled: true, // default true
    store: "~/.openclaw/cron/jobs.json",
    maxConcurrentRuns: 1, // default 1
    webhook: "https://example.invalid/legacy", // deprecated fallback for stored notify:true jobs
    webhookToken: "replace-with-dedicated-webhook-token", // optional bearer token for webhook mode
    sessionRetention: "24h", // duration string or false
    runLog: {
      maxBytes: "2mb", // default 2_000_000 bytes
      keepLines: 2000, // default 2000
    },
  },
}
```

Run-log pruning behavior:

- `cron.runLog.maxBytes`: max run-log file size before pruning.
- `cron.runLog.keepLines`: when pruning, keep only the newest N lines.
- Both apply to `cron/runs/<jobId>.jsonl` files.

Webhook behavior:

- Preferred: set `delivery.mode: "webhook"` with `delivery.to: "https://..."` per job.
- Webhook URLs must be valid `http://` or `https://` URLs.
- When posted, payload is the cron finished event JSON.
- If `cron.webhookToken` is set, auth header is `Authorization: Bearer <cron.webhookToken>`.
- If `cron.webhookToken` is not set, no `Authorization` header is sent.
- Deprecated fallback: stored legacy jobs with `notify: true` still use `cron.webhook` when present.

Disable cron entirely:

- `cron.enabled: false` (config)
- `OPENCLAW_SKIP_CRON=1` (env)

## [​](https://docs.openclaw.ai/automation/cron-jobs\#maintenance)  Maintenance

Cron has two built-in maintenance paths: isolated run-session retention and run-log pruning.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#defaults)  Defaults

- `cron.sessionRetention`: `24h` (set `false` to disable run-session pruning)
- `cron.runLog.maxBytes`: `2_000_000` bytes
- `cron.runLog.keepLines`: `2000`

### [​](https://docs.openclaw.ai/automation/cron-jobs\#how-it-works)  How it works

- Isolated runs create session entries (`...:cron:<jobId>:run:<uuid>`) and transcript files.
- The reaper removes expired run-session entries older than `cron.sessionRetention`.
- For removed run sessions no longer referenced by the session store, OpenClaw archives transcript files and purges old deleted archives on the same retention window.
- After each run append, `cron/runs/<jobId>.jsonl` is size-checked:

  - if file size exceeds `runLog.maxBytes`, it is trimmed to the newest `runLog.keepLines` lines.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#performance-caveat-for-high-volume-schedulers)  Performance caveat for high volume schedulers

High-frequency cron setups can generate large run-session and run-log footprints. Maintenance is built in, but loose limits can still create avoidable IO and cleanup work.What to watch:

- long `cron.sessionRetention` windows with many isolated runs
- high `cron.runLog.keepLines` combined with large `runLog.maxBytes`
- many noisy recurring jobs writing to the same `cron/runs/<jobId>.jsonl`

What to do:

- keep `cron.sessionRetention` as short as your debugging/audit needs allow
- keep run logs bounded with moderate `runLog.maxBytes` and `runLog.keepLines`
- move noisy background jobs to isolated mode with delivery rules that avoid unnecessary chatter
- review growth periodically with `openclaw cron runs` and adjust retention before logs become large

### [​](https://docs.openclaw.ai/automation/cron-jobs\#customize-examples)  Customize examples

Keep run sessions for a week and allow bigger run logs:

Copy

```
{
  cron: {
    sessionRetention: "7d",
    runLog: {
      maxBytes: "10mb",
      keepLines: 5000,
    },
  },
}
```

Disable isolated run-session pruning but keep run-log pruning:

Copy

```
{
  cron: {
    sessionRetention: false,
    runLog: {
      maxBytes: "5mb",
      keepLines: 3000,
    },
  },
}
```

Tune for high-volume cron usage (example):

Copy

```
{
  cron: {
    sessionRetention: "12h",
    runLog: {
      maxBytes: "3mb",
      keepLines: 1500,
    },
  },
}
```

## [​](https://docs.openclaw.ai/automation/cron-jobs\#cli-quickstart)  CLI quickstart

One-shot reminder (UTC ISO, auto-delete after success):

Copy

```
openclaw cron add \
  --name "Send reminder" \
  --at "2026-01-12T18:00:00Z" \
  --session main \
  --system-event "Reminder: submit expense report." \
  --wake now \
  --delete-after-run
```

One-shot reminder (main session, wake immediately):

Copy

```
openclaw cron add \
  --name "Calendar check" \
  --at "20m" \
  --session main \
  --system-event "Next heartbeat: check calendar." \
  --wake now
```

Recurring isolated job (announce to WhatsApp):

Copy

```
openclaw cron add \
  --name "Morning status" \
  --cron "0 7 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize inbox + calendar for today." \
  --announce \
  --channel whatsapp \
  --to "+15551234567"
```

Recurring cron job with explicit 30-second stagger:

Copy

```
openclaw cron add \
  --name "Minute watcher" \
  --cron "0 * * * * *" \
  --tz "UTC" \
  --stagger 30s \
  --session isolated \
  --message "Run minute watcher checks." \
  --announce
```

Recurring isolated job (deliver to a Telegram topic):

Copy

```
openclaw cron add \
  --name "Nightly summary (topic)" \
  --cron "0 22 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize today; send to the nightly topic." \
  --announce \
  --channel telegram \
  --to "-1001234567890:topic:123"
```

Isolated job with model and thinking override:

Copy

```
openclaw cron add \
  --name "Deep analysis" \
  --cron "0 6 * * 1" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Weekly deep analysis of project progress." \
  --model "opus" \
  --thinking high \
  --announce \
  --channel whatsapp \
  --to "+15551234567"
```

Agent selection (multi-agent setups):

Copy

```
# Pin a job to agent "ops" (falls back to default if that agent is missing)
openclaw cron add --name "Ops sweep" --cron "0 6 * * *" --session isolated --message "Check ops queue" --agent ops

# Switch or clear the agent on an existing job
openclaw cron edit <jobId> --agent ops
openclaw cron edit <jobId> --clear-agent
```

Manual run (force is the default, use `--due` to only run when due):

Copy

```
openclaw cron run <jobId>
openclaw cron run <jobId> --due
```

Edit an existing job (patch fields):

Copy

```
openclaw cron edit <jobId> \
  --message "Updated prompt" \
  --model "opus" \
  --thinking low
```

Force an existing cron job to run exactly on schedule (no stagger):

Copy

```
openclaw cron edit <jobId> --exact
```

Run history:

Copy

```
openclaw cron runs --id <jobId> --limit 50
```

Immediate system event without creating a job:

Copy

```
openclaw system event --mode now --text "Next heartbeat: check battery."
```

## [​](https://docs.openclaw.ai/automation/cron-jobs\#gateway-api-surface)  Gateway API surface

- `cron.list`, `cron.status`, `cron.add`, `cron.update`, `cron.remove`
- `cron.run` (force or due), `cron.runs`
For immediate system events without a job, use [`openclaw system event`](https://docs.openclaw.ai/cli/system).

## [​](https://docs.openclaw.ai/automation/cron-jobs\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/automation/cron-jobs\#%E2%80%9Cnothing-runs%E2%80%9D)  “Nothing runs”

- Check cron is enabled: `cron.enabled` and `OPENCLAW_SKIP_CRON`.
- Check the Gateway is running continuously (cron runs inside the Gateway process).
- For `cron` schedules: confirm timezone (`--tz`) vs the host timezone.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#a-recurring-job-keeps-delaying-after-failures)  A recurring job keeps delaying after failures

- OpenClaw applies exponential retry backoff for recurring jobs after consecutive errors:
30s, 1m, 5m, 15m, then 60m between retries.
- Backoff resets automatically after the next successful run.
- One-shot (`at`) jobs disable after a terminal run (`ok`, `error`, or `skipped`) and do not retry.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#telegram-delivers-to-the-wrong-place)  Telegram delivers to the wrong place

- For forum topics, use `-100…:topic:<id>` so it’s explicit and unambiguous.
- If you see `telegram:...` prefixes in logs or stored “last route” targets, that’s normal;
cron delivery accepts them and still parses topic IDs correctly.

### [​](https://docs.openclaw.ai/automation/cron-jobs\#subagent-announce-delivery-retries)  Subagent announce delivery retries

- When a subagent run completes, the gateway announces the result to the requester session.
- If the announce flow returns `false` (e.g. requester session is busy), the gateway retries up to 3 times with tracking via `announceRetryCount`.
- Announces older than 5 minutes past `endedAt` are force-expired to prevent stale entries from looping indefinitely.
- If you see repeated announce deliveries in logs, check the subagent registry for entries with high `announceRetryCount` values.

[Hooks](https://docs.openclaw.ai/automation/hooks) [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)

Ctrl+I

---

---

<!-- Page: Cron vs Heartbeat -->

## Cron vs Heartbeat

**Source:** https://docs.openclaw.ai/automation/cron-vs-heartbeat

[Skip to main content](https://docs.openclaw.ai/automation/cron-vs-heartbeat#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Cron vs Heartbeat

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Cron vs Heartbeat: When to Use Each](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cron-vs-heartbeat-when-to-use-each)
- [Quick Decision Guide](https://docs.openclaw.ai/automation/cron-vs-heartbeat#quick-decision-guide)
- [Heartbeat: Periodic Awareness](https://docs.openclaw.ai/automation/cron-vs-heartbeat#heartbeat-periodic-awareness)
- [When to use heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat#when-to-use-heartbeat)
- [Heartbeat advantages](https://docs.openclaw.ai/automation/cron-vs-heartbeat#heartbeat-advantages)
- [Heartbeat example: HEARTBEAT.md checklist](https://docs.openclaw.ai/automation/cron-vs-heartbeat#heartbeat-example-heartbeat-md-checklist)
- [Configuring heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat#configuring-heartbeat)
- [Cron: Precise Scheduling](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cron-precise-scheduling)
- [When to use cron](https://docs.openclaw.ai/automation/cron-vs-heartbeat#when-to-use-cron)
- [Cron advantages](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cron-advantages)
- [Cron example: Daily morning briefing](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cron-example-daily-morning-briefing)
- [Cron example: One-shot reminder](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cron-example-one-shot-reminder)
- [Decision Flowchart](https://docs.openclaw.ai/automation/cron-vs-heartbeat#decision-flowchart)
- [Combining Both](https://docs.openclaw.ai/automation/cron-vs-heartbeat#combining-both)
- [Example: Efficient automation setup](https://docs.openclaw.ai/automation/cron-vs-heartbeat#example-efficient-automation-setup)
- [Lobster: Deterministic workflows with approvals](https://docs.openclaw.ai/automation/cron-vs-heartbeat#lobster-deterministic-workflows-with-approvals)
- [When Lobster fits](https://docs.openclaw.ai/automation/cron-vs-heartbeat#when-lobster-fits)
- [How it pairs with heartbeat and cron](https://docs.openclaw.ai/automation/cron-vs-heartbeat#how-it-pairs-with-heartbeat-and-cron)
- [Operational notes (from the code)](https://docs.openclaw.ai/automation/cron-vs-heartbeat#operational-notes-from-the-code)
- [Main Session vs Isolated Session](https://docs.openclaw.ai/automation/cron-vs-heartbeat#main-session-vs-isolated-session)
- [When to use main session cron](https://docs.openclaw.ai/automation/cron-vs-heartbeat#when-to-use-main-session-cron)
- [When to use isolated cron](https://docs.openclaw.ai/automation/cron-vs-heartbeat#when-to-use-isolated-cron)
- [Cost Considerations](https://docs.openclaw.ai/automation/cron-vs-heartbeat#cost-considerations)
- [Related](https://docs.openclaw.ai/automation/cron-vs-heartbeat#related)

# [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cron-vs-heartbeat-when-to-use-each)  Cron vs Heartbeat: When to Use Each

Both heartbeats and cron jobs let you run tasks on a schedule. This guide helps you choose the right mechanism for your use case.

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#quick-decision-guide)  Quick Decision Guide

| Use Case | Recommended | Why |
| --- | --- | --- |
| Check inbox every 30 min | Heartbeat | Batches with other checks, context-aware |
| Send daily report at 9am sharp | Cron (isolated) | Exact timing needed |
| Monitor calendar for upcoming events | Heartbeat | Natural fit for periodic awareness |
| Run weekly deep analysis | Cron (isolated) | Standalone task, can use different model |
| Remind me in 20 minutes | Cron (main, `--at`) | One-shot with precise timing |
| Background project health check | Heartbeat | Piggybacks on existing cycle |

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#heartbeat-periodic-awareness)  Heartbeat: Periodic Awareness

Heartbeats run in the **main session** at a regular interval (default: 30 min). They’re designed for the agent to check on things and surface anything important.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#when-to-use-heartbeat)  When to use heartbeat

- **Multiple periodic checks**: Instead of 5 separate cron jobs checking inbox, calendar, weather, notifications, and project status, a single heartbeat can batch all of these.
- **Context-aware decisions**: The agent has full main-session context, so it can make smart decisions about what’s urgent vs. what can wait.
- **Conversational continuity**: Heartbeat runs share the same session, so the agent remembers recent conversations and can follow up naturally.
- **Low-overhead monitoring**: One heartbeat replaces many small polling tasks.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#heartbeat-advantages)  Heartbeat advantages

- **Batches multiple checks**: One agent turn can review inbox, calendar, and notifications together.
- **Reduces API calls**: A single heartbeat is cheaper than 5 isolated cron jobs.
- **Context-aware**: The agent knows what you’ve been working on and can prioritize accordingly.
- **Smart suppression**: If nothing needs attention, the agent replies `HEARTBEAT_OK` and no message is delivered.
- **Natural timing**: Drifts slightly based on queue load, which is fine for most monitoring.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#heartbeat-example-heartbeat-md-checklist)  Heartbeat example: HEARTBEAT.md checklist

Copy

```
# Heartbeat checklist

- Check email for urgent messages
- Review calendar for events in next 2 hours
- If a background task finished, summarize results
- If idle for 8+ hours, send a brief check-in
```

The agent reads this on each heartbeat and handles all items in one turn.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#configuring-heartbeat)  Configuring heartbeat

Copy

```
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m", // interval
        target: "last", // explicit alert delivery target (default is "none")
        activeHours: { start: "08:00", end: "22:00" }, // optional
      },
    },
  },
}
```

See [Heartbeat](https://docs.openclaw.ai/gateway/heartbeat) for full configuration.

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cron-precise-scheduling)  Cron: Precise Scheduling

Cron jobs run at precise times and can run in isolated sessions without affecting main context.
Recurring top-of-hour schedules are automatically spread by a deterministic
per-job offset in a 0-5 minute window.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#when-to-use-cron)  When to use cron

- **Exact timing required**: “Send this at 9:00 AM every Monday” (not “sometime around 9”).
- **Standalone tasks**: Tasks that don’t need conversational context.
- **Different model/thinking**: Heavy analysis that warrants a more powerful model.
- **One-shot reminders**: “Remind me in 20 minutes” with `--at`.
- **Noisy/frequent tasks**: Tasks that would clutter main session history.
- **External triggers**: Tasks that should run independently of whether the agent is otherwise active.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cron-advantages)  Cron advantages

- **Precise timing**: 5-field or 6-field (seconds) cron expressions with timezone support.
- **Built-in load spreading**: recurring top-of-hour schedules are staggered by up to 5 minutes by default.
- **Per-job control**: override stagger with `--stagger <duration>` or force exact timing with `--exact`.
- **Session isolation**: Runs in `cron:<jobId>` without polluting main history.
- **Model overrides**: Use a cheaper or more powerful model per job.
- **Delivery control**: Isolated jobs default to `announce` (summary); choose `none` as needed.
- **Immediate delivery**: Announce mode posts directly without waiting for heartbeat.
- **No agent context needed**: Runs even if main session is idle or compacted.
- **One-shot support**: `--at` for precise future timestamps.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cron-example-daily-morning-briefing)  Cron example: Daily morning briefing

Copy

```
openclaw cron add \
  --name "Morning briefing" \
  --cron "0 7 * * *" \
  --tz "America/New_York" \
  --session isolated \
  --message "Generate today's briefing: weather, calendar, top emails, news summary." \
  --model opus \
  --announce \
  --channel whatsapp \
  --to "+15551234567"
```

This runs at exactly 7:00 AM New York time, uses Opus for quality, and announces a summary directly to WhatsApp.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cron-example-one-shot-reminder)  Cron example: One-shot reminder

Copy

```
openclaw cron add \
  --name "Meeting reminder" \
  --at "20m" \
  --session main \
  --system-event "Reminder: standup meeting starts in 10 minutes." \
  --wake now \
  --delete-after-run
```

See [Cron jobs](https://docs.openclaw.ai/automation/cron-jobs) for full CLI reference.

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#decision-flowchart)  Decision Flowchart

Copy

```
Does the task need to run at an EXACT time?
  YES -> Use cron
  NO  -> Continue...

Does the task need isolation from main session?
  YES -> Use cron (isolated)
  NO  -> Continue...

Can this task be batched with other periodic checks?
  YES -> Use heartbeat (add to HEARTBEAT.md)
  NO  -> Use cron

Is this a one-shot reminder?
  YES -> Use cron with --at
  NO  -> Continue...

Does it need a different model or thinking level?
  YES -> Use cron (isolated) with --model/--thinking
  NO  -> Use heartbeat
```

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#combining-both)  Combining Both

The most efficient setup uses **both**:

1. **Heartbeat** handles routine monitoring (inbox, calendar, notifications) in one batched turn every 30 minutes.
2. **Cron** handles precise schedules (daily reports, weekly reviews) and one-shot reminders.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#example-efficient-automation-setup)  Example: Efficient automation setup

**HEARTBEAT.md** (checked every 30 min):

Copy

```
# Heartbeat checklist

- Scan inbox for urgent emails
- Check calendar for events in next 2h
- Review any pending tasks
- Light check-in if quiet for 8+ hours
```

**Cron jobs** (precise timing):

Copy

```
# Daily morning briefing at 7am
openclaw cron add --name "Morning brief" --cron "0 7 * * *" --session isolated --message "..." --announce

# Weekly project review on Mondays at 9am
openclaw cron add --name "Weekly review" --cron "0 9 * * 1" --session isolated --message "..." --model opus

# One-shot reminder
openclaw cron add --name "Call back" --at "2h" --session main --system-event "Call back the client" --wake now
```

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#lobster-deterministic-workflows-with-approvals)  Lobster: Deterministic workflows with approvals

Lobster is the workflow runtime for **multi-step tool pipelines** that need deterministic execution and explicit approvals.
Use it when the task is more than a single agent turn, and you want a resumable workflow with human checkpoints.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#when-lobster-fits)  When Lobster fits

- **Multi-step automation**: You need a fixed pipeline of tool calls, not a one-off prompt.
- **Approval gates**: Side effects should pause until you approve, then resume.
- **Resumable runs**: Continue a paused workflow without re-running earlier steps.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#how-it-pairs-with-heartbeat-and-cron)  How it pairs with heartbeat and cron

- **Heartbeat/cron** decide _when_ a run happens.
- **Lobster** defines _what steps_ happen once the run starts.

For scheduled workflows, use cron or heartbeat to trigger an agent turn that calls Lobster.
For ad-hoc workflows, call Lobster directly.

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#operational-notes-from-the-code)  Operational notes (from the code)

- Lobster runs as a **local subprocess** (`lobster` CLI) in tool mode and returns a **JSON envelope**.
- If the tool returns `needs_approval`, you resume with a `resumeToken` and `approve` flag.
- The tool is an **optional plugin**; enable it additively via `tools.alsoAllow: ["lobster"]` (recommended).
- Lobster expects the `lobster` CLI to be available on `PATH`.

See [Lobster](https://docs.openclaw.ai/tools/lobster) for full usage and examples.

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#main-session-vs-isolated-session)  Main Session vs Isolated Session

Both heartbeat and cron can interact with the main session, but differently:

|  | Heartbeat | Cron (main) | Cron (isolated) |
| --- | --- | --- | --- |
| Session | Main | Main (via system event) | `cron:<jobId>` |
| History | Shared | Shared | Fresh each run |
| Context | Full | Full | None (starts clean) |
| Model | Main session model | Main session model | Can override |
| Output | Delivered if not `HEARTBEAT_OK` | Heartbeat prompt + event | Announce summary (default) |

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#when-to-use-main-session-cron)  When to use main session cron

Use `--session main` with `--system-event` when you want:

- The reminder/event to appear in main session context
- The agent to handle it during the next heartbeat with full context
- No separate isolated run

Copy

```
openclaw cron add \
  --name "Check project" \
  --every "4h" \
  --session main \
  --system-event "Time for a project health check" \
  --wake now
```

### [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#when-to-use-isolated-cron)  When to use isolated cron

Use `--session isolated` when you want:

- A clean slate without prior context
- Different model or thinking settings
- Announce summaries directly to a channel
- History that doesn’t clutter main session

Copy

```
openclaw cron add \
  --name "Deep analysis" \
  --cron "0 6 * * 0" \
  --session isolated \
  --message "Weekly codebase analysis..." \
  --model opus \
  --thinking high \
  --announce
```

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#cost-considerations)  Cost Considerations

| Mechanism | Cost Profile |
| --- | --- |
| Heartbeat | One turn every N minutes; scales with HEARTBEAT.md size |
| Cron (main) | Adds event to next heartbeat (no isolated turn) |
| Cron (isolated) | Full agent turn per job; can use cheaper model |

**Tips**:

- Keep `HEARTBEAT.md` small to minimize token overhead.
- Batch similar checks into heartbeat instead of multiple cron jobs.
- Use `target: "none"` on heartbeat if you only want internal processing.
- Use isolated cron with a cheaper model for routine tasks.

## [​](https://docs.openclaw.ai/automation/cron-vs-heartbeat\#related)  Related

- [Heartbeat](https://docs.openclaw.ai/gateway/heartbeat) \- full heartbeat configuration
- [Cron jobs](https://docs.openclaw.ai/automation/cron-jobs) \- full cron CLI and API reference
- [System](https://docs.openclaw.ai/cli/system) \- system events + heartbeat controls

[Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs) [Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)

Ctrl+I

---

---

<!-- Page: Hooks -->

## Hooks

**Source:** https://docs.openclaw.ai/automation/hooks

[Skip to main content](https://docs.openclaw.ai/automation/hooks#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Hooks

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Hooks](https://docs.openclaw.ai/automation/hooks#hooks)
- [Getting Oriented](https://docs.openclaw.ai/automation/hooks#getting-oriented)
- [Overview](https://docs.openclaw.ai/automation/hooks#overview)
- [Getting Started](https://docs.openclaw.ai/automation/hooks#getting-started)
- [Bundled Hooks](https://docs.openclaw.ai/automation/hooks#bundled-hooks)
- [Onboarding](https://docs.openclaw.ai/automation/hooks#onboarding)
- [Hook Discovery](https://docs.openclaw.ai/automation/hooks#hook-discovery)
- [Hook Packs (npm/archives)](https://docs.openclaw.ai/automation/hooks#hook-packs-npm%2Farchives)
- [Hook Structure](https://docs.openclaw.ai/automation/hooks#hook-structure)
- [HOOK.md Format](https://docs.openclaw.ai/automation/hooks#hook-md-format)
- [Metadata Fields](https://docs.openclaw.ai/automation/hooks#metadata-fields)
- [Handler Implementation](https://docs.openclaw.ai/automation/hooks#handler-implementation)
- [Event Context](https://docs.openclaw.ai/automation/hooks#event-context)
- [Event Types](https://docs.openclaw.ai/automation/hooks#event-types)
- [Command Events](https://docs.openclaw.ai/automation/hooks#command-events)
- [Agent Events](https://docs.openclaw.ai/automation/hooks#agent-events)
- [Gateway Events](https://docs.openclaw.ai/automation/hooks#gateway-events)
- [Message Events](https://docs.openclaw.ai/automation/hooks#message-events)
- [Message Event Context](https://docs.openclaw.ai/automation/hooks#message-event-context)
- [Example: Message Logger Hook](https://docs.openclaw.ai/automation/hooks#example-message-logger-hook)
- [Tool Result Hooks (Plugin API)](https://docs.openclaw.ai/automation/hooks#tool-result-hooks-plugin-api)
- [Future Events](https://docs.openclaw.ai/automation/hooks#future-events)
- [Creating Custom Hooks](https://docs.openclaw.ai/automation/hooks#creating-custom-hooks)
- [1\. Choose Location](https://docs.openclaw.ai/automation/hooks#1-choose-location)
- [2\. Create Directory Structure](https://docs.openclaw.ai/automation/hooks#2-create-directory-structure)
- [3\. Create HOOK.md](https://docs.openclaw.ai/automation/hooks#3-create-hook-md)
- [4\. Create handler.ts](https://docs.openclaw.ai/automation/hooks#4-create-handler-ts)
- [5\. Enable and Test](https://docs.openclaw.ai/automation/hooks#5-enable-and-test)
- [Configuration](https://docs.openclaw.ai/automation/hooks#configuration)
- [New Config Format (Recommended)](https://docs.openclaw.ai/automation/hooks#new-config-format-recommended)
- [Per-Hook Configuration](https://docs.openclaw.ai/automation/hooks#per-hook-configuration)
- [Extra Directories](https://docs.openclaw.ai/automation/hooks#extra-directories)
- [Legacy Config Format (Still Supported)](https://docs.openclaw.ai/automation/hooks#legacy-config-format-still-supported)
- [CLI Commands](https://docs.openclaw.ai/automation/hooks#cli-commands)
- [List Hooks](https://docs.openclaw.ai/automation/hooks#list-hooks)
- [Hook Information](https://docs.openclaw.ai/automation/hooks#hook-information)
- [Check Eligibility](https://docs.openclaw.ai/automation/hooks#check-eligibility)
- [Enable/Disable](https://docs.openclaw.ai/automation/hooks#enable%2Fdisable)
- [Bundled hook reference](https://docs.openclaw.ai/automation/hooks#bundled-hook-reference)
- [session-memory](https://docs.openclaw.ai/automation/hooks#session-memory)
- [bootstrap-extra-files](https://docs.openclaw.ai/automation/hooks#bootstrap-extra-files)
- [command-logger](https://docs.openclaw.ai/automation/hooks#command-logger)
- [boot-md](https://docs.openclaw.ai/automation/hooks#boot-md)
- [Best Practices](https://docs.openclaw.ai/automation/hooks#best-practices)
- [Keep Handlers Fast](https://docs.openclaw.ai/automation/hooks#keep-handlers-fast)
- [Handle Errors Gracefully](https://docs.openclaw.ai/automation/hooks#handle-errors-gracefully)
- [Filter Events Early](https://docs.openclaw.ai/automation/hooks#filter-events-early)
- [Use Specific Event Keys](https://docs.openclaw.ai/automation/hooks#use-specific-event-keys)
- [Debugging](https://docs.openclaw.ai/automation/hooks#debugging)
- [Enable Hook Logging](https://docs.openclaw.ai/automation/hooks#enable-hook-logging)
- [Check Discovery](https://docs.openclaw.ai/automation/hooks#check-discovery)
- [Check Registration](https://docs.openclaw.ai/automation/hooks#check-registration)
- [Verify Eligibility](https://docs.openclaw.ai/automation/hooks#verify-eligibility)
- [Testing](https://docs.openclaw.ai/automation/hooks#testing)
- [Gateway Logs](https://docs.openclaw.ai/automation/hooks#gateway-logs)
- [Test Hooks Directly](https://docs.openclaw.ai/automation/hooks#test-hooks-directly)
- [Architecture](https://docs.openclaw.ai/automation/hooks#architecture)
- [Core Components](https://docs.openclaw.ai/automation/hooks#core-components)
- [Discovery Flow](https://docs.openclaw.ai/automation/hooks#discovery-flow)
- [Event Flow](https://docs.openclaw.ai/automation/hooks#event-flow)
- [Troubleshooting](https://docs.openclaw.ai/automation/hooks#troubleshooting)
- [Hook Not Discovered](https://docs.openclaw.ai/automation/hooks#hook-not-discovered)
- [Hook Not Eligible](https://docs.openclaw.ai/automation/hooks#hook-not-eligible)
- [Hook Not Executing](https://docs.openclaw.ai/automation/hooks#hook-not-executing)
- [Handler Errors](https://docs.openclaw.ai/automation/hooks#handler-errors)
- [Migration Guide](https://docs.openclaw.ai/automation/hooks#migration-guide)
- [From Legacy Config to Discovery](https://docs.openclaw.ai/automation/hooks#from-legacy-config-to-discovery)
- [See Also](https://docs.openclaw.ai/automation/hooks#see-also)

# [​](https://docs.openclaw.ai/automation/hooks\#hooks)  Hooks

Hooks provide an extensible event-driven system for automating actions in response to agent commands and events. Hooks are automatically discovered from directories and can be managed via CLI commands, similar to how skills work in OpenClaw.

## [​](https://docs.openclaw.ai/automation/hooks\#getting-oriented)  Getting Oriented

Hooks are small scripts that run when something happens. There are two kinds:

- **Hooks** (this page): run inside the Gateway when agent events fire, like `/new`, `/reset`, `/stop`, or lifecycle events.
- **Webhooks**: external HTTP webhooks that let other systems trigger work in OpenClaw. See [Webhook Hooks](https://docs.openclaw.ai/automation/webhook) or use `openclaw webhooks` for Gmail helper commands.

Hooks can also be bundled inside plugins; see [Plugins](https://docs.openclaw.ai/tools/plugin#plugin-hooks).Common uses:

- Save a memory snapshot when you reset a session
- Keep an audit trail of commands for troubleshooting or compliance
- Trigger follow-up automation when a session starts or ends
- Write files into the agent workspace or call external APIs when events fire

If you can write a small TypeScript function, you can write a hook. Hooks are discovered automatically, and you enable or disable them via the CLI.

## [​](https://docs.openclaw.ai/automation/hooks\#overview)  Overview

The hooks system allows you to:

- Save session context to memory when `/new` is issued
- Log all commands for auditing
- Trigger custom automations on agent lifecycle events
- Extend OpenClaw’s behavior without modifying core code

## [​](https://docs.openclaw.ai/automation/hooks\#getting-started)  Getting Started

### [​](https://docs.openclaw.ai/automation/hooks\#bundled-hooks)  Bundled Hooks

OpenClaw ships with four bundled hooks that are automatically discovered:

- **💾 session-memory**: Saves session context to your agent workspace (default `~/.openclaw/workspace/memory/`) when you issue `/new`
- **📎 bootstrap-extra-files**: Injects additional workspace bootstrap files from configured glob/path patterns during `agent:bootstrap`
- **📝 command-logger**: Logs all command events to `~/.openclaw/logs/commands.log`
- **🚀 boot-md**: Runs `BOOT.md` when the gateway starts (requires internal hooks enabled)

List available hooks:

Copy

```
openclaw hooks list
```

Enable a hook:

Copy

```
openclaw hooks enable session-memory
```

Check hook status:

Copy

```
openclaw hooks check
```

Get detailed information:

Copy

```
openclaw hooks info session-memory
```

### [​](https://docs.openclaw.ai/automation/hooks\#onboarding)  Onboarding

During onboarding (`openclaw onboard`), you’ll be prompted to enable recommended hooks. The wizard automatically discovers eligible hooks and presents them for selection.

## [​](https://docs.openclaw.ai/automation/hooks\#hook-discovery)  Hook Discovery

Hooks are automatically discovered from three directories (in order of precedence):

1. **Workspace hooks**: `<workspace>/hooks/` (per-agent, highest precedence)
2. **Managed hooks**: `~/.openclaw/hooks/` (user-installed, shared across workspaces)
3. **Bundled hooks**: `<openclaw>/dist/hooks/bundled/` (shipped with OpenClaw)

Managed hook directories can be either a **single hook** or a **hook pack** (package directory).Each hook is a directory containing:

Copy

```
my-hook/
├── HOOK.md          # Metadata + documentation
└── handler.ts       # Handler implementation
```

## [​](https://docs.openclaw.ai/automation/hooks\#hook-packs-npm/archives)  Hook Packs (npm/archives)

Hook packs are standard npm packages that export one or more hooks via `openclaw.hooks` in
`package.json`. Install them with:

Copy

```
openclaw hooks install <path-or-spec>
```

Npm specs are registry-only (package name + optional version/tag). Git/URL/file specs are rejected.Example `package.json`:

Copy

```
{
  "name": "@acme/my-hooks",
  "version": "0.1.0",
  "openclaw": {
    "hooks": ["./hooks/my-hook", "./hooks/other-hook"]
  }
}
```

Each entry points to a hook directory containing `HOOK.md` and `handler.ts` (or `index.ts`).
Hook packs can ship dependencies; they will be installed under `~/.openclaw/hooks/<id>`.
Each `openclaw.hooks` entry must stay inside the package directory after symlink
resolution; entries that escape are rejected.Security note: `openclaw hooks install` installs dependencies with `npm install --ignore-scripts`
(no lifecycle scripts). Keep hook pack dependency trees “pure JS/TS” and avoid packages that rely
on `postinstall` builds.

## [​](https://docs.openclaw.ai/automation/hooks\#hook-structure)  Hook Structure

### [​](https://docs.openclaw.ai/automation/hooks\#hook-md-format)  HOOK.md Format

The `HOOK.md` file contains metadata in YAML frontmatter plus Markdown documentation:

Copy

```
---
name: my-hook
description: "Short description of what this hook does"
homepage: https://docs.openclaw.ai/automation/hooks#my-hook
metadata:
  { "openclaw": { "emoji": "🔗", "events": ["command:new"], "requires": { "bins": ["node"] } } }
---

# My Hook

Detailed documentation goes here...

## What It Does

- Listens for `/new` commands
- Performs some action
- Logs the result

## Requirements

- Node.js must be installed

## Configuration

No configuration needed.
```

### [​](https://docs.openclaw.ai/automation/hooks\#metadata-fields)  Metadata Fields

The `metadata.openclaw` object supports:

- **`emoji`**: Display emoji for CLI (e.g., `"💾"`)
- **`events`**: Array of events to listen for (e.g., `["command:new", "command:reset"]`)
- **`export`**: Named export to use (defaults to `"default"`)
- **`homepage`**: Documentation URL
- **`requires`**: Optional requirements

  - **`bins`**: Required binaries on PATH (e.g., `["git", "node"]`)
  - **`anyBins`**: At least one of these binaries must be present
  - **`env`**: Required environment variables
  - **`config`**: Required config paths (e.g., `["workspace.dir"]`)
  - **`os`**: Required platforms (e.g., `["darwin", "linux"]`)
- **`always`**: Bypass eligibility checks (boolean)
- **`install`**: Installation methods (for bundled hooks: `[{"id":"bundled","kind":"bundled"}]`)

### [​](https://docs.openclaw.ai/automation/hooks\#handler-implementation)  Handler Implementation

The `handler.ts` file exports a `HookHandler` function:

Copy

```
const myHandler = async (event) => {
  // Only trigger on 'new' command
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  console.log(`[my-hook] New command triggered`);
  console.log(`  Session: ${event.sessionKey}`);
  console.log(`  Timestamp: ${event.timestamp.toISOString()}`);

  // Your custom logic here

  // Optionally send message to user
  event.messages.push("✨ My hook executed!");
};

export default myHandler;
```

#### [​](https://docs.openclaw.ai/automation/hooks\#event-context)  Event Context

Each event includes:

Copy

```
{
  type: 'command' | 'session' | 'agent' | 'gateway' | 'message',
  action: string,              // e.g., 'new', 'reset', 'stop', 'received', 'sent'
  sessionKey: string,          // Session identifier
  timestamp: Date,             // When the event occurred
  messages: string[],          // Push messages here to send to user
  context: {
    // Command events:
    sessionEntry?: SessionEntry,
    sessionId?: string,
    sessionFile?: string,
    commandSource?: string,    // e.g., 'whatsapp', 'telegram'
    senderId?: string,
    workspaceDir?: string,
    bootstrapFiles?: WorkspaceBootstrapFile[],
    cfg?: OpenClawConfig,
    // Message events (see Message Events section for full details):
    from?: string,             // message:received
    to?: string,               // message:sent
    content?: string,
    channelId?: string,
    success?: boolean,         // message:sent
  }
}
```

## [​](https://docs.openclaw.ai/automation/hooks\#event-types)  Event Types

### [​](https://docs.openclaw.ai/automation/hooks\#command-events)  Command Events

Triggered when agent commands are issued:

- **`command`**: All command events (general listener)
- **`command:new`**: When `/new` command is issued
- **`command:reset`**: When `/reset` command is issued
- **`command:stop`**: When `/stop` command is issued

### [​](https://docs.openclaw.ai/automation/hooks\#agent-events)  Agent Events

- **`agent:bootstrap`**: Before workspace bootstrap files are injected (hooks may mutate `context.bootstrapFiles`)

### [​](https://docs.openclaw.ai/automation/hooks\#gateway-events)  Gateway Events

Triggered when the gateway starts:

- **`gateway:startup`**: After channels start and hooks are loaded

### [​](https://docs.openclaw.ai/automation/hooks\#message-events)  Message Events

Triggered when messages are received or sent:

- **`message`**: All message events (general listener)
- **`message:received`**: When an inbound message is received from any channel
- **`message:sent`**: When an outbound message is successfully sent

#### [​](https://docs.openclaw.ai/automation/hooks\#message-event-context)  Message Event Context

Message events include rich context about the message:

Copy

```
// message:received context
{
  from: string,           // Sender identifier (phone number, user ID, etc.)
  content: string,        // Message content
  timestamp?: number,     // Unix timestamp when received
  channelId: string,      // Channel (e.g., "whatsapp", "telegram", "discord")
  accountId?: string,     // Provider account ID for multi-account setups
  conversationId?: string, // Chat/conversation ID
  messageId?: string,     // Message ID from the provider
  metadata?: {            // Additional provider-specific data
    to?: string,
    provider?: string,
    surface?: string,
    threadId?: string,
    senderId?: string,
    senderName?: string,
    senderUsername?: string,
    senderE164?: string,
  }
}

// message:sent context
{
  to: string,             // Recipient identifier
  content: string,        // Message content that was sent
  success: boolean,       // Whether the send succeeded
  error?: string,         // Error message if sending failed
  channelId: string,      // Channel (e.g., "whatsapp", "telegram", "discord")
  accountId?: string,     // Provider account ID
  conversationId?: string, // Chat/conversation ID
  messageId?: string,     // Message ID returned by the provider
}
```

#### [​](https://docs.openclaw.ai/automation/hooks\#example-message-logger-hook)  Example: Message Logger Hook

Copy

```
const isMessageReceivedEvent = (event: { type: string; action: string }) =>
  event.type === "message" && event.action === "received";
const isMessageSentEvent = (event: { type: string; action: string }) =>
  event.type === "message" && event.action === "sent";

const handler = async (event) => {
  if (isMessageReceivedEvent(event as { type: string; action: string })) {
    console.log(`[message-logger] Received from ${event.context.from}: ${event.context.content}`);
  } else if (isMessageSentEvent(event as { type: string; action: string })) {
    console.log(`[message-logger] Sent to ${event.context.to}: ${event.context.content}`);
  }
};

export default handler;
```

### [​](https://docs.openclaw.ai/automation/hooks\#tool-result-hooks-plugin-api)  Tool Result Hooks (Plugin API)

These hooks are not event-stream listeners; they let plugins synchronously adjust tool results before OpenClaw persists them.

- **`tool_result_persist`**: transform tool results before they are written to the session transcript. Must be synchronous; return the updated tool result payload or `undefined` to keep it as-is. See [Agent Loop](https://docs.openclaw.ai/concepts/agent-loop).

### [​](https://docs.openclaw.ai/automation/hooks\#future-events)  Future Events

Planned event types:

- **`session:start`**: When a new session begins
- **`session:end`**: When a session ends
- **`agent:error`**: When an agent encounters an error

## [​](https://docs.openclaw.ai/automation/hooks\#creating-custom-hooks)  Creating Custom Hooks

### [​](https://docs.openclaw.ai/automation/hooks\#1-choose-location)  1\. Choose Location

- **Workspace hooks** (`<workspace>/hooks/`): Per-agent, highest precedence
- **Managed hooks** (`~/.openclaw/hooks/`): Shared across workspaces

### [​](https://docs.openclaw.ai/automation/hooks\#2-create-directory-structure)  2\. Create Directory Structure

Copy

```
mkdir -p ~/.openclaw/hooks/my-hook
cd ~/.openclaw/hooks/my-hook
```

### [​](https://docs.openclaw.ai/automation/hooks\#3-create-hook-md)  3\. Create HOOK.md

Copy

```
---
name: my-hook
description: "Does something useful"
metadata: { "openclaw": { "emoji": "🎯", "events": ["command:new"] } }
---

# My Custom Hook

This hook does something useful when you issue `/new`.
```

### [​](https://docs.openclaw.ai/automation/hooks\#4-create-handler-ts)  4\. Create handler.ts

Copy

```
const handler = async (event) => {
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  console.log("[my-hook] Running!");
  // Your logic here
};

export default handler;
```

### [​](https://docs.openclaw.ai/automation/hooks\#5-enable-and-test)  5\. Enable and Test

Copy

```
# Verify hook is discovered
openclaw hooks list

# Enable it
openclaw hooks enable my-hook

# Restart your gateway process (menu bar app restart on macOS, or restart your dev process)

# Trigger the event
# Send /new via your messaging channel
```

## [​](https://docs.openclaw.ai/automation/hooks\#configuration)  Configuration

### [​](https://docs.openclaw.ai/automation/hooks\#new-config-format-recommended)  New Config Format (Recommended)

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "session-memory": { "enabled": true },
        "command-logger": { "enabled": false }
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks\#per-hook-configuration)  Per-Hook Configuration

Hooks can have custom configuration:

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "my-hook": {
          "enabled": true,
          "env": {
            "MY_CUSTOM_VAR": "value"
          }
        }
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks\#extra-directories)  Extra Directories

Load hooks from additional directories:

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "load": {
        "extraDirs": ["/path/to/more/hooks"]
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks\#legacy-config-format-still-supported)  Legacy Config Format (Still Supported)

The old config format still works for backwards compatibility:

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "handlers": [\
        {\
          "event": "command:new",\
          "module": "./hooks/handlers/my-handler.ts",\
          "export": "default"\
        }\
      ]
    }
  }
}
```

Note: `module` must be a workspace-relative path. Absolute paths and traversal outside the workspace are rejected.**Migration**: Use the new discovery-based system for new hooks. Legacy handlers are loaded after directory-based hooks.

## [​](https://docs.openclaw.ai/automation/hooks\#cli-commands)  CLI Commands

### [​](https://docs.openclaw.ai/automation/hooks\#list-hooks)  List Hooks

Copy

```
# List all hooks
openclaw hooks list

# Show only eligible hooks
openclaw hooks list --eligible

# Verbose output (show missing requirements)
openclaw hooks list --verbose

# JSON output
openclaw hooks list --json
```

### [​](https://docs.openclaw.ai/automation/hooks\#hook-information)  Hook Information

Copy

```
# Show detailed info about a hook
openclaw hooks info session-memory

# JSON output
openclaw hooks info session-memory --json
```

### [​](https://docs.openclaw.ai/automation/hooks\#check-eligibility)  Check Eligibility

Copy

```
# Show eligibility summary
openclaw hooks check

# JSON output
openclaw hooks check --json
```

### [​](https://docs.openclaw.ai/automation/hooks\#enable/disable)  Enable/Disable

Copy

```
# Enable a hook
openclaw hooks enable session-memory

# Disable a hook
openclaw hooks disable command-logger
```

## [​](https://docs.openclaw.ai/automation/hooks\#bundled-hook-reference)  Bundled hook reference

### [​](https://docs.openclaw.ai/automation/hooks\#session-memory)  session-memory

Saves session context to memory when you issue `/new`.**Events**: `command:new`**Requirements**: `workspace.dir` must be configured**Output**: `<workspace>/memory/YYYY-MM-DD-slug.md` (defaults to `~/.openclaw/workspace`)**What it does**:

1. Uses the pre-reset session entry to locate the correct transcript
2. Extracts the last 15 lines of conversation
3. Uses LLM to generate a descriptive filename slug
4. Saves session metadata to a dated memory file

**Example output**:

Copy

```
# Session: 2026-01-16 14:30:00 UTC

- **Session Key**: agent:main:main
- **Session ID**: abc123def456
- **Source**: telegram
```

**Filename examples**:

- `2026-01-16-vendor-pitch.md`
- `2026-01-16-api-design.md`
- `2026-01-16-1430.md` (fallback timestamp if slug generation fails)

**Enable**:

Copy

```
openclaw hooks enable session-memory
```

### [​](https://docs.openclaw.ai/automation/hooks\#bootstrap-extra-files)  bootstrap-extra-files

Injects additional bootstrap files (for example monorepo-local `AGENTS.md` / `TOOLS.md`) during `agent:bootstrap`.**Events**: `agent:bootstrap`**Requirements**: `workspace.dir` must be configured**Output**: No files written; bootstrap context is modified in-memory only.**Config**:

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "bootstrap-extra-files": {
          "enabled": true,
          "paths": ["packages/*/AGENTS.md", "packages/*/TOOLS.md"]
        }
      }
    }
  }
}
```

**Notes**:

- Paths are resolved relative to workspace.
- Files must stay inside workspace (realpath-checked).
- Only recognized bootstrap basenames are loaded.
- Subagent allowlist is preserved (`AGENTS.md` and `TOOLS.md` only).

**Enable**:

Copy

```
openclaw hooks enable bootstrap-extra-files
```

### [​](https://docs.openclaw.ai/automation/hooks\#command-logger)  command-logger

Logs all command events to a centralized audit file.**Events**: `command`**Requirements**: None**Output**: `~/.openclaw/logs/commands.log`**What it does**:

1. Captures event details (command action, timestamp, session key, sender ID, source)
2. Appends to log file in JSONL format
3. Runs silently in the background

**Example log entries**:

Copy

```
{"timestamp":"2026-01-16T14:30:00.000Z","action":"new","sessionKey":"agent:main:main","senderId":"+1234567890","source":"telegram"}
{"timestamp":"2026-01-16T15:45:22.000Z","action":"stop","sessionKey":"agent:main:main","senderId":"user@example.com","source":"whatsapp"}
```

**View logs**:

Copy

```
# View recent commands
tail -n 20 ~/.openclaw/logs/commands.log

# Pretty-print with jq
cat ~/.openclaw/logs/commands.log | jq .

# Filter by action
grep '"action":"new"' ~/.openclaw/logs/commands.log | jq .
```

**Enable**:

Copy

```
openclaw hooks enable command-logger
```

### [​](https://docs.openclaw.ai/automation/hooks\#boot-md)  boot-md

Runs `BOOT.md` when the gateway starts (after channels start).
Internal hooks must be enabled for this to run.**Events**: `gateway:startup`**Requirements**: `workspace.dir` must be configured**What it does**:

1. Reads `BOOT.md` from your workspace
2. Runs the instructions via the agent runner
3. Sends any requested outbound messages via the message tool

**Enable**:

Copy

```
openclaw hooks enable boot-md
```

## [​](https://docs.openclaw.ai/automation/hooks\#best-practices)  Best Practices

### [​](https://docs.openclaw.ai/automation/hooks\#keep-handlers-fast)  Keep Handlers Fast

Hooks run during command processing. Keep them lightweight:

Copy

```
// ✓ Good - async work, returns immediately
const handler: HookHandler = async (event) => {
  void processInBackground(event); // Fire and forget
};

// ✗ Bad - blocks command processing
const handler: HookHandler = async (event) => {
  await slowDatabaseQuery(event);
  await evenSlowerAPICall(event);
};
```

### [​](https://docs.openclaw.ai/automation/hooks\#handle-errors-gracefully)  Handle Errors Gracefully

Always wrap risky operations:

Copy

```
const handler: HookHandler = async (event) => {
  try {
    await riskyOperation(event);
  } catch (err) {
    console.error("[my-handler] Failed:", err instanceof Error ? err.message : String(err));
    // Don't throw - let other handlers run
  }
};
```

### [​](https://docs.openclaw.ai/automation/hooks\#filter-events-early)  Filter Events Early

Return early if the event isn’t relevant:

Copy

```
const handler: HookHandler = async (event) => {
  // Only handle 'new' commands
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  // Your logic here
};
```

### [​](https://docs.openclaw.ai/automation/hooks\#use-specific-event-keys)  Use Specific Event Keys

Specify exact events in metadata when possible:

Copy

```
metadata: { "openclaw": { "events": ["command:new"] } } # Specific
```

Rather than:

Copy

```
metadata: { "openclaw": { "events": ["command"] } } # General - more overhead
```

## [​](https://docs.openclaw.ai/automation/hooks\#debugging)  Debugging

### [​](https://docs.openclaw.ai/automation/hooks\#enable-hook-logging)  Enable Hook Logging

The gateway logs hook loading at startup:

Copy

```
Registered hook: session-memory -> command:new
Registered hook: bootstrap-extra-files -> agent:bootstrap
Registered hook: command-logger -> command
Registered hook: boot-md -> gateway:startup
```

### [​](https://docs.openclaw.ai/automation/hooks\#check-discovery)  Check Discovery

List all discovered hooks:

Copy

```
openclaw hooks list --verbose
```

### [​](https://docs.openclaw.ai/automation/hooks\#check-registration)  Check Registration

In your handler, log when it’s called:

Copy

```
const handler: HookHandler = async (event) => {
  console.log("[my-handler] Triggered:", event.type, event.action);
  // Your logic
};
```

### [​](https://docs.openclaw.ai/automation/hooks\#verify-eligibility)  Verify Eligibility

Check why a hook isn’t eligible:

Copy

```
openclaw hooks info my-hook
```

Look for missing requirements in the output.

## [​](https://docs.openclaw.ai/automation/hooks\#testing)  Testing

### [​](https://docs.openclaw.ai/automation/hooks\#gateway-logs)  Gateway Logs

Monitor gateway logs to see hook execution:

Copy

```
# macOS
./scripts/clawlog.sh -f

# Other platforms
tail -f ~/.openclaw/gateway.log
```

### [​](https://docs.openclaw.ai/automation/hooks\#test-hooks-directly)  Test Hooks Directly

Test your handlers in isolation:

Copy

```
import { test } from "vitest";
import myHandler from "./hooks/my-hook/handler.js";

test("my handler works", async () => {
  const event = {
    type: "command",
    action: "new",
    sessionKey: "test-session",
    timestamp: new Date(),
    messages: [],
    context: { foo: "bar" },
  };

  await myHandler(event);

  // Assert side effects
});
```

## [​](https://docs.openclaw.ai/automation/hooks\#architecture)  Architecture

### [​](https://docs.openclaw.ai/automation/hooks\#core-components)  Core Components

- **`src/hooks/types.ts`**: Type definitions
- **`src/hooks/workspace.ts`**: Directory scanning and loading
- **`src/hooks/frontmatter.ts`**: HOOK.md metadata parsing
- **`src/hooks/config.ts`**: Eligibility checking
- **`src/hooks/hooks-status.ts`**: Status reporting
- **`src/hooks/loader.ts`**: Dynamic module loader
- **`src/cli/hooks-cli.ts`**: CLI commands
- **`src/gateway/server-startup.ts`**: Loads hooks at gateway start
- **`src/auto-reply/reply/commands-core.ts`**: Triggers command events

### [​](https://docs.openclaw.ai/automation/hooks\#discovery-flow)  Discovery Flow

Copy

```
Gateway startup
    ↓
Scan directories (workspace → managed → bundled)
    ↓
Parse HOOK.md files
    ↓
Check eligibility (bins, env, config, os)
    ↓
Load handlers from eligible hooks
    ↓
Register handlers for events
```

### [​](https://docs.openclaw.ai/automation/hooks\#event-flow)  Event Flow

Copy

```
User sends /new
    ↓
Command validation
    ↓
Create hook event
    ↓
Trigger hook (all registered handlers)
    ↓
Command processing continues
    ↓
Session reset
```

## [​](https://docs.openclaw.ai/automation/hooks\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/automation/hooks\#hook-not-discovered)  Hook Not Discovered

1. Check directory structure:






Copy











```
ls -la ~/.openclaw/hooks/my-hook/
# Should show: HOOK.md, handler.ts
```

2. Verify HOOK.md format:






Copy











```
cat ~/.openclaw/hooks/my-hook/HOOK.md
# Should have YAML frontmatter with name and metadata
```

3. List all discovered hooks:






Copy











```
openclaw hooks list
```


### [​](https://docs.openclaw.ai/automation/hooks\#hook-not-eligible)  Hook Not Eligible

Check requirements:

Copy

```
openclaw hooks info my-hook
```

Look for missing:

- Binaries (check PATH)
- Environment variables
- Config values
- OS compatibility

### [​](https://docs.openclaw.ai/automation/hooks\#hook-not-executing)  Hook Not Executing

1. Verify hook is enabled:






Copy











```
openclaw hooks list
# Should show ✓ next to enabled hooks
```

2. Restart your gateway process so hooks reload.
3. Check gateway logs for errors:






Copy











```
./scripts/clawlog.sh | grep hook
```


### [​](https://docs.openclaw.ai/automation/hooks\#handler-errors)  Handler Errors

Check for TypeScript/import errors:

Copy

```
# Test import directly
node -e "import('./path/to/handler.ts').then(console.log)"
```

## [​](https://docs.openclaw.ai/automation/hooks\#migration-guide)  Migration Guide

### [​](https://docs.openclaw.ai/automation/hooks\#from-legacy-config-to-discovery)  From Legacy Config to Discovery

**Before**:

Copy

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "handlers": [\
        {\
          "event": "command:new",\
          "module": "./hooks/handlers/my-handler.ts"\
        }\
      ]
    }
  }
}
```

**After**:

1. Create hook directory:






Copy











```
mkdir -p ~/.openclaw/hooks/my-hook
mv ./hooks/handlers/my-handler.ts ~/.openclaw/hooks/my-hook/handler.ts
```

2. Create HOOK.md:






Copy











```
   ---
name: my-hook
description: "My custom hook"
metadata: { "openclaw": { "emoji": "🎯", "events": ["command:new"] } }
   ---

# My Hook

Does something useful.
```

3. Update config:






Copy











```
{
     "hooks": {
       "internal": {
         "enabled": true,
         "entries": {
           "my-hook": { "enabled": true }
         }
       }
     }
}
```

4. Verify and restart your gateway process:






Copy











```
openclaw hooks list
# Should show: 🎯 my-hook ✓
```


**Benefits of migration**:

- Automatic discovery
- CLI management
- Eligibility checking
- Better documentation
- Consistent structure

## [​](https://docs.openclaw.ai/automation/hooks\#see-also)  See Also

- [CLI Reference: hooks](https://docs.openclaw.ai/cli/hooks)
- [Bundled Hooks README](https://github.com/openclaw/openclaw/tree/main/src/hooks/bundled)
- [Webhook Hooks](https://docs.openclaw.ai/automation/webhook)
- [Configuration](https://docs.openclaw.ai/gateway/configuration#hooks)

[Zalo Personal Plugin](https://docs.openclaw.ai/plugins/zalouser) [Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs)

Ctrl+I

---

---

<!-- Page: Webhooks -->

## Webhooks

**Source:** https://docs.openclaw.ai/automation/webhook

[Skip to main content](https://docs.openclaw.ai/automation/webhook#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Webhooks

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Webhooks](https://docs.openclaw.ai/automation/webhook#webhooks)
- [Enable](https://docs.openclaw.ai/automation/webhook#enable)
- [Auth](https://docs.openclaw.ai/automation/webhook#auth)
- [Endpoints](https://docs.openclaw.ai/automation/webhook#endpoints)
- [POST /hooks/wake](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2Fwake)
- [POST /hooks/agent](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2Fagent)
- [Session key policy (breaking change)](https://docs.openclaw.ai/automation/webhook#session-key-policy-breaking-change)
- [POST /hooks/<name> (mapped)](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2F%3Cname%3E-mapped)
- [Responses](https://docs.openclaw.ai/automation/webhook#responses)
- [Examples](https://docs.openclaw.ai/automation/webhook#examples)
- [Use a different model](https://docs.openclaw.ai/automation/webhook#use-a-different-model)
- [Security](https://docs.openclaw.ai/automation/webhook#security)

# [​](https://docs.openclaw.ai/automation/webhook\#webhooks)  Webhooks

Gateway can expose a small HTTP webhook endpoint for external triggers.

## [​](https://docs.openclaw.ai/automation/webhook\#enable)  Enable

Copy

```
{
  hooks: {
    enabled: true,
    token: "shared-secret",
    path: "/hooks",
    // Optional: restrict explicit `agentId` routing to this allowlist.
    // Omit or include "*" to allow any agent.
    // Set [] to deny all explicit `agentId` routing.
    allowedAgentIds: ["hooks", "main"],
  },
}
```

Notes:

- `hooks.token` is required when `hooks.enabled=true`.
- `hooks.path` defaults to `/hooks`.

## [​](https://docs.openclaw.ai/automation/webhook\#auth)  Auth

Every request must include the hook token. Prefer headers:

- `Authorization: Bearer <token>` (recommended)
- `x-openclaw-token: <token>`
- Query-string tokens are rejected (`?token=...` returns `400`).

## [​](https://docs.openclaw.ai/automation/webhook\#endpoints)  Endpoints

### [​](https://docs.openclaw.ai/automation/webhook\#post-/hooks/wake)  `POST /hooks/wake`

Payload:

Copy

```
{ "text": "System line", "mode": "now" }
```

- `text` **required** (string): The description of the event (e.g., “New email received”).
- `mode` optional (`now` \| `next-heartbeat`): Whether to trigger an immediate heartbeat (default `now`) or wait for the next periodic check.

Effect:

- Enqueues a system event for the **main** session
- If `mode=now`, triggers an immediate heartbeat

### [​](https://docs.openclaw.ai/automation/webhook\#post-/hooks/agent)  `POST /hooks/agent`

Payload:

Copy

```
{
  "message": "Run this",
  "name": "Email",
  "agentId": "hooks",
  "sessionKey": "hook:email:msg-123",
  "wakeMode": "now",
  "deliver": true,
  "channel": "last",
  "to": "+15551234567",
  "model": "openai/gpt-5.2-mini",
  "thinking": "low",
  "timeoutSeconds": 120
}
```

- `message` **required** (string): The prompt or message for the agent to process.
- `name` optional (string): Human-readable name for the hook (e.g., “GitHub”), used as a prefix in session summaries.
- `agentId` optional (string): Route this hook to a specific agent. Unknown IDs fall back to the default agent. When set, the hook runs using the resolved agent’s workspace and configuration.
- `sessionKey` optional (string): The key used to identify the agent’s session. By default this field is rejected unless `hooks.allowRequestSessionKey=true`.
- `wakeMode` optional (`now` \| `next-heartbeat`): Whether to trigger an immediate heartbeat (default `now`) or wait for the next periodic check.
- `deliver` optional (boolean): If `true`, the agent’s response will be sent to the messaging channel. Defaults to `true`. Responses that are only heartbeat acknowledgments are automatically skipped.
- `channel` optional (string): The messaging channel for delivery. One of: `last`, `whatsapp`, `telegram`, `discord`, `slack`, `mattermost` (plugin), `signal`, `imessage`, `msteams`. Defaults to `last`.
- `to` optional (string): The recipient identifier for the channel (e.g., phone number for WhatsApp/Signal, chat ID for Telegram, channel ID for Discord/Slack/Mattermost (plugin), conversation ID for MS Teams). Defaults to the last recipient in the main session.
- `model` optional (string): Model override (e.g., `anthropic/claude-3-5-sonnet` or an alias). Must be in the allowed model list if restricted.
- `thinking` optional (string): Thinking level override (e.g., `low`, `medium`, `high`).
- `timeoutSeconds` optional (number): Maximum duration for the agent run in seconds.

Effect:

- Runs an **isolated** agent turn (own session key)
- Always posts a summary into the **main** session
- If `wakeMode=now`, triggers an immediate heartbeat

## [​](https://docs.openclaw.ai/automation/webhook\#session-key-policy-breaking-change)  Session key policy (breaking change)

`/hooks/agent` payload `sessionKey` overrides are disabled by default.

- Recommended: set a fixed `hooks.defaultSessionKey` and keep request overrides off.
- Optional: allow request overrides only when needed, and restrict prefixes.

Recommended config:

Copy

```
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    defaultSessionKey: "hook:ingress",
    allowRequestSessionKey: false,
    allowedSessionKeyPrefixes: ["hook:"],
  },
}
```

Compatibility config (legacy behavior):

Copy

```
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    allowRequestSessionKey: true,
    allowedSessionKeyPrefixes: ["hook:"], // strongly recommended
  },
}
```

### [​](https://docs.openclaw.ai/automation/webhook\#post-/hooks/%3Cname%3E-mapped)  `POST /hooks/<name>` (mapped)

Custom hook names are resolved via `hooks.mappings` (see configuration). A mapping can
turn arbitrary payloads into `wake` or `agent` actions, with optional templates or
code transforms.Mapping options (summary):

- `hooks.presets: ["gmail"]` enables the built-in Gmail mapping.
- `hooks.mappings` lets you define `match`, `action`, and templates in config.
- `hooks.transformsDir` \+ `transform.module` loads a JS/TS module for custom logic.

  - `hooks.transformsDir` (if set) must stay within the transforms root under your OpenClaw config directory (typically `~/.openclaw/hooks/transforms`).
  - `transform.module` must resolve within the effective transforms directory (traversal/escape paths are rejected).
- Use `match.source` to keep a generic ingest endpoint (payload-driven routing).
- TS transforms require a TS loader (e.g. `bun` or `tsx`) or precompiled `.js` at runtime.
- Set `deliver: true` \+ `channel`/`to` on mappings to route replies to a chat surface
(`channel` defaults to `last` and falls back to WhatsApp).
- `agentId` routes the hook to a specific agent; unknown IDs fall back to the default agent.
- `hooks.allowedAgentIds` restricts explicit `agentId` routing. Omit it (or include `*`) to allow any agent. Set `[]` to deny explicit `agentId` routing.
- `hooks.defaultSessionKey` sets the default session for hook agent runs when no explicit key is provided.
- `hooks.allowRequestSessionKey` controls whether `/hooks/agent` payloads may set `sessionKey` (default: `false`).
- `hooks.allowedSessionKeyPrefixes` optionally restricts explicit `sessionKey` values from request payloads and mappings.
- `allowUnsafeExternalContent: true` disables the external content safety wrapper for that hook
(dangerous; only for trusted internal sources).
- `openclaw webhooks gmail setup` writes `hooks.gmail` config for `openclaw webhooks gmail run`.
See [Gmail Pub/Sub](https://docs.openclaw.ai/automation/gmail-pubsub) for the full Gmail watch flow.

## [​](https://docs.openclaw.ai/automation/webhook\#responses)  Responses

- `200` for `/hooks/wake`
- `202` for `/hooks/agent` (async run started)
- `401` on auth failure
- `429` after repeated auth failures from the same client (check `Retry-After`)
- `400` on invalid payload
- `413` on oversized payloads

## [​](https://docs.openclaw.ai/automation/webhook\#examples)  Examples

Copy

```
curl -X POST http://127.0.0.1:18789/hooks/wake \
  -H 'Authorization: Bearer SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"text":"New email received","mode":"now"}'
```

Copy

```
curl -X POST http://127.0.0.1:18789/hooks/agent \
  -H 'x-openclaw-token: SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"message":"Summarize inbox","name":"Email","wakeMode":"next-heartbeat"}'
```

### [​](https://docs.openclaw.ai/automation/webhook\#use-a-different-model)  Use a different model

Add `model` to the agent payload (or mapping) to override the model for that run:

Copy

```
curl -X POST http://127.0.0.1:18789/hooks/agent \
  -H 'x-openclaw-token: SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"message":"Summarize inbox","name":"Email","model":"openai/gpt-5.2-mini"}'
```

If you enforce `agents.defaults.models`, make sure the override model is included there.

Copy

```
curl -X POST http://127.0.0.1:18789/hooks/gmail \
  -H 'Authorization: Bearer SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"source":"gmail","messages":[{"from":"Ada","subject":"Hello","snippet":"Hi"}]}'
```

## [​](https://docs.openclaw.ai/automation/webhook\#security)  Security

- Keep hook endpoints behind loopback, tailnet, or trusted reverse proxy.
- Use a dedicated hook token; do not reuse gateway auth tokens.
- Repeated auth failures are rate-limited per client address to slow brute-force attempts.
- If you use multi-agent routing, set `hooks.allowedAgentIds` to limit explicit `agentId` selection.
- Keep `hooks.allowRequestSessionKey=false` unless you require caller-selected sessions.
- If you enable request `sessionKey`, restrict `hooks.allowedSessionKeyPrefixes` (for example, `["hook:"]`).
- Avoid including sensitive raw payloads in webhook logs.
- Hook payloads are treated as untrusted and wrapped with safety boundaries by default.
If you must disable this for a specific hook, set `allowUnsafeExternalContent: true`
in that hook’s mapping (dangerous).

[Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting) [Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)

Ctrl+I

---

---

<!-- Page: Gmail PubSub -->

## Gmail PubSub

**Source:** https://docs.openclaw.ai/automation/gmail-pubsub

[Skip to main content](https://docs.openclaw.ai/automation/gmail-pubsub#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Gmail PubSub

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Gmail Pub/Sub -> OpenClaw](https://docs.openclaw.ai/automation/gmail-pubsub#gmail-pub%2Fsub-%3E-openclaw)
- [Prereqs](https://docs.openclaw.ai/automation/gmail-pubsub#prereqs)
- [Wizard (recommended)](https://docs.openclaw.ai/automation/gmail-pubsub#wizard-recommended)
- [One-time setup](https://docs.openclaw.ai/automation/gmail-pubsub#one-time-setup)
- [Start the watch](https://docs.openclaw.ai/automation/gmail-pubsub#start-the-watch)
- [Run the push handler](https://docs.openclaw.ai/automation/gmail-pubsub#run-the-push-handler)
- [Expose the handler (advanced, unsupported)](https://docs.openclaw.ai/automation/gmail-pubsub#expose-the-handler-advanced-unsupported)
- [Test](https://docs.openclaw.ai/automation/gmail-pubsub#test)
- [Troubleshooting](https://docs.openclaw.ai/automation/gmail-pubsub#troubleshooting)
- [Cleanup](https://docs.openclaw.ai/automation/gmail-pubsub#cleanup)

# [​](https://docs.openclaw.ai/automation/gmail-pubsub\#gmail-pub/sub-%3E-openclaw)  Gmail Pub/Sub -> OpenClaw

Goal: Gmail watch -> Pub/Sub push -> `gog gmail watch serve` -\> OpenClaw webhook.

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#prereqs)  Prereqs

- `gcloud` installed and logged in ( [install guide](https://docs.cloud.google.com/sdk/docs/install-sdk)).
- `gog` (gogcli) installed and authorized for the Gmail account ( [gogcli.sh](https://gogcli.sh/)).
- OpenClaw hooks enabled (see [Webhooks](https://docs.openclaw.ai/automation/webhook)).
- `tailscale` logged in ( [tailscale.com](https://tailscale.com/)). Supported setup uses Tailscale Funnel for the public HTTPS endpoint.
Other tunnel services can work, but are DIY/unsupported and require manual wiring.
Right now, Tailscale is what we support.

Example hook config (enable Gmail preset mapping):

Copy

```
{
  hooks: {
    enabled: true,
    token: "OPENCLAW_HOOK_TOKEN",
    path: "/hooks",
    presets: ["gmail"],
  },
}
```

To deliver the Gmail summary to a chat surface, override the preset with a mapping
that sets `deliver` \+ optional `channel`/`to`:

Copy

```
{
  hooks: {
    enabled: true,
    token: "OPENCLAW_HOOK_TOKEN",
    presets: ["gmail"],
    mappings: [\
      {\
        match: { path: "gmail" },\
        action: "agent",\
        wakeMode: "now",\
        name: "Gmail",\
        sessionKey: "hook:gmail:{{messages[0].id}}",\
        messageTemplate: "New email from {{messages[0].from}}\nSubject: {{messages[0].subject}}\n{{messages[0].snippet}}\n{{messages[0].body}}",\
        model: "openai/gpt-5.2-mini",\
        deliver: true,\
        channel: "last",\
        // to: "+15551234567"\
      },\
    ],
  },
}
```

If you want a fixed channel, set `channel` \+ `to`. Otherwise `channel: "last"`
uses the last delivery route (falls back to WhatsApp).To force a cheaper model for Gmail runs, set `model` in the mapping
(`provider/model` or alias). If you enforce `agents.defaults.models`, include it there.To set a default model and thinking level specifically for Gmail hooks, add
`hooks.gmail.model` / `hooks.gmail.thinking` in your config:

Copy

```
{
  hooks: {
    gmail: {
      model: "openrouter/meta-llama/llama-3.3-70b-instruct:free",
      thinking: "off",
    },
  },
}
```

Notes:

- Per-hook `model`/`thinking` in the mapping still overrides these defaults.
- Fallback order: `hooks.gmail.model` → `agents.defaults.model.fallbacks` → primary (auth/rate-limit/timeouts).
- If `agents.defaults.models` is set, the Gmail model must be in the allowlist.
- Gmail hook content is wrapped with external-content safety boundaries by default.
To disable (dangerous), set `hooks.gmail.allowUnsafeExternalContent: true`.

To customize payload handling further, add `hooks.mappings` or a JS/TS transform module
under `~/.openclaw/hooks/transforms` (see [Webhooks](https://docs.openclaw.ai/automation/webhook)).

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#wizard-recommended)  Wizard (recommended)

Use the OpenClaw helper to wire everything together (installs deps on macOS via brew):

Copy

```
openclaw webhooks gmail setup \
  --account openclaw@gmail.com
```

Defaults:

- Uses Tailscale Funnel for the public push endpoint.
- Writes `hooks.gmail` config for `openclaw webhooks gmail run`.
- Enables the Gmail hook preset (`hooks.presets: ["gmail"]`).

Path note: when `tailscale.mode` is enabled, OpenClaw automatically sets
`hooks.gmail.serve.path` to `/` and keeps the public path at
`hooks.gmail.tailscale.path` (default `/gmail-pubsub`) because Tailscale
strips the set-path prefix before proxying.
If you need the backend to receive the prefixed path, set
`hooks.gmail.tailscale.target` (or `--tailscale-target`) to a full URL like
`http://127.0.0.1:8788/gmail-pubsub` and match `hooks.gmail.serve.path`.Want a custom endpoint? Use `--push-endpoint <url>` or `--tailscale off`.Platform note: on macOS the wizard installs `gcloud`, `gogcli`, and `tailscale`
via Homebrew; on Linux install them manually first.Gateway auto-start (recommended):

- When `hooks.enabled=true` and `hooks.gmail.account` is set, the Gateway starts
`gog gmail watch serve` on boot and auto-renews the watch.
- Set `OPENCLAW_SKIP_GMAIL_WATCHER=1` to opt out (useful if you run the daemon yourself).
- Do not run the manual daemon at the same time, or you will hit
`listen tcp 127.0.0.1:8788: bind: address already in use`.

Manual daemon (starts `gog gmail watch serve` \+ auto-renew):

Copy

```
openclaw webhooks gmail run
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#one-time-setup)  One-time setup

1. Select the GCP project **that owns the OAuth client** used by `gog`.

Copy

```
gcloud auth login
gcloud config set project <project-id>
```

Note: Gmail watch requires the Pub/Sub topic to live in the same project as the OAuth client.

2. Enable APIs:

Copy

```
gcloud services enable gmail.googleapis.com pubsub.googleapis.com
```

3. Create a topic:

Copy

```
gcloud pubsub topics create gog-gmail-watch
```

4. Allow Gmail push to publish:

Copy

```
gcloud pubsub topics add-iam-policy-binding gog-gmail-watch \
  --member=serviceAccount:gmail-api-push@system.gserviceaccount.com \
  --role=roles/pubsub.publisher
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#start-the-watch)  Start the watch

Copy

```
gog gmail watch start \
  --account openclaw@gmail.com \
  --label INBOX \
  --topic projects/<project-id>/topics/gog-gmail-watch
```

Save the `history_id` from the output (for debugging).

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#run-the-push-handler)  Run the push handler

Local example (shared token auth):

Copy

```
gog gmail watch serve \
  --account openclaw@gmail.com \
  --bind 127.0.0.1 \
  --port 8788 \
  --path /gmail-pubsub \
  --token <shared> \
  --hook-url http://127.0.0.1:18789/hooks/gmail \
  --hook-token OPENCLAW_HOOK_TOKEN \
  --include-body \
  --max-bytes 20000
```

Notes:

- `--token` protects the push endpoint (`x-gog-token` or `?token=`).
- `--hook-url` points to OpenClaw `/hooks/gmail` (mapped; isolated run + summary to main).
- `--include-body` and `--max-bytes` control the body snippet sent to OpenClaw.

Recommended: `openclaw webhooks gmail run` wraps the same flow and auto-renews the watch.

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#expose-the-handler-advanced-unsupported)  Expose the handler (advanced, unsupported)

If you need a non-Tailscale tunnel, wire it manually and use the public URL in the push
subscription (unsupported, no guardrails):

Copy

```
cloudflared tunnel --url http://127.0.0.1:8788 --no-autoupdate
```

Use the generated URL as the push endpoint:

Copy

```
gcloud pubsub subscriptions create gog-gmail-watch-push \
  --topic gog-gmail-watch \
  --push-endpoint "https://<public-url>/gmail-pubsub?token=<shared>"
```

Production: use a stable HTTPS endpoint and configure Pub/Sub OIDC JWT, then run:

Copy

```
gog gmail watch serve --verify-oidc --oidc-email <svc@...>
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#test)  Test

Send a message to the watched inbox:

Copy

```
gog gmail send \
  --account openclaw@gmail.com \
  --to openclaw@gmail.com \
  --subject "watch test" \
  --body "ping"
```

Check watch state and history:

Copy

```
gog gmail watch status --account openclaw@gmail.com
gog gmail history --account openclaw@gmail.com --since <historyId>
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#troubleshooting)  Troubleshooting

- `Invalid topicName`: project mismatch (topic not in the OAuth client project).
- `User not authorized`: missing `roles/pubsub.publisher` on the topic.
- Empty messages: Gmail push only provides `historyId`; fetch via `gog gmail history`.

## [​](https://docs.openclaw.ai/automation/gmail-pubsub\#cleanup)  Cleanup

Copy

```
gog gmail watch stop --account openclaw@gmail.com
gcloud pubsub subscriptions delete gog-gmail-watch-push
gcloud pubsub topics delete gog-gmail-watch
```

[Webhooks](https://docs.openclaw.ai/automation/webhook) [Polls](https://docs.openclaw.ai/automation/poll)

Ctrl+I

---

---

<!-- Page: Polls -->

## Polls

**Source:** https://docs.openclaw.ai/automation/poll

[Skip to main content](https://docs.openclaw.ai/automation/poll#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Polls

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Polls](https://docs.openclaw.ai/automation/poll#polls)
- [Supported channels](https://docs.openclaw.ai/automation/poll#supported-channels)
- [CLI](https://docs.openclaw.ai/automation/poll#cli)
- [Gateway RPC](https://docs.openclaw.ai/automation/poll#gateway-rpc)
- [Channel differences](https://docs.openclaw.ai/automation/poll#channel-differences)
- [Agent tool (Message)](https://docs.openclaw.ai/automation/poll#agent-tool-message)

# [​](https://docs.openclaw.ai/automation/poll\#polls)  Polls

## [​](https://docs.openclaw.ai/automation/poll\#supported-channels)  Supported channels

- WhatsApp (web channel)
- Discord
- MS Teams (Adaptive Cards)

## [​](https://docs.openclaw.ai/automation/poll\#cli)  CLI

Copy

```
# WhatsApp
openclaw message poll --target +15555550123 \
  --poll-question "Lunch today?" --poll-option "Yes" --poll-option "No" --poll-option "Maybe"
openclaw message poll --target 123456789@g.us \
  --poll-question "Meeting time?" --poll-option "10am" --poll-option "2pm" --poll-option "4pm" --poll-multi

# Discord
openclaw message poll --channel discord --target channel:123456789 \
  --poll-question "Snack?" --poll-option "Pizza" --poll-option "Sushi"
openclaw message poll --channel discord --target channel:123456789 \
  --poll-question "Plan?" --poll-option "A" --poll-option "B" --poll-duration-hours 48

# MS Teams
openclaw message poll --channel msteams --target conversation:19:abc@thread.tacv2 \
  --poll-question "Lunch?" --poll-option "Pizza" --poll-option "Sushi"
```

Options:

- `--channel`: `whatsapp` (default), `discord`, or `msteams`
- `--poll-multi`: allow selecting multiple options
- `--poll-duration-hours`: Discord-only (defaults to 24 when omitted)

## [​](https://docs.openclaw.ai/automation/poll\#gateway-rpc)  Gateway RPC

Method: `poll`Params:

- `to` (string, required)
- `question` (string, required)
- `options` (string\[\], required)
- `maxSelections` (number, optional)
- `durationHours` (number, optional)
- `channel` (string, optional, default: `whatsapp`)
- `idempotencyKey` (string, required)

## [​](https://docs.openclaw.ai/automation/poll\#channel-differences)  Channel differences

- WhatsApp: 2-12 options, `maxSelections` must be within option count, ignores `durationHours`.
- Discord: 2-10 options, `durationHours` clamped to 1-768 hours (default 24). `maxSelections > 1` enables multi-select; Discord does not support a strict selection count.
- MS Teams: Adaptive Card polls (OpenClaw-managed). No native poll API; `durationHours` is ignored.

## [​](https://docs.openclaw.ai/automation/poll\#agent-tool-message)  Agent tool (Message)

Use the `message` tool with `poll` action (`to`, `pollQuestion`, `pollOption`, optional `pollMulti`, `pollDurationHours`, `channel`).Note: Discord has no “pick exactly N” mode; `pollMulti` maps to multi-select.
Teams polls are rendered as Adaptive Cards and require the gateway to stay online
to record votes in `~/.openclaw/msteams-polls.json`.

[Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub) [Auth Monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

Ctrl+I

---

---

<!-- Page: Auth Monitoring -->

## Auth Monitoring

**Source:** https://docs.openclaw.ai/automation/auth-monitoring

[Skip to main content](https://docs.openclaw.ai/automation/auth-monitoring#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Auth Monitoring

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Auth monitoring](https://docs.openclaw.ai/automation/auth-monitoring#auth-monitoring)
- [Preferred: CLI check (portable)](https://docs.openclaw.ai/automation/auth-monitoring#preferred-cli-check-portable)
- [Optional scripts (ops / phone workflows)](https://docs.openclaw.ai/automation/auth-monitoring#optional-scripts-ops-%2F-phone-workflows)

# [​](https://docs.openclaw.ai/automation/auth-monitoring\#auth-monitoring)  Auth monitoring

OpenClaw exposes OAuth expiry health via `openclaw models status`. Use that for
automation and alerting; scripts are optional extras for phone workflows.

## [​](https://docs.openclaw.ai/automation/auth-monitoring\#preferred-cli-check-portable)  Preferred: CLI check (portable)

Copy

```
openclaw models status --check
```

Exit codes:

- `0`: OK
- `1`: expired or missing credentials
- `2`: expiring soon (within 24h)

This works in cron/systemd and requires no extra scripts.

## [​](https://docs.openclaw.ai/automation/auth-monitoring\#optional-scripts-ops-/-phone-workflows)  Optional scripts (ops / phone workflows)

These live under `scripts/` and are **optional**. They assume SSH access to the
gateway host and are tuned for systemd + Termux.

- `scripts/claude-auth-status.sh` now uses `openclaw models status --json` as the
source of truth (falling back to direct file reads if the CLI is unavailable),
so keep `openclaw` on `PATH` for timers.
- `scripts/auth-monitor.sh`: cron/systemd timer target; sends alerts (ntfy or phone).
- `scripts/systemd/openclaw-auth-monitor.{service,timer}`: systemd user timer.
- `scripts/claude-auth-status.sh`: Claude Code + OpenClaw auth checker (full/json/simple).
- `scripts/mobile-reauth.sh`: guided re‑auth flow over SSH.
- `scripts/termux-quick-auth.sh`: one‑tap widget status + open auth URL.
- `scripts/termux-auth-widget.sh`: full guided widget flow.
- `scripts/termux-sync-widget.sh`: sync Claude Code creds → OpenClaw.

If you don’t need phone automation or systemd timers, skip these scripts.

[Polls](https://docs.openclaw.ai/automation/poll) [Nodes](https://docs.openclaw.ai/nodes)

Ctrl+I

---

---

<!-- Page: Automation Troubleshooting -->

## Automation Troubleshooting

**Source:** https://docs.openclaw.ai/automation/troubleshooting

[Skip to main content](https://docs.openclaw.ai/automation/troubleshooting#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Automation

Automation Troubleshooting

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Automation troubleshooting](https://docs.openclaw.ai/automation/troubleshooting#automation-troubleshooting)
- [Command ladder](https://docs.openclaw.ai/automation/troubleshooting#command-ladder)
- [Cron not firing](https://docs.openclaw.ai/automation/troubleshooting#cron-not-firing)
- [Cron fired but no delivery](https://docs.openclaw.ai/automation/troubleshooting#cron-fired-but-no-delivery)
- [Heartbeat suppressed or skipped](https://docs.openclaw.ai/automation/troubleshooting#heartbeat-suppressed-or-skipped)
- [Timezone and activeHours gotchas](https://docs.openclaw.ai/automation/troubleshooting#timezone-and-activehours-gotchas)

# [​](https://docs.openclaw.ai/automation/troubleshooting\#automation-troubleshooting)  Automation troubleshooting

Use this page for scheduler and delivery issues (`cron` \+ `heartbeat`).

## [​](https://docs.openclaw.ai/automation/troubleshooting\#command-ladder)  Command ladder

Copy

```
openclaw status
openclaw gateway status
openclaw logs --follow
openclaw doctor
openclaw channels status --probe
```

Then run automation checks:

Copy

```
openclaw cron status
openclaw cron list
openclaw system heartbeat last
```

## [​](https://docs.openclaw.ai/automation/troubleshooting\#cron-not-firing)  Cron not firing

Copy

```
openclaw cron status
openclaw cron list
openclaw cron runs --id <jobId> --limit 20
openclaw logs --follow
```

Good output looks like:

- `cron status` reports enabled and a future `nextWakeAtMs`.
- Job is enabled and has a valid schedule/timezone.
- `cron runs` shows `ok` or explicit skip reason.

Common signatures:

- `cron: scheduler disabled; jobs will not run automatically` → cron disabled in config/env.
- `cron: timer tick failed` → scheduler tick crashed; inspect surrounding stack/log context.
- `reason: not-due` in run output → manual run called without `--force` and job not due yet.

## [​](https://docs.openclaw.ai/automation/troubleshooting\#cron-fired-but-no-delivery)  Cron fired but no delivery

Copy

```
openclaw cron runs --id <jobId> --limit 20
openclaw cron list
openclaw channels status --probe
openclaw logs --follow
```

Good output looks like:

- Run status is `ok`.
- Delivery mode/target are set for isolated jobs.
- Channel probe reports target channel connected.

Common signatures:

- Run succeeded but delivery mode is `none` → no external message is expected.
- Delivery target missing/invalid (`channel`/`to`) → run may succeed internally but skip outbound.
- Channel auth errors (`unauthorized`, `missing_scope`, `Forbidden`) → delivery blocked by channel credentials/permissions.

## [​](https://docs.openclaw.ai/automation/troubleshooting\#heartbeat-suppressed-or-skipped)  Heartbeat suppressed or skipped

Copy

```
openclaw system heartbeat last
openclaw logs --follow
openclaw config get agents.defaults.heartbeat
openclaw channels status --probe
```

Good output looks like:

- Heartbeat enabled with non-zero interval.
- Last heartbeat result is `ran` (or skip reason is understood).

Common signatures:

- `heartbeat skipped` with `reason=quiet-hours` → outside `activeHours`.
- `requests-in-flight` → main lane busy; heartbeat deferred.
- `empty-heartbeat-file` → interval heartbeat skipped because `HEARTBEAT.md` has no actionable content and no tagged cron event is queued.
- `alerts-disabled` → visibility settings suppress outbound heartbeat messages.

## [​](https://docs.openclaw.ai/automation/troubleshooting\#timezone-and-activehours-gotchas)  Timezone and activeHours gotchas

Copy

```
openclaw config get agents.defaults.heartbeat.activeHours
openclaw config get agents.defaults.heartbeat.activeHours.timezone
openclaw config get agents.defaults.userTimezone || echo "agents.defaults.userTimezone not set"
openclaw cron list
openclaw logs --follow
```

Quick rules:

- `Config path not found: agents.defaults.userTimezone` means the key is unset; heartbeat falls back to host timezone (or `activeHours.timezone` if set).
- Cron without `--tz` uses gateway host timezone.
- Heartbeat `activeHours` uses configured timezone resolution (`user`, `local`, or explicit IANA tz).
- ISO timestamps without timezone are treated as UTC for cron `at` schedules.

Common signatures:

- Jobs run at the wrong wall-clock time after host timezone changes.
- Heartbeat always skipped during your daytime because `activeHours.timezone` is wrong.

Related:

- [/automation/cron-jobs](https://docs.openclaw.ai/automation/cron-jobs)
- [/gateway/heartbeat](https://docs.openclaw.ai/gateway/heartbeat)
- [/automation/cron-vs-heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)
- [/concepts/timezone](https://docs.openclaw.ai/concepts/timezone)

[Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat) [Webhooks](https://docs.openclaw.ai/automation/webhook)

Ctrl+I

---
