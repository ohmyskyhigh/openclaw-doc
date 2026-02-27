# Channels Overview


---

<!-- Page: Chat Channels -->

## Chat Channels

**Source:** https://docs.openclaw.ai/channels

[Skip to main content](https://docs.openclaw.ai/channels#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Overview

Chat Channels

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Chat Channels](https://docs.openclaw.ai/channels#chat-channels)
- [Supported channels](https://docs.openclaw.ai/channels#supported-channels)
- [Notes](https://docs.openclaw.ai/channels#notes)

# [​](https://docs.openclaw.ai/channels\#chat-channels)  Chat Channels

OpenClaw can talk to you on any chat app you already use. Each channel connects via the Gateway.
Text is supported everywhere; media and reactions vary by channel.

## [​](https://docs.openclaw.ai/channels\#supported-channels)  Supported channels

- [WhatsApp](https://docs.openclaw.ai/channels/whatsapp) — Most popular; uses Baileys and requires QR pairing.
- [Telegram](https://docs.openclaw.ai/channels/telegram) — Bot API via grammY; supports groups.
- [Discord](https://docs.openclaw.ai/channels/discord) — Discord Bot API + Gateway; supports servers, channels, and DMs.
- [IRC](https://docs.openclaw.ai/channels/irc) — Classic IRC servers; channels + DMs with pairing/allowlist controls.
- [Slack](https://docs.openclaw.ai/channels/slack) — Bolt SDK; workspace apps.
- [Feishu](https://docs.openclaw.ai/channels/feishu) — Feishu/Lark bot via WebSocket (plugin, installed separately).
- [Google Chat](https://docs.openclaw.ai/channels/googlechat) — Google Chat API app via HTTP webhook.
- [Mattermost](https://docs.openclaw.ai/channels/mattermost) — Bot API + WebSocket; channels, groups, DMs (plugin, installed separately).
- [Signal](https://docs.openclaw.ai/channels/signal) — signal-cli; privacy-focused.
- [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles) — **Recommended for iMessage**; uses the BlueBubbles macOS server REST API with full feature support (edit, unsend, effects, reactions, group management — edit currently broken on macOS 26 Tahoe).
- [iMessage (legacy)](https://docs.openclaw.ai/channels/imessage) — Legacy macOS integration via imsg CLI (deprecated, use BlueBubbles for new setups).
- [Microsoft Teams](https://docs.openclaw.ai/channels/msteams) — Bot Framework; enterprise support (plugin, installed separately).
- [Synology Chat](https://docs.openclaw.ai/channels/synology-chat) — Synology NAS Chat via outgoing+incoming webhooks (plugin, installed separately).
- [LINE](https://docs.openclaw.ai/channels/line) — LINE Messaging API bot (plugin, installed separately).
- [Nextcloud Talk](https://docs.openclaw.ai/channels/nextcloud-talk) — Self-hosted chat via Nextcloud Talk (plugin, installed separately).
- [Matrix](https://docs.openclaw.ai/channels/matrix) — Matrix protocol (plugin, installed separately).
- [Nostr](https://docs.openclaw.ai/channels/nostr) — Decentralized DMs via NIP-04 (plugin, installed separately).
- [Tlon](https://docs.openclaw.ai/channels/tlon) — Urbit-based messenger (plugin, installed separately).
- [Twitch](https://docs.openclaw.ai/channels/twitch) — Twitch chat via IRC connection (plugin, installed separately).
- [Zalo](https://docs.openclaw.ai/channels/zalo) — Zalo Bot API; Vietnam’s popular messenger (plugin, installed separately).
- [Zalo Personal](https://docs.openclaw.ai/channels/zalouser) — Zalo personal account via QR login (plugin, installed separately).
- [WebChat](https://docs.openclaw.ai/web/webchat) — Gateway WebChat UI over WebSocket.

## [​](https://docs.openclaw.ai/channels\#notes)  Notes

- Channels can run simultaneously; configure multiple and OpenClaw will route per chat.
- Fastest setup is usually **Telegram** (simple bot token). WhatsApp requires QR pairing and
stores more state on disk.
- Group behavior varies by channel; see [Groups](https://docs.openclaw.ai/channels/groups).
- DM pairing and allowlists are enforced for safety; see [Security](https://docs.openclaw.ai/gateway/security).
- Troubleshooting: [Channel troubleshooting](https://docs.openclaw.ai/channels/troubleshooting).
- Model providers are documented separately; see [Model Providers](https://docs.openclaw.ai/providers/models).

[WhatsApp](https://docs.openclaw.ai/channels/whatsapp)

Ctrl+I

---

---

<!-- Page: Channel Routing -->

## Channel Routing

**Source:** https://docs.openclaw.ai/channels/channel-routing

[Skip to main content](https://docs.openclaw.ai/channels/channel-routing#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Channel Routing

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Channels & routing](https://docs.openclaw.ai/channels/channel-routing#channels-%26-routing)
- [Key terms](https://docs.openclaw.ai/channels/channel-routing#key-terms)
- [Session key shapes (examples)](https://docs.openclaw.ai/channels/channel-routing#session-key-shapes-examples)
- [Routing rules (how an agent is chosen)](https://docs.openclaw.ai/channels/channel-routing#routing-rules-how-an-agent-is-chosen)
- [Broadcast groups (run multiple agents)](https://docs.openclaw.ai/channels/channel-routing#broadcast-groups-run-multiple-agents)
- [Config overview](https://docs.openclaw.ai/channels/channel-routing#config-overview)
- [Session storage](https://docs.openclaw.ai/channels/channel-routing#session-storage)
- [WebChat behavior](https://docs.openclaw.ai/channels/channel-routing#webchat-behavior)
- [Reply context](https://docs.openclaw.ai/channels/channel-routing#reply-context)

# [​](https://docs.openclaw.ai/channels/channel-routing\#channels-&-routing)  Channels & routing

OpenClaw routes replies **back to the channel where a message came from**. The
model does not choose a channel; routing is deterministic and controlled by the
host configuration.

## [​](https://docs.openclaw.ai/channels/channel-routing\#key-terms)  Key terms

- **Channel**: `whatsapp`, `telegram`, `discord`, `slack`, `signal`, `imessage`, `webchat`.
- **AccountId**: per‑channel account instance (when supported).
- **AgentId**: an isolated workspace + session store (“brain”).
- **SessionKey**: the bucket key used to store context and control concurrency.

## [​](https://docs.openclaw.ai/channels/channel-routing\#session-key-shapes-examples)  Session key shapes (examples)

Direct messages collapse to the agent’s **main** session:

- `agent:<agentId>:<mainKey>` (default: `agent:main:main`)

Groups and channels remain isolated per channel:

- Groups: `agent:<agentId>:<channel>:group:<id>`
- Channels/rooms: `agent:<agentId>:<channel>:channel:<id>`

Threads:

- Slack/Discord threads append `:thread:<threadId>` to the base key.
- Telegram forum topics embed `:topic:<topicId>` in the group key.

Examples:

- `agent:main:telegram:group:-1001234567890:topic:42`
- `agent:main:discord:channel:123456:thread:987654`

## [​](https://docs.openclaw.ai/channels/channel-routing\#routing-rules-how-an-agent-is-chosen)  Routing rules (how an agent is chosen)

Routing picks **one agent** for each inbound message:

1. **Exact peer match** (`bindings` with `peer.kind` \+ `peer.id`).
2. **Parent peer match** (thread inheritance).
3. **Guild + roles match** (Discord) via `guildId` \+ `roles`.
4. **Guild match** (Discord) via `guildId`.
5. **Team match** (Slack) via `teamId`.
6. **Account match** (`accountId` on the channel).
7. **Channel match** (any account on that channel, `accountId: "*"`).
8. **Default agent** (`agents.list[].default`, else first list entry, fallback to `main`).

When a binding includes multiple match fields (`peer`, `guildId`, `teamId`, `roles`), **all provided fields must match** for that binding to apply.The matched agent determines which workspace and session store are used.

## [​](https://docs.openclaw.ai/channels/channel-routing\#broadcast-groups-run-multiple-agents)  Broadcast groups (run multiple agents)

Broadcast groups let you run **multiple agents** for the same peer **when OpenClaw would normally reply** (for example: in WhatsApp groups, after mention/activation gating).Config:

Copy

```
{
  broadcast: {
    strategy: "parallel",
    "120363403215116621@g.us": ["alfred", "baerbel"],
    "+15555550123": ["support", "logger"],
  },
}
```

See: [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups).

## [​](https://docs.openclaw.ai/channels/channel-routing\#config-overview)  Config overview

- `agents.list`: named agent definitions (workspace, model, etc.).
- `bindings`: map inbound channels/accounts/peers to agents.

Example:

Copy

```
{
  agents: {
    list: [{ id: "support", name: "Support", workspace: "~/.openclaw/workspace-support" }],
  },
  bindings: [\
    { match: { channel: "slack", teamId: "T123" }, agentId: "support" },\
    { match: { channel: "telegram", peer: { kind: "group", id: "-100123" } }, agentId: "support" },\
  ],
}
```

## [​](https://docs.openclaw.ai/channels/channel-routing\#session-storage)  Session storage

Session stores live under the state directory (default `~/.openclaw`):

- `~/.openclaw/agents/<agentId>/sessions/sessions.json`
- JSONL transcripts live alongside the store

You can override the store path via `session.store` and `{agentId}` templating.

## [​](https://docs.openclaw.ai/channels/channel-routing\#webchat-behavior)  WebChat behavior

WebChat attaches to the **selected agent** and defaults to the agent’s main
session. Because of this, WebChat lets you see cross‑channel context for that
agent in one place.

## [​](https://docs.openclaw.ai/channels/channel-routing\#reply-context)  Reply context

Inbound replies include:

- `ReplyToId`, `ReplyToBody`, and `ReplyToSender` when available.
- Quoted context is appended to `Body` as a `[Replying to ...]` block.

This is consistent across channels.

[Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups) [Channel Location Parsing](https://docs.openclaw.ai/channels/location)

Ctrl+I

---

---

<!-- Page: Groups -->

## Groups

**Source:** https://docs.openclaw.ai/channels/groups

[Skip to main content](https://docs.openclaw.ai/channels/groups#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Groups

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Groups](https://docs.openclaw.ai/channels/groups#groups)
- [Beginner intro (2 minutes)](https://docs.openclaw.ai/channels/groups#beginner-intro-2-minutes)
- [Session keys](https://docs.openclaw.ai/channels/groups#session-keys)
- [Pattern: personal DMs + public groups (single agent)](https://docs.openclaw.ai/channels/groups#pattern-personal-dms-%2B-public-groups-single-agent)
- [Display labels](https://docs.openclaw.ai/channels/groups#display-labels)
- [Group policy](https://docs.openclaw.ai/channels/groups#group-policy)
- [Mention gating (default)](https://docs.openclaw.ai/channels/groups#mention-gating-default)
- [Group/channel tool restrictions (optional)](https://docs.openclaw.ai/channels/groups#group%2Fchannel-tool-restrictions-optional)
- [Group allowlists](https://docs.openclaw.ai/channels/groups#group-allowlists)
- [Activation (owner-only)](https://docs.openclaw.ai/channels/groups#activation-owner-only)
- [Context fields](https://docs.openclaw.ai/channels/groups#context-fields)
- [iMessage specifics](https://docs.openclaw.ai/channels/groups#imessage-specifics)
- [WhatsApp specifics](https://docs.openclaw.ai/channels/groups#whatsapp-specifics)

# [​](https://docs.openclaw.ai/channels/groups\#groups)  Groups

OpenClaw treats group chats consistently across surfaces: WhatsApp, Telegram, Discord, Slack, Signal, iMessage, Microsoft Teams, Zalo.

## [​](https://docs.openclaw.ai/channels/groups\#beginner-intro-2-minutes)  Beginner intro (2 minutes)

OpenClaw “lives” on your own messaging accounts. There is no separate WhatsApp bot user.
If **you** are in a group, OpenClaw can see that group and respond there.Default behavior:

- Groups are restricted (`groupPolicy: "allowlist"`).
- Replies require a mention unless you explicitly disable mention gating.

Translation: allowlisted senders can trigger OpenClaw by mentioning it.

> TL;DR
>
> - **DM access** is controlled by `*.allowFrom`.
> - **Group access** is controlled by `*.groupPolicy` \+ allowlists (`*.groups`, `*.groupAllowFrom`).
> - **Reply triggering** is controlled by mention gating (`requireMention`, `/activation`).

Quick flow (what happens to a group message):

Copy

```
groupPolicy? disabled -> drop
groupPolicy? allowlist -> group allowed? no -> drop
requireMention? yes -> mentioned? no -> store for context only
otherwise -> reply
```

![Group message flow](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/images/groups-flow.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=9cb1d2837115f2d744dc27374ce33e2b)If you want…

| Goal | What to set |
| --- | --- |
| Allow all groups but only reply on @mentions | `groups: { "*": { requireMention: true } }` |
| Disable all group replies | `groupPolicy: "disabled"` |
| Only specific groups | `groups: { "<group-id>": { ... } }` (no `"*"` key) |
| Only you can trigger in groups | `groupPolicy: "allowlist"`, `groupAllowFrom: ["+1555..."]` |

## [​](https://docs.openclaw.ai/channels/groups\#session-keys)  Session keys

- Group sessions use `agent:<agentId>:<channel>:group:<id>` session keys (rooms/channels use `agent:<agentId>:<channel>:channel:<id>`).
- Telegram forum topics add `:topic:<threadId>` to the group id so each topic has its own session.
- Direct chats use the main session (or per-sender if configured).
- Heartbeats are skipped for group sessions.

## [​](https://docs.openclaw.ai/channels/groups\#pattern-personal-dms-+-public-groups-single-agent)  Pattern: personal DMs + public groups (single agent)

Yes — this works well if your “personal” traffic is **DMs** and your “public” traffic is **groups**.Why: in single-agent mode, DMs typically land in the **main** session key (`agent:main:main`), while groups always use **non-main** session keys (`agent:main:<channel>:group:<id>`). If you enable sandboxing with `mode: "non-main"`, those group sessions run in Docker while your main DM session stays on-host.This gives you one agent “brain” (shared workspace + memory), but two execution postures:

- **DMs**: full tools (host)
- **Groups**: sandbox + restricted tools (Docker)

> If you need truly separate workspaces/personas (“personal” and “public” must never mix), use a second agent + bindings. See [Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent).

Example (DMs on host, groups sandboxed + messaging-only tools):

Copy

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main", // groups/channels are non-main -> sandboxed
        scope: "session", // strongest isolation (one container per group/channel)
        workspaceAccess: "none",
      },
    },
  },
  tools: {
    sandbox: {
      tools: {
        // If allow is non-empty, everything else is blocked (deny still wins).
        allow: ["group:messaging", "group:sessions"],
        deny: ["group:runtime", "group:fs", "group:ui", "nodes", "cron", "gateway"],
      },
    },
  },
}
```

Want “groups can only see folder X” instead of “no host access”? Keep `workspaceAccess: "none"` and mount only allowlisted paths into the sandbox:

Copy

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",
        scope: "session",
        workspaceAccess: "none",
        docker: {
          binds: [\
            // hostPath:containerPath:mode\
            "/home/user/FriendsShared:/data:ro",\
          ],
        },
      },
    },
  },
}
```

Related:

- Configuration keys and defaults: [Gateway configuration](https://docs.openclaw.ai/gateway/configuration#agentsdefaultssandbox)
- Debugging why a tool is blocked: [Sandbox vs Tool Policy vs Elevated](https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated)
- Bind mounts details: [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing#custom-bind-mounts)

## [​](https://docs.openclaw.ai/channels/groups\#display-labels)  Display labels

- UI labels use `displayName` when available, formatted as `<channel>:<token>`.
- `#room` is reserved for rooms/channels; group chats use `g-<slug>` (lowercase, spaces -> `-`, keep `#@+._-`).

## [​](https://docs.openclaw.ai/channels/groups\#group-policy)  Group policy

Control how group/room messages are handled per channel:

Copy

```
{
  channels: {
    whatsapp: {
      groupPolicy: "disabled", // "open" | "disabled" | "allowlist"
      groupAllowFrom: ["+15551234567"],
    },
    telegram: {
      groupPolicy: "disabled",
      groupAllowFrom: ["123456789"], // numeric Telegram user id (wizard can resolve @username)
    },
    signal: {
      groupPolicy: "disabled",
      groupAllowFrom: ["+15551234567"],
    },
    imessage: {
      groupPolicy: "disabled",
      groupAllowFrom: ["chat_id:123"],
    },
    msteams: {
      groupPolicy: "disabled",
      groupAllowFrom: ["user@org.com"],
    },
    discord: {
      groupPolicy: "allowlist",
      guilds: {
        GUILD_ID: { channels: { help: { allow: true } } },
      },
    },
    slack: {
      groupPolicy: "allowlist",
      channels: { "#general": { allow: true } },
    },
    matrix: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["@owner:example.org"],
      groups: {
        "!roomId:example.org": { allow: true },
        "#alias:example.org": { allow: true },
      },
    },
  },
}
```

| Policy | Behavior |
| --- | --- |
| `"open"` | Groups bypass allowlists; mention-gating still applies. |
| `"disabled"` | Block all group messages entirely. |
| `"allowlist"` | Only allow groups/rooms that match the configured allowlist. |

Notes:

- `groupPolicy` is separate from mention-gating (which requires @mentions).
- WhatsApp/Telegram/Signal/iMessage/Microsoft Teams/Zalo: use `groupAllowFrom` (fallback: explicit `allowFrom`).
- DM pairing approvals (`*-allowFrom` store entries) apply to DM access only; group sender authorization stays explicit to group allowlists.
- Discord: allowlist uses `channels.discord.guilds.<id>.channels`.
- Slack: allowlist uses `channels.slack.channels`.
- Matrix: allowlist uses `channels.matrix.groups` (room IDs, aliases, or names). Use `channels.matrix.groupAllowFrom` to restrict senders; per-room `users` allowlists are also supported.
- Group DMs are controlled separately (`channels.discord.dm.*`, `channels.slack.dm.*`).
- Telegram allowlist can match user IDs (`"123456789"`, `"telegram:123456789"`, `"tg:123456789"`) or usernames (`"@alice"` or `"alice"`); prefixes are case-insensitive.
- Default is `groupPolicy: "allowlist"`; if your group allowlist is empty, group messages are blocked.
- Runtime safety: when a provider block is completely missing (`channels.<provider>` absent), group policy falls back to a fail-closed mode (typically `allowlist`) instead of inheriting `channels.defaults.groupPolicy`.

Quick mental model (evaluation order for group messages):

1. `groupPolicy` (open/disabled/allowlist)
2. group allowlists (`*.groups`, `*.groupAllowFrom`, channel-specific allowlist)
3. mention gating (`requireMention`, `/activation`)

## [​](https://docs.openclaw.ai/channels/groups\#mention-gating-default)  Mention gating (default)

Group messages require a mention unless overridden per group. Defaults live per subsystem under `*.groups."*"`.Replying to a bot message counts as an implicit mention (when the channel supports reply metadata). This applies to Telegram, WhatsApp, Slack, Discord, and Microsoft Teams.

Copy

```
{
  channels: {
    whatsapp: {
      groups: {
        "*": { requireMention: true },
        "123@g.us": { requireMention: false },
      },
    },
    telegram: {
      groups: {
        "*": { requireMention: true },
        "123456789": { requireMention: false },
      },
    },
    imessage: {
      groups: {
        "*": { requireMention: true },
        "123": { requireMention: false },
      },
    },
  },
  agents: {
    list: [\
      {\
        id: "main",\
        groupChat: {\
          mentionPatterns: ["@openclaw", "openclaw", "\\+15555550123"],\
          historyLimit: 50,\
        },\
      },\
    ],
  },
}
```

Notes:

- `mentionPatterns` are case-insensitive regexes.
- Surfaces that provide explicit mentions still pass; patterns are a fallback.
- Per-agent override: `agents.list[].groupChat.mentionPatterns` (useful when multiple agents share a group).
- Mention gating is only enforced when mention detection is possible (native mentions or `mentionPatterns` are configured).
- Discord defaults live in `channels.discord.guilds."*"` (overridable per guild/channel).
- Group history context is wrapped uniformly across channels and is **pending-only** (messages skipped due to mention gating); use `messages.groupChat.historyLimit` for the global default and `channels.<channel>.historyLimit` (or `channels.<channel>.accounts.*.historyLimit`) for overrides. Set `0` to disable.

## [​](https://docs.openclaw.ai/channels/groups\#group/channel-tool-restrictions-optional)  Group/channel tool restrictions (optional)

Some channel configs support restricting which tools are available **inside a specific group/room/channel**.

- `tools`: allow/deny tools for the whole group.
- `toolsBySender`: per-sender overrides within the group.
Use explicit key prefixes:
`id:<senderId>`, `e164:<phone>`, `username:<handle>`, `name:<displayName>`, and `"*"` wildcard.
Legacy unprefixed keys are still accepted and matched as `id:` only.

Resolution order (most specific wins):

1. group/channel `toolsBySender` match
2. group/channel `tools`
3. default (`"*"`) `toolsBySender` match
4. default (`"*"`) `tools`

Example (Telegram):

Copy

```
{
  channels: {
    telegram: {
      groups: {
        "*": { tools: { deny: ["exec"] } },
        "-1001234567890": {
          tools: { deny: ["exec", "read", "write"] },
          toolsBySender: {
            "id:123456789": { alsoAllow: ["exec"] },
          },
        },
      },
    },
  },
}
```

Notes:

- Group/channel tool restrictions are applied in addition to global/agent tool policy (deny still wins).
- Some channels use different nesting for rooms/channels (e.g., Discord `guilds.*.channels.*`, Slack `channels.*`, MS Teams `teams.*.channels.*`).

## [​](https://docs.openclaw.ai/channels/groups\#group-allowlists)  Group allowlists

When `channels.whatsapp.groups`, `channels.telegram.groups`, or `channels.imessage.groups` is configured, the keys act as a group allowlist. Use `"*"` to allow all groups while still setting default mention behavior.Common intents (copy/paste):

1. Disable all group replies

Copy

```
{
  channels: { whatsapp: { groupPolicy: "disabled" } },
}
```

2. Allow only specific groups (WhatsApp)

Copy

```
{
  channels: {
    whatsapp: {
      groups: {
        "123@g.us": { requireMention: true },
        "456@g.us": { requireMention: false },
      },
    },
  },
}
```

3. Allow all groups but require mention (explicit)

Copy

```
{
  channels: {
    whatsapp: {
      groups: { "*": { requireMention: true } },
    },
  },
}
```

4. Only the owner can trigger in groups (WhatsApp)

Copy

```
{
  channels: {
    whatsapp: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["+15551234567"],
      groups: { "*": { requireMention: true } },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/groups\#activation-owner-only)  Activation (owner-only)

Group owners can toggle per-group activation:

- `/activation mention`
- `/activation always`

Owner is determined by `channels.whatsapp.allowFrom` (or the bot’s self E.164 when unset). Send the command as a standalone message. Other surfaces currently ignore `/activation`.

## [​](https://docs.openclaw.ai/channels/groups\#context-fields)  Context fields

Group inbound payloads set:

- `ChatType=group`
- `GroupSubject` (if known)
- `GroupMembers` (if known)
- `WasMentioned` (mention gating result)
- Telegram forum topics also include `MessageThreadId` and `IsForum`.

The agent system prompt includes a group intro on the first turn of a new group session. It reminds the model to respond like a human, avoid Markdown tables, and avoid typing literal `\n` sequences.

## [​](https://docs.openclaw.ai/channels/groups\#imessage-specifics)  iMessage specifics

- Prefer `chat_id:<id>` when routing or allowlisting.
- List chats: `imsg chats --limit 20`.
- Group replies always go back to the same `chat_id`.

## [​](https://docs.openclaw.ai/channels/groups\#whatsapp-specifics)  WhatsApp specifics

See [Group messages](https://docs.openclaw.ai/channels/group-messages) for WhatsApp-only behavior (history injection, mention handling details).

[Group Messages](https://docs.openclaw.ai/channels/group-messages) [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups)

Ctrl+I

![Group message flow](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/images/groups-flow.svg?w=1100&fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=bd1ba6dd358add0a3dfceeed5b50b62d)

---

---

<!-- Page: Group Messages -->

## Group Messages

**Source:** https://docs.openclaw.ai/channels/group-messages

[Skip to main content](https://docs.openclaw.ai/channels/group-messages#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Group Messages

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Group messages (WhatsApp web channel)](https://docs.openclaw.ai/channels/group-messages#group-messages-whatsapp-web-channel)
- [What’s implemented (2025-12-03)](https://docs.openclaw.ai/channels/group-messages#what%E2%80%99s-implemented-2025-12-03)
- [Config example (WhatsApp)](https://docs.openclaw.ai/channels/group-messages#config-example-whatsapp)
- [Activation command (owner-only)](https://docs.openclaw.ai/channels/group-messages#activation-command-owner-only)
- [How to use](https://docs.openclaw.ai/channels/group-messages#how-to-use)
- [Testing / verification](https://docs.openclaw.ai/channels/group-messages#testing-%2F-verification)
- [Known considerations](https://docs.openclaw.ai/channels/group-messages#known-considerations)

# [​](https://docs.openclaw.ai/channels/group-messages\#group-messages-whatsapp-web-channel)  Group messages (WhatsApp web channel)

Goal: let Clawd sit in WhatsApp groups, wake up only when pinged, and keep that thread separate from the personal DM session.Note: `agents.list[].groupChat.mentionPatterns` is now used by Telegram/Discord/Slack/iMessage as well; this doc focuses on WhatsApp-specific behavior. For multi-agent setups, set `agents.list[].groupChat.mentionPatterns` per agent (or use `messages.groupChat.mentionPatterns` as a global fallback).

## [​](https://docs.openclaw.ai/channels/group-messages\#what%E2%80%99s-implemented-2025-12-03)  What’s implemented (2025-12-03)

- Activation modes: `mention` (default) or `always`. `mention` requires a ping (real WhatsApp @-mentions via `mentionedJids`, regex patterns, or the bot’s E.164 anywhere in the text). `always` wakes the agent on every message but it should reply only when it can add meaningful value; otherwise it returns the silent token `NO_REPLY`. Defaults can be set in config (`channels.whatsapp.groups`) and overridden per group via `/activation`. When `channels.whatsapp.groups` is set, it also acts as a group allowlist (include `"*"` to allow all).
- Group policy: `channels.whatsapp.groupPolicy` controls whether group messages are accepted (`open|disabled|allowlist`). `allowlist` uses `channels.whatsapp.groupAllowFrom` (fallback: explicit `channels.whatsapp.allowFrom`). Default is `allowlist` (blocked until you add senders).
- Per-group sessions: session keys look like `agent:<agentId>:whatsapp:group:<jid>` so commands such as `/verbose on` or `/think high` (sent as standalone messages) are scoped to that group; personal DM state is untouched. Heartbeats are skipped for group threads.
- Context injection: **pending-only** group messages (default 50) that _did not_ trigger a run are prefixed under `[Chat messages since your last reply - for context]`, with the triggering line under `[Current message - respond to this]`. Messages already in the session are not re-injected.
- Sender surfacing: every group batch now ends with `[from: Sender Name (+E164)]` so Pi knows who is speaking.
- Ephemeral/view-once: we unwrap those before extracting text/mentions, so pings inside them still trigger.
- Group system prompt: on the first turn of a group session (and whenever `/activation` changes the mode) we inject a short blurb into the system prompt like `You are replying inside the WhatsApp group "<subject>". Group members: Alice (+44...), Bob (+43...), … Activation: trigger-only … Address the specific sender noted in the message context.` If metadata isn’t available we still tell the agent it’s a group chat.

## [​](https://docs.openclaw.ai/channels/group-messages\#config-example-whatsapp)  Config example (WhatsApp)

Add a `groupChat` block to `~/.openclaw/openclaw.json` so display-name pings work even when WhatsApp strips the visual `@` in the text body:

Copy

```
{
  channels: {
    whatsapp: {
      groups: {
        "*": { requireMention: true },
      },
    },
  },
  agents: {
    list: [\
      {\
        id: "main",\
        groupChat: {\
          historyLimit: 50,\
          mentionPatterns: ["@?openclaw", "\\+?15555550123"],\
        },\
      },\
    ],
  },
}
```

Notes:

- The regexes are case-insensitive; they cover a display-name ping like `@openclaw` and the raw number with or without `+`/spaces.
- WhatsApp still sends canonical mentions via `mentionedJids` when someone taps the contact, so the number fallback is rarely needed but is a useful safety net.

### [​](https://docs.openclaw.ai/channels/group-messages\#activation-command-owner-only)  Activation command (owner-only)

Use the group chat command:

- `/activation mention`
- `/activation always`

Only the owner number (from `channels.whatsapp.allowFrom`, or the bot’s own E.164 when unset) can change this. Send `/status` as a standalone message in the group to see the current activation mode.

## [​](https://docs.openclaw.ai/channels/group-messages\#how-to-use)  How to use

1. Add your WhatsApp account (the one running OpenClaw) to the group.
2. Say `@openclaw …` (or include the number). Only allowlisted senders can trigger it unless you set `groupPolicy: "open"`.
3. The agent prompt will include recent group context plus the trailing `[from: …]` marker so it can address the right person.
4. Session-level directives (`/verbose on`, `/think high`, `/new` or `/reset`, `/compact`) apply only to that group’s session; send them as standalone messages so they register. Your personal DM session remains independent.

## [​](https://docs.openclaw.ai/channels/group-messages\#testing-/-verification)  Testing / verification

- Manual smoke:
  - Send an `@openclaw` ping in the group and confirm a reply that references the sender name.
  - Send a second ping and verify the history block is included then cleared on the next turn.
- Check gateway logs (run with `--verbose`) to see `inbound web message` entries showing `from: <groupJid>` and the `[from: …]` suffix.

## [​](https://docs.openclaw.ai/channels/group-messages\#known-considerations)  Known considerations

- Heartbeats are intentionally skipped for groups to avoid noisy broadcasts.
- Echo suppression uses the combined batch string; if you send identical text twice without mentions, only the first will get a response.
- Session store entries will appear as `agent:<agentId>:whatsapp:group:<jid>` in the session store (`~/.openclaw/agents/<agentId>/sessions/sessions.json` by default); a missing entry just means the group hasn’t triggered a run yet.
- Typing indicators in groups follow `agents.defaults.typingMode` (default: `message` when unmentioned).

[Pairing](https://docs.openclaw.ai/channels/pairing) [Groups](https://docs.openclaw.ai/channels/groups)

Ctrl+I

---

---

<!-- Page: Broadcast Groups -->

## Broadcast Groups

**Source:** https://docs.openclaw.ai/channels/broadcast-groups

[Skip to main content](https://docs.openclaw.ai/channels/broadcast-groups#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Broadcast Groups

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups#broadcast-groups)
- [Overview](https://docs.openclaw.ai/channels/broadcast-groups#overview)
- [Use Cases](https://docs.openclaw.ai/channels/broadcast-groups#use-cases)
- [1\. Specialized Agent Teams](https://docs.openclaw.ai/channels/broadcast-groups#1-specialized-agent-teams)
- [2\. Multi-Language Support](https://docs.openclaw.ai/channels/broadcast-groups#2-multi-language-support)
- [3\. Quality Assurance Workflows](https://docs.openclaw.ai/channels/broadcast-groups#3-quality-assurance-workflows)
- [4\. Task Automation](https://docs.openclaw.ai/channels/broadcast-groups#4-task-automation)
- [Configuration](https://docs.openclaw.ai/channels/broadcast-groups#configuration)
- [Basic Setup](https://docs.openclaw.ai/channels/broadcast-groups#basic-setup)
- [Processing Strategy](https://docs.openclaw.ai/channels/broadcast-groups#processing-strategy)
- [Parallel (Default)](https://docs.openclaw.ai/channels/broadcast-groups#parallel-default)
- [Sequential](https://docs.openclaw.ai/channels/broadcast-groups#sequential)
- [Complete Example](https://docs.openclaw.ai/channels/broadcast-groups#complete-example)
- [How It Works](https://docs.openclaw.ai/channels/broadcast-groups#how-it-works)
- [Message Flow](https://docs.openclaw.ai/channels/broadcast-groups#message-flow)
- [Session Isolation](https://docs.openclaw.ai/channels/broadcast-groups#session-isolation)
- [Example: Isolated Sessions](https://docs.openclaw.ai/channels/broadcast-groups#example-isolated-sessions)
- [Best Practices](https://docs.openclaw.ai/channels/broadcast-groups#best-practices)
- [1\. Keep Agents Focused](https://docs.openclaw.ai/channels/broadcast-groups#1-keep-agents-focused)
- [2\. Use Descriptive Names](https://docs.openclaw.ai/channels/broadcast-groups#2-use-descriptive-names)
- [3\. Configure Different Tool Access](https://docs.openclaw.ai/channels/broadcast-groups#3-configure-different-tool-access)
- [4\. Monitor Performance](https://docs.openclaw.ai/channels/broadcast-groups#4-monitor-performance)
- [5\. Handle Failures Gracefully](https://docs.openclaw.ai/channels/broadcast-groups#5-handle-failures-gracefully)
- [Compatibility](https://docs.openclaw.ai/channels/broadcast-groups#compatibility)
- [Providers](https://docs.openclaw.ai/channels/broadcast-groups#providers)
- [Routing](https://docs.openclaw.ai/channels/broadcast-groups#routing)
- [Troubleshooting](https://docs.openclaw.ai/channels/broadcast-groups#troubleshooting)
- [Agents Not Responding](https://docs.openclaw.ai/channels/broadcast-groups#agents-not-responding)
- [Only One Agent Responding](https://docs.openclaw.ai/channels/broadcast-groups#only-one-agent-responding)
- [Performance Issues](https://docs.openclaw.ai/channels/broadcast-groups#performance-issues)
- [Examples](https://docs.openclaw.ai/channels/broadcast-groups#examples)
- [Example 1: Code Review Team](https://docs.openclaw.ai/channels/broadcast-groups#example-1-code-review-team)
- [Example 2: Multi-Language Support](https://docs.openclaw.ai/channels/broadcast-groups#example-2-multi-language-support)
- [API Reference](https://docs.openclaw.ai/channels/broadcast-groups#api-reference)
- [Config Schema](https://docs.openclaw.ai/channels/broadcast-groups#config-schema)
- [Fields](https://docs.openclaw.ai/channels/broadcast-groups#fields)
- [Limitations](https://docs.openclaw.ai/channels/broadcast-groups#limitations)
- [Future Enhancements](https://docs.openclaw.ai/channels/broadcast-groups#future-enhancements)
- [See Also](https://docs.openclaw.ai/channels/broadcast-groups#see-also)

# [​](https://docs.openclaw.ai/channels/broadcast-groups\#broadcast-groups)  Broadcast Groups

**Status:** Experimental

**Version:** Added in 2026.1.9

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#overview)  Overview

Broadcast Groups enable multiple agents to process and respond to the same message simultaneously. This allows you to create specialized agent teams that work together in a single WhatsApp group or DM — all using one phone number.Current scope: **WhatsApp only** (web channel).Broadcast groups are evaluated after channel allowlists and group activation rules. In WhatsApp groups, this means broadcasts happen when OpenClaw would normally reply (for example: on mention, depending on your group settings).

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#use-cases)  Use Cases

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#1-specialized-agent-teams)  1\. Specialized Agent Teams

Deploy multiple agents with atomic, focused responsibilities:

Copy

```
Group: "Development Team"
Agents:
  - CodeReviewer (reviews code snippets)
  - DocumentationBot (generates docs)
  - SecurityAuditor (checks for vulnerabilities)
  - TestGenerator (suggests test cases)
```

Each agent processes the same message and provides its specialized perspective.

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#2-multi-language-support)  2\. Multi-Language Support

Copy

```
Group: "International Support"
Agents:
  - Agent_EN (responds in English)
  - Agent_DE (responds in German)
  - Agent_ES (responds in Spanish)
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#3-quality-assurance-workflows)  3\. Quality Assurance Workflows

Copy

```
Group: "Customer Support"
Agents:
  - SupportAgent (provides answer)
  - QAAgent (reviews quality, only responds if issues found)
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#4-task-automation)  4\. Task Automation

Copy

```
Group: "Project Management"
Agents:
  - TaskTracker (updates task database)
  - TimeLogger (logs time spent)
  - ReportGenerator (creates summaries)
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#configuration)  Configuration

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#basic-setup)  Basic Setup

Add a top-level `broadcast` section (next to `bindings`). Keys are WhatsApp peer ids:

- group chats: group JID (e.g. `120363403215116621@g.us`)
- DMs: E.164 phone number (e.g. `+15551234567`)

Copy

```
{
  "broadcast": {
    "120363403215116621@g.us": ["alfred", "baerbel", "assistant3"]
  }
}
```

**Result:** When OpenClaw would reply in this chat, it will run all three agents.

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#processing-strategy)  Processing Strategy

Control how agents process messages:

#### [​](https://docs.openclaw.ai/channels/broadcast-groups\#parallel-default)  Parallel (Default)

All agents process simultaneously:

Copy

```
{
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": ["alfred", "baerbel"]
  }
}
```

#### [​](https://docs.openclaw.ai/channels/broadcast-groups\#sequential)  Sequential

Agents process in order (one waits for previous to finish):

Copy

```
{
  "broadcast": {
    "strategy": "sequential",
    "120363403215116621@g.us": ["alfred", "baerbel"]
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#complete-example)  Complete Example

Copy

```
{
  "agents": {
    "list": [\
      {\
        "id": "code-reviewer",\
        "name": "Code Reviewer",\
        "workspace": "/path/to/code-reviewer",\
        "sandbox": { "mode": "all" }\
      },\
      {\
        "id": "security-auditor",\
        "name": "Security Auditor",\
        "workspace": "/path/to/security-auditor",\
        "sandbox": { "mode": "all" }\
      },\
      {\
        "id": "docs-generator",\
        "name": "Documentation Generator",\
        "workspace": "/path/to/docs-generator",\
        "sandbox": { "mode": "all" }\
      }\
    ]
  },
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": ["code-reviewer", "security-auditor", "docs-generator"],
    "120363424282127706@g.us": ["support-en", "support-de"],
    "+15555550123": ["assistant", "logger"]
  }
}
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#how-it-works)  How It Works

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#message-flow)  Message Flow

1. **Incoming message** arrives in a WhatsApp group
2. **Broadcast check**: System checks if peer ID is in `broadcast`
3. **If in broadcast list**:

   - All listed agents process the message
   - Each agent has its own session key and isolated context
   - Agents process in parallel (default) or sequentially
4. **If not in broadcast list**:

   - Normal routing applies (first matching binding)

Note: broadcast groups do not bypass channel allowlists or group activation rules (mentions/commands/etc). They only change _which agents run_ when a message is eligible for processing.

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#session-isolation)  Session Isolation

Each agent in a broadcast group maintains completely separate:

- **Session keys** (`agent:alfred:whatsapp:group:120363...` vs `agent:baerbel:whatsapp:group:120363...`)
- **Conversation history** (agent doesn’t see other agents’ messages)
- **Workspace** (separate sandboxes if configured)
- **Tool access** (different allow/deny lists)
- **Memory/context** (separate IDENTITY.md, SOUL.md, etc.)
- **Group context buffer** (recent group messages used for context) is shared per peer, so all broadcast agents see the same context when triggered

This allows each agent to have:

- Different personalities
- Different tool access (e.g., read-only vs. read-write)
- Different models (e.g., opus vs. sonnet)
- Different skills installed

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#example-isolated-sessions)  Example: Isolated Sessions

In group `120363403215116621@g.us` with agents `["alfred", "baerbel"]`:**Alfred’s context:**

Copy

```
Session: agent:alfred:whatsapp:group:120363403215116621@g.us
History: [user message, alfred's previous responses]
Workspace: /Users/pascal/openclaw-alfred/
Tools: read, write, exec
```

**Bärbel’s context:**

Copy

```
Session: agent:baerbel:whatsapp:group:120363403215116621@g.us
History: [user message, baerbel's previous responses]
Workspace: /Users/pascal/openclaw-baerbel/
Tools: read only
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#best-practices)  Best Practices

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#1-keep-agents-focused)  1\. Keep Agents Focused

Design each agent with a single, clear responsibility:

Copy

```
{
  "broadcast": {
    "DEV_GROUP": ["formatter", "linter", "tester"]
  }
}
```

✅ **Good:** Each agent has one job

❌ **Bad:** One generic “dev-helper” agent

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#2-use-descriptive-names)  2\. Use Descriptive Names

Make it clear what each agent does:

Copy

```
{
  "agents": {
    "security-scanner": { "name": "Security Scanner" },
    "code-formatter": { "name": "Code Formatter" },
    "test-generator": { "name": "Test Generator" }
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#3-configure-different-tool-access)  3\. Configure Different Tool Access

Give agents only the tools they need:

Copy

```
{
  "agents": {
    "reviewer": {
      "tools": { "allow": ["read", "exec"] } // Read-only
    },
    "fixer": {
      "tools": { "allow": ["read", "write", "edit", "exec"] } // Read-write
    }
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#4-monitor-performance)  4\. Monitor Performance

With many agents, consider:

- Using `"strategy": "parallel"` (default) for speed
- Limiting broadcast groups to 5-10 agents
- Using faster models for simpler agents

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#5-handle-failures-gracefully)  5\. Handle Failures Gracefully

Agents fail independently. One agent’s error doesn’t block others:

Copy

```
Message → [Agent A ✓, Agent B ✗ error, Agent C ✓]
Result: Agent A and C respond, Agent B logs error
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#compatibility)  Compatibility

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#providers)  Providers

Broadcast groups currently work with:

- ✅ WhatsApp (implemented)
- 🚧 Telegram (planned)
- 🚧 Discord (planned)
- 🚧 Slack (planned)

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#routing)  Routing

Broadcast groups work alongside existing routing:

Copy

```
{
  "bindings": [\
    {\
      "match": { "channel": "whatsapp", "peer": { "kind": "group", "id": "GROUP_A" } },\
      "agentId": "alfred"\
    }\
  ],
  "broadcast": {
    "GROUP_B": ["agent1", "agent2"]
  }
}
```

- `GROUP_A`: Only alfred responds (normal routing)
- `GROUP_B`: agent1 AND agent2 respond (broadcast)

**Precedence:**`broadcast` takes priority over `bindings`.

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#agents-not-responding)  Agents Not Responding

**Check:**

1. Agent IDs exist in `agents.list`
2. Peer ID format is correct (e.g., `120363403215116621@g.us`)
3. Agents are not in deny lists

**Debug:**

Copy

```
tail -f ~/.openclaw/logs/gateway.log | grep broadcast
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#only-one-agent-responding)  Only One Agent Responding

**Cause:** Peer ID might be in `bindings` but not `broadcast`.**Fix:** Add to broadcast config or remove from bindings.

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#performance-issues)  Performance Issues

**If slow with many agents:**

- Reduce number of agents per group
- Use lighter models (sonnet instead of opus)
- Check sandbox startup time

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#examples)  Examples

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#example-1-code-review-team)  Example 1: Code Review Team

Copy

```
{
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": [\
      "code-formatter",\
      "security-scanner",\
      "test-coverage",\
      "docs-checker"\
    ]
  },
  "agents": {
    "list": [\
      {\
        "id": "code-formatter",\
        "workspace": "~/agents/formatter",\
        "tools": { "allow": ["read", "write"] }\
      },\
      {\
        "id": "security-scanner",\
        "workspace": "~/agents/security",\
        "tools": { "allow": ["read", "exec"] }\
      },\
      {\
        "id": "test-coverage",\
        "workspace": "~/agents/testing",\
        "tools": { "allow": ["read", "exec"] }\
      },\
      { "id": "docs-checker", "workspace": "~/agents/docs", "tools": { "allow": ["read"] } }\
    ]
  }
}
```

**User sends:** Code snippet

**Responses:**

- code-formatter: “Fixed indentation and added type hints”
- security-scanner: “⚠️ SQL injection vulnerability in line 12”
- test-coverage: “Coverage is 45%, missing tests for error cases”
- docs-checker: “Missing docstring for function `process_data`”

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#example-2-multi-language-support)  Example 2: Multi-Language Support

Copy

```
{
  "broadcast": {
    "strategy": "sequential",
    "+15555550123": ["detect-language", "translator-en", "translator-de"]
  },
  "agents": {
    "list": [\
      { "id": "detect-language", "workspace": "~/agents/lang-detect" },\
      { "id": "translator-en", "workspace": "~/agents/translate-en" },\
      { "id": "translator-de", "workspace": "~/agents/translate-de" }\
    ]
  }
}
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#api-reference)  API Reference

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#config-schema)  Config Schema

Copy

```
interface OpenClawConfig {
  broadcast?: {
    strategy?: "parallel" | "sequential";
    [peerId: string]: string[];
  };
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups\#fields)  Fields

- `strategy`(optional): How to process agents

  - `"parallel"` (default): All agents process simultaneously
  - `"sequential"`: Agents process in array order
- `[peerId]`: WhatsApp group JID, E.164 number, or other peer ID

  - Value: Array of agent IDs that should process messages

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#limitations)  Limitations

1. **Max agents:** No hard limit, but 10+ agents may be slow
2. **Shared context:** Agents don’t see each other’s responses (by design)
3. **Message ordering:** Parallel responses may arrive in any order
4. **Rate limits:** All agents count toward WhatsApp rate limits

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#future-enhancements)  Future Enhancements

Planned features:

- [ ]  Shared context mode (agents see each other’s responses)
- [ ]  Agent coordination (agents can signal each other)
- [ ]  Dynamic agent selection (choose agents based on message content)
- [ ]  Agent priorities (some agents respond before others)

## [​](https://docs.openclaw.ai/channels/broadcast-groups\#see-also)  See Also

- [Multi-Agent Configuration](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools)
- [Routing Configuration](https://docs.openclaw.ai/channels/channel-routing)
- [Session Management](https://docs.openclaw.ai/concepts/sessions)

[Groups](https://docs.openclaw.ai/channels/groups) [Channel Routing](https://docs.openclaw.ai/channels/channel-routing)

Ctrl+I

---

---

<!-- Page: Channel Location Parsing -->

## Channel Location Parsing

**Source:** https://docs.openclaw.ai/channels/location

[Skip to main content](https://docs.openclaw.ai/channels/location#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Channel Location Parsing

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Channel location parsing](https://docs.openclaw.ai/channels/location#channel-location-parsing)
- [Text formatting](https://docs.openclaw.ai/channels/location#text-formatting)
- [Context fields](https://docs.openclaw.ai/channels/location#context-fields)
- [Channel notes](https://docs.openclaw.ai/channels/location#channel-notes)

# [​](https://docs.openclaw.ai/channels/location\#channel-location-parsing)  Channel location parsing

OpenClaw normalizes shared locations from chat channels into:

- human-readable text appended to the inbound body, and
- structured fields in the auto-reply context payload.

Currently supported:

- **Telegram** (location pins + venues + live locations)
- **WhatsApp** (locationMessage + liveLocationMessage)
- **Matrix** (`m.location` with `geo_uri`)

## [​](https://docs.openclaw.ai/channels/location\#text-formatting)  Text formatting

Locations are rendered as friendly lines without brackets:

- Pin:
  - `📍 48.858844, 2.294351 ±12m`
- Named place:
  - `📍 Eiffel Tower — Champ de Mars, Paris (48.858844, 2.294351 ±12m)`
- Live share:
  - `🛰 Live location: 48.858844, 2.294351 ±12m`

If the channel includes a caption/comment, it is appended on the next line:

Copy

```
📍 48.858844, 2.294351 ±12m
Meet here
```

## [​](https://docs.openclaw.ai/channels/location\#context-fields)  Context fields

When a location is present, these fields are added to `ctx`:

- `LocationLat` (number)
- `LocationLon` (number)
- `LocationAccuracy` (number, meters; optional)
- `LocationName` (string; optional)
- `LocationAddress` (string; optional)
- `LocationSource` (`pin | place | live`)
- `LocationIsLive` (boolean)

## [​](https://docs.openclaw.ai/channels/location\#channel-notes)  Channel notes

- **Telegram**: venues map to `LocationName/LocationAddress`; live locations use `live_period`.
- **WhatsApp**: `locationMessage.comment` and `liveLocationMessage.caption` are appended as the caption line.
- **Matrix**: `geo_uri` is parsed as a pin location; altitude is ignored and `LocationIsLive` is always false.

[Channel Routing](https://docs.openclaw.ai/channels/channel-routing) [Channel Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)

Ctrl+I

---

---

<!-- Page: Pairing -->

## Pairing

**Source:** https://docs.openclaw.ai/channels/pairing

[Skip to main content](https://docs.openclaw.ai/channels/pairing#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Configuration

Pairing

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Pairing](https://docs.openclaw.ai/channels/pairing#pairing)
- [1) DM pairing (inbound chat access)](https://docs.openclaw.ai/channels/pairing#1-dm-pairing-inbound-chat-access)
- [Approve a sender](https://docs.openclaw.ai/channels/pairing#approve-a-sender)
- [Where the state lives](https://docs.openclaw.ai/channels/pairing#where-the-state-lives)
- [2) Node device pairing (iOS/Android/macOS/headless nodes)](https://docs.openclaw.ai/channels/pairing#2-node-device-pairing-ios%2Fandroid%2Fmacos%2Fheadless-nodes)
- [Pair via Telegram (recommended for iOS)](https://docs.openclaw.ai/channels/pairing#pair-via-telegram-recommended-for-ios)
- [Approve a node device](https://docs.openclaw.ai/channels/pairing#approve-a-node-device)
- [Node pairing state storage](https://docs.openclaw.ai/channels/pairing#node-pairing-state-storage)
- [Notes](https://docs.openclaw.ai/channels/pairing#notes)
- [Related docs](https://docs.openclaw.ai/channels/pairing#related-docs)

# [​](https://docs.openclaw.ai/channels/pairing\#pairing)  Pairing

“Pairing” is OpenClaw’s explicit **owner approval** step.
It is used in two places:

1. **DM pairing** (who is allowed to talk to the bot)
2. **Node pairing** (which devices/nodes are allowed to join the gateway network)

Security context: [Security](https://docs.openclaw.ai/gateway/security)

## [​](https://docs.openclaw.ai/channels/pairing\#1-dm-pairing-inbound-chat-access)  1) DM pairing (inbound chat access)

When a channel is configured with DM policy `pairing`, unknown senders get a short code and their message is **not processed** until you approve.Default DM policies are documented in: [Security](https://docs.openclaw.ai/gateway/security)Pairing codes:

- 8 characters, uppercase, no ambiguous chars (`0O1I`).
- **Expire after 1 hour**. The bot only sends the pairing message when a new request is created (roughly once per hour per sender).
- Pending DM pairing requests are capped at **3 per channel** by default; additional requests are ignored until one expires or is approved.

### [​](https://docs.openclaw.ai/channels/pairing\#approve-a-sender)  Approve a sender

Copy

```
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

Supported channels: `telegram`, `whatsapp`, `signal`, `imessage`, `discord`, `slack`, `feishu`.

### [​](https://docs.openclaw.ai/channels/pairing\#where-the-state-lives)  Where the state lives

Stored under `~/.openclaw/credentials/`:

- Pending requests: `<channel>-pairing.json`
- Approved allowlist store:
  - Default account: `<channel>-allowFrom.json`
  - Non-default account: `<channel>-<accountId>-allowFrom.json`

Account scoping behavior:

- Non-default accounts read/write only their scoped allowlist file.
- Default account uses the channel-scoped unscoped allowlist file.

Treat these as sensitive (they gate access to your assistant).

## [​](https://docs.openclaw.ai/channels/pairing\#2-node-device-pairing-ios/android/macos/headless-nodes)  2) Node device pairing (iOS/Android/macOS/headless nodes)

Nodes connect to the Gateway as **devices** with `role: node`. The Gateway
creates a device pairing request that must be approved.

### [​](https://docs.openclaw.ai/channels/pairing\#pair-via-telegram-recommended-for-ios)  Pair via Telegram (recommended for iOS)

If you use the `device-pair` plugin, you can do first-time device pairing entirely from Telegram:

1. In Telegram, message your bot: `/pair`
2. The bot replies with two messages: an instruction message and a separate **setup code** message (easy to copy/paste in Telegram).
3. On your phone, open the OpenClaw iOS app → Settings → Gateway.
4. Paste the setup code and connect.
5. Back in Telegram: `/pair approve`

The setup code is a base64-encoded JSON payload that contains:

- `url`: the Gateway WebSocket URL (`ws://...` or `wss://...`)
- `token`: a short-lived pairing token

Treat the setup code like a password while it is valid.

### [​](https://docs.openclaw.ai/channels/pairing\#approve-a-node-device)  Approve a node device

Copy

```
openclaw devices list
openclaw devices approve <requestId>
openclaw devices reject <requestId>
```

### [​](https://docs.openclaw.ai/channels/pairing\#node-pairing-state-storage)  Node pairing state storage

Stored under `~/.openclaw/devices/`:

- `pending.json` (short-lived; pending requests expire)
- `paired.json` (paired devices + tokens)

### [​](https://docs.openclaw.ai/channels/pairing\#notes)  Notes

- The legacy `node.pair.*` API (CLI: `openclaw nodes pending/approve`) is a
separate gateway-owned pairing store. WS nodes still require device pairing.

## [​](https://docs.openclaw.ai/channels/pairing\#related-docs)  Related docs

- Security model + prompt injection: [Security](https://docs.openclaw.ai/gateway/security)
- Updating safely (run doctor): [Updating](https://docs.openclaw.ai/install/updating)
- Channel configs:
  - Telegram: [Telegram](https://docs.openclaw.ai/channels/telegram)
  - WhatsApp: [WhatsApp](https://docs.openclaw.ai/channels/whatsapp)
  - Signal: [Signal](https://docs.openclaw.ai/channels/signal)
  - BlueBubbles (iMessage): [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles)
  - iMessage (legacy): [iMessage](https://docs.openclaw.ai/channels/imessage)
  - Discord: [Discord](https://docs.openclaw.ai/channels/discord)
  - Slack: [Slack](https://docs.openclaw.ai/channels/slack)

[Zalo Personal](https://docs.openclaw.ai/channels/zalouser) [Group Messages](https://docs.openclaw.ai/channels/group-messages)

Ctrl+I

---
