# Channels Team Platforms


---

<!-- Page: Discord -->

## Discord

**Source:** https://docs.openclaw.ai/channels/discord

[Skip to main content](https://docs.openclaw.ai/channels/discord#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Discord

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Discord (Bot API)](https://docs.openclaw.ai/channels/discord#discord-bot-api)
- [Quick setup](https://docs.openclaw.ai/channels/discord#quick-setup)
- [Recommended: Set up a guild workspace](https://docs.openclaw.ai/channels/discord#recommended-set-up-a-guild-workspace)
- [Runtime model](https://docs.openclaw.ai/channels/discord#runtime-model)
- [Forum channels](https://docs.openclaw.ai/channels/discord#forum-channels)
- [Interactive components](https://docs.openclaw.ai/channels/discord#interactive-components)
- [Access control and routing](https://docs.openclaw.ai/channels/discord#access-control-and-routing)
- [Role-based agent routing](https://docs.openclaw.ai/channels/discord#role-based-agent-routing)
- [Developer Portal setup](https://docs.openclaw.ai/channels/discord#developer-portal-setup)
- [Native commands and command auth](https://docs.openclaw.ai/channels/discord#native-commands-and-command-auth)
- [Feature details](https://docs.openclaw.ai/channels/discord#feature-details)
- [Tools and action gates](https://docs.openclaw.ai/channels/discord#tools-and-action-gates)
- [Components v2 UI](https://docs.openclaw.ai/channels/discord#components-v2-ui)
- [Voice channels](https://docs.openclaw.ai/channels/discord#voice-channels)
- [Voice messages](https://docs.openclaw.ai/channels/discord#voice-messages)
- [Troubleshooting](https://docs.openclaw.ai/channels/discord#troubleshooting)
- [Configuration reference pointers](https://docs.openclaw.ai/channels/discord#configuration-reference-pointers)
- [Safety and operations](https://docs.openclaw.ai/channels/discord#safety-and-operations)
- [Related](https://docs.openclaw.ai/channels/discord#related)

# [​](https://docs.openclaw.ai/channels/discord\#discord-bot-api)  Discord (Bot API)

Status: ready for DMs and guild channels via the official Discord gateway.

[**Pairing** \\
\\
Discord DMs default to pairing mode.](https://docs.openclaw.ai/channels/pairing) [**Slash commands** \\
\\
Native command behavior and command catalog.](https://docs.openclaw.ai/tools/slash-commands) [**Channel troubleshooting** \\
\\
Cross-channel diagnostics and repair flow.](https://docs.openclaw.ai/channels/troubleshooting)

## [​](https://docs.openclaw.ai/channels/discord\#quick-setup)  Quick setup

You will need to create a new application with a bot, add the bot to your server, and pair it to OpenClaw. We recommend adding your bot to your own private server. If you don’t have one yet, [create one first](https://support.discord.com/hc/en-us/articles/204849977-How-do-I-create-a-server) (choose **Create My Own > For me and my friends**).

1

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Create a Discord application and bot

Go to the [Discord Developer Portal](https://discord.com/developers/applications) and click **New Application**. Name it something like “OpenClaw”.Click **Bot** on the sidebar. Set the **Username** to whatever you call your OpenClaw agent.

2

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Enable privileged intents

Still on the **Bot** page, scroll down to **Privileged Gateway Intents** and enable:

- **Message Content Intent** (required)
- **Server Members Intent** (recommended; required for role allowlists and name-to-ID matching)
- **Presence Intent** (optional; only needed for presence updates)

3

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Copy your bot token

Scroll back up on the **Bot** page and click **Reset Token**.

Despite the name, this generates your first token — nothing is being “reset.”

Copy the token and save it somewhere. This is your **Bot Token** and you will need it shortly.

4

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Generate an invite URL and add the bot to your server

Click **OAuth2** on the sidebar. You’ll generate an invite URL with the right permissions to add the bot to your server.Scroll down to **OAuth2 URL Generator** and enable:

- `bot`
- `applications.commands`

A **Bot Permissions** section will appear below. Enable:

- View Channels
- Send Messages
- Read Message History
- Embed Links
- Attach Files
- Add Reactions (optional)

Copy the generated URL at the bottom, paste it into your browser, select your server, and click **Continue** to connect. You should now see your bot in the Discord server.

5

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Enable Developer Mode and collect your IDs

Back in the Discord app, you need to enable Developer Mode so you can copy internal IDs.

1. Click **User Settings** (gear icon next to your avatar) → **Advanced** → toggle on **Developer Mode**
2. Right-click your **server icon** in the sidebar → **Copy Server ID**
3. Right-click your **own avatar** → **Copy User ID**

Save your **Server ID** and **User ID** alongside your Bot Token — you’ll send all three to OpenClaw in the next step.

6

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Allow DMs from server members

For pairing to work, Discord needs to allow your bot to DM you. Right-click your **server icon** → **Privacy Settings** → toggle on **Direct Messages**.This lets server members (including bots) send you DMs. Keep this enabled if you want to use Discord DMs with OpenClaw. If you only plan to use guild channels, you can disable DMs after pairing.

7

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Step 0: Set your bot token securely (do not send it in chat)

Your Discord bot token is a secret (like a password). Set it on the machine running OpenClaw before messaging your agent.

Copy

```
openclaw config set channels.discord.token '"YOUR_BOT_TOKEN"' --json
openclaw config set channels.discord.enabled true --json
openclaw gateway
```

If OpenClaw is already running as a background service, use `openclaw gateway restart` instead.

8

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Configure OpenClaw and pair

- Ask your agent

- CLI / config


Chat with your OpenClaw agent on any existing channel (e.g. Telegram) and tell it. If Discord is your first channel, use the CLI / config tab instead.

> “I already set my Discord bot token in config. Please finish Discord setup with User ID `<user_id>` and Server ID `<server_id>`.”

If you prefer file-based config, set:

Copy

```
{
  channels: {
    discord: {
      enabled: true,
      token: "YOUR_BOT_TOKEN",
    },
  },
}
```

Env fallback for the default account:

Copy

```
DISCORD_BOT_TOKEN=...
```

9

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Approve first DM pairing

Wait until the gateway is running, then DM your bot in Discord. It will respond with a pairing code.

- Ask your agent

- CLI


Send the pairing code to your agent on your existing channel:

> “Approve this Discord pairing code: `<CODE>`”

Copy

```
openclaw pairing list discord
openclaw pairing approve discord <CODE>
```

Pairing codes expire after 1 hour.You should now be able to chat with your agent in Discord via DM.

Token resolution is account-aware. Config token values win over env fallback. `DISCORD_BOT_TOKEN` is only used for the default account.

## [​](https://docs.openclaw.ai/channels/discord\#recommended-set-up-a-guild-workspace)  Recommended: Set up a guild workspace

Once DMs are working, you can set up your Discord server as a full workspace where each channel gets its own agent session with its own context. This is recommended for private servers where it’s just you and your bot.

1

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Add your server to the guild allowlist

This enables your agent to respond in any channel on your server, not just DMs.

- Ask your agent

- Config


> “Add my Discord Server ID `<server_id>` to the guild allowlist”

Copy

```
{
  channels: {
    discord: {
      groupPolicy: "allowlist",
      guilds: {
        YOUR_SERVER_ID: {
          requireMention: true,
          users: ["YOUR_USER_ID"],
        },
      },
    },
  },
}
```

2

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Allow responses without @mention

By default, your agent only responds in guild channels when @mentioned. For a private server, you probably want it to respond to every message.

- Ask your agent

- Config


> “Allow my agent to respond on this server without having to be @mentioned”

Set `requireMention: false` in your guild config:

Copy

```
{
  channels: {
    discord: {
      guilds: {
        YOUR_SERVER_ID: {
          requireMention: false,
        },
      },
    },
  },
}
```

3

[Navigate to header](https://docs.openclaw.ai/channels/discord#)

Plan for memory in guild channels

By default, long-term memory (MEMORY.md) only loads in DM sessions. Guild channels do not auto-load MEMORY.md.

- Ask your agent

- Manual


> “When I ask questions in Discord channels, use memory\_search or memory\_get if you need long-term context from MEMORY.md.”

If you need shared context in every channel, put the stable instructions in `AGENTS.md` or `USER.md` (they are injected for every session). Keep long-term notes in `MEMORY.md` and access them on demand with memory tools.

Now create some channels on your Discord server and start chatting. Your agent can see the channel name, and each channel gets its own isolated session — so you can set up `#coding`, `#home`, `#research`, or whatever fits your workflow.

## [​](https://docs.openclaw.ai/channels/discord\#runtime-model)  Runtime model

- Gateway owns the Discord connection.
- Reply routing is deterministic: Discord inbound replies back to Discord.
- By default (`session.dmScope=main`), direct chats share the agent main session (`agent:main:main`).
- Guild channels are isolated session keys (`agent:<agentId>:discord:channel:<channelId>`).
- Group DMs are ignored by default (`channels.discord.dm.groupEnabled=false`).
- Native slash commands run in isolated command sessions (`agent:<agentId>:discord:slash:<userId>`), while still carrying `CommandTargetSessionKey` to the routed conversation session.

## [​](https://docs.openclaw.ai/channels/discord\#forum-channels)  Forum channels

Discord forum and media channels only accept thread posts. OpenClaw supports two ways to create them:

- Send a message to the forum parent (`channel:<forumId>`) to auto-create a thread. The thread title uses the first non-empty line of your message.
- Use `openclaw message thread create` to create a thread directly. Do not pass `--message-id` for forum channels.

Example: send to forum parent to create a thread

Copy

```
openclaw message send --channel discord --target channel:<forumId> \
  --message "Topic title\nBody of the post"
```

Example: create a forum thread explicitly

Copy

```
openclaw message thread create --channel discord --target channel:<forumId> \
  --thread-name "Topic title" --message "Body of the post"
```

Forum parents do not accept Discord components. If you need components, send to the thread itself (`channel:<threadId>`).

## [​](https://docs.openclaw.ai/channels/discord\#interactive-components)  Interactive components

OpenClaw supports Discord components v2 containers for agent messages. Use the message tool with a `components` payload. Interaction results are routed back to the agent as normal inbound messages and follow the existing Discord `replyToMode` settings.Supported blocks:

- `text`, `section`, `separator`, `actions`, `media-gallery`, `file`
- Action rows allow up to 5 buttons or a single select menu
- Select types: `string`, `user`, `role`, `mentionable`, `channel`

By default, components are single use. Set `components.reusable=true` to allow buttons, selects, and forms to be used multiple times until they expire.To restrict who can click a button, set `allowedUsers` on that button (Discord user IDs, tags, or `*`). When configured, unmatched users receive an ephemeral denial.The `/model` and `/models` slash commands open an interactive model picker with provider and model dropdowns plus a Submit step. The picker reply is ephemeral and only the invoking user can use it.File attachments:

- `file` blocks must point to an attachment reference (`attachment://<filename>`)
- Provide the attachment via `media`/`path`/`filePath` (single file); use `media-gallery` for multiple files
- Use `filename` to override the upload name when it should match the attachment reference

Modal forms:

- Add `components.modal` with up to 5 fields
- Field types: `text`, `checkbox`, `radio`, `select`, `role-select`, `user-select`
- OpenClaw adds a trigger button automatically

Example:

Copy

```
{
  channel: "discord",
  action: "send",
  to: "channel:123456789012345678",
  message: "Optional fallback text",
  components: {
    reusable: true,
    text: "Choose a path",
    blocks: [\
      {\
        type: "actions",\
        buttons: [\
          {\
            label: "Approve",\
            style: "success",\
            allowedUsers: ["123456789012345678"],\
          },\
          { label: "Decline", style: "danger" },\
        ],\
      },\
      {\
        type: "actions",\
        select: {\
          type: "string",\
          placeholder: "Pick an option",\
          options: [\
            { label: "Option A", value: "a" },\
            { label: "Option B", value: "b" },\
          ],\
        },\
      },\
    ],
    modal: {
      title: "Details",
      triggerLabel: "Open form",
      fields: [\
        { type: "text", label: "Requester" },\
        {\
          type: "select",\
          label: "Priority",\
          options: [\
            { label: "Low", value: "low" },\
            { label: "High", value: "high" },\
          ],\
        },\
      ],
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/discord\#access-control-and-routing)  Access control and routing

- DM policy

- Guild policy

- Mentions and group DMs


`channels.discord.dmPolicy` controls DM access (legacy: `channels.discord.dm.policy`):

- `pairing` (default)
- `allowlist`
- `open` (requires `channels.discord.allowFrom` to include `"*"`; legacy: `channels.discord.dm.allowFrom`)
- `disabled`

If DM policy is not open, unknown users are blocked (or prompted for pairing in `pairing` mode).Multi-account precedence:

- `channels.discord.accounts.default.allowFrom` applies only to the `default` account.
- Named accounts inherit `channels.discord.allowFrom` when their own `allowFrom` is unset.
- Named accounts do not inherit `channels.discord.accounts.default.allowFrom`.

DM target format for delivery:

- `user:<id>`
- `<@id>` mention

Bare numeric IDs are ambiguous and rejected unless an explicit user/channel target kind is provided.

Guild handling is controlled by `channels.discord.groupPolicy`:

- `open`
- `allowlist`
- `disabled`

Secure baseline when `channels.discord` exists is `allowlist`.`allowlist` behavior:

- guild must match `channels.discord.guilds` (`id` preferred, slug accepted)
- optional sender allowlists: `users` (stable IDs recommended) and `roles` (role IDs only); if either is configured, senders are allowed when they match `users` OR `roles`
- direct name/tag matching is disabled by default; enable `channels.discord.dangerouslyAllowNameMatching: true` only as break-glass compatibility mode
- names/tags are supported for `users`, but IDs are safer; `openclaw security audit` warns when name/tag entries are used
- if a guild has `channels` configured, non-listed channels are denied
- if a guild has no `channels` block, all channels in that allowlisted guild are allowed

Example:

Copy

```
{
  channels: {
    discord: {
      groupPolicy: "allowlist",
      guilds: {
        "123456789012345678": {
          requireMention: true,
          users: ["987654321098765432"],
          roles: ["123456789012345678"],
          channels: {
            general: { allow: true },
            help: { allow: true, requireMention: true },
          },
        },
      },
    },
  },
}
```

If you only set `DISCORD_BOT_TOKEN` and do not create a `channels.discord` block, runtime fallback is `groupPolicy="allowlist"` (with a warning in logs), even if `channels.defaults.groupPolicy` is `open`.

Guild messages are mention-gated by default.Mention detection includes:

- explicit bot mention
- configured mention patterns (`agents.list[].groupChat.mentionPatterns`, fallback `messages.groupChat.mentionPatterns`)
- implicit reply-to-bot behavior in supported cases

`requireMention` is configured per guild/channel (`channels.discord.guilds...`).Group DMs:

- default: ignored (`dm.groupEnabled=false`)
- optional allowlist via `dm.groupChannels` (channel IDs or slugs)

### [​](https://docs.openclaw.ai/channels/discord\#role-based-agent-routing)  Role-based agent routing

Use `bindings[].match.roles` to route Discord guild members to different agents by role ID. Role-based bindings accept role IDs only and are evaluated after peer or parent-peer bindings and before guild-only bindings. If a binding also sets other match fields (for example `peer` \+ `guildId` \+ `roles`), all configured fields must match.

Copy

```
{
  bindings: [\
    {\
      agentId: "opus",\
      match: {\
        channel: "discord",\
        guildId: "123456789012345678",\
        roles: ["111111111111111111"],\
      },\
    },\
    {\
      agentId: "sonnet",\
      match: {\
        channel: "discord",\
        guildId: "123456789012345678",\
      },\
    },\
  ],
}
```

## [​](https://docs.openclaw.ai/channels/discord\#developer-portal-setup)  Developer Portal setup

Create app and bot

1. Discord Developer Portal -> **Applications** -\> **New Application**
2. **Bot** -\> **Add Bot**
3. Copy bot token

Privileged intents

In **Bot -> Privileged Gateway Intents**, enable:

- Message Content Intent
- Server Members Intent (recommended)

Presence intent is optional and only required if you want to receive presence updates. Setting bot presence (`setPresence`) does not require enabling presence updates for members.

OAuth scopes and baseline permissions

OAuth URL generator:

- scopes: `bot`, `applications.commands`

Typical baseline permissions:

- View Channels
- Send Messages
- Read Message History
- Embed Links
- Attach Files
- Add Reactions (optional)

Avoid `Administrator` unless explicitly needed.

Copy IDs

Enable Discord Developer Mode, then copy:

- server ID
- channel ID
- user ID

Prefer numeric IDs in OpenClaw config for reliable audits and probes.

## [​](https://docs.openclaw.ai/channels/discord\#native-commands-and-command-auth)  Native commands and command auth

- `commands.native` defaults to `"auto"` and is enabled for Discord.
- Per-channel override: `channels.discord.commands.native`.
- `commands.native=false` explicitly clears previously registered Discord native commands.
- Native command auth uses the same Discord allowlists/policies as normal message handling.
- Commands may still be visible in Discord UI for users who are not authorized; execution still enforces OpenClaw auth and returns “not authorized”.

See [Slash commands](https://docs.openclaw.ai/tools/slash-commands) for command catalog and behavior.Default slash command settings:

- `ephemeral: true`

## [​](https://docs.openclaw.ai/channels/discord\#feature-details)  Feature details

Reply tags and native replies

Discord supports reply tags in agent output:

- `[[reply_to_current]]`
- `[[reply_to:<id>]]`

Controlled by `channels.discord.replyToMode`:

- `off` (default)
- `first`
- `all`

Note: `off` disables implicit reply threading. Explicit `[[reply_to_*]]` tags are still honored.Message IDs are surfaced in context/history so agents can target specific messages.

Live stream preview

OpenClaw can stream draft replies by sending a temporary message and editing it as text arrives.

- `channels.discord.streaming` controls preview streaming (`off` \| `partial` \| `block` \| `progress`, default: `off`).
- `progress` is accepted for cross-channel consistency and maps to `partial` on Discord.
- `channels.discord.streamMode` is a legacy alias and is auto-migrated.
- `partial` edits a single preview message as tokens arrive.
- `block` emits draft-sized chunks (use `draftChunk` to tune size and breakpoints).

Example:

Copy

```
{
  channels: {
    discord: {
      streaming: "partial",
    },
  },
}
```

`block` mode chunking defaults (clamped to `channels.discord.textChunkLimit`):

Copy

```
{
  channels: {
    discord: {
      streaming: "block",
      draftChunk: {
        minChars: 200,
        maxChars: 800,
        breakPreference: "paragraph",
      },
    },
  },
}
```

Preview streaming is text-only; media replies fall back to normal delivery.Note: preview streaming is separate from block streaming. When block streaming is explicitly
enabled for Discord, OpenClaw skips the preview stream to avoid double streaming.

History, context, and thread behavior

Guild history context:

- `channels.discord.historyLimit` default `20`
- fallback: `messages.groupChat.historyLimit`
- `0` disables

DM history controls:

- `channels.discord.dmHistoryLimit`
- `channels.discord.dms["<user_id>"].historyLimit`

Thread behavior:

- Discord threads are routed as channel sessions
- parent thread metadata can be used for parent-session linkage
- thread config inherits parent channel config unless a thread-specific entry exists

Channel topics are injected as **untrusted** context (not as system prompt).

Thread-bound sessions for subagents

Discord can bind a thread to a session target so follow-up messages in that thread keep routing to the same session (including subagent sessions).Commands:

- `/focus <target>` bind current/new thread to a subagent/session target
- `/unfocus` remove current thread binding
- `/agents` show active runs and binding state
- `/session ttl <duration|off>` inspect/update auto-unfocus TTL for focused bindings

Config:

Copy

```
{
  session: {
    threadBindings: {
      enabled: true,
      ttlHours: 24,
    },
  },
  channels: {
    discord: {
      threadBindings: {
        enabled: true,
        ttlHours: 24,
        spawnSubagentSessions: false, // opt-in
      },
    },
  },
}
```

Notes:

- `session.threadBindings.*` sets global defaults.
- `channels.discord.threadBindings.*` overrides Discord behavior.
- `spawnSubagentSessions` must be true to auto-create/bind threads for `sessions_spawn({ thread: true })`.
- `spawnAcpSessions` must be true to auto-create/bind threads for ACP (`/acp spawn ... --thread ...` or `sessions_spawn({ runtime: "acp", thread: true })`).
- If thread bindings are disabled for an account, `/focus` and related thread binding operations are unavailable.

See [Sub-agents](https://docs.openclaw.ai/tools/subagents), [ACP Agents](https://docs.openclaw.ai/tools/acp-agents), and [Configuration Reference](https://docs.openclaw.ai/gateway/configuration-reference).

Reaction notifications

Per-guild reaction notification mode:

- `off`
- `own` (default)
- `all`
- `allowlist` (uses `guilds.<id>.users`)

Reaction events are turned into system events and attached to the routed Discord session.

Ack reactions

`ackReaction` sends an acknowledgement emoji while OpenClaw is processing an inbound message.Resolution order:

- `channels.discord.accounts.<accountId>.ackReaction`
- `channels.discord.ackReaction`
- `messages.ackReaction`
- agent identity emoji fallback (`agents.list[].identity.emoji`, else ”👀”)

Notes:

- Discord accepts unicode emoji or custom emoji names.
- Use `""` to disable the reaction for a channel or account.

Config writes

Channel-initiated config writes are enabled by default.This affects `/config set|unset` flows (when command features are enabled).Disable:

Copy

```
{
  channels: {
    discord: {
      configWrites: false,
    },
  },
}
```

Gateway proxy

Route Discord gateway WebSocket traffic and startup REST lookups (application ID + allowlist resolution) through an HTTP(S) proxy with `channels.discord.proxy`.

Copy

```
{
  channels: {
    discord: {
      proxy: "http://proxy.example:8080",
    },
  },
}
```

Per-account override:

Copy

```
{
  channels: {
    discord: {
      accounts: {
        primary: {
          proxy: "http://proxy.example:8080",
        },
      },
    },
  },
}
```

PluralKit support

Enable PluralKit resolution to map proxied messages to system member identity:

Copy

```
{
  channels: {
    discord: {
      pluralkit: {
        enabled: true,
        token: "pk_live_...", // optional; needed for private systems
      },
    },
  },
}
```

Notes:

- allowlists can use `pk:<memberId>`
- member display names are matched by name/slug only when `channels.discord.dangerouslyAllowNameMatching: true`
- lookups use original message ID and are time-window constrained
- if lookup fails, proxied messages are treated as bot messages and dropped unless `allowBots=true`

Presence configuration

Presence updates are applied only when you set a status or activity field.Status only example:

Copy

```
{
  channels: {
    discord: {
      status: "idle",
    },
  },
}
```

Activity example (custom status is the default activity type):

Copy

```
{
  channels: {
    discord: {
      activity: "Focus time",
      activityType: 4,
    },
  },
}
```

Streaming example:

Copy

```
{
  channels: {
    discord: {
      activity: "Live coding",
      activityType: 1,
      activityUrl: "https://twitch.tv/openclaw",
    },
  },
}
```

Activity type map:

- 0: Playing
- 1: Streaming (requires `activityUrl`)
- 2: Listening
- 3: Watching
- 4: Custom (uses the activity text as the status state; emoji is optional)
- 5: Competing

Exec approvals in Discord

Discord supports button-based exec approvals in DMs and can optionally post approval prompts in the originating channel.Config path:

- `channels.discord.execApprovals.enabled`
- `channels.discord.execApprovals.approvers`
- `channels.discord.execApprovals.target` (`dm` \| `channel` \| `both`, default: `dm`)
- `agentFilter`, `sessionFilter`, `cleanupAfterResolve`

When `target` is `channel` or `both`, the approval prompt is visible in the channel. Only configured approvers can use the buttons; other users receive an ephemeral denial. Approval prompts include the command text, so only enable channel delivery in trusted channels. If the channel ID cannot be derived from the session key, OpenClaw falls back to DM delivery.If approvals fail with unknown approval IDs, verify approver list and feature enablement.Related docs: [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals)

## [​](https://docs.openclaw.ai/channels/discord\#tools-and-action-gates)  Tools and action gates

Discord message actions include messaging, channel admin, moderation, presence, and metadata actions.Core examples:

- messaging: `sendMessage`, `readMessages`, `editMessage`, `deleteMessage`, `threadReply`
- reactions: `react`, `reactions`, `emojiList`
- moderation: `timeout`, `kick`, `ban`
- presence: `setPresence`

Action gates live under `channels.discord.actions.*`.Default gate behavior:

| Action group | Default |
| --- | --- |
| reactions, messages, threads, pins, polls, search, memberInfo, roleInfo, channelInfo, channels, voiceStatus, events, stickers, emojiUploads, stickerUploads, permissions | enabled |
| roles | disabled |
| moderation | disabled |
| presence | disabled |

## [​](https://docs.openclaw.ai/channels/discord\#components-v2-ui)  Components v2 UI

OpenClaw uses Discord components v2 for exec approvals and cross-context markers. Discord message actions can also accept `components` for custom UI (advanced; requires Carbon component instances), while legacy `embeds` remain available but are not recommended.

- `channels.discord.ui.components.accentColor` sets the accent color used by Discord component containers (hex).
- Set per account with `channels.discord.accounts.<id>.ui.components.accentColor`.
- `embeds` are ignored when components v2 are present.

Example:

Copy

```
{
  channels: {
    discord: {
      ui: {
        components: {
          accentColor: "#5865F2",
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/discord\#voice-channels)  Voice channels

OpenClaw can join Discord voice channels for realtime, continuous conversations. This is separate from voice message attachments.Requirements:

- Enable native commands (`commands.native` or `channels.discord.commands.native`).
- Configure `channels.discord.voice`.
- The bot needs Connect + Speak permissions in the target voice channel.

Use the Discord-only native command `/vc join|leave|status` to control sessions. The command uses the account default agent and follows the same allowlist and group policy rules as other Discord commands.Auto-join example:

Copy

```
{
  channels: {
    discord: {
      voice: {
        enabled: true,
        autoJoin: [\
          {\
            guildId: "123456789012345678",\
            channelId: "234567890123456789",\
          },\
        ],
        daveEncryption: true,
        decryptionFailureTolerance: 24,
        tts: {
          provider: "openai",
          openai: { voice: "alloy" },
        },
      },
    },
  },
}
```

Notes:

- `voice.tts` overrides `messages.tts` for voice playback only.
- Voice is enabled by default; set `channels.discord.voice.enabled=false` to disable it.
- `voice.daveEncryption` and `voice.decryptionFailureTolerance` pass through to `@discordjs/voice` join options.
- `@discordjs/voice` defaults are `daveEncryption=true` and `decryptionFailureTolerance=24` if unset.
- OpenClaw also watches receive decrypt failures and auto-recovers by leaving/rejoining the voice channel after repeated failures in a short window.
- If receive logs repeatedly show `DecryptionFailed(UnencryptedWhenPassthroughDisabled)`, this may be the upstream `@discordjs/voice` receive bug tracked in [discord.js #11419](https://github.com/discordjs/discord.js/issues/11419).

## [​](https://docs.openclaw.ai/channels/discord\#voice-messages)  Voice messages

Discord voice messages show a waveform preview and require OGG/Opus audio plus metadata. OpenClaw generates the waveform automatically, but it needs `ffmpeg` and `ffprobe` available on the gateway host to inspect and convert audio files.Requirements and constraints:

- Provide a **local file path** (URLs are rejected).
- Omit text content (Discord does not allow text + voice message in the same payload).
- Any audio format is accepted; OpenClaw converts to OGG/Opus when needed.

Example:

Copy

```
message(action="send", channel="discord", target="channel:123", path="/path/to/audio.mp3", asVoice=true)
```

## [​](https://docs.openclaw.ai/channels/discord\#troubleshooting)  Troubleshooting

Used disallowed intents or bot sees no guild messages

- enable Message Content Intent
- enable Server Members Intent when you depend on user/member resolution
- restart gateway after changing intents

Guild messages blocked unexpectedly

- verify `groupPolicy`
- verify guild allowlist under `channels.discord.guilds`
- if guild `channels` map exists, only listed channels are allowed
- verify `requireMention` behavior and mention patterns

Useful checks:

Copy

```
openclaw doctor
openclaw channels status --probe
openclaw logs --follow
```

Require mention false but still blocked

Common causes:

- `groupPolicy="allowlist"` without matching guild/channel allowlist
- `requireMention` configured in the wrong place (must be under `channels.discord.guilds` or channel entry)
- sender blocked by guild/channel `users` allowlist

Permissions audit mismatches

`channels status --probe` permission checks only work for numeric channel IDs.If you use slug keys, runtime matching can still work, but probe cannot fully verify permissions.

DM and pairing issues

- DM disabled: `channels.discord.dm.enabled=false`
- DM policy disabled: `channels.discord.dmPolicy="disabled"` (legacy: `channels.discord.dm.policy`)
- awaiting pairing approval in `pairing` mode

Bot to bot loops

By default bot-authored messages are ignored.If you set `channels.discord.allowBots=true`, use strict mention and allowlist rules to avoid loop behavior.

Voice STT drops with DecryptionFailed(...)

- keep OpenClaw current (`openclaw update`) so the Discord voice receive recovery logic is present
- confirm `channels.discord.voice.daveEncryption=true` (default)
- start from `channels.discord.voice.decryptionFailureTolerance=24` (upstream default) and tune only if needed
- watch logs for:
  - `discord voice: DAVE decrypt failures detected`
  - `discord voice: repeated decrypt failures; attempting rejoin`
- if failures continue after automatic rejoin, collect logs and compare against [discord.js #11419](https://github.com/discordjs/discord.js/issues/11419)

## [​](https://docs.openclaw.ai/channels/discord\#configuration-reference-pointers)  Configuration reference pointers

Primary reference:

- [Configuration reference - Discord](https://docs.openclaw.ai/gateway/configuration-reference#discord)

High-signal Discord fields:

- startup/auth: `enabled`, `token`, `accounts.*`, `allowBots`
- policy: `groupPolicy`, `dm.*`, `guilds.*`, `guilds.*.channels.*`
- command: `commands.native`, `commands.useAccessGroups`, `configWrites`, `slashCommand.*`
- reply/history: `replyToMode`, `historyLimit`, `dmHistoryLimit`, `dms.*.historyLimit`
- delivery: `textChunkLimit`, `chunkMode`, `maxLinesPerMessage`
- streaming: `streaming` (legacy alias: `streamMode`), `draftChunk`, `blockStreaming`, `blockStreamingCoalesce`
- media/retry: `mediaMaxMb`, `retry`
- actions: `actions.*`
- presence: `activity`, `status`, `activityType`, `activityUrl`
- UI: `ui.components.accentColor`
- features: `pluralkit`, `execApprovals`, `intents`, `agentComponents`, `heartbeat`, `responsePrefix`

## [​](https://docs.openclaw.ai/channels/discord\#safety-and-operations)  Safety and operations

- Treat bot tokens as secrets (`DISCORD_BOT_TOKEN` preferred in supervised environments).
- Grant least-privilege Discord permissions.
- If command deploy/state is stale, restart gateway and re-check with `openclaw channels status --probe`.

## [​](https://docs.openclaw.ai/channels/discord\#related)  Related

- [Pairing](https://docs.openclaw.ai/channels/pairing)
- [Channel routing](https://docs.openclaw.ai/channels/channel-routing)
- [Multi-agent routing](https://docs.openclaw.ai/concepts/multi-agent)
- [Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)
- [Slash commands](https://docs.openclaw.ai/tools/slash-commands)

[Telegram](https://docs.openclaw.ai/channels/telegram) [IRC](https://docs.openclaw.ai/channels/irc)

Ctrl+I

---

---

<!-- Page: Slack -->

## Slack

**Source:** https://docs.openclaw.ai/channels/slack

[Skip to main content](https://docs.openclaw.ai/channels/slack#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Slack

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Slack](https://docs.openclaw.ai/channels/slack#slack)
- [Quick setup](https://docs.openclaw.ai/channels/slack#quick-setup)
- [Token model](https://docs.openclaw.ai/channels/slack#token-model)
- [Access control and routing](https://docs.openclaw.ai/channels/slack#access-control-and-routing)
- [Commands and slash behavior](https://docs.openclaw.ai/channels/slack#commands-and-slash-behavior)
- [Threading, sessions, and reply tags](https://docs.openclaw.ai/channels/slack#threading-sessions-and-reply-tags)
- [Media, chunking, and delivery](https://docs.openclaw.ai/channels/slack#media-chunking-and-delivery)
- [Actions and gates](https://docs.openclaw.ai/channels/slack#actions-and-gates)
- [Events and operational behavior](https://docs.openclaw.ai/channels/slack#events-and-operational-behavior)
- [Ack reactions](https://docs.openclaw.ai/channels/slack#ack-reactions)
- [Manifest and scope checklist](https://docs.openclaw.ai/channels/slack#manifest-and-scope-checklist)
- [Troubleshooting](https://docs.openclaw.ai/channels/slack#troubleshooting)
- [Text streaming](https://docs.openclaw.ai/channels/slack#text-streaming)
- [Requirements](https://docs.openclaw.ai/channels/slack#requirements)
- [Behavior](https://docs.openclaw.ai/channels/slack#behavior)
- [Configuration reference pointers](https://docs.openclaw.ai/channels/slack#configuration-reference-pointers)
- [Related](https://docs.openclaw.ai/channels/slack#related)

# [​](https://docs.openclaw.ai/channels/slack\#slack)  Slack

Status: production-ready for DMs + channels via Slack app integrations. Default mode is Socket Mode; HTTP Events API mode is also supported.

[**Pairing** \\
\\
Slack DMs default to pairing mode.](https://docs.openclaw.ai/channels/pairing) [**Slash commands** \\
\\
Native command behavior and command catalog.](https://docs.openclaw.ai/tools/slash-commands) [**Channel troubleshooting** \\
\\
Cross-channel diagnostics and repair playbooks.](https://docs.openclaw.ai/channels/troubleshooting)

## [​](https://docs.openclaw.ai/channels/slack\#quick-setup)  Quick setup

- Socket Mode (default)

- HTTP Events API mode


1

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Create Slack app and tokens

In Slack app settings:

- enable **Socket Mode**
- create **App Token** (`xapp-...`) with `connections:write`
- install app and copy **Bot Token** (`xoxb-...`)

2

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Configure OpenClaw

Copy

```
{
  channels: {
    slack: {
      enabled: true,
      mode: "socket",
      appToken: "xapp-...",
      botToken: "xoxb-...",
    },
  },
}
```

Env fallback (default account only):

Copy

```
SLACK_APP_TOKEN=xapp-...
SLACK_BOT_TOKEN=xoxb-...
```

3

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Subscribe app events

Subscribe bot events for:

- `app_mention`
- `message.channels`, `message.groups`, `message.im`, `message.mpim`
- `reaction_added`, `reaction_removed`
- `member_joined_channel`, `member_left_channel`
- `channel_rename`
- `pin_added`, `pin_removed`

Also enable App Home **Messages Tab** for DMs.

4

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Start gateway

Copy

```
openclaw gateway
```

1

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Configure Slack app for HTTP

- set mode to HTTP (`channels.slack.mode="http"`)
- copy Slack **Signing Secret**
- set Event Subscriptions + Interactivity + Slash command Request URL to the same webhook path (default `/slack/events`)

2

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Configure OpenClaw HTTP mode

Copy

```
{
  channels: {
    slack: {
      enabled: true,
      mode: "http",
      botToken: "xoxb-...",
      signingSecret: "your-signing-secret",
      webhookPath: "/slack/events",
    },
  },
}
```

3

[Navigate to header](https://docs.openclaw.ai/channels/slack#)

Use unique webhook paths for multi-account HTTP

Per-account HTTP mode is supported.Give each account a distinct `webhookPath` so registrations do not collide.

## [​](https://docs.openclaw.ai/channels/slack\#token-model)  Token model

- `botToken` \+ `appToken` are required for Socket Mode.
- HTTP mode requires `botToken` \+ `signingSecret`.
- Config tokens override env fallback.
- `SLACK_BOT_TOKEN` / `SLACK_APP_TOKEN` env fallback applies only to the default account.
- `userToken` (`xoxp-...`) is config-only (no env fallback) and defaults to read-only behavior (`userTokenReadOnly: true`).
- Optional: add `chat:write.customize` if you want outgoing messages to use the active agent identity (custom `username` and icon). `icon_emoji` uses `:emoji_name:` syntax.

For actions/directory reads, user token can be preferred when configured. For writes, bot token remains preferred; user-token writes are only allowed when `userTokenReadOnly: false` and bot token is unavailable.

## [​](https://docs.openclaw.ai/channels/slack\#access-control-and-routing)  Access control and routing

- DM policy

- Channel policy

- Mentions and channel users


`channels.slack.dmPolicy` controls DM access (legacy: `channels.slack.dm.policy`):

- `pairing` (default)
- `allowlist`
- `open` (requires `channels.slack.allowFrom` to include `"*"`; legacy: `channels.slack.dm.allowFrom`)
- `disabled`

DM flags:

- `dm.enabled` (default true)
- `channels.slack.allowFrom` (preferred)
- `dm.allowFrom` (legacy)
- `dm.groupEnabled` (group DMs default false)
- `dm.groupChannels` (optional MPIM allowlist)

Multi-account precedence:

- `channels.slack.accounts.default.allowFrom` applies only to the `default` account.
- Named accounts inherit `channels.slack.allowFrom` when their own `allowFrom` is unset.
- Named accounts do not inherit `channels.slack.accounts.default.allowFrom`.

Pairing in DMs uses `openclaw pairing approve slack <code>`.

`channels.slack.groupPolicy` controls channel handling:

- `open`
- `allowlist`
- `disabled`

Channel allowlist lives under `channels.slack.channels`.Runtime note: if `channels.slack` is completely missing (env-only setup), runtime falls back to `groupPolicy="allowlist"` and logs a warning (even if `channels.defaults.groupPolicy` is set).Name/ID resolution:

- channel allowlist entries and DM allowlist entries are resolved at startup when token access allows
- unresolved entries are kept as configured
- inbound authorization matching is ID-first by default; direct username/slug matching requires `channels.slack.dangerouslyAllowNameMatching: true`

Channel messages are mention-gated by default.Mention sources:

- explicit app mention (`<@botId>`)
- mention regex patterns (`agents.list[].groupChat.mentionPatterns`, fallback `messages.groupChat.mentionPatterns`)
- implicit reply-to-bot thread behavior

Per-channel controls (`channels.slack.channels.<id|name>`):

- `requireMention`
- `users` (allowlist)
- `allowBots`
- `skills`
- `systemPrompt`
- `tools`, `toolsBySender`
- `toolsBySender` key format: `id:`, `e164:`, `username:`, `name:`, or `"*"` wildcard
(legacy unprefixed keys still map to `id:` only)

## [​](https://docs.openclaw.ai/channels/slack\#commands-and-slash-behavior)  Commands and slash behavior

- Native command auto-mode is **off** for Slack (`commands.native: "auto"` does not enable Slack native commands).
- Enable native Slack command handlers with `channels.slack.commands.native: true` (or global `commands.native: true`).
- When native commands are enabled, register matching slash commands in Slack (`/<command>` names).
- If native commands are not enabled, you can run a single configured slash command via `channels.slack.slashCommand`.
- Native arg menus now adapt their rendering strategy:
  - up to 5 options: button blocks
  - 6-100 options: static select menu
  - more than 100 options: external select with async option filtering when interactivity options handlers are available
  - if encoded option values exceed Slack limits, the flow falls back to buttons
- For long option payloads, Slash command argument menus use a confirm dialog before dispatching a selected value.

Default slash command settings:

- `enabled: false`
- `name: "openclaw"`
- `sessionPrefix: "slack:slash"`
- `ephemeral: true`

Slash sessions use isolated keys:

- `agent:<agentId>:slack:slash:<userId>`

and still route command execution against the target conversation session (`CommandTargetSessionKey`).

## [​](https://docs.openclaw.ai/channels/slack\#threading-sessions-and-reply-tags)  Threading, sessions, and reply tags

- DMs route as `direct`; channels as `channel`; MPIMs as `group`.
- With default `session.dmScope=main`, Slack DMs collapse to agent main session.
- Channel sessions: `agent:<agentId>:slack:channel:<channelId>`.
- Thread replies can create thread session suffixes (`:thread:<threadTs>`) when applicable.
- `channels.slack.thread.historyScope` default is `thread`; `thread.inheritParent` default is `false`.
- `channels.slack.thread.initialHistoryLimit` controls how many existing thread messages are fetched when a new thread session starts (default `20`; set `0` to disable).

Reply threading controls:

- `channels.slack.replyToMode`: `off|first|all` (default `off`)
- `channels.slack.replyToModeByChatType`: per `direct|group|channel`
- legacy fallback for direct chats: `channels.slack.dm.replyToMode`

Manual reply tags are supported:

- `[[reply_to_current]]`
- `[[reply_to:<id>]]`

Note: `replyToMode="off"` disables **all** reply threading in Slack, including explicit `[[reply_to_*]]` tags. This differs from Telegram, where explicit tags are still honored in `"off"` mode. The difference reflects the platform threading models: Slack threads hide messages from the channel, while Telegram replies remain visible in the main chat flow.

## [​](https://docs.openclaw.ai/channels/slack\#media-chunking-and-delivery)  Media, chunking, and delivery

Inbound attachments

Slack file attachments are downloaded from Slack-hosted private URLs (token-authenticated request flow) and written to the media store when fetch succeeds and size limits permit.Runtime inbound size cap defaults to `20MB` unless overridden by `channels.slack.mediaMaxMb`.

Outbound text and files

- text chunks use `channels.slack.textChunkLimit` (default 4000)
- `channels.slack.chunkMode="newline"` enables paragraph-first splitting
- file sends use Slack upload APIs and can include thread replies (`thread_ts`)
- outbound media cap follows `channels.slack.mediaMaxMb` when configured; otherwise channel sends use MIME-kind defaults from media pipeline

Delivery targets

Preferred explicit targets:

- `user:<id>` for DMs
- `channel:<id>` for channels

Slack DMs are opened via Slack conversation APIs when sending to user targets.

## [​](https://docs.openclaw.ai/channels/slack\#actions-and-gates)  Actions and gates

Slack actions are controlled by `channels.slack.actions.*`.Available action groups in current Slack tooling:

| Group | Default |
| --- | --- |
| messages | enabled |
| reactions | enabled |
| pins | enabled |
| memberInfo | enabled |
| emojiList | enabled |

## [​](https://docs.openclaw.ai/channels/slack\#events-and-operational-behavior)  Events and operational behavior

- Message edits/deletes/thread broadcasts are mapped into system events.
- Reaction add/remove events are mapped into system events.
- Member join/leave, channel created/renamed, and pin add/remove events are mapped into system events.
- Assistant thread status updates (for “is typing…” indicators in threads) use `assistant.threads.setStatus` and require bot scope `assistant:write`.
- `channel_id_changed` can migrate channel config keys when `configWrites` is enabled.
- Channel topic/purpose metadata is treated as untrusted context and can be injected into routing context.
- Block actions and modal interactions emit structured `Slack interaction: ...` system events with rich payload fields:

  - block actions: selected values, labels, picker values, and `workflow_*` metadata
  - modal `view_submission` and `view_closed` events with routed channel metadata and form inputs

## [​](https://docs.openclaw.ai/channels/slack\#ack-reactions)  Ack reactions

`ackReaction` sends an acknowledgement emoji while OpenClaw is processing an inbound message.Resolution order:

- `channels.slack.accounts.<accountId>.ackReaction`
- `channels.slack.ackReaction`
- `messages.ackReaction`
- agent identity emoji fallback (`agents.list[].identity.emoji`, else ”👀”)

Notes:

- Slack expects shortcodes (for example `"eyes"`).
- Use `""` to disable the reaction for a channel or account.

## [​](https://docs.openclaw.ai/channels/slack\#manifest-and-scope-checklist)  Manifest and scope checklist

Slack app manifest example

Copy

```
{
  "display_information": {
    "name": "OpenClaw",
    "description": "Slack connector for OpenClaw"
  },
  "features": {
    "bot_user": {
      "display_name": "OpenClaw",
      "always_online": false
    },
    "app_home": {
      "messages_tab_enabled": true,
      "messages_tab_read_only_enabled": false
    },
    "slash_commands": [\
      {\
        "command": "/openclaw",\
        "description": "Send a message to OpenClaw",\
        "should_escape": false\
      }\
    ]
  },
  "oauth_config": {
    "scopes": {
      "bot": [\
        "chat:write",\
        "channels:history",\
        "channels:read",\
        "groups:history",\
        "im:history",\
        "mpim:history",\
        "users:read",\
        "app_mentions:read",\
        "assistant:write",\
        "reactions:read",\
        "reactions:write",\
        "pins:read",\
        "pins:write",\
        "emoji:read",\
        "commands",\
        "files:read",\
        "files:write"\
      ]
    }
  },
  "settings": {
    "socket_mode_enabled": true,
    "event_subscriptions": {
      "bot_events": [\
        "app_mention",\
        "message.channels",\
        "message.groups",\
        "message.im",\
        "message.mpim",\
        "reaction_added",\
        "reaction_removed",\
        "member_joined_channel",\
        "member_left_channel",\
        "channel_rename",\
        "pin_added",\
        "pin_removed"\
      ]
    }
  }
}
```

Optional user-token scopes (read operations)

If you configure `channels.slack.userToken`, typical read scopes are:

- `channels:history`, `groups:history`, `im:history`, `mpim:history`
- `channels:read`, `groups:read`, `im:read`, `mpim:read`
- `users:read`
- `reactions:read`
- `pins:read`
- `emoji:read`
- `search:read` (if you depend on Slack search reads)

## [​](https://docs.openclaw.ai/channels/slack\#troubleshooting)  Troubleshooting

No replies in channels

Check, in order:

- `groupPolicy`
- channel allowlist (`channels.slack.channels`)
- `requireMention`
- per-channel `users` allowlist

Useful commands:

Copy

```
openclaw channels status --probe
openclaw logs --follow
openclaw doctor
```

DM messages ignored

Check:

- `channels.slack.dm.enabled`
- `channels.slack.dmPolicy` (or legacy `channels.slack.dm.policy`)
- pairing approvals / allowlist entries

Copy

```
openclaw pairing list slack
```

Socket mode not connecting

Validate bot + app tokens and Socket Mode enablement in Slack app settings.

HTTP mode not receiving events

Validate:

- signing secret
- webhook path
- Slack Request URLs (Events + Interactivity + Slash Commands)
- unique `webhookPath` per HTTP account

Native/slash commands not firing

Verify whether you intended:

- native command mode (`channels.slack.commands.native: true`) with matching slash commands registered in Slack
- or single slash command mode (`channels.slack.slashCommand.enabled: true`)

Also check `commands.useAccessGroups` and channel/user allowlists.

## [​](https://docs.openclaw.ai/channels/slack\#text-streaming)  Text streaming

OpenClaw supports Slack native text streaming via the Agents and AI Apps API.`channels.slack.streaming` controls live preview behavior:

- `off`: disable live preview streaming.
- `partial` (default): replace preview text with the latest partial output.
- `block`: append chunked preview updates.
- `progress`: show progress status text while generating, then send final text.

`channels.slack.nativeStreaming` controls Slack’s native streaming API (`chat.startStream` / `chat.appendStream` / `chat.stopStream`) when `streaming` is `partial` (default: `true`).Disable native Slack streaming (keep draft preview behavior):

Copy

```
channels:
  slack:
    streaming: partial
    nativeStreaming: false
```

Legacy keys:

- `channels.slack.streamMode` (`replace | status_final | append`) is auto-migrated to `channels.slack.streaming`.
- boolean `channels.slack.streaming` is auto-migrated to `channels.slack.nativeStreaming`.

### [​](https://docs.openclaw.ai/channels/slack\#requirements)  Requirements

1. Enable **Agents and AI Apps** in your Slack app settings.
2. Ensure the app has the `assistant:write` scope.
3. A reply thread must be available for that message. Thread selection still follows `replyToMode`.

### [​](https://docs.openclaw.ai/channels/slack\#behavior)  Behavior

- First text chunk starts a stream (`chat.startStream`).
- Later text chunks append to the same stream (`chat.appendStream`).
- End of reply finalizes stream (`chat.stopStream`).
- Media and non-text payloads fall back to normal delivery.
- If streaming fails mid-reply, OpenClaw falls back to normal delivery for remaining payloads.

## [​](https://docs.openclaw.ai/channels/slack\#configuration-reference-pointers)  Configuration reference pointers

Primary reference:

- [Configuration reference - Slack](https://docs.openclaw.ai/gateway/configuration-reference#slack)High-signal Slack fields:  - mode/auth: `mode`, `botToken`, `appToken`, `signingSecret`, `webhookPath`, `accounts.*`
  - DM access: `dm.enabled`, `dmPolicy`, `allowFrom` (legacy: `dm.policy`, `dm.allowFrom`), `dm.groupEnabled`, `dm.groupChannels`
  - compatibility toggle: `dangerouslyAllowNameMatching` (break-glass; keep off unless needed)
  - channel access: `groupPolicy`, `channels.*`, `channels.*.users`, `channels.*.requireMention`
  - threading/history: `replyToMode`, `replyToModeByChatType`, `thread.*`, `historyLimit`, `dmHistoryLimit`, `dms.*.historyLimit`
  - delivery: `textChunkLimit`, `chunkMode`, `mediaMaxMb`, `streaming`, `nativeStreaming`
  - ops/features: `configWrites`, `commands.native`, `slashCommand.*`, `actions.*`, `userToken`, `userTokenReadOnly`

## [​](https://docs.openclaw.ai/channels/slack\#related)  Related

- [Pairing](https://docs.openclaw.ai/channels/pairing)
- [Channel routing](https://docs.openclaw.ai/channels/channel-routing)
- [Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)
- [Configuration](https://docs.openclaw.ai/gateway/configuration)
- [Slash commands](https://docs.openclaw.ai/tools/slash-commands)

[IRC](https://docs.openclaw.ai/channels/irc) [Feishu](https://docs.openclaw.ai/channels/feishu)

Ctrl+I

---

---

<!-- Page: Slack (Events API) -->

## Slack

**Source:** https://docs.openclaw.ai/channels/slack?plain=1

[Skip to main content](https://docs.openclaw.ai/channels/slack?plain=1#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Slack

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Slack](https://docs.openclaw.ai/channels/slack?plain=1#slack)
- [Quick setup](https://docs.openclaw.ai/channels/slack?plain=1#quick-setup)
- [Token model](https://docs.openclaw.ai/channels/slack?plain=1#token-model)
- [Access control and routing](https://docs.openclaw.ai/channels/slack?plain=1#access-control-and-routing)
- [Commands and slash behavior](https://docs.openclaw.ai/channels/slack?plain=1#commands-and-slash-behavior)
- [Threading, sessions, and reply tags](https://docs.openclaw.ai/channels/slack?plain=1#threading-sessions-and-reply-tags)
- [Media, chunking, and delivery](https://docs.openclaw.ai/channels/slack?plain=1#media-chunking-and-delivery)
- [Actions and gates](https://docs.openclaw.ai/channels/slack?plain=1#actions-and-gates)
- [Events and operational behavior](https://docs.openclaw.ai/channels/slack?plain=1#events-and-operational-behavior)
- [Ack reactions](https://docs.openclaw.ai/channels/slack?plain=1#ack-reactions)
- [Manifest and scope checklist](https://docs.openclaw.ai/channels/slack?plain=1#manifest-and-scope-checklist)
- [Troubleshooting](https://docs.openclaw.ai/channels/slack?plain=1#troubleshooting)
- [Text streaming](https://docs.openclaw.ai/channels/slack?plain=1#text-streaming)
- [Requirements](https://docs.openclaw.ai/channels/slack?plain=1#requirements)
- [Behavior](https://docs.openclaw.ai/channels/slack?plain=1#behavior)
- [Configuration reference pointers](https://docs.openclaw.ai/channels/slack?plain=1#configuration-reference-pointers)
- [Related](https://docs.openclaw.ai/channels/slack?plain=1#related)

# [​](https://docs.openclaw.ai/channels/slack?plain=1\#slack)  Slack

Status: production-ready for DMs + channels via Slack app integrations. Default mode is Socket Mode; HTTP Events API mode is also supported.

[**Pairing** \\
\\
Slack DMs default to pairing mode.](https://docs.openclaw.ai/channels/pairing) [**Slash commands** \\
\\
Native command behavior and command catalog.](https://docs.openclaw.ai/tools/slash-commands) [**Channel troubleshooting** \\
\\
Cross-channel diagnostics and repair playbooks.](https://docs.openclaw.ai/channels/troubleshooting)

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#quick-setup)  Quick setup

- Socket Mode (default)

- HTTP Events API mode


1

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Create Slack app and tokens

In Slack app settings:

- enable **Socket Mode**
- create **App Token** (`xapp-...`) with `connections:write`
- install app and copy **Bot Token** (`xoxb-...`)

2

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Configure OpenClaw

Copy

```
{
  channels: {
    slack: {
      enabled: true,
      mode: "socket",
      appToken: "xapp-...",
      botToken: "xoxb-...",
    },
  },
}
```

Env fallback (default account only):

Copy

```
SLACK_APP_TOKEN=xapp-...
SLACK_BOT_TOKEN=xoxb-...
```

3

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Subscribe app events

Subscribe bot events for:

- `app_mention`
- `message.channels`, `message.groups`, `message.im`, `message.mpim`
- `reaction_added`, `reaction_removed`
- `member_joined_channel`, `member_left_channel`
- `channel_rename`
- `pin_added`, `pin_removed`

Also enable App Home **Messages Tab** for DMs.

4

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Start gateway

Copy

```
openclaw gateway
```

1

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Configure Slack app for HTTP

- set mode to HTTP (`channels.slack.mode="http"`)
- copy Slack **Signing Secret**
- set Event Subscriptions + Interactivity + Slash command Request URL to the same webhook path (default `/slack/events`)

2

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Configure OpenClaw HTTP mode

Copy

```
{
  channels: {
    slack: {
      enabled: true,
      mode: "http",
      botToken: "xoxb-...",
      signingSecret: "your-signing-secret",
      webhookPath: "/slack/events",
    },
  },
}
```

3

[Navigate to header](https://docs.openclaw.ai/channels/slack?plain=1#)

Use unique webhook paths for multi-account HTTP

Per-account HTTP mode is supported.Give each account a distinct `webhookPath` so registrations do not collide.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#token-model)  Token model

- `botToken` \+ `appToken` are required for Socket Mode.
- HTTP mode requires `botToken` \+ `signingSecret`.
- Config tokens override env fallback.
- `SLACK_BOT_TOKEN` / `SLACK_APP_TOKEN` env fallback applies only to the default account.
- `userToken` (`xoxp-...`) is config-only (no env fallback) and defaults to read-only behavior (`userTokenReadOnly: true`).
- Optional: add `chat:write.customize` if you want outgoing messages to use the active agent identity (custom `username` and icon). `icon_emoji` uses `:emoji_name:` syntax.

For actions/directory reads, user token can be preferred when configured. For writes, bot token remains preferred; user-token writes are only allowed when `userTokenReadOnly: false` and bot token is unavailable.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#access-control-and-routing)  Access control and routing

- DM policy

- Channel policy

- Mentions and channel users


`channels.slack.dmPolicy` controls DM access (legacy: `channels.slack.dm.policy`):

- `pairing` (default)
- `allowlist`
- `open` (requires `channels.slack.allowFrom` to include `"*"`; legacy: `channels.slack.dm.allowFrom`)
- `disabled`

DM flags:

- `dm.enabled` (default true)
- `channels.slack.allowFrom` (preferred)
- `dm.allowFrom` (legacy)
- `dm.groupEnabled` (group DMs default false)
- `dm.groupChannels` (optional MPIM allowlist)

Pairing in DMs uses `openclaw pairing approve slack <code>`.

`channels.slack.groupPolicy` controls channel handling:

- `open`
- `allowlist`
- `disabled`

Channel allowlist lives under `channels.slack.channels`.Runtime note: if `channels.slack` is completely missing (env-only setup), runtime falls back to `groupPolicy="allowlist"` and logs a warning (even if `channels.defaults.groupPolicy` is set).Name/ID resolution:

- channel allowlist entries and DM allowlist entries are resolved at startup when token access allows
- unresolved entries are kept as configured
- inbound authorization matching is ID-first by default; direct username/slug matching requires `channels.slack.dangerouslyAllowNameMatching: true`

Channel messages are mention-gated by default.Mention sources:

- explicit app mention (`<@botId>`)
- mention regex patterns (`agents.list[].groupChat.mentionPatterns`, fallback `messages.groupChat.mentionPatterns`)
- implicit reply-to-bot thread behavior

Per-channel controls (`channels.slack.channels.<id|name>`):

- `requireMention`
- `users` (allowlist)
- `allowBots`
- `skills`
- `systemPrompt`
- `tools`, `toolsBySender`
- `toolsBySender` key format: `id:`, `e164:`, `username:`, `name:`, or `"*"` wildcard
(legacy unprefixed keys still map to `id:` only)

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#commands-and-slash-behavior)  Commands and slash behavior

- Native command auto-mode is **off** for Slack (`commands.native: "auto"` does not enable Slack native commands).
- Enable native Slack command handlers with `channels.slack.commands.native: true` (or global `commands.native: true`).
- When native commands are enabled, register matching slash commands in Slack (`/<command>` names).
- If native commands are not enabled, you can run a single configured slash command via `channels.slack.slashCommand`.
- Native arg menus now adapt their rendering strategy:
  - up to 5 options: button blocks
  - 6-100 options: static select menu
  - more than 100 options: external select with async option filtering when interactivity options handlers are available
  - if encoded option values exceed Slack limits, the flow falls back to buttons
- For long option payloads, Slash command argument menus use a confirm dialog before dispatching a selected value.

Default slash command settings:

- `enabled: false`
- `name: "openclaw"`
- `sessionPrefix: "slack:slash"`
- `ephemeral: true`

Slash sessions use isolated keys:

- `agent:<agentId>:slack:slash:<userId>`

and still route command execution against the target conversation session (`CommandTargetSessionKey`).

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#threading-sessions-and-reply-tags)  Threading, sessions, and reply tags

- DMs route as `direct`; channels as `channel`; MPIMs as `group`.
- With default `session.dmScope=main`, Slack DMs collapse to agent main session.
- Channel sessions: `agent:<agentId>:slack:channel:<channelId>`.
- Thread replies can create thread session suffixes (`:thread:<threadTs>`) when applicable.
- `channels.slack.thread.historyScope` default is `thread`; `thread.inheritParent` default is `false`.
- `channels.slack.thread.initialHistoryLimit` controls how many existing thread messages are fetched when a new thread session starts (default `20`; set `0` to disable).

Reply threading controls:

- `channels.slack.replyToMode`: `off|first|all` (default `off`)
- `channels.slack.replyToModeByChatType`: per `direct|group|channel`
- legacy fallback for direct chats: `channels.slack.dm.replyToMode`

Manual reply tags are supported:

- `[[reply_to_current]]`
- `[[reply_to:<id>]]`

Note: `replyToMode="off"` disables **all** reply threading in Slack, including explicit `[[reply_to_*]]` tags. This differs from Telegram, where explicit tags are still honored in `"off"` mode. The difference reflects the platform threading models: Slack threads hide messages from the channel, while Telegram replies remain visible in the main chat flow.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#media-chunking-and-delivery)  Media, chunking, and delivery

Inbound attachments

Slack file attachments are downloaded from Slack-hosted private URLs (token-authenticated request flow) and written to the media store when fetch succeeds and size limits permit.Runtime inbound size cap defaults to `20MB` unless overridden by `channels.slack.mediaMaxMb`.

Outbound text and files

- text chunks use `channels.slack.textChunkLimit` (default 4000)
- `channels.slack.chunkMode="newline"` enables paragraph-first splitting
- file sends use Slack upload APIs and can include thread replies (`thread_ts`)
- outbound media cap follows `channels.slack.mediaMaxMb` when configured; otherwise channel sends use MIME-kind defaults from media pipeline

Delivery targets

Preferred explicit targets:

- `user:<id>` for DMs
- `channel:<id>` for channels

Slack DMs are opened via Slack conversation APIs when sending to user targets.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#actions-and-gates)  Actions and gates

Slack actions are controlled by `channels.slack.actions.*`.Available action groups in current Slack tooling:

| Group | Default |
| --- | --- |
| messages | enabled |
| reactions | enabled |
| pins | enabled |
| memberInfo | enabled |
| emojiList | enabled |

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#events-and-operational-behavior)  Events and operational behavior

- Message edits/deletes/thread broadcasts are mapped into system events.
- Reaction add/remove events are mapped into system events.
- Member join/leave, channel created/renamed, and pin add/remove events are mapped into system events.
- Assistant thread status updates (for “is typing…” indicators in threads) use `assistant.threads.setStatus` and require bot scope `assistant:write`.
- `channel_id_changed` can migrate channel config keys when `configWrites` is enabled.
- Channel topic/purpose metadata is treated as untrusted context and can be injected into routing context.
- Block actions and modal interactions emit structured `Slack interaction: ...` system events with rich payload fields:

  - block actions: selected values, labels, picker values, and `workflow_*` metadata
  - modal `view_submission` and `view_closed` events with routed channel metadata and form inputs

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#ack-reactions)  Ack reactions

`ackReaction` sends an acknowledgement emoji while OpenClaw is processing an inbound message.Resolution order:

- `channels.slack.accounts.<accountId>.ackReaction`
- `channels.slack.ackReaction`
- `messages.ackReaction`
- agent identity emoji fallback (`agents.list[].identity.emoji`, else ”👀”)

Notes:

- Slack expects shortcodes (for example `"eyes"`).
- Use `""` to disable the reaction for a channel or account.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#manifest-and-scope-checklist)  Manifest and scope checklist

Slack app manifest example

Copy

```
{
  "display_information": {
    "name": "OpenClaw",
    "description": "Slack connector for OpenClaw"
  },
  "features": {
    "bot_user": {
      "display_name": "OpenClaw",
      "always_online": false
    },
    "app_home": {
      "messages_tab_enabled": true,
      "messages_tab_read_only_enabled": false
    },
    "slash_commands": [\
      {\
        "command": "/openclaw",\
        "description": "Send a message to OpenClaw",\
        "should_escape": false\
      }\
    ]
  },
  "oauth_config": {
    "scopes": {
      "bot": [\
        "chat:write",\
        "channels:history",\
        "channels:read",\
        "groups:history",\
        "im:history",\
        "mpim:history",\
        "users:read",\
        "app_mentions:read",\
        "assistant:write",\
        "reactions:read",\
        "reactions:write",\
        "pins:read",\
        "pins:write",\
        "emoji:read",\
        "commands",\
        "files:read",\
        "files:write"\
      ]
    }
  },
  "settings": {
    "socket_mode_enabled": true,
    "event_subscriptions": {
      "bot_events": [\
        "app_mention",\
        "message.channels",\
        "message.groups",\
        "message.im",\
        "message.mpim",\
        "reaction_added",\
        "reaction_removed",\
        "member_joined_channel",\
        "member_left_channel",\
        "channel_rename",\
        "pin_added",\
        "pin_removed"\
      ]
    }
  }
}
```

Optional user-token scopes (read operations)

If you configure `channels.slack.userToken`, typical read scopes are:

- `channels:history`, `groups:history`, `im:history`, `mpim:history`
- `channels:read`, `groups:read`, `im:read`, `mpim:read`
- `users:read`
- `reactions:read`
- `pins:read`
- `emoji:read`
- `search:read` (if you depend on Slack search reads)

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#troubleshooting)  Troubleshooting

No replies in channels

Check, in order:

- `groupPolicy`
- channel allowlist (`channels.slack.channels`)
- `requireMention`
- per-channel `users` allowlist

Useful commands:

Copy

```
openclaw channels status --probe
openclaw logs --follow
openclaw doctor
```

DM messages ignored

Check:

- `channels.slack.dm.enabled`
- `channels.slack.dmPolicy` (or legacy `channels.slack.dm.policy`)
- pairing approvals / allowlist entries

Copy

```
openclaw pairing list slack
```

Socket mode not connecting

Validate bot + app tokens and Socket Mode enablement in Slack app settings.

HTTP mode not receiving events

Validate:

- signing secret
- webhook path
- Slack Request URLs (Events + Interactivity + Slash Commands)
- unique `webhookPath` per HTTP account

Native/slash commands not firing

Verify whether you intended:

- native command mode (`channels.slack.commands.native: true`) with matching slash commands registered in Slack
- or single slash command mode (`channels.slack.slashCommand.enabled: true`)

Also check `commands.useAccessGroups` and channel/user allowlists.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#text-streaming)  Text streaming

OpenClaw supports Slack native text streaming via the Agents and AI Apps API.`channels.slack.streaming` controls live preview behavior:

- `off`: disable live preview streaming.
- `partial` (default): replace preview text with the latest partial output.
- `block`: append chunked preview updates.
- `progress`: show progress status text while generating, then send final text.

`channels.slack.nativeStreaming` controls Slack’s native streaming API (`chat.startStream` / `chat.appendStream` / `chat.stopStream`) when `streaming` is `partial` (default: `true`).Disable native Slack streaming (keep draft preview behavior):

Copy

```
channels:
  slack:
    streaming: partial
    nativeStreaming: false
```

Legacy keys:

- `channels.slack.streamMode` (`replace | status_final | append`) is auto-migrated to `channels.slack.streaming`.
- boolean `channels.slack.streaming` is auto-migrated to `channels.slack.nativeStreaming`.

### [​](https://docs.openclaw.ai/channels/slack?plain=1\#requirements)  Requirements

1. Enable **Agents and AI Apps** in your Slack app settings.
2. Ensure the app has the `assistant:write` scope.
3. A reply thread must be available for that message. Thread selection still follows `replyToMode`.

### [​](https://docs.openclaw.ai/channels/slack?plain=1\#behavior)  Behavior

- First text chunk starts a stream (`chat.startStream`).
- Later text chunks append to the same stream (`chat.appendStream`).
- End of reply finalizes stream (`chat.stopStream`).
- Media and non-text payloads fall back to normal delivery.
- If streaming fails mid-reply, OpenClaw falls back to normal delivery for remaining payloads.

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#configuration-reference-pointers)  Configuration reference pointers

Primary reference:

- [Configuration reference - Slack](https://docs.openclaw.ai/gateway/configuration-reference#slack)High-signal Slack fields:  - mode/auth: `mode`, `botToken`, `appToken`, `signingSecret`, `webhookPath`, `accounts.*`
  - DM access: `dm.enabled`, `dmPolicy`, `allowFrom` (legacy: `dm.policy`, `dm.allowFrom`), `dm.groupEnabled`, `dm.groupChannels`
  - compatibility toggle: `dangerouslyAllowNameMatching` (break-glass; keep off unless needed)
  - channel access: `groupPolicy`, `channels.*`, `channels.*.users`, `channels.*.requireMention`
  - threading/history: `replyToMode`, `replyToModeByChatType`, `thread.*`, `historyLimit`, `dmHistoryLimit`, `dms.*.historyLimit`
  - delivery: `textChunkLimit`, `chunkMode`, `mediaMaxMb`, `streaming`, `nativeStreaming`
  - ops/features: `configWrites`, `commands.native`, `slashCommand.*`, `actions.*`, `userToken`, `userTokenReadOnly`

## [​](https://docs.openclaw.ai/channels/slack?plain=1\#related)  Related

- [Pairing](https://docs.openclaw.ai/channels/pairing)
- [Channel routing](https://docs.openclaw.ai/channels/channel-routing)
- [Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)
- [Configuration](https://docs.openclaw.ai/gateway/configuration)
- [Slash commands](https://docs.openclaw.ai/tools/slash-commands)

[IRC](https://docs.openclaw.ai/channels/irc) [Feishu](https://docs.openclaw.ai/channels/feishu)

Ctrl+I

---

---

<!-- Page: Microsoft Teams -->

## Microsoft Teams

**Source:** https://docs.openclaw.ai/channels/msteams

[Skip to main content](https://docs.openclaw.ai/channels/msteams#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Microsoft Teams

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Microsoft Teams (plugin)](https://docs.openclaw.ai/channels/msteams#microsoft-teams-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/msteams#plugin-required)
- [Quick setup (beginner)](https://docs.openclaw.ai/channels/msteams#quick-setup-beginner)
- [Goals](https://docs.openclaw.ai/channels/msteams#goals)
- [Config writes](https://docs.openclaw.ai/channels/msteams#config-writes)
- [Access control (DMs + groups)](https://docs.openclaw.ai/channels/msteams#access-control-dms-%2B-groups)
- [How it works](https://docs.openclaw.ai/channels/msteams#how-it-works)
- [Azure Bot Setup (Prerequisites)](https://docs.openclaw.ai/channels/msteams#azure-bot-setup-prerequisites)
- [Step 1: Create Azure Bot](https://docs.openclaw.ai/channels/msteams#step-1-create-azure-bot)
- [Step 2: Get Credentials](https://docs.openclaw.ai/channels/msteams#step-2-get-credentials)
- [Step 3: Configure Messaging Endpoint](https://docs.openclaw.ai/channels/msteams#step-3-configure-messaging-endpoint)
- [Step 4: Enable Teams Channel](https://docs.openclaw.ai/channels/msteams#step-4-enable-teams-channel)
- [Local Development (Tunneling)](https://docs.openclaw.ai/channels/msteams#local-development-tunneling)
- [Teams Developer Portal (Alternative)](https://docs.openclaw.ai/channels/msteams#teams-developer-portal-alternative)
- [Testing the Bot](https://docs.openclaw.ai/channels/msteams#testing-the-bot)
- [Setup (minimal text-only)](https://docs.openclaw.ai/channels/msteams#setup-minimal-text-only)
- [History context](https://docs.openclaw.ai/channels/msteams#history-context)
- [Current Teams RSC Permissions (Manifest)](https://docs.openclaw.ai/channels/msteams#current-teams-rsc-permissions-manifest)
- [Example Teams Manifest (redacted)](https://docs.openclaw.ai/channels/msteams#example-teams-manifest-redacted)
- [Manifest caveats (must-have fields)](https://docs.openclaw.ai/channels/msteams#manifest-caveats-must-have-fields)
- [Updating an existing app](https://docs.openclaw.ai/channels/msteams#updating-an-existing-app)
- [Capabilities: RSC only vs Graph](https://docs.openclaw.ai/channels/msteams#capabilities-rsc-only-vs-graph)
- [With Teams RSC only (app installed, no Graph API permissions)](https://docs.openclaw.ai/channels/msteams#with-teams-rsc-only-app-installed-no-graph-api-permissions)
- [With Teams RSC + Microsoft Graph Application permissions](https://docs.openclaw.ai/channels/msteams#with-teams-rsc-%2B-microsoft-graph-application-permissions)
- [RSC vs Graph API](https://docs.openclaw.ai/channels/msteams#rsc-vs-graph-api)
- [Graph-enabled media + history (required for channels)](https://docs.openclaw.ai/channels/msteams#graph-enabled-media-%2B-history-required-for-channels)
- [Known Limitations](https://docs.openclaw.ai/channels/msteams#known-limitations)
- [Webhook timeouts](https://docs.openclaw.ai/channels/msteams#webhook-timeouts)
- [Formatting](https://docs.openclaw.ai/channels/msteams#formatting)
- [Configuration](https://docs.openclaw.ai/channels/msteams#configuration)
- [Routing & Sessions](https://docs.openclaw.ai/channels/msteams#routing-%26-sessions)
- [Reply Style: Threads vs Posts](https://docs.openclaw.ai/channels/msteams#reply-style-threads-vs-posts)
- [Attachments & Images](https://docs.openclaw.ai/channels/msteams#attachments-%26-images)
- [Sending files in group chats](https://docs.openclaw.ai/channels/msteams#sending-files-in-group-chats)
- [Why group chats need SharePoint](https://docs.openclaw.ai/channels/msteams#why-group-chats-need-sharepoint)
- [Setup](https://docs.openclaw.ai/channels/msteams#setup)
- [Sharing behavior](https://docs.openclaw.ai/channels/msteams#sharing-behavior)
- [Fallback behavior](https://docs.openclaw.ai/channels/msteams#fallback-behavior)
- [Files stored location](https://docs.openclaw.ai/channels/msteams#files-stored-location)
- [Polls (Adaptive Cards)](https://docs.openclaw.ai/channels/msteams#polls-adaptive-cards)
- [Adaptive Cards (arbitrary)](https://docs.openclaw.ai/channels/msteams#adaptive-cards-arbitrary)
- [Target formats](https://docs.openclaw.ai/channels/msteams#target-formats)
- [Proactive messaging](https://docs.openclaw.ai/channels/msteams#proactive-messaging)
- [Team and Channel IDs (Common Gotcha)](https://docs.openclaw.ai/channels/msteams#team-and-channel-ids-common-gotcha)
- [Private Channels](https://docs.openclaw.ai/channels/msteams#private-channels)
- [Troubleshooting](https://docs.openclaw.ai/channels/msteams#troubleshooting)
- [Common issues](https://docs.openclaw.ai/channels/msteams#common-issues)
- [Manifest upload errors](https://docs.openclaw.ai/channels/msteams#manifest-upload-errors)
- [RSC permissions not working](https://docs.openclaw.ai/channels/msteams#rsc-permissions-not-working)
- [References](https://docs.openclaw.ai/channels/msteams#references)

# [​](https://docs.openclaw.ai/channels/msteams\#microsoft-teams-plugin)  Microsoft Teams (plugin)

> “Abandon all hope, ye who enter here.”

Updated: 2026-01-21Status: text + DM attachments are supported; channel/group file sending requires `sharePointSiteId` \+ Graph permissions (see [Sending files in group chats](https://docs.openclaw.ai/channels/msteams#sending-files-in-group-chats)). Polls are sent via Adaptive Cards.

## [​](https://docs.openclaw.ai/channels/msteams\#plugin-required)  Plugin required

Microsoft Teams ships as a plugin and is not bundled with the core install.**Breaking change (2026.1.15):** MS Teams moved out of core. If you use it, you must install the plugin.Explainable: keeps core installs lighter and lets MS Teams dependencies update independently.Install via CLI (npm registry):

Copy

```
openclaw plugins install @openclaw/msteams
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/msteams
```

If you choose Teams during configure/onboarding and a git checkout is detected,
OpenClaw will offer the local install path automatically.Details: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/channels/msteams\#quick-setup-beginner)  Quick setup (beginner)

1. Install the Microsoft Teams plugin.
2. Create an **Azure Bot** (App ID + client secret + tenant ID).
3. Configure OpenClaw with those credentials.
4. Expose `/api/messages` (port 3978 by default) via a public URL or tunnel.
5. Install the Teams app package and start the gateway.

Minimal config:

Copy

```
{
  channels: {
    msteams: {
      enabled: true,
      appId: "<APP_ID>",
      appPassword: "<APP_PASSWORD>",
      tenantId: "<TENANT_ID>",
      webhook: { port: 3978, path: "/api/messages" },
    },
  },
}
```

Note: group chats are blocked by default (`channels.msteams.groupPolicy: "allowlist"`). To allow group replies, set `channels.msteams.groupAllowFrom` (or use `groupPolicy: "open"` to allow any member, mention-gated).

## [​](https://docs.openclaw.ai/channels/msteams\#goals)  Goals

- Talk to OpenClaw via Teams DMs, group chats, or channels.
- Keep routing deterministic: replies always go back to the channel they arrived on.
- Default to safe channel behavior (mentions required unless configured otherwise).

## [​](https://docs.openclaw.ai/channels/msteams\#config-writes)  Config writes

By default, Microsoft Teams is allowed to write config updates triggered by `/config set|unset` (requires `commands.config: true`).Disable with:

Copy

```
{
  channels: { msteams: { configWrites: false } },
}
```

## [​](https://docs.openclaw.ai/channels/msteams\#access-control-dms-+-groups)  Access control (DMs + groups)

**DM access**

- Default: `channels.msteams.dmPolicy = "pairing"`. Unknown senders are ignored until approved.
- `channels.msteams.allowFrom` should use stable AAD object IDs.
- UPNs/display names are mutable; direct matching is disabled by default and only enabled with `channels.msteams.dangerouslyAllowNameMatching: true`.
- The wizard can resolve names to IDs via Microsoft Graph when credentials allow.

**Group access**

- Default: `channels.msteams.groupPolicy = "allowlist"` (blocked unless you add `groupAllowFrom`). Use `channels.defaults.groupPolicy` to override the default when unset.
- `channels.msteams.groupAllowFrom` controls which senders can trigger in group chats/channels (falls back to `channels.msteams.allowFrom`).
- Set `groupPolicy: "open"` to allow any member (still mention‑gated by default).
- To allow **no channels**, set `channels.msteams.groupPolicy: "disabled"`.

Example:

Copy

```
{
  channels: {
    msteams: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["user@org.com"],
    },
  },
}
```

**Teams + channel allowlist**

- Scope group/channel replies by listing teams and channels under `channels.msteams.teams`.
- Keys can be team IDs or names; channel keys can be conversation IDs or names.
- When `groupPolicy="allowlist"` and a teams allowlist is present, only listed teams/channels are accepted (mention‑gated).
- The configure wizard accepts `Team/Channel` entries and stores them for you.
- On startup, OpenClaw resolves team/channel and user allowlist names to IDs (when Graph permissions allow)
and logs the mapping; unresolved entries are kept as typed.

Example:

Copy

```
{
  channels: {
    msteams: {
      groupPolicy: "allowlist",
      teams: {
        "My Team": {
          channels: {
            General: { requireMention: true },
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/msteams\#how-it-works)  How it works

1. Install the Microsoft Teams plugin.
2. Create an **Azure Bot** (App ID + secret + tenant ID).
3. Build a **Teams app package** that references the bot and includes the RSC permissions below.
4. Upload/install the Teams app into a team (or personal scope for DMs).
5. Configure `msteams` in `~/.openclaw/openclaw.json` (or env vars) and start the gateway.
6. The gateway listens for Bot Framework webhook traffic on `/api/messages` by default.

## [​](https://docs.openclaw.ai/channels/msteams\#azure-bot-setup-prerequisites)  Azure Bot Setup (Prerequisites)

Before configuring OpenClaw, you need to create an Azure Bot resource.

### [​](https://docs.openclaw.ai/channels/msteams\#step-1-create-azure-bot)  Step 1: Create Azure Bot

1. Go to [Create Azure Bot](https://portal.azure.com/#create/Microsoft.AzureBot)
2. Fill in the **Basics** tab:





| Field | Value |
| --- | --- |
| **Bot handle** | Your bot name, e.g., `openclaw-msteams` (must be unique) |
| **Subscription** | Select your Azure subscription |
| **Resource group** | Create new or use existing |
| **Pricing tier** | **Free** for dev/testing |
| **Type of App** | **Single Tenant** (recommended - see note below) |
| **Creation type** | **Create new Microsoft App ID** |


> **Deprecation notice:** Creation of new multi-tenant bots was deprecated after 2025-07-31. Use **Single Tenant** for new bots.

3. Click **Review + create** → **Create** (wait ~1-2 minutes)

### [​](https://docs.openclaw.ai/channels/msteams\#step-2-get-credentials)  Step 2: Get Credentials

1. Go to your Azure Bot resource → **Configuration**
2. Copy **Microsoft App ID** → this is your `appId`
3. Click **Manage Password** → go to the App Registration
4. Under **Certificates & secrets** → **New client secret** → copy the **Value** → this is your `appPassword`
5. Go to **Overview** → copy **Directory (tenant) ID** → this is your `tenantId`

### [​](https://docs.openclaw.ai/channels/msteams\#step-3-configure-messaging-endpoint)  Step 3: Configure Messaging Endpoint

1. In Azure Bot → **Configuration**
2. Set **Messaging endpoint** to your webhook URL:

   - Production: `https://your-domain.com/api/messages`
   - Local dev: Use a tunnel (see [Local Development](https://docs.openclaw.ai/channels/msteams#local-development-tunneling) below)

### [​](https://docs.openclaw.ai/channels/msteams\#step-4-enable-teams-channel)  Step 4: Enable Teams Channel

1. In Azure Bot → **Channels**
2. Click **Microsoft Teams** → Configure → Save
3. Accept the Terms of Service

## [​](https://docs.openclaw.ai/channels/msteams\#local-development-tunneling)  Local Development (Tunneling)

Teams can’t reach `localhost`. Use a tunnel for local development:**Option A: ngrok**

Copy

```
ngrok http 3978
# Copy the https URL, e.g., https://abc123.ngrok.io
# Set messaging endpoint to: https://abc123.ngrok.io/api/messages
```

**Option B: Tailscale Funnel**

Copy

```
tailscale funnel 3978
# Use your Tailscale funnel URL as the messaging endpoint
```

## [​](https://docs.openclaw.ai/channels/msteams\#teams-developer-portal-alternative)  Teams Developer Portal (Alternative)

Instead of manually creating a manifest ZIP, you can use the [Teams Developer Portal](https://dev.teams.microsoft.com/apps):

1. Click **\+ New app**
2. Fill in basic info (name, description, developer info)
3. Go to **App features** → **Bot**
4. Select **Enter a bot ID manually** and paste your Azure Bot App ID
5. Check scopes: **Personal**, **Team**, **Group Chat**
6. Click **Distribute** → **Download app package**
7. In Teams: **Apps** → **Manage your apps** → **Upload a custom app** → select the ZIP

This is often easier than hand-editing JSON manifests.

## [​](https://docs.openclaw.ai/channels/msteams\#testing-the-bot)  Testing the Bot

**Option A: Azure Web Chat (verify webhook first)**

1. In Azure Portal → your Azure Bot resource → **Test in Web Chat**
2. Send a message - you should see a response
3. This confirms your webhook endpoint works before Teams setup

**Option B: Teams (after app installation)**

1. Install the Teams app (sideload or org catalog)
2. Find the bot in Teams and send a DM
3. Check gateway logs for incoming activity

## [​](https://docs.openclaw.ai/channels/msteams\#setup-minimal-text-only)  Setup (minimal text-only)

1. **Install the Microsoft Teams plugin**   - From npm: `openclaw plugins install @openclaw/msteams`
   - From a local checkout: `openclaw plugins install ./extensions/msteams`
2. **Bot registration**   - Create an Azure Bot (see above) and note:
     - App ID
     - Client secret (App password)
     - Tenant ID (single-tenant)
3. **Teams app manifest**   - Include a `bot` entry with `botId = <App ID>`.
   - Scopes: `personal`, `team`, `groupChat`.
   - `supportsFiles: true` (required for personal scope file handling).
   - Add RSC permissions (below).
   - Create icons: `outline.png` (32x32) and `color.png` (192x192).
   - Zip all three files together: `manifest.json`, `outline.png`, `color.png`.
4. **Configure OpenClaw**






Copy











```
{
     "msteams": {
       "enabled": true,
       "appId": "<APP_ID>",
       "appPassword": "<APP_PASSWORD>",
       "tenantId": "<TENANT_ID>",
       "webhook": { "port": 3978, "path": "/api/messages" }
     }
}
```










You can also use environment variables instead of config keys:   - `MSTEAMS_APP_ID`
   - `MSTEAMS_APP_PASSWORD`
   - `MSTEAMS_TENANT_ID`
5. **Bot endpoint**   - Set the Azure Bot Messaging Endpoint to:
     - `https://<host>:3978/api/messages` (or your chosen path/port).
6. **Run the gateway**   - The Teams channel starts automatically when the plugin is installed and `msteams` config exists with credentials.

## [​](https://docs.openclaw.ai/channels/msteams\#history-context)  History context

- `channels.msteams.historyLimit` controls how many recent channel/group messages are wrapped into the prompt.
- Falls back to `messages.groupChat.historyLimit`. Set `0` to disable (default 50).
- DM history can be limited with `channels.msteams.dmHistoryLimit` (user turns). Per-user overrides: `channels.msteams.dms["<user_id>"].historyLimit`.

## [​](https://docs.openclaw.ai/channels/msteams\#current-teams-rsc-permissions-manifest)  Current Teams RSC Permissions (Manifest)

These are the **existing resourceSpecific permissions** in our Teams app manifest. They only apply inside the team/chat where the app is installed.**For channels (team scope):**

- `ChannelMessage.Read.Group` (Application) - receive all channel messages without @mention
- `ChannelMessage.Send.Group` (Application)
- `Member.Read.Group` (Application)
- `Owner.Read.Group` (Application)
- `ChannelSettings.Read.Group` (Application)
- `TeamMember.Read.Group` (Application)
- `TeamSettings.Read.Group` (Application)

**For group chats:**

- `ChatMessage.Read.Chat` (Application) - receive all group chat messages without @mention

## [​](https://docs.openclaw.ai/channels/msteams\#example-teams-manifest-redacted)  Example Teams Manifest (redacted)

Minimal, valid example with the required fields. Replace IDs and URLs.

Copy

```
{
  "$schema": "https://developer.microsoft.com/en-us/json-schemas/teams/v1.23/MicrosoftTeams.schema.json",
  "manifestVersion": "1.23",
  "version": "1.0.0",
  "id": "00000000-0000-0000-0000-000000000000",
  "name": { "short": "OpenClaw" },
  "developer": {
    "name": "Your Org",
    "websiteUrl": "https://example.com",
    "privacyUrl": "https://example.com/privacy",
    "termsOfUseUrl": "https://example.com/terms"
  },
  "description": { "short": "OpenClaw in Teams", "full": "OpenClaw in Teams" },
  "icons": { "outline": "outline.png", "color": "color.png" },
  "accentColor": "#5B6DEF",
  "bots": [\
    {\
      "botId": "11111111-1111-1111-1111-111111111111",\
      "scopes": ["personal", "team", "groupChat"],\
      "isNotificationOnly": false,\
      "supportsCalling": false,\
      "supportsVideo": false,\
      "supportsFiles": true\
    }\
  ],
  "webApplicationInfo": {
    "id": "11111111-1111-1111-1111-111111111111"
  },
  "authorization": {
    "permissions": {
      "resourceSpecific": [\
        { "name": "ChannelMessage.Read.Group", "type": "Application" },\
        { "name": "ChannelMessage.Send.Group", "type": "Application" },\
        { "name": "Member.Read.Group", "type": "Application" },\
        { "name": "Owner.Read.Group", "type": "Application" },\
        { "name": "ChannelSettings.Read.Group", "type": "Application" },\
        { "name": "TeamMember.Read.Group", "type": "Application" },\
        { "name": "TeamSettings.Read.Group", "type": "Application" },\
        { "name": "ChatMessage.Read.Chat", "type": "Application" }\
      ]
    }
  }
}
```

### [​](https://docs.openclaw.ai/channels/msteams\#manifest-caveats-must-have-fields)  Manifest caveats (must-have fields)

- `bots[].botId` **must** match the Azure Bot App ID.
- `webApplicationInfo.id` **must** match the Azure Bot App ID.
- `bots[].scopes` must include the surfaces you plan to use (`personal`, `team`, `groupChat`).
- `bots[].supportsFiles: true` is required for file handling in personal scope.
- `authorization.permissions.resourceSpecific` must include channel read/send if you want channel traffic.

### [​](https://docs.openclaw.ai/channels/msteams\#updating-an-existing-app)  Updating an existing app

To update an already-installed Teams app (e.g., to add RSC permissions):

1. Update your `manifest.json` with the new settings
2. **Increment the `version` field** (e.g., `1.0.0` → `1.1.0`)
3. **Re-zip** the manifest with icons (`manifest.json`, `outline.png`, `color.png`)
4. Upload the new zip:
   - **Option A (Teams Admin Center):** Teams Admin Center → Teams apps → Manage apps → find your app → Upload new version
   - **Option B (Sideload):** In Teams → Apps → Manage your apps → Upload a custom app
5. **For team channels:** Reinstall the app in each team for new permissions to take effect
6. **Fully quit and relaunch Teams** (not just close the window) to clear cached app metadata

## [​](https://docs.openclaw.ai/channels/msteams\#capabilities-rsc-only-vs-graph)  Capabilities: RSC only vs Graph

### [​](https://docs.openclaw.ai/channels/msteams\#with-teams-rsc-only-app-installed-no-graph-api-permissions)  With **Teams RSC only** (app installed, no Graph API permissions)

Works:

- Read channel message **text** content.
- Send channel message **text** content.
- Receive **personal (DM)** file attachments.

Does NOT work:

- Channel/group **image or file contents** (payload only includes HTML stub).
- Downloading attachments stored in SharePoint/OneDrive.
- Reading message history (beyond the live webhook event).

### [​](https://docs.openclaw.ai/channels/msteams\#with-teams-rsc-+-microsoft-graph-application-permissions)  With **Teams RSC + Microsoft Graph Application permissions**

Adds:

- Downloading hosted contents (images pasted into messages).
- Downloading file attachments stored in SharePoint/OneDrive.
- Reading channel/chat message history via Graph.

### [​](https://docs.openclaw.ai/channels/msteams\#rsc-vs-graph-api)  RSC vs Graph API

| Capability | RSC Permissions | Graph API |
| --- | --- | --- |
| **Real-time messages** | Yes (via webhook) | No (polling only) |
| **Historical messages** | No | Yes (can query history) |
| **Setup complexity** | App manifest only | Requires admin consent + token flow |
| **Works offline** | No (must be running) | Yes (query anytime) |

**Bottom line:** RSC is for real-time listening; Graph API is for historical access. For catching up on missed messages while offline, you need Graph API with `ChannelMessage.Read.All` (requires admin consent).

## [​](https://docs.openclaw.ai/channels/msteams\#graph-enabled-media-+-history-required-for-channels)  Graph-enabled media + history (required for channels)

If you need images/files in **channels** or want to fetch **message history**, you must enable Microsoft Graph permissions and grant admin consent.

1. In Entra ID (Azure AD) **App Registration**, add Microsoft Graph **Application permissions**:

   - `ChannelMessage.Read.All` (channel attachments + history)
   - `Chat.Read.All` or `ChatMessage.Read.All` (group chats)
2. **Grant admin consent** for the tenant.
3. Bump the Teams app **manifest version**, re-upload, and **reinstall the app in Teams**.
4. **Fully quit and relaunch Teams** to clear cached app metadata.

**Additional permission for user mentions:** User @mentions work out of the box for users in the conversation. However, if you want to dynamically search and mention users who are **not in the current conversation**, add `User.Read.All` (Application) permission and grant admin consent.

## [​](https://docs.openclaw.ai/channels/msteams\#known-limitations)  Known Limitations

### [​](https://docs.openclaw.ai/channels/msteams\#webhook-timeouts)  Webhook timeouts

Teams delivers messages via HTTP webhook. If processing takes too long (e.g., slow LLM responses), you may see:

- Gateway timeouts
- Teams retrying the message (causing duplicates)
- Dropped replies

OpenClaw handles this by returning quickly and sending replies proactively, but very slow responses may still cause issues.

### [​](https://docs.openclaw.ai/channels/msteams\#formatting)  Formatting

Teams markdown is more limited than Slack or Discord:

- Basic formatting works: **bold**, _italic_, `code`, links
- Complex markdown (tables, nested lists) may not render correctly
- Adaptive Cards are supported for polls and arbitrary card sends (see below)

## [​](https://docs.openclaw.ai/channels/msteams\#configuration)  Configuration

Key settings (see `/gateway/configuration` for shared channel patterns):

- `channels.msteams.enabled`: enable/disable the channel.
- `channels.msteams.appId`, `channels.msteams.appPassword`, `channels.msteams.tenantId`: bot credentials.
- `channels.msteams.webhook.port` (default `3978`)
- `channels.msteams.webhook.path` (default `/api/messages`)
- `channels.msteams.dmPolicy`: `pairing | allowlist | open | disabled` (default: pairing)
- `channels.msteams.allowFrom`: DM allowlist (AAD object IDs recommended). The wizard resolves names to IDs during setup when Graph access is available.
- `channels.msteams.dangerouslyAllowNameMatching`: break-glass toggle to re-enable mutable UPN/display-name matching.
- `channels.msteams.textChunkLimit`: outbound text chunk size.
- `channels.msteams.chunkMode`: `length` (default) or `newline` to split on blank lines (paragraph boundaries) before length chunking.
- `channels.msteams.mediaAllowHosts`: allowlist for inbound attachment hosts (defaults to Microsoft/Teams domains).
- `channels.msteams.mediaAuthAllowHosts`: allowlist for attaching Authorization headers on media retries (defaults to Graph + Bot Framework hosts).
- `channels.msteams.requireMention`: require @mention in channels/groups (default true).
- `channels.msteams.replyStyle`: `thread | top-level` (see [Reply Style](https://docs.openclaw.ai/channels/msteams#reply-style-threads-vs-posts)).
- `channels.msteams.teams.<teamId>.replyStyle`: per-team override.
- `channels.msteams.teams.<teamId>.requireMention`: per-team override.
- `channels.msteams.teams.<teamId>.tools`: default per-team tool policy overrides (`allow`/`deny`/`alsoAllow`) used when a channel override is missing.
- `channels.msteams.teams.<teamId>.toolsBySender`: default per-team per-sender tool policy overrides (`"*"` wildcard supported).
- `channels.msteams.teams.<teamId>.channels.<conversationId>.replyStyle`: per-channel override.
- `channels.msteams.teams.<teamId>.channels.<conversationId>.requireMention`: per-channel override.
- `channels.msteams.teams.<teamId>.channels.<conversationId>.tools`: per-channel tool policy overrides (`allow`/`deny`/`alsoAllow`).
- `channels.msteams.teams.<teamId>.channels.<conversationId>.toolsBySender`: per-channel per-sender tool policy overrides (`"*"` wildcard supported).
- `toolsBySender` keys should use explicit prefixes:
`id:`, `e164:`, `username:`, `name:` (legacy unprefixed keys still map to `id:` only).
- `channels.msteams.sharePointSiteId`: SharePoint site ID for file uploads in group chats/channels (see [Sending files in group chats](https://docs.openclaw.ai/channels/msteams#sending-files-in-group-chats)).

## [​](https://docs.openclaw.ai/channels/msteams\#routing-&-sessions)  Routing & Sessions

- Session keys follow the standard agent format (see [/concepts/session](https://docs.openclaw.ai/concepts/session)):

  - Direct messages share the main session (`agent:<agentId>:<mainKey>`).
  - Channel/group messages use conversation id:
    - `agent:<agentId>:msteams:channel:<conversationId>`
    - `agent:<agentId>:msteams:group:<conversationId>`

## [​](https://docs.openclaw.ai/channels/msteams\#reply-style-threads-vs-posts)  Reply Style: Threads vs Posts

Teams recently introduced two channel UI styles over the same underlying data model:

| Style | Description | Recommended `replyStyle` |
| --- | --- | --- |
| **Posts** (classic) | Messages appear as cards with threaded replies underneath | `thread` (default) |
| **Threads** (Slack-like) | Messages flow linearly, more like Slack | `top-level` |

**The problem:** The Teams API does not expose which UI style a channel uses. If you use the wrong `replyStyle`:

- `thread` in a Threads-style channel → replies appear nested awkwardly
- `top-level` in a Posts-style channel → replies appear as separate top-level posts instead of in-thread

**Solution:** Configure `replyStyle` per-channel based on how the channel is set up:

Copy

```
{
  "msteams": {
    "replyStyle": "thread",
    "teams": {
      "19:abc...@thread.tacv2": {
        "channels": {
          "19:xyz...@thread.tacv2": {
            "replyStyle": "top-level"
          }
        }
      }
    }
  }
}
```

## [​](https://docs.openclaw.ai/channels/msteams\#attachments-&-images)  Attachments & Images

**Current limitations:**

- **DMs:** Images and file attachments work via Teams bot file APIs.
- **Channels/groups:** Attachments live in M365 storage (SharePoint/OneDrive). The webhook payload only includes an HTML stub, not the actual file bytes. **Graph API permissions are required** to download channel attachments.

Without Graph permissions, channel messages with images will be received as text-only (the image content is not accessible to the bot).
By default, OpenClaw only downloads media from Microsoft/Teams hostnames. Override with `channels.msteams.mediaAllowHosts` (use `["*"]` to allow any host).
Authorization headers are only attached for hosts in `channels.msteams.mediaAuthAllowHosts` (defaults to Graph + Bot Framework hosts). Keep this list strict (avoid multi-tenant suffixes).

## [​](https://docs.openclaw.ai/channels/msteams\#sending-files-in-group-chats)  Sending files in group chats

Bots can send files in DMs using the FileConsentCard flow (built-in). However, **sending files in group chats/channels** requires additional setup:

| Context | How files are sent | Setup needed |
| --- | --- | --- |
| **DMs** | FileConsentCard → user accepts → bot uploads | Works out of the box |
| **Group chats/channels** | Upload to SharePoint → share link | Requires `sharePointSiteId` \+ Graph permissions |
| **Images (any context)** | Base64-encoded inline | Works out of the box |

### [​](https://docs.openclaw.ai/channels/msteams\#why-group-chats-need-sharepoint)  Why group chats need SharePoint

Bots don’t have a personal OneDrive drive (the `/me/drive` Graph API endpoint doesn’t work for application identities). To send files in group chats/channels, the bot uploads to a **SharePoint site** and creates a sharing link.

### [​](https://docs.openclaw.ai/channels/msteams\#setup)  Setup

1. **Add Graph API permissions** in Entra ID (Azure AD) → App Registration:   - `Sites.ReadWrite.All` (Application) - upload files to SharePoint
   - `Chat.Read.All` (Application) - optional, enables per-user sharing links
2. **Grant admin consent** for the tenant.
3. **Get your SharePoint site ID:**






Copy











```
# Via Graph Explorer or curl with a valid token:
curl -H "Authorization: Bearer $TOKEN" \
     "https://graph.microsoft.com/v1.0/sites/{hostname}:/{site-path}"

# Example: for a site at "contoso.sharepoint.com/sites/BotFiles"
curl -H "Authorization: Bearer $TOKEN" \
     "https://graph.microsoft.com/v1.0/sites/contoso.sharepoint.com:/sites/BotFiles"

# Response includes: "id": "contoso.sharepoint.com,guid1,guid2"
```

4. **Configure OpenClaw:**






Copy











```
{
     channels: {
       msteams: {
         // ... other config ...
         sharePointSiteId: "contoso.sharepoint.com,guid1,guid2",
       },
     },
}
```


### [​](https://docs.openclaw.ai/channels/msteams\#sharing-behavior)  Sharing behavior

| Permission | Sharing behavior |
| --- | --- |
| `Sites.ReadWrite.All` only | Organization-wide sharing link (anyone in org can access) |
| `Sites.ReadWrite.All` \+ `Chat.Read.All` | Per-user sharing link (only chat members can access) |

Per-user sharing is more secure as only the chat participants can access the file. If `Chat.Read.All` permission is missing, the bot falls back to organization-wide sharing.

### [​](https://docs.openclaw.ai/channels/msteams\#fallback-behavior)  Fallback behavior

| Scenario | Result |
| --- | --- |
| Group chat + file + `sharePointSiteId` configured | Upload to SharePoint, send sharing link |
| Group chat + file + no `sharePointSiteId` | Attempt OneDrive upload (may fail), send text only |
| Personal chat + file | FileConsentCard flow (works without SharePoint) |
| Any context + image | Base64-encoded inline (works without SharePoint) |

### [​](https://docs.openclaw.ai/channels/msteams\#files-stored-location)  Files stored location

Uploaded files are stored in a `/OpenClawShared/` folder in the configured SharePoint site’s default document library.

## [​](https://docs.openclaw.ai/channels/msteams\#polls-adaptive-cards)  Polls (Adaptive Cards)

OpenClaw sends Teams polls as Adaptive Cards (there is no native Teams poll API).

- CLI: `openclaw message poll --channel msteams --target conversation:<id> ...`
- Votes are recorded by the gateway in `~/.openclaw/msteams-polls.json`.
- The gateway must stay online to record votes.
- Polls do not auto-post result summaries yet (inspect the store file if needed).

## [​](https://docs.openclaw.ai/channels/msteams\#adaptive-cards-arbitrary)  Adaptive Cards (arbitrary)

Send any Adaptive Card JSON to Teams users or conversations using the `message` tool or CLI.The `card` parameter accepts an Adaptive Card JSON object. When `card` is provided, the message text is optional.**Agent tool:**

Copy

```
{
  "action": "send",
  "channel": "msteams",
  "target": "user:<id>",
  "card": {
    "type": "AdaptiveCard",
    "version": "1.5",
    "body": [{ "type": "TextBlock", "text": "Hello!" }]
  }
}
```

**CLI:**

Copy

```
openclaw message send --channel msteams \
  --target "conversation:19:abc...@thread.tacv2" \
  --card '{"type":"AdaptiveCard","version":"1.5","body":[{"type":"TextBlock","text":"Hello!"}]}'
```

See [Adaptive Cards documentation](https://adaptivecards.io/) for card schema and examples. For target format details, see [Target formats](https://docs.openclaw.ai/channels/msteams#target-formats) below.

## [​](https://docs.openclaw.ai/channels/msteams\#target-formats)  Target formats

MSTeams targets use prefixes to distinguish between users and conversations:

| Target type | Format | Example |
| --- | --- | --- |
| User (by ID) | `user:<aad-object-id>` | `user:40a1a0ed-4ff2-4164-a219-55518990c197` |
| User (by name) | `user:<display-name>` | `user:John Smith` (requires Graph API) |
| Group/channel | `conversation:<conversation-id>` | `conversation:19:abc123...@thread.tacv2` |
| Group/channel (raw) | `<conversation-id>` | `19:abc123...@thread.tacv2` (if contains `@thread`) |

**CLI examples:**

Copy

```
# Send to a user by ID
openclaw message send --channel msteams --target "user:40a1a0ed-..." --message "Hello"

# Send to a user by display name (triggers Graph API lookup)
openclaw message send --channel msteams --target "user:John Smith" --message "Hello"

# Send to a group chat or channel
openclaw message send --channel msteams --target "conversation:19:abc...@thread.tacv2" --message "Hello"

# Send an Adaptive Card to a conversation
openclaw message send --channel msteams --target "conversation:19:abc...@thread.tacv2" \
  --card '{"type":"AdaptiveCard","version":"1.5","body":[{"type":"TextBlock","text":"Hello"}]}'
```

**Agent tool examples:**

Copy

```
{
  "action": "send",
  "channel": "msteams",
  "target": "user:John Smith",
  "message": "Hello!"
}
```

Copy

```
{
  "action": "send",
  "channel": "msteams",
  "target": "conversation:19:abc...@thread.tacv2",
  "card": {
    "type": "AdaptiveCard",
    "version": "1.5",
    "body": [{ "type": "TextBlock", "text": "Hello" }]
  }
}
```

Note: Without the `user:` prefix, names default to group/team resolution. Always use `user:` when targeting people by display name.

## [​](https://docs.openclaw.ai/channels/msteams\#proactive-messaging)  Proactive messaging

- Proactive messages are only possible **after** a user has interacted, because we store conversation references at that point.
- See `/gateway/configuration` for `dmPolicy` and allowlist gating.

## [​](https://docs.openclaw.ai/channels/msteams\#team-and-channel-ids-common-gotcha)  Team and Channel IDs (Common Gotcha)

The `groupId` query parameter in Teams URLs is **NOT** the team ID used for configuration. Extract IDs from the URL path instead:**Team URL:**

Copy

```
https://teams.microsoft.com/l/team/19%3ABk4j...%40thread.tacv2/conversations?groupId=...
                                    └────────────────────────────┘
                                    Team ID (URL-decode this)
```

**Channel URL:**

Copy

```
https://teams.microsoft.com/l/channel/19%3A15bc...%40thread.tacv2/ChannelName?groupId=...
                                      └─────────────────────────┘
                                      Channel ID (URL-decode this)
```

**For config:**

- Team ID = path segment after `/team/` (URL-decoded, e.g., `19:Bk4j...@thread.tacv2`)
- Channel ID = path segment after `/channel/` (URL-decoded)
- **Ignore** the `groupId` query parameter

## [​](https://docs.openclaw.ai/channels/msteams\#private-channels)  Private Channels

Bots have limited support in private channels:

| Feature | Standard Channels | Private Channels |
| --- | --- | --- |
| Bot installation | Yes | Limited |
| Real-time messages (webhook) | Yes | May not work |
| RSC permissions | Yes | May behave differently |
| @mentions | Yes | If bot is accessible |
| Graph API history | Yes | Yes (with permissions) |

**Workarounds if private channels don’t work:**

1. Use standard channels for bot interactions
2. Use DMs - users can always message the bot directly
3. Use Graph API for historical access (requires `ChannelMessage.Read.All`)

## [​](https://docs.openclaw.ai/channels/msteams\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/channels/msteams\#common-issues)  Common issues

- **Images not showing in channels:** Graph permissions or admin consent missing. Reinstall the Teams app and fully quit/reopen Teams.
- **No responses in channel:** mentions are required by default; set `channels.msteams.requireMention=false` or configure per team/channel.
- **Version mismatch (Teams still shows old manifest):** remove + re-add the app and fully quit Teams to refresh.
- **401 Unauthorized from webhook:** Expected when testing manually without Azure JWT - means endpoint is reachable but auth failed. Use Azure Web Chat to test properly.

### [​](https://docs.openclaw.ai/channels/msteams\#manifest-upload-errors)  Manifest upload errors

- **“Icon file cannot be empty”:** The manifest references icon files that are 0 bytes. Create valid PNG icons (32x32 for `outline.png`, 192x192 for `color.png`).
- **“webApplicationInfo.Id already in use”:** The app is still installed in another team/chat. Find and uninstall it first, or wait 5-10 minutes for propagation.
- **“Something went wrong” on upload:** Upload via [https://admin.teams.microsoft.com](https://admin.teams.microsoft.com/) instead, open browser DevTools (F12) → Network tab, and check the response body for the actual error.
- **Sideload failing:** Try “Upload an app to your org’s app catalog” instead of “Upload a custom app” - this often bypasses sideload restrictions.

### [​](https://docs.openclaw.ai/channels/msteams\#rsc-permissions-not-working)  RSC permissions not working

1. Verify `webApplicationInfo.id` matches your bot’s App ID exactly
2. Re-upload the app and reinstall in the team/chat
3. Check if your org admin has blocked RSC permissions
4. Confirm you’re using the right scope: `ChannelMessage.Read.Group` for teams, `ChatMessage.Read.Chat` for group chats

## [​](https://docs.openclaw.ai/channels/msteams\#references)  References

- [Create Azure Bot](https://learn.microsoft.com/en-us/azure/bot-service/bot-service-quickstart-registration) \- Azure Bot setup guide
- [Teams Developer Portal](https://dev.teams.microsoft.com/apps) \- create/manage Teams apps
- [Teams app manifest schema](https://learn.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema)
- [Receive channel messages with RSC](https://learn.microsoft.com/en-us/microsoftteams/platform/bots/how-to/conversations/channel-messages-with-rsc)
- [RSC permissions reference](https://learn.microsoft.com/en-us/microsoftteams/platform/graph-api/rsc/resource-specific-consent)
- [Teams bot file handling](https://learn.microsoft.com/en-us/microsoftteams/platform/bots/how-to/bots-filesv4) (channel/group requires Graph)
- [Proactive messaging](https://learn.microsoft.com/en-us/microsoftteams/platform/bots/how-to/conversations/send-proactive-messages)

[BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles) [Synology Chat](https://docs.openclaw.ai/channels/synology-chat)

Ctrl+I

---

---

<!-- Page: Mattermost -->

## Mattermost

**Source:** https://docs.openclaw.ai/channels/mattermost

[Skip to main content](https://docs.openclaw.ai/channels/mattermost#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Mattermost

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Mattermost (plugin)](https://docs.openclaw.ai/channels/mattermost#mattermost-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/mattermost#plugin-required)
- [Quick setup](https://docs.openclaw.ai/channels/mattermost#quick-setup)
- [Environment variables (default account)](https://docs.openclaw.ai/channels/mattermost#environment-variables-default-account)
- [Chat modes](https://docs.openclaw.ai/channels/mattermost#chat-modes)
- [Access control (DMs)](https://docs.openclaw.ai/channels/mattermost#access-control-dms)
- [Channels (groups)](https://docs.openclaw.ai/channels/mattermost#channels-groups)
- [Targets for outbound delivery](https://docs.openclaw.ai/channels/mattermost#targets-for-outbound-delivery)
- [Reactions (message tool)](https://docs.openclaw.ai/channels/mattermost#reactions-message-tool)
- [Multi-account](https://docs.openclaw.ai/channels/mattermost#multi-account)
- [Troubleshooting](https://docs.openclaw.ai/channels/mattermost#troubleshooting)

# [​](https://docs.openclaw.ai/channels/mattermost\#mattermost-plugin)  Mattermost (plugin)

Status: supported via plugin (bot token + WebSocket events). Channels, groups, and DMs are supported.
Mattermost is a self-hostable team messaging platform; see the official site at
[mattermost.com](https://mattermost.com/) for product details and downloads.

## [​](https://docs.openclaw.ai/channels/mattermost\#plugin-required)  Plugin required

Mattermost ships as a plugin and is not bundled with the core install.Install via CLI (npm registry):

Copy

```
openclaw plugins install @openclaw/mattermost
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/mattermost
```

If you choose Mattermost during configure/onboarding and a git checkout is detected,
OpenClaw will offer the local install path automatically.Details: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/channels/mattermost\#quick-setup)  Quick setup

1. Install the Mattermost plugin.
2. Create a Mattermost bot account and copy the **bot token**.
3. Copy the Mattermost **base URL** (e.g., `https://chat.example.com`).
4. Configure OpenClaw and start the gateway.

Minimal config:

Copy

```
{
  channels: {
    mattermost: {
      enabled: true,
      botToken: "mm-token",
      baseUrl: "https://chat.example.com",
      dmPolicy: "pairing",
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/mattermost\#environment-variables-default-account)  Environment variables (default account)

Set these on the gateway host if you prefer env vars:

- `MATTERMOST_BOT_TOKEN=...`
- `MATTERMOST_URL=https://chat.example.com`

Env vars apply only to the **default** account (`default`). Other accounts must use config values.

## [​](https://docs.openclaw.ai/channels/mattermost\#chat-modes)  Chat modes

Mattermost responds to DMs automatically. Channel behavior is controlled by `chatmode`:

- `oncall` (default): respond only when @mentioned in channels.
- `onmessage`: respond to every channel message.
- `onchar`: respond when a message starts with a trigger prefix.

Config example:

Copy

```
{
  channels: {
    mattermost: {
      chatmode: "onchar",
      oncharPrefixes: [">", "!"],
    },
  },
}
```

Notes:

- `onchar` still responds to explicit @mentions.
- `channels.mattermost.requireMention` is honored for legacy configs but `chatmode` is preferred.

## [​](https://docs.openclaw.ai/channels/mattermost\#access-control-dms)  Access control (DMs)

- Default: `channels.mattermost.dmPolicy = "pairing"` (unknown senders get a pairing code).
- Approve via:
  - `openclaw pairing list mattermost`
  - `openclaw pairing approve mattermost <CODE>`
- Public DMs: `channels.mattermost.dmPolicy="open"` plus `channels.mattermost.allowFrom=["*"]`.

## [​](https://docs.openclaw.ai/channels/mattermost\#channels-groups)  Channels (groups)

- Default: `channels.mattermost.groupPolicy = "allowlist"` (mention-gated).
- Allowlist senders with `channels.mattermost.groupAllowFrom` (user IDs recommended).
- `@username` matching is mutable and only enabled when `channels.mattermost.dangerouslyAllowNameMatching: true`.
- Open channels: `channels.mattermost.groupPolicy="open"` (mention-gated).
- Runtime note: if `channels.mattermost` is completely missing, runtime falls back to `groupPolicy="allowlist"` for group checks (even if `channels.defaults.groupPolicy` is set).

## [​](https://docs.openclaw.ai/channels/mattermost\#targets-for-outbound-delivery)  Targets for outbound delivery

Use these target formats with `openclaw message send` or cron/webhooks:

- `channel:<id>` for a channel
- `user:<id>` for a DM
- `@username` for a DM (resolved via the Mattermost API)

Bare IDs are treated as channels.

## [​](https://docs.openclaw.ai/channels/mattermost\#reactions-message-tool)  Reactions (message tool)

- Use `message action=react` with `channel=mattermost`.
- `messageId` is the Mattermost post id.
- `emoji` accepts names like `thumbsup` or `:+1:` (colons are optional).
- Set `remove=true` (boolean) to remove a reaction.
- Reaction add/remove events are forwarded as system events to the routed agent session.

Examples:

Copy

```
message action=react channel=mattermost target=channel:<channelId> messageId=<postId> emoji=thumbsup
message action=react channel=mattermost target=channel:<channelId> messageId=<postId> emoji=thumbsup remove=true
```

Config:

- `channels.mattermost.actions.reactions`: enable/disable reaction actions (default true).
- Per-account override: `channels.mattermost.accounts.<id>.actions.reactions`.

## [​](https://docs.openclaw.ai/channels/mattermost\#multi-account)  Multi-account

Mattermost supports multiple accounts under `channels.mattermost.accounts`:

Copy

```
{
  channels: {
    mattermost: {
      accounts: {
        default: { name: "Primary", botToken: "mm-token", baseUrl: "https://chat.example.com" },
        alerts: { name: "Alerts", botToken: "mm-token-2", baseUrl: "https://alerts.example.com" },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/mattermost\#troubleshooting)  Troubleshooting

- No replies in channels: ensure the bot is in the channel and mention it (oncall), use a trigger prefix (onchar), or set `chatmode: "onmessage"`.
- Auth errors: check the bot token, base URL, and whether the account is enabled.
- Multi-account issues: env vars only apply to the `default` account.

[Google Chat](https://docs.openclaw.ai/channels/googlechat) [Signal](https://docs.openclaw.ai/channels/signal)

Ctrl+I

---

---

<!-- Page: Google Chat -->

## Google Chat

**Source:** https://docs.openclaw.ai/channels/googlechat

[Skip to main content](https://docs.openclaw.ai/channels/googlechat#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Google Chat

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Google Chat (Chat API)](https://docs.openclaw.ai/channels/googlechat#google-chat-chat-api)
- [Quick setup (beginner)](https://docs.openclaw.ai/channels/googlechat#quick-setup-beginner)
- [Add to Google Chat](https://docs.openclaw.ai/channels/googlechat#add-to-google-chat)
- [Public URL (Webhook-only)](https://docs.openclaw.ai/channels/googlechat#public-url-webhook-only)
- [Option A: Tailscale Funnel (Recommended)](https://docs.openclaw.ai/channels/googlechat#option-a-tailscale-funnel-recommended)
- [Option B: Reverse Proxy (Caddy)](https://docs.openclaw.ai/channels/googlechat#option-b-reverse-proxy-caddy)
- [Option C: Cloudflare Tunnel](https://docs.openclaw.ai/channels/googlechat#option-c-cloudflare-tunnel)
- [How it works](https://docs.openclaw.ai/channels/googlechat#how-it-works)
- [Targets](https://docs.openclaw.ai/channels/googlechat#targets)
- [Config highlights](https://docs.openclaw.ai/channels/googlechat#config-highlights)
- [Troubleshooting](https://docs.openclaw.ai/channels/googlechat#troubleshooting)
- [405 Method Not Allowed](https://docs.openclaw.ai/channels/googlechat#405-method-not-allowed)
- [Other issues](https://docs.openclaw.ai/channels/googlechat#other-issues)

# [​](https://docs.openclaw.ai/channels/googlechat\#google-chat-chat-api)  Google Chat (Chat API)

Status: ready for DMs + spaces via Google Chat API webhooks (HTTP only).

## [​](https://docs.openclaw.ai/channels/googlechat\#quick-setup-beginner)  Quick setup (beginner)

1. Create a Google Cloud project and enable the **Google Chat API**.

   - Go to: [Google Chat API Credentials](https://console.cloud.google.com/apis/api/chat.googleapis.com/credentials)
   - Enable the API if it is not already enabled.
2. Create a **Service Account**:

   - Press **Create Credentials** \> **Service Account**.
   - Name it whatever you want (e.g., `openclaw-chat`).
   - Leave permissions blank (press **Continue**).
   - Leave principals with access blank (press **Done**).
3. Create and download the **JSON Key**:

   - In the list of service accounts, click on the one you just created.
   - Go to the **Keys** tab.
   - Click **Add Key** \> **Create new key**.
   - Select **JSON** and press **Create**.
4. Store the downloaded JSON file on your gateway host (e.g., `~/.openclaw/googlechat-service-account.json`).
5. Create a Google Chat app in the [Google Cloud Console Chat Configuration](https://console.cloud.google.com/apis/api/chat.googleapis.com/hangouts-chat):

   - Fill in the **Application info**:

     - **App name**: (e.g. `OpenClaw`)
     - **Avatar URL**: (e.g. `https://openclaw.ai/logo.png`)
     - **Description**: (e.g. `Personal AI Assistant`)
   - Enable **Interactive features**.
   - Under **Functionality**, check **Join spaces and group conversations**.
   - Under **Connection settings**, select **HTTP endpoint URL**.
   - Under **Triggers**, select **Use a common HTTP endpoint URL for all triggers** and set it to your gateway’s public URL followed by `/googlechat`.

     - _Tip: Run `openclaw status` to find your gateway’s public URL._
   - Under **Visibility**, check **Make this Chat app available to specific people and groups in <Your Domain>**.
   - Enter your email address (e.g. `user@example.com`) in the text box.
   - Click **Save** at the bottom.
6. **Enable the app status**:

   - After saving, **refresh the page**.
   - Look for the **App status** section (usually near the top or bottom after saving).
   - Change the status to **Live - available to users**.
   - Click **Save** again.
7. Configure OpenClaw with the service account path + webhook audience:
   - Env: `GOOGLE_CHAT_SERVICE_ACCOUNT_FILE=/path/to/service-account.json`
   - Or config: `channels.googlechat.serviceAccountFile: "/path/to/service-account.json"`.
8. Set the webhook audience type + value (matches your Chat app config).
9. Start the gateway. Google Chat will POST to your webhook path.

## [​](https://docs.openclaw.ai/channels/googlechat\#add-to-google-chat)  Add to Google Chat

Once the gateway is running and your email is added to the visibility list:

1. Go to [Google Chat](https://chat.google.com/).
2. Click the **+** (plus) icon next to **Direct Messages**.
3. In the search bar (where you usually add people), type the **App name** you configured in the Google Cloud Console.

   - **Note**: The bot will _not_ appear in the “Marketplace” browse list because it is a private app. You must search for it by name.
4. Select your bot from the results.
5. Click **Add** or **Chat** to start a 1:1 conversation.
6. Send “Hello” to trigger the assistant!

## [​](https://docs.openclaw.ai/channels/googlechat\#public-url-webhook-only)  Public URL (Webhook-only)

Google Chat webhooks require a public HTTPS endpoint. For security, **only expose the `/googlechat` path** to the internet. Keep the OpenClaw dashboard and other sensitive endpoints on your private network.

### [​](https://docs.openclaw.ai/channels/googlechat\#option-a-tailscale-funnel-recommended)  Option A: Tailscale Funnel (Recommended)

Use Tailscale Serve for the private dashboard and Funnel for the public webhook path. This keeps `/` private while exposing only `/googlechat`.

1. **Check what address your gateway is bound to:**






Copy











```
ss -tlnp | grep 18789
```










Note the IP address (e.g., `127.0.0.1`, `0.0.0.0`, or your Tailscale IP like `100.x.x.x`).
2. **Expose the dashboard to the tailnet only (port 8443):**






Copy











```
# If bound to localhost (127.0.0.1 or 0.0.0.0):
tailscale serve --bg --https 8443 http://127.0.0.1:18789

# If bound to Tailscale IP only (e.g., 100.106.161.80):
tailscale serve --bg --https 8443 http://100.106.161.80:18789
```

3. **Expose only the webhook path publicly:**






Copy











```
# If bound to localhost (127.0.0.1 or 0.0.0.0):
tailscale funnel --bg --set-path /googlechat http://127.0.0.1:18789/googlechat

# If bound to Tailscale IP only (e.g., 100.106.161.80):
tailscale funnel --bg --set-path /googlechat http://100.106.161.80:18789/googlechat
```

4. **Authorize the node for Funnel access:**
If prompted, visit the authorization URL shown in the output to enable Funnel for this node in your tailnet policy.
5. **Verify the configuration:**






Copy











```
tailscale serve status
tailscale funnel status
```


Your public webhook URL will be:
`https://<node-name>.<tailnet>.ts.net/googlechat`Your private dashboard stays tailnet-only:
`https://<node-name>.<tailnet>.ts.net:8443/`Use the public URL (without `:8443`) in the Google Chat app config.

> Note: This configuration persists across reboots. To remove it later, run `tailscale funnel reset` and `tailscale serve reset`.

### [​](https://docs.openclaw.ai/channels/googlechat\#option-b-reverse-proxy-caddy)  Option B: Reverse Proxy (Caddy)

If you use a reverse proxy like Caddy, only proxy the specific path:

Copy

```
your-domain.com {
    reverse_proxy /googlechat* localhost:18789
}
```

With this config, any request to `your-domain.com/` will be ignored or returned as 404, while `your-domain.com/googlechat` is safely routed to OpenClaw.

### [​](https://docs.openclaw.ai/channels/googlechat\#option-c-cloudflare-tunnel)  Option C: Cloudflare Tunnel

Configure your tunnel’s ingress rules to only route the webhook path:

- **Path**: `/googlechat` -\> `http://localhost:18789/googlechat`
- **Default Rule**: HTTP 404 (Not Found)

## [​](https://docs.openclaw.ai/channels/googlechat\#how-it-works)  How it works

1. Google Chat sends webhook POSTs to the gateway. Each request includes an `Authorization: Bearer <token>` header.
2. OpenClaw verifies the token against the configured `audienceType` \+ `audience`:

   - `audienceType: "app-url"` → audience is your HTTPS webhook URL.
   - `audienceType: "project-number"` → audience is the Cloud project number.
3. Messages are routed by space:
   - DMs use session key `agent:<agentId>:googlechat:dm:<spaceId>`.
   - Spaces use session key `agent:<agentId>:googlechat:group:<spaceId>`.
4. DM access is pairing by default. Unknown senders receive a pairing code; approve with:
   - `openclaw pairing approve googlechat <code>`
5. Group spaces require @-mention by default. Use `botUser` if mention detection needs the app’s user name.

## [​](https://docs.openclaw.ai/channels/googlechat\#targets)  Targets

Use these identifiers for delivery and allowlists:

- Direct messages: `users/<userId>` (recommended).
- Raw email `name@example.com` is mutable and only used for direct allowlist matching when `channels.googlechat.dangerouslyAllowNameMatching: true`.
- Deprecated: `users/<email>` is treated as a user id, not an email allowlist.
- Spaces: `spaces/<spaceId>`.

## [​](https://docs.openclaw.ai/channels/googlechat\#config-highlights)  Config highlights

Copy

```
{
  channels: {
    googlechat: {
      enabled: true,
      serviceAccountFile: "/path/to/service-account.json",
      audienceType: "app-url",
      audience: "https://gateway.example.com/googlechat",
      webhookPath: "/googlechat",
      botUser: "users/1234567890", // optional; helps mention detection
      dm: {
        policy: "pairing",
        allowFrom: ["users/1234567890"],
      },
      groupPolicy: "allowlist",
      groups: {
        "spaces/AAAA": {
          allow: true,
          requireMention: true,
          users: ["users/1234567890"],
          systemPrompt: "Short answers only.",
        },
      },
      actions: { reactions: true },
      typingIndicator: "message",
      mediaMaxMb: 20,
    },
  },
}
```

Notes:

- Service account credentials can also be passed inline with `serviceAccount` (JSON string).
- Default webhook path is `/googlechat` if `webhookPath` isn’t set.
- `dangerouslyAllowNameMatching` re-enables mutable email principal matching for allowlists (break-glass compatibility mode).
- Reactions are available via the `reactions` tool and `channels action` when `actions.reactions` is enabled.
- `typingIndicator` supports `none`, `message` (default), and `reaction` (reaction requires user OAuth).
- Attachments are downloaded through the Chat API and stored in the media pipeline (size capped by `mediaMaxMb`).

## [​](https://docs.openclaw.ai/channels/googlechat\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/channels/googlechat\#405-method-not-allowed)  405 Method Not Allowed

If Google Cloud Logs Explorer shows errors like:

Copy

```
status code: 405, reason phrase: HTTP error response: HTTP/1.1 405 Method Not Allowed
```

This means the webhook handler isn’t registered. Common causes:

1. **Channel not configured**: The `channels.googlechat` section is missing from your config. Verify with:






Copy











```
openclaw config get channels.googlechat
```










If it returns “Config path not found”, add the configuration (see [Config highlights](https://docs.openclaw.ai/channels/googlechat#config-highlights)).
2. **Plugin not enabled**: Check plugin status:






Copy











```
openclaw plugins list | grep googlechat
```










If it shows “disabled”, add `plugins.entries.googlechat.enabled: true` to your config.
3. **Gateway not restarted**: After adding config, restart the gateway:






Copy











```
openclaw gateway restart
```


Verify the channel is running:

Copy

```
openclaw channels status
# Should show: Google Chat default: enabled, configured, ...
```

### [​](https://docs.openclaw.ai/channels/googlechat\#other-issues)  Other issues

- Check `openclaw channels status --probe` for auth errors or missing audience config.
- If no messages arrive, confirm the Chat app’s webhook URL + event subscriptions.
- If mention gating blocks replies, set `botUser` to the app’s user resource name and verify `requireMention`.
- Use `openclaw logs --follow` while sending a test message to see if requests reach the gateway.

Related docs:

- [Gateway configuration](https://docs.openclaw.ai/gateway/configuration)
- [Security](https://docs.openclaw.ai/gateway/security)
- [Reactions](https://docs.openclaw.ai/tools/reactions)

[Feishu](https://docs.openclaw.ai/channels/feishu) [Mattermost](https://docs.openclaw.ai/channels/mattermost)

Ctrl+I

---

---

<!-- Page: Feishu -->

## Feishu

**Source:** https://docs.openclaw.ai/channels/feishu

[Skip to main content](https://docs.openclaw.ai/channels/feishu#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Feishu

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Feishu bot](https://docs.openclaw.ai/channels/feishu#feishu-bot)
- [Plugin required](https://docs.openclaw.ai/channels/feishu#plugin-required)
- [Quickstart](https://docs.openclaw.ai/channels/feishu#quickstart)
- [Method 1: onboarding wizard (recommended)](https://docs.openclaw.ai/channels/feishu#method-1-onboarding-wizard-recommended)
- [Method 2: CLI setup](https://docs.openclaw.ai/channels/feishu#method-2-cli-setup)
- [Step 1: Create a Feishu app](https://docs.openclaw.ai/channels/feishu#step-1-create-a-feishu-app)
- [1\. Open Feishu Open Platform](https://docs.openclaw.ai/channels/feishu#1-open-feishu-open-platform)
- [2\. Create an app](https://docs.openclaw.ai/channels/feishu#2-create-an-app)
- [3\. Copy credentials](https://docs.openclaw.ai/channels/feishu#3-copy-credentials)
- [4\. Configure permissions](https://docs.openclaw.ai/channels/feishu#4-configure-permissions)
- [5\. Enable bot capability](https://docs.openclaw.ai/channels/feishu#5-enable-bot-capability)
- [6\. Configure event subscription](https://docs.openclaw.ai/channels/feishu#6-configure-event-subscription)
- [7\. Publish the app](https://docs.openclaw.ai/channels/feishu#7-publish-the-app)
- [Step 2: Configure OpenClaw](https://docs.openclaw.ai/channels/feishu#step-2-configure-openclaw)
- [Configure with the wizard (recommended)](https://docs.openclaw.ai/channels/feishu#configure-with-the-wizard-recommended)
- [Configure via config file](https://docs.openclaw.ai/channels/feishu#configure-via-config-file)
- [Configure via environment variables](https://docs.openclaw.ai/channels/feishu#configure-via-environment-variables)
- [Lark (global) domain](https://docs.openclaw.ai/channels/feishu#lark-global-domain)
- [Step 3: Start + test](https://docs.openclaw.ai/channels/feishu#step-3-start-%2B-test)
- [1\. Start the gateway](https://docs.openclaw.ai/channels/feishu#1-start-the-gateway)
- [2\. Send a test message](https://docs.openclaw.ai/channels/feishu#2-send-a-test-message)
- [3\. Approve pairing](https://docs.openclaw.ai/channels/feishu#3-approve-pairing)
- [Overview](https://docs.openclaw.ai/channels/feishu#overview)
- [Access control](https://docs.openclaw.ai/channels/feishu#access-control)
- [Direct messages](https://docs.openclaw.ai/channels/feishu#direct-messages)
- [Group chats](https://docs.openclaw.ai/channels/feishu#group-chats)
- [Group configuration examples](https://docs.openclaw.ai/channels/feishu#group-configuration-examples)
- [Allow all groups, require @mention (default)](https://docs.openclaw.ai/channels/feishu#allow-all-groups-require-%40mention-default)
- [Allow all groups, no @mention required](https://docs.openclaw.ai/channels/feishu#allow-all-groups-no-%40mention-required)
- [Allow specific users in groups only](https://docs.openclaw.ai/channels/feishu#allow-specific-users-in-groups-only)
- [Get group/user IDs](https://docs.openclaw.ai/channels/feishu#get-group%2Fuser-ids)
- [Group IDs (chat\_id)](https://docs.openclaw.ai/channels/feishu#group-ids-chat_id)
- [User IDs (open\_id)](https://docs.openclaw.ai/channels/feishu#user-ids-open_id)
- [Common commands](https://docs.openclaw.ai/channels/feishu#common-commands)
- [Gateway management commands](https://docs.openclaw.ai/channels/feishu#gateway-management-commands)
- [Troubleshooting](https://docs.openclaw.ai/channels/feishu#troubleshooting)
- [Bot does not respond in group chats](https://docs.openclaw.ai/channels/feishu#bot-does-not-respond-in-group-chats)
- [Bot does not receive messages](https://docs.openclaw.ai/channels/feishu#bot-does-not-receive-messages)
- [App Secret leak](https://docs.openclaw.ai/channels/feishu#app-secret-leak)
- [Message send failures](https://docs.openclaw.ai/channels/feishu#message-send-failures)
- [Advanced configuration](https://docs.openclaw.ai/channels/feishu#advanced-configuration)
- [Multiple accounts](https://docs.openclaw.ai/channels/feishu#multiple-accounts)
- [Message limits](https://docs.openclaw.ai/channels/feishu#message-limits)
- [Streaming](https://docs.openclaw.ai/channels/feishu#streaming)
- [Multi-agent routing](https://docs.openclaw.ai/channels/feishu#multi-agent-routing)
- [Configuration reference](https://docs.openclaw.ai/channels/feishu#configuration-reference)
- [dmPolicy reference](https://docs.openclaw.ai/channels/feishu#dmpolicy-reference)
- [Supported message types](https://docs.openclaw.ai/channels/feishu#supported-message-types)
- [Receive](https://docs.openclaw.ai/channels/feishu#receive)
- [Send](https://docs.openclaw.ai/channels/feishu#send)

# [​](https://docs.openclaw.ai/channels/feishu\#feishu-bot)  Feishu bot

Feishu (Lark) is a team chat platform used by companies for messaging and collaboration. This plugin connects OpenClaw to a Feishu/Lark bot using the platform’s WebSocket event subscription so messages can be received without exposing a public webhook URL.

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#plugin-required)  Plugin required

Install the Feishu plugin:

Copy

```
openclaw plugins install @openclaw/feishu
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/feishu
```

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#quickstart)  Quickstart

There are two ways to add the Feishu channel:

### [​](https://docs.openclaw.ai/channels/feishu\#method-1-onboarding-wizard-recommended)  Method 1: onboarding wizard (recommended)

If you just installed OpenClaw, run the wizard:

Copy

```
openclaw onboard
```

The wizard guides you through:

1. Creating a Feishu app and collecting credentials
2. Configuring app credentials in OpenClaw
3. Starting the gateway

✅ **After configuration**, check gateway status:

- `openclaw gateway status`
- `openclaw logs --follow`

### [​](https://docs.openclaw.ai/channels/feishu\#method-2-cli-setup)  Method 2: CLI setup

If you already completed initial install, add the channel via CLI:

Copy

```
openclaw channels add
```

Choose **Feishu**, then enter the App ID and App Secret.✅ **After configuration**, manage the gateway:

- `openclaw gateway status`
- `openclaw gateway restart`
- `openclaw logs --follow`

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#step-1-create-a-feishu-app)  Step 1: Create a Feishu app

### [​](https://docs.openclaw.ai/channels/feishu\#1-open-feishu-open-platform)  1\. Open Feishu Open Platform

Visit [Feishu Open Platform](https://open.feishu.cn/app) and sign in.Lark (global) tenants should use [https://open.larksuite.com/app](https://open.larksuite.com/app) and set `domain: "lark"` in the Feishu config.

### [​](https://docs.openclaw.ai/channels/feishu\#2-create-an-app)  2\. Create an app

1. Click **Create enterprise app**
2. Fill in the app name + description
3. Choose an app icon

![Create enterprise app](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step2-create-app.png?fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=a3d0a511fea278250c353f5c33f03584)

### [​](https://docs.openclaw.ai/channels/feishu\#3-copy-credentials)  3\. Copy credentials

From **Credentials & Basic Info**, copy:

- **App ID** (format: `cli_xxx`)
- **App Secret**

❗ **Important:** keep the App Secret private.![Get credentials](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step3-credentials.png?fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=3a6ac22e96d76e4b85a1171ea207608b)

### [​](https://docs.openclaw.ai/channels/feishu\#4-configure-permissions)  4\. Configure permissions

On **Permissions**, click **Batch import** and paste:

Copy

```
{
  "scopes": {
    "tenant": [\
      "aily:file:read",\
      "aily:file:write",\
      "application:application.app_message_stats.overview:readonly",\
      "application:application:self_manage",\
      "application:bot.menu:write",\
      "contact:user.employee_id:readonly",\
      "corehr:file:download",\
      "event:ip_list",\
      "im:chat.access_event.bot_p2p_chat:read",\
      "im:chat.members:bot_access",\
      "im:message",\
      "im:message.group_at_msg:readonly",\
      "im:message.p2p_msg:readonly",\
      "im:message:readonly",\
      "im:message:send_as_bot",\
      "im:resource"\
    ],
    "user": ["aily:file:read", "aily:file:write", "im:chat.access_event.bot_p2p_chat:read"]
  }
}
```

![Configure permissions](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step4-permissions.png?fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=a386d201628f65771d9d423056d9dc59)

### [​](https://docs.openclaw.ai/channels/feishu\#5-enable-bot-capability)  5\. Enable bot capability

In **App Capability** \> **Bot**:

1. Enable bot capability
2. Set the bot name

![Enable bot capability](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step5-bot-capability.png?fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=4c330500fd7db2e72569dc2a379697ee)

### [​](https://docs.openclaw.ai/channels/feishu\#6-configure-event-subscription)  6\. Configure event subscription

⚠️ **Important:** before setting event subscription, make sure:

1. You already ran `openclaw channels add` for Feishu
2. The gateway is running (`openclaw gateway status`)

In **Event Subscription**:

1. Choose **Use long connection to receive events** (WebSocket)
2. Add the event: `im.message.receive_v1`

⚠️ If the gateway is not running, the long-connection setup may fail to save.![Configure event subscription](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step6-event-subscription.png?fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=00aeb4809d9df159d846e0be19bc871e)

### [​](https://docs.openclaw.ai/channels/feishu\#7-publish-the-app)  7\. Publish the app

1. Create a version in **Version Management & Release**
2. Submit for review and publish
3. Wait for admin approval (enterprise apps usually auto-approve)

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#step-2-configure-openclaw)  Step 2: Configure OpenClaw

### [​](https://docs.openclaw.ai/channels/feishu\#configure-with-the-wizard-recommended)  Configure with the wizard (recommended)

Copy

```
openclaw channels add
```

Choose **Feishu** and paste your App ID + App Secret.

### [​](https://docs.openclaw.ai/channels/feishu\#configure-via-config-file)  Configure via config file

Edit `~/.openclaw/openclaw.json`:

Copy

```
{
  channels: {
    feishu: {
      enabled: true,
      dmPolicy: "pairing",
      accounts: {
        main: {
          appId: "cli_xxx",
          appSecret: "xxx",
          botName: "My AI assistant",
        },
      },
    },
  },
}
```

If you use `connectionMode: "webhook"`, set `verificationToken`. The Feishu webhook server binds to `127.0.0.1` by default; set `webhookHost` only if you intentionally need a different bind address.

### [​](https://docs.openclaw.ai/channels/feishu\#configure-via-environment-variables)  Configure via environment variables

Copy

```
export FEISHU_APP_ID="cli_xxx"
export FEISHU_APP_SECRET="xxx"
```

### [​](https://docs.openclaw.ai/channels/feishu\#lark-global-domain)  Lark (global) domain

If your tenant is on Lark (international), set the domain to `lark` (or a full domain string). You can set it at `channels.feishu.domain` or per account (`channels.feishu.accounts.<id>.domain`).

Copy

```
{
  channels: {
    feishu: {
      domain: "lark",
      accounts: {
        main: {
          appId: "cli_xxx",
          appSecret: "xxx",
        },
      },
    },
  },
}
```

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#step-3-start-+-test)  Step 3: Start + test

### [​](https://docs.openclaw.ai/channels/feishu\#1-start-the-gateway)  1\. Start the gateway

Copy

```
openclaw gateway
```

### [​](https://docs.openclaw.ai/channels/feishu\#2-send-a-test-message)  2\. Send a test message

In Feishu, find your bot and send a message.

### [​](https://docs.openclaw.ai/channels/feishu\#3-approve-pairing)  3\. Approve pairing

By default, the bot replies with a pairing code. Approve it:

Copy

```
openclaw pairing approve feishu <CODE>
```

After approval, you can chat normally.

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#overview)  Overview

- **Feishu bot channel**: Feishu bot managed by the gateway
- **Deterministic routing**: replies always return to Feishu
- **Session isolation**: DMs share a main session; groups are isolated
- **WebSocket connection**: long connection via Feishu SDK, no public URL needed

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#access-control)  Access control

### [​](https://docs.openclaw.ai/channels/feishu\#direct-messages)  Direct messages

- **Default**: `dmPolicy: "pairing"` (unknown users get a pairing code)
- **Approve pairing**:






Copy











```
openclaw pairing list feishu
openclaw pairing approve feishu <CODE>
```

- **Allowlist mode**: set `channels.feishu.allowFrom` with allowed Open IDs

### [​](https://docs.openclaw.ai/channels/feishu\#group-chats)  Group chats

**1\. Group policy** (`channels.feishu.groupPolicy`):

- `"open"` = allow everyone in groups (default)
- `"allowlist"` = only allow `groupAllowFrom`
- `"disabled"` = disable group messages

**2\. Mention requirement** (`channels.feishu.groups.<chat_id>.requireMention`):

- `true` = require @mention (default)
- `false` = respond without mentions

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#group-configuration-examples)  Group configuration examples

### [​](https://docs.openclaw.ai/channels/feishu\#allow-all-groups-require-@mention-default)  Allow all groups, require @mention (default)

Copy

```
{
  channels: {
    feishu: {
      groupPolicy: "open",
      // Default requireMention: true
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/feishu\#allow-all-groups-no-@mention-required)  Allow all groups, no @mention required

Copy

```
{
  channels: {
    feishu: {
      groups: {
        oc_xxx: { requireMention: false },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/feishu\#allow-specific-users-in-groups-only)  Allow specific users in groups only

Copy

```
{
  channels: {
    feishu: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["ou_xxx", "ou_yyy"],
    },
  },
}
```

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#get-group/user-ids)  Get group/user IDs

### [​](https://docs.openclaw.ai/channels/feishu\#group-ids-chat_id)  Group IDs (chat\_id)

Group IDs look like `oc_xxx`.**Method 1 (recommended)**

1. Start the gateway and @mention the bot in the group
2. Run `openclaw logs --follow` and look for `chat_id`

**Method 2**Use the Feishu API debugger to list group chats.

### [​](https://docs.openclaw.ai/channels/feishu\#user-ids-open_id)  User IDs (open\_id)

User IDs look like `ou_xxx`.**Method 1 (recommended)**

1. Start the gateway and DM the bot
2. Run `openclaw logs --follow` and look for `open_id`

**Method 2**Check pairing requests for user Open IDs:

Copy

```
openclaw pairing list feishu
```

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#common-commands)  Common commands

| Command | Description |
| --- | --- |
| `/status` | Show bot status |
| `/reset` | Reset the session |
| `/model` | Show/switch model |

> Note: Feishu does not support native command menus yet, so commands must be sent as text.

## [​](https://docs.openclaw.ai/channels/feishu\#gateway-management-commands)  Gateway management commands

| Command | Description |
| --- | --- |
| `openclaw gateway status` | Show gateway status |
| `openclaw gateway install` | Install/start gateway service |
| `openclaw gateway stop` | Stop gateway service |
| `openclaw gateway restart` | Restart gateway service |
| `openclaw logs --follow` | Tail gateway logs |

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/channels/feishu\#bot-does-not-respond-in-group-chats)  Bot does not respond in group chats

1. Ensure the bot is added to the group
2. Ensure you @mention the bot (default behavior)
3. Check `groupPolicy` is not set to `"disabled"`
4. Check logs: `openclaw logs --follow`

### [​](https://docs.openclaw.ai/channels/feishu\#bot-does-not-receive-messages)  Bot does not receive messages

1. Ensure the app is published and approved
2. Ensure event subscription includes `im.message.receive_v1`
3. Ensure **long connection** is enabled
4. Ensure app permissions are complete
5. Ensure the gateway is running: `openclaw gateway status`
6. Check logs: `openclaw logs --follow`

### [​](https://docs.openclaw.ai/channels/feishu\#app-secret-leak)  App Secret leak

1. Reset the App Secret in Feishu Open Platform
2. Update the App Secret in your config
3. Restart the gateway

### [​](https://docs.openclaw.ai/channels/feishu\#message-send-failures)  Message send failures

1. Ensure the app has `im:message:send_as_bot` permission
2. Ensure the app is published
3. Check logs for detailed errors

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#advanced-configuration)  Advanced configuration

### [​](https://docs.openclaw.ai/channels/feishu\#multiple-accounts)  Multiple accounts

Copy

```
{
  channels: {
    feishu: {
      accounts: {
        main: {
          appId: "cli_xxx",
          appSecret: "xxx",
          botName: "Primary bot",
        },
        backup: {
          appId: "cli_yyy",
          appSecret: "yyy",
          botName: "Backup bot",
          enabled: false,
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/feishu\#message-limits)  Message limits

- `textChunkLimit`: outbound text chunk size (default: 2000 chars)
- `mediaMaxMb`: media upload/download limit (default: 30MB)

### [​](https://docs.openclaw.ai/channels/feishu\#streaming)  Streaming

Feishu supports streaming replies via interactive cards. When enabled, the bot updates a card as it generates text.

Copy

```
{
  channels: {
    feishu: {
      streaming: true, // enable streaming card output (default true)
      blockStreaming: true, // enable block-level streaming (default true)
    },
  },
}
```

Set `streaming: false` to wait for the full reply before sending.

### [​](https://docs.openclaw.ai/channels/feishu\#multi-agent-routing)  Multi-agent routing

Use `bindings` to route Feishu DMs or groups to different agents.

Copy

```
{
  agents: {
    list: [\
      { id: "main" },\
      {\
        id: "clawd-fan",\
        workspace: "/home/user/clawd-fan",\
        agentDir: "/home/user/.openclaw/agents/clawd-fan/agent",\
      },\
      {\
        id: "clawd-xi",\
        workspace: "/home/user/clawd-xi",\
        agentDir: "/home/user/.openclaw/agents/clawd-xi/agent",\
      },\
    ],
  },
  bindings: [\
    {\
      agentId: "main",\
      match: {\
        channel: "feishu",\
        peer: { kind: "direct", id: "ou_xxx" },\
      },\
    },\
    {\
      agentId: "clawd-fan",\
      match: {\
        channel: "feishu",\
        peer: { kind: "direct", id: "ou_yyy" },\
      },\
    },\
    {\
      agentId: "clawd-xi",\
      match: {\
        channel: "feishu",\
        peer: { kind: "group", id: "oc_zzz" },\
      },\
    },\
  ],
}
```

Routing fields:

- `match.channel`: `"feishu"`
- `match.peer.kind`: `"direct"` or `"group"`
- `match.peer.id`: user Open ID (`ou_xxx`) or group ID (`oc_xxx`)

See [Get group/user IDs](https://docs.openclaw.ai/channels/feishu#get-groupuser-ids) for lookup tips.

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#configuration-reference)  Configuration reference

Full configuration: [Gateway configuration](https://docs.openclaw.ai/gateway/configuration)Key options:

| Setting | Description | Default |
| --- | --- | --- |
| `channels.feishu.enabled` | Enable/disable channel | `true` |
| `channels.feishu.domain` | API domain (`feishu` or `lark`) | `feishu` |
| `channels.feishu.connectionMode` | Event transport mode | `websocket` |
| `channels.feishu.verificationToken` | Required for webhook mode | - |
| `channels.feishu.webhookPath` | Webhook route path | `/feishu/events` |
| `channels.feishu.webhookHost` | Webhook bind host | `127.0.0.1` |
| `channels.feishu.webhookPort` | Webhook bind port | `3000` |
| `channels.feishu.accounts.<id>.appId` | App ID | - |
| `channels.feishu.accounts.<id>.appSecret` | App Secret | - |
| `channels.feishu.accounts.<id>.domain` | Per-account API domain override | `feishu` |
| `channels.feishu.dmPolicy` | DM policy | `pairing` |
| `channels.feishu.allowFrom` | DM allowlist (open\_id list) | - |
| `channels.feishu.groupPolicy` | Group policy | `open` |
| `channels.feishu.groupAllowFrom` | Group allowlist | - |
| `channels.feishu.groups.<chat_id>.requireMention` | Require @mention | `true` |
| `channels.feishu.groups.<chat_id>.enabled` | Enable group | `true` |
| `channels.feishu.textChunkLimit` | Message chunk size | `2000` |
| `channels.feishu.mediaMaxMb` | Media size limit | `30` |
| `channels.feishu.streaming` | Enable streaming card output | `true` |
| `channels.feishu.blockStreaming` | Enable block streaming | `true` |

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#dmpolicy-reference)  dmPolicy reference

| Value | Behavior |
| --- | --- |
| `"pairing"` | **Default.** Unknown users get a pairing code; must be approved |
| `"allowlist"` | Only users in `allowFrom` can chat |
| `"open"` | Allow all users (requires `"*"` in allowFrom) |
| `"disabled"` | Disable DMs |

* * *

## [​](https://docs.openclaw.ai/channels/feishu\#supported-message-types)  Supported message types

### [​](https://docs.openclaw.ai/channels/feishu\#receive)  Receive

- ✅ Text
- ✅ Rich text (post)
- ✅ Images
- ✅ Files
- ✅ Audio
- ✅ Video
- ✅ Stickers

### [​](https://docs.openclaw.ai/channels/feishu\#send)  Send

- ✅ Text
- ✅ Images
- ✅ Files
- ✅ Audio
- ⚠️ Rich text (partial support)

[Slack](https://docs.openclaw.ai/channels/slack) [Google Chat](https://docs.openclaw.ai/channels/googlechat)

Ctrl+I

![Create enterprise app](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step2-create-app.png?w=1100&fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=1eb92424c734bff1c0e004a3d85c5d77)

![Get credentials](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step3-credentials.png?w=1100&fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=fad3a366183ca561c8b6a0d808aa7158)

![Configure permissions](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step4-permissions.png?w=1100&fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=04c44566762d323e6dafec830b3b12f2)

![Enable bot capability](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step5-bot-capability.png?w=1100&fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=29107c5a0a58b1e8cfe033ae2d76b011)

![Configure event subscription](https://mintcdn.com/clawdhub/6NERQ7Dymau_gJ4k/images/feishu-step6-event-subscription.png?w=1100&fit=max&auto=format&n=6NERQ7Dymau_gJ4k&q=85&s=f11ce5b381e1820794d2020c82f07be9)

---

---

<!-- Page: Matrix -->

## Matrix

**Source:** https://docs.openclaw.ai/channels/matrix

[Skip to main content](https://docs.openclaw.ai/channels/matrix#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Matrix

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Matrix (plugin)](https://docs.openclaw.ai/channels/matrix#matrix-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/matrix#plugin-required)
- [Setup](https://docs.openclaw.ai/channels/matrix#setup)
- [Encryption (E2EE)](https://docs.openclaw.ai/channels/matrix#encryption-e2ee)
- [Multi-account](https://docs.openclaw.ai/channels/matrix#multi-account)
- [Routing model](https://docs.openclaw.ai/channels/matrix#routing-model)
- [Access control (DMs)](https://docs.openclaw.ai/channels/matrix#access-control-dms)
- [Rooms (groups)](https://docs.openclaw.ai/channels/matrix#rooms-groups)
- [Threads](https://docs.openclaw.ai/channels/matrix#threads)
- [Capabilities](https://docs.openclaw.ai/channels/matrix#capabilities)
- [Troubleshooting](https://docs.openclaw.ai/channels/matrix#troubleshooting)
- [Configuration reference (Matrix)](https://docs.openclaw.ai/channels/matrix#configuration-reference-matrix)

# [​](https://docs.openclaw.ai/channels/matrix\#matrix-plugin)  Matrix (plugin)

Matrix is an open, decentralized messaging protocol. OpenClaw connects as a Matrix **user**
on any homeserver, so you need a Matrix account for the bot. Once it is logged in, you can DM
the bot directly or invite it to rooms (Matrix “groups”). Beeper is a valid client option too,
but it requires E2EE to be enabled.Status: supported via plugin (@vector-im/matrix-bot-sdk). Direct messages, rooms, threads, media, reactions,
polls (send + poll-start as text), location, and E2EE (with crypto support).

## [​](https://docs.openclaw.ai/channels/matrix\#plugin-required)  Plugin required

Matrix ships as a plugin and is not bundled with the core install.Install via CLI (npm registry):

Copy

```
openclaw plugins install @openclaw/matrix
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/matrix
```

If you choose Matrix during configure/onboarding and a git checkout is detected,
OpenClaw will offer the local install path automatically.Details: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/channels/matrix\#setup)  Setup

1. Install the Matrix plugin:   - From npm: `openclaw plugins install @openclaw/matrix`
   - From a local checkout: `openclaw plugins install ./extensions/matrix`
2. Create a Matrix account on a homeserver:   - Browse hosting options at [https://matrix.org/ecosystem/hosting/](https://matrix.org/ecosystem/hosting/)
   - Or host it yourself.
3. Get an access token for the bot account:

   - Use the Matrix login API with `curl` at your home server:

Copy

```
curl --request POST \
  --url https://matrix.example.org/_matrix/client/v3/login \
  --header 'Content-Type: application/json' \
  --data '{
  "type": "m.login.password",
  "identifier": {
    "type": "m.id.user",
    "user": "your-user-name"
  },
  "password": "your-password"
}'
```

   - Replace `matrix.example.org` with your homeserver URL.
   - Or set `channels.matrix.userId` \+ `channels.matrix.password`: OpenClaw calls the same
     login endpoint, stores the access token in `~/.openclaw/credentials/matrix/credentials.json`,
     and reuses it on next start.
4. Configure credentials:   - Env: `MATRIX_HOMESERVER`, `MATRIX_ACCESS_TOKEN` (or `MATRIX_USER_ID` \+ `MATRIX_PASSWORD`)
   - Or config: `channels.matrix.*`
   - If both are set, config takes precedence.
   - With access token: user ID is fetched automatically via `/whoami`.
   - When set, `channels.matrix.userId` should be the full Matrix ID (example: `@bot:example.org`).
5. Restart the gateway (or finish onboarding).
6. Start a DM with the bot or invite it to a room from any Matrix client
(Element, Beeper, etc.; see [https://matrix.org/ecosystem/clients/](https://matrix.org/ecosystem/clients/)). Beeper requires E2EE,
so set `channels.matrix.encryption: true` and verify the device.

Minimal config (access token, user ID auto-fetched):

Copy

```
{
  channels: {
    matrix: {
      enabled: true,
      homeserver: "https://matrix.example.org",
      accessToken: "syt_***",
      dm: { policy: "pairing" },
    },
  },
}
```

E2EE config (end to end encryption enabled):

Copy

```
{
  channels: {
    matrix: {
      enabled: true,
      homeserver: "https://matrix.example.org",
      accessToken: "syt_***",
      encryption: true,
      dm: { policy: "pairing" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/matrix\#encryption-e2ee)  Encryption (E2EE)

End-to-end encryption is **supported** via the Rust crypto SDK.Enable with `channels.matrix.encryption: true`:

- If the crypto module loads, encrypted rooms are decrypted automatically.
- Outbound media is encrypted when sending to encrypted rooms.
- On first connection, OpenClaw requests device verification from your other sessions.
- Verify the device in another Matrix client (Element, etc.) to enable key sharing.
- If the crypto module cannot be loaded, E2EE is disabled and encrypted rooms will not decrypt;
OpenClaw logs a warning.
- If you see missing crypto module errors (for example, `@matrix-org/matrix-sdk-crypto-nodejs-*`),
allow build scripts for `@matrix-org/matrix-sdk-crypto-nodejs` and run
`pnpm rebuild @matrix-org/matrix-sdk-crypto-nodejs` or fetch the binary with
`node node_modules/@matrix-org/matrix-sdk-crypto-nodejs/download-lib.js`.

Crypto state is stored per account + access token in
`~/.openclaw/matrix/accounts/<account>/<homeserver>__<user>/<token-hash>/crypto/`
(SQLite database). Sync state lives alongside it in `bot-storage.json`.
If the access token (device) changes, a new store is created and the bot must be
re-verified for encrypted rooms.**Device verification:**
When E2EE is enabled, the bot will request verification from your other sessions on startup.
Open Element (or another client) and approve the verification request to establish trust.
Once verified, the bot can decrypt messages in encrypted rooms.

## [​](https://docs.openclaw.ai/channels/matrix\#multi-account)  Multi-account

Multi-account support: use `channels.matrix.accounts` with per-account credentials and optional `name`. See [`gateway/configuration`](https://docs.openclaw.ai/gateway/configuration#telegramaccounts--discordaccounts--slackaccounts--signalaccounts--imessageaccounts) for the shared pattern.Each account runs as a separate Matrix user on any homeserver. Per-account config
inherits from the top-level `channels.matrix` settings and can override any option
(DM policy, groups, encryption, etc.).

Copy

```
{
  channels: {
    matrix: {
      enabled: true,
      dm: { policy: "pairing" },
      accounts: {
        assistant: {
          name: "Main assistant",
          homeserver: "https://matrix.example.org",
          accessToken: "syt_assistant_***",
          encryption: true,
        },
        alerts: {
          name: "Alerts bot",
          homeserver: "https://matrix.example.org",
          accessToken: "syt_alerts_***",
          dm: { policy: "allowlist", allowFrom: ["@admin:example.org"] },
        },
      },
    },
  },
}
```

Notes:

- Account startup is serialized to avoid race conditions with concurrent module imports.
- Env variables (`MATRIX_HOMESERVER`, `MATRIX_ACCESS_TOKEN`, etc.) only apply to the **default** account.
- Base channel settings (DM policy, group policy, mention gating, etc.) apply to all accounts unless overridden per account.
- Use `bindings[].match.accountId` to route each account to a different agent.
- Crypto state is stored per account + access token (separate key stores per account).

## [​](https://docs.openclaw.ai/channels/matrix\#routing-model)  Routing model

- Replies always go back to Matrix.
- DMs share the agent’s main session; rooms map to group sessions.

## [​](https://docs.openclaw.ai/channels/matrix\#access-control-dms)  Access control (DMs)

- Default: `channels.matrix.dm.policy = "pairing"`. Unknown senders get a pairing code.
- Approve via:
  - `openclaw pairing list matrix`
  - `openclaw pairing approve matrix <CODE>`
- Public DMs: `channels.matrix.dm.policy="open"` plus `channels.matrix.dm.allowFrom=["*"]`.
- `channels.matrix.dm.allowFrom` accepts full Matrix user IDs (example: `@user:server`). The wizard resolves display names to user IDs when directory search finds a single exact match.
- Do not use display names or bare localparts (example: `"Alice"` or `"alice"`). They are ambiguous and are ignored for allowlist matching. Use full `@user:server` IDs.

## [​](https://docs.openclaw.ai/channels/matrix\#rooms-groups)  Rooms (groups)

- Default: `channels.matrix.groupPolicy = "allowlist"` (mention-gated). Use `channels.defaults.groupPolicy` to override the default when unset.
- Runtime note: if `channels.matrix` is completely missing, runtime falls back to `groupPolicy="allowlist"` for room checks (even if `channels.defaults.groupPolicy` is set).
- Allowlist rooms with `channels.matrix.groups` (room IDs or aliases; names are resolved to IDs when directory search finds a single exact match):

Copy

```
{
  channels: {
    matrix: {
      groupPolicy: "allowlist",
      groups: {
        "!roomId:example.org": { allow: true },
        "#alias:example.org": { allow: true },
      },
      groupAllowFrom: ["@owner:example.org"],
    },
  },
}
```

- `requireMention: false` enables auto-reply in that room.
- `groups."*"` can set defaults for mention gating across rooms.
- `groupAllowFrom` restricts which senders can trigger the bot in rooms (full Matrix user IDs).
- Per-room `users` allowlists can further restrict senders inside a specific room (use full Matrix user IDs).
- The configure wizard prompts for room allowlists (room IDs, aliases, or names) and resolves names only on an exact, unique match.
- On startup, OpenClaw resolves room/user names in allowlists to IDs and logs the mapping; unresolved entries are ignored for allowlist matching.
- Invites are auto-joined by default; control with `channels.matrix.autoJoin` and `channels.matrix.autoJoinAllowlist`.
- To allow **no rooms**, set `channels.matrix.groupPolicy: "disabled"` (or keep an empty allowlist).
- Legacy key: `channels.matrix.rooms` (same shape as `groups`).

## [​](https://docs.openclaw.ai/channels/matrix\#threads)  Threads

- Reply threading is supported.
- `channels.matrix.threadReplies`controls whether replies stay in threads:

  - `off`, `inbound` (default), `always`
- `channels.matrix.replyToMode`controls reply-to metadata when not replying in a thread:

  - `off` (default), `first`, `all`

## [​](https://docs.openclaw.ai/channels/matrix\#capabilities)  Capabilities

| Feature | Status |
| --- | --- |
| Direct messages | ✅ Supported |
| Rooms | ✅ Supported |
| Threads | ✅ Supported |
| Media | ✅ Supported |
| E2EE | ✅ Supported (crypto module required) |
| Reactions | ✅ Supported (send/read via tools) |
| Polls | ✅ Send supported; inbound poll starts are converted to text (responses/ends ignored) |
| Location | ✅ Supported (geo URI; altitude ignored) |
| Native commands | ✅ Supported |

## [​](https://docs.openclaw.ai/channels/matrix\#troubleshooting)  Troubleshooting

Run this ladder first:

Copy

```
openclaw status
openclaw gateway status
openclaw logs --follow
openclaw doctor
openclaw channels status --probe
```

Then confirm DM pairing state if needed:

Copy

```
openclaw pairing list matrix
```

Common failures:

- Logged in but room messages ignored: room blocked by `groupPolicy` or room allowlist.
- DMs ignored: sender pending approval when `channels.matrix.dm.policy="pairing"`.
- Encrypted rooms fail: crypto support or encryption settings mismatch.

For triage flow: [/channels/troubleshooting](https://docs.openclaw.ai/channels/troubleshooting).

## [​](https://docs.openclaw.ai/channels/matrix\#configuration-reference-matrix)  Configuration reference (Matrix)

Full configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)Provider options:

- `channels.matrix.enabled`: enable/disable channel startup.
- `channels.matrix.homeserver`: homeserver URL.
- `channels.matrix.userId`: Matrix user ID (optional with access token).
- `channels.matrix.accessToken`: access token.
- `channels.matrix.password`: password for login (token stored).
- `channels.matrix.deviceName`: device display name.
- `channels.matrix.encryption`: enable E2EE (default: false).
- `channels.matrix.initialSyncLimit`: initial sync limit.
- `channels.matrix.threadReplies`: `off | inbound | always` (default: inbound).
- `channels.matrix.textChunkLimit`: outbound text chunk size (chars).
- `channels.matrix.chunkMode`: `length` (default) or `newline` to split on blank lines (paragraph boundaries) before length chunking.
- `channels.matrix.dm.policy`: `pairing | allowlist | open | disabled` (default: pairing).
- `channels.matrix.dm.allowFrom`: DM allowlist (full Matrix user IDs). `open` requires `"*"`. The wizard resolves names to IDs when possible.
- `channels.matrix.groupPolicy`: `allowlist | open | disabled` (default: allowlist).
- `channels.matrix.groupAllowFrom`: allowlisted senders for group messages (full Matrix user IDs).
- `channels.matrix.allowlistOnly`: force allowlist rules for DMs + rooms.
- `channels.matrix.groups`: group allowlist + per-room settings map.
- `channels.matrix.rooms`: legacy group allowlist/config.
- `channels.matrix.replyToMode`: reply-to mode for threads/tags.
- `channels.matrix.mediaMaxMb`: inbound/outbound media cap (MB).
- `channels.matrix.autoJoin`: invite handling (`always | allowlist | off`, default: always).
- `channels.matrix.autoJoinAllowlist`: allowed room IDs/aliases for auto-join.
- `channels.matrix.accounts`: multi-account configuration keyed by account ID (each account inherits top-level settings).
- `channels.matrix.actions`: per-action tool gating (reactions/messages/pins/memberInfo/channelInfo).

[LINE](https://docs.openclaw.ai/channels/line) [Nextcloud Talk](https://docs.openclaw.ai/channels/nextcloud-talk)

Ctrl+I

---

---

<!-- Page: IRC -->

## IRC

**Source:** https://docs.openclaw.ai/channels/irc

[Skip to main content](https://docs.openclaw.ai/channels/irc#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

IRC

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Quick start](https://docs.openclaw.ai/channels/irc#quick-start)
- [Security defaults](https://docs.openclaw.ai/channels/irc#security-defaults)
- [Access control](https://docs.openclaw.ai/channels/irc#access-control)
- [Common gotcha: allowFrom is for DMs, not channels](https://docs.openclaw.ai/channels/irc#common-gotcha-allowfrom-is-for-dms-not-channels)
- [Reply triggering (mentions)](https://docs.openclaw.ai/channels/irc#reply-triggering-mentions)
- [Security note (recommended for public channels)](https://docs.openclaw.ai/channels/irc#security-note-recommended-for-public-channels)
- [Same tools for everyone in the channel](https://docs.openclaw.ai/channels/irc#same-tools-for-everyone-in-the-channel)
- [Different tools per sender (owner gets more power)](https://docs.openclaw.ai/channels/irc#different-tools-per-sender-owner-gets-more-power)
- [NickServ](https://docs.openclaw.ai/channels/irc#nickserv)
- [Environment variables](https://docs.openclaw.ai/channels/irc#environment-variables)
- [Troubleshooting](https://docs.openclaw.ai/channels/irc#troubleshooting)

Use IRC when you want OpenClaw in classic channels (`#room`) and direct messages.
IRC ships as an extension plugin, but it is configured in the main config under `channels.irc`.

## [​](https://docs.openclaw.ai/channels/irc\#quick-start)  Quick start

1. Enable IRC config in `~/.openclaw/openclaw.json`.
2. Set at least:

Copy

```
{
  "channels": {
    "irc": {
      "enabled": true,
      "host": "irc.libera.chat",
      "port": 6697,
      "tls": true,
      "nick": "openclaw-bot",
      "channels": ["#openclaw"]
    }
  }
}
```

3. Start/restart gateway:

Copy

```
openclaw gateway run
```

## [​](https://docs.openclaw.ai/channels/irc\#security-defaults)  Security defaults

- `channels.irc.dmPolicy` defaults to `"pairing"`.
- `channels.irc.groupPolicy` defaults to `"allowlist"`.
- With `groupPolicy="allowlist"`, set `channels.irc.groups` to define allowed channels.
- Use TLS (`channels.irc.tls=true`) unless you intentionally accept plaintext transport.

## [​](https://docs.openclaw.ai/channels/irc\#access-control)  Access control

There are two separate “gates” for IRC channels:

1. **Channel access** (`groupPolicy` \+ `groups`): whether the bot accepts messages from a channel at all.
2. **Sender access** (`groupAllowFrom` / per-channel `groups["#channel"].allowFrom`): who is allowed to trigger the bot inside that channel.

Config keys:

- DM allowlist (DM sender access): `channels.irc.allowFrom`
- Group sender allowlist (channel sender access): `channels.irc.groupAllowFrom`
- Per-channel controls (channel + sender + mention rules): `channels.irc.groups["#channel"]`
- `channels.irc.groupPolicy="open"` allows unconfigured channels ( **still mention-gated by default**)

Allowlist entries should use stable sender identities (`nick!user@host`).
Bare nick matching is mutable and only enabled when `channels.irc.dangerouslyAllowNameMatching: true`.

### [​](https://docs.openclaw.ai/channels/irc\#common-gotcha-allowfrom-is-for-dms-not-channels)  Common gotcha: `allowFrom` is for DMs, not channels

If you see logs like:

- `irc: drop group sender alice!ident@host (policy=allowlist)`

…it means the sender wasn’t allowed for **group/channel** messages. Fix it by either:

- setting `channels.irc.groupAllowFrom` (global for all channels), or
- setting per-channel sender allowlists: `channels.irc.groups["#channel"].allowFrom`

Example (allow anyone in `#tuirc-dev` to talk to the bot):

Copy

```
{
  channels: {
    irc: {
      groupPolicy: "allowlist",
      groups: {
        "#tuirc-dev": { allowFrom: ["*"] },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/irc\#reply-triggering-mentions)  Reply triggering (mentions)

Even if a channel is allowed (via `groupPolicy` \+ `groups`) and the sender is allowed, OpenClaw defaults to **mention-gating** in group contexts.That means you may see logs like `drop channel … (missing-mention)` unless the message includes a mention pattern that matches the bot.To make the bot reply in an IRC channel **without needing a mention**, disable mention gating for that channel:

Copy

```
{
  channels: {
    irc: {
      groupPolicy: "allowlist",
      groups: {
        "#tuirc-dev": {
          requireMention: false,
          allowFrom: ["*"],
        },
      },
    },
  },
}
```

Or to allow **all** IRC channels (no per-channel allowlist) and still reply without mentions:

Copy

```
{
  channels: {
    irc: {
      groupPolicy: "open",
      groups: {
        "*": { requireMention: false, allowFrom: ["*"] },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/irc\#security-note-recommended-for-public-channels)  Security note (recommended for public channels)

If you allow `allowFrom: ["*"]` in a public channel, anyone can prompt the bot.
To reduce risk, restrict tools for that channel.

### [​](https://docs.openclaw.ai/channels/irc\#same-tools-for-everyone-in-the-channel)  Same tools for everyone in the channel

Copy

```
{
  channels: {
    irc: {
      groups: {
        "#tuirc-dev": {
          allowFrom: ["*"],
          tools: {
            deny: ["group:runtime", "group:fs", "gateway", "nodes", "cron", "browser"],
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/irc\#different-tools-per-sender-owner-gets-more-power)  Different tools per sender (owner gets more power)

Use `toolsBySender` to apply a stricter policy to `"*"` and a looser one to your nick:

Copy

```
{
  channels: {
    irc: {
      groups: {
        "#tuirc-dev": {
          allowFrom: ["*"],
          toolsBySender: {
            "*": {
              deny: ["group:runtime", "group:fs", "gateway", "nodes", "cron", "browser"],
            },
            "id:eigen": {
              deny: ["gateway", "nodes", "cron"],
            },
          },
        },
      },
    },
  },
}
```

Notes:

- `toolsBySender` keys should use `id:` for IRC sender identity values:
`id:eigen` or `id:eigen!~eigen@174.127.248.171` for stronger matching.
- Legacy unprefixed keys are still accepted and matched as `id:` only.
- The first matching sender policy wins; `"*"` is the wildcard fallback.

For more on group access vs mention-gating (and how they interact), see: [/channels/groups](https://docs.openclaw.ai/channels/groups).

## [​](https://docs.openclaw.ai/channels/irc\#nickserv)  NickServ

To identify with NickServ after connect:

Copy

```
{
  "channels": {
    "irc": {
      "nickserv": {
        "enabled": true,
        "service": "NickServ",
        "password": "your-nickserv-password"
      }
    }
  }
}
```

Optional one-time registration on connect:

Copy

```
{
  "channels": {
    "irc": {
      "nickserv": {
        "register": true,
        "registerEmail": "bot@example.com"
      }
    }
  }
}
```

Disable `register` after the nick is registered to avoid repeated REGISTER attempts.

## [​](https://docs.openclaw.ai/channels/irc\#environment-variables)  Environment variables

Default account supports:

- `IRC_HOST`
- `IRC_PORT`
- `IRC_TLS`
- `IRC_NICK`
- `IRC_USERNAME`
- `IRC_REALNAME`
- `IRC_PASSWORD`
- `IRC_CHANNELS` (comma-separated)
- `IRC_NICKSERV_PASSWORD`
- `IRC_NICKSERV_REGISTER_EMAIL`

## [​](https://docs.openclaw.ai/channels/irc\#troubleshooting)  Troubleshooting

- If the bot connects but never replies in channels, verify `channels.irc.groups` **and** whether mention-gating is dropping messages (`missing-mention`). If you want it to reply without pings, set `requireMention:false` for the channel.
- If login fails, verify nick availability and server password.
- If TLS fails on a custom network, verify host/port and certificate setup.

[Discord](https://docs.openclaw.ai/channels/discord) [Slack](https://docs.openclaw.ai/channels/slack)

Ctrl+I

---

---

<!-- Page: Nextcloud Talk -->

## Nextcloud Talk

**Source:** https://docs.openclaw.ai/channels/nextcloud-talk

[Skip to main content](https://docs.openclaw.ai/channels/nextcloud-talk#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Nextcloud Talk

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Nextcloud Talk (plugin)](https://docs.openclaw.ai/channels/nextcloud-talk#nextcloud-talk-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/nextcloud-talk#plugin-required)
- [Quick setup (beginner)](https://docs.openclaw.ai/channels/nextcloud-talk#quick-setup-beginner)
- [Notes](https://docs.openclaw.ai/channels/nextcloud-talk#notes)
- [Access control (DMs)](https://docs.openclaw.ai/channels/nextcloud-talk#access-control-dms)
- [Rooms (groups)](https://docs.openclaw.ai/channels/nextcloud-talk#rooms-groups)
- [Capabilities](https://docs.openclaw.ai/channels/nextcloud-talk#capabilities)
- [Configuration reference (Nextcloud Talk)](https://docs.openclaw.ai/channels/nextcloud-talk#configuration-reference-nextcloud-talk)

# [​](https://docs.openclaw.ai/channels/nextcloud-talk\#nextcloud-talk-plugin)  Nextcloud Talk (plugin)

Status: supported via plugin (webhook bot). Direct messages, rooms, reactions, and markdown messages are supported.

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#plugin-required)  Plugin required

Nextcloud Talk ships as a plugin and is not bundled with the core install.Install via CLI (npm registry):

Copy

```
openclaw plugins install @openclaw/nextcloud-talk
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/nextcloud-talk
```

If you choose Nextcloud Talk during configure/onboarding and a git checkout is detected,
OpenClaw will offer the local install path automatically.Details: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#quick-setup-beginner)  Quick setup (beginner)

1. Install the Nextcloud Talk plugin.
2. On your Nextcloud server, create a bot:






Copy











```
./occ talk:bot:install "OpenClaw" "<shared-secret>" "<webhook-url>" --feature reaction
```

3. Enable the bot in the target room settings.
4. Configure OpenClaw:   - Config: `channels.nextcloud-talk.baseUrl` \+ `channels.nextcloud-talk.botSecret`
   - Or env: `NEXTCLOUD_TALK_BOT_SECRET` (default account only)
5. Restart the gateway (or finish onboarding).

Minimal config:

Copy

```
{
  channels: {
    "nextcloud-talk": {
      enabled: true,
      baseUrl: "https://cloud.example.com",
      botSecret: "shared-secret",
      dmPolicy: "pairing",
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#notes)  Notes

- Bots cannot initiate DMs. The user must message the bot first.
- Webhook URL must be reachable by the Gateway; set `webhookPublicUrl` if behind a proxy.
- Media uploads are not supported by the bot API; media is sent as URLs.
- The webhook payload does not distinguish DMs vs rooms; set `apiUser` \+ `apiPassword` to enable room-type lookups (otherwise DMs are treated as rooms).

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#access-control-dms)  Access control (DMs)

- Default: `channels.nextcloud-talk.dmPolicy = "pairing"`. Unknown senders get a pairing code.
- Approve via:
  - `openclaw pairing list nextcloud-talk`
  - `openclaw pairing approve nextcloud-talk <CODE>`
- Public DMs: `channels.nextcloud-talk.dmPolicy="open"` plus `channels.nextcloud-talk.allowFrom=["*"]`.
- `allowFrom` matches Nextcloud user IDs only; display names are ignored.

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#rooms-groups)  Rooms (groups)

- Default: `channels.nextcloud-talk.groupPolicy = "allowlist"` (mention-gated).
- Allowlist rooms with `channels.nextcloud-talk.rooms`:

Copy

```
{
  channels: {
    "nextcloud-talk": {
      rooms: {
        "room-token": { requireMention: true },
      },
    },
  },
}
```

- To allow no rooms, keep the allowlist empty or set `channels.nextcloud-talk.groupPolicy="disabled"`.

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#capabilities)  Capabilities

| Feature | Status |
| --- | --- |
| Direct messages | Supported |
| Rooms | Supported |
| Threads | Not supported |
| Media | URL-only |
| Reactions | Supported |
| Native commands | Not supported |

## [​](https://docs.openclaw.ai/channels/nextcloud-talk\#configuration-reference-nextcloud-talk)  Configuration reference (Nextcloud Talk)

Full configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)Provider options:

- `channels.nextcloud-talk.enabled`: enable/disable channel startup.
- `channels.nextcloud-talk.baseUrl`: Nextcloud instance URL.
- `channels.nextcloud-talk.botSecret`: bot shared secret.
- `channels.nextcloud-talk.botSecretFile`: secret file path.
- `channels.nextcloud-talk.apiUser`: API user for room lookups (DM detection).
- `channels.nextcloud-talk.apiPassword`: API/app password for room lookups.
- `channels.nextcloud-talk.apiPasswordFile`: API password file path.
- `channels.nextcloud-talk.webhookPort`: webhook listener port (default: 8788).
- `channels.nextcloud-talk.webhookHost`: webhook host (default: 0.0.0.0).
- `channels.nextcloud-talk.webhookPath`: webhook path (default: /nextcloud-talk-webhook).
- `channels.nextcloud-talk.webhookPublicUrl`: externally reachable webhook URL.
- `channels.nextcloud-talk.dmPolicy`: `pairing | allowlist | open | disabled`.
- `channels.nextcloud-talk.allowFrom`: DM allowlist (user IDs). `open` requires `"*"`.
- `channels.nextcloud-talk.groupPolicy`: `allowlist | open | disabled`.
- `channels.nextcloud-talk.groupAllowFrom`: group allowlist (user IDs).
- `channels.nextcloud-talk.rooms`: per-room settings and allowlist.
- `channels.nextcloud-talk.historyLimit`: group history limit (0 disables).
- `channels.nextcloud-talk.dmHistoryLimit`: DM history limit (0 disables).
- `channels.nextcloud-talk.dms`: per-DM overrides (historyLimit).
- `channels.nextcloud-talk.textChunkLimit`: outbound text chunk size (chars).
- `channels.nextcloud-talk.chunkMode`: `length` (default) or `newline` to split on blank lines (paragraph boundaries) before length chunking.
- `channels.nextcloud-talk.blockStreaming`: disable block streaming for this channel.
- `channels.nextcloud-talk.blockStreamingCoalesce`: block streaming coalesce tuning.
- `channels.nextcloud-talk.mediaMaxMb`: inbound media cap (MB).

[Matrix](https://docs.openclaw.ai/channels/matrix) [Nostr](https://docs.openclaw.ai/channels/nostr)

Ctrl+I

---

---

<!-- Page: Synology Chat -->

## Synology Chat

**Source:** https://docs.openclaw.ai/channels/synology-chat

[Skip to main content](https://docs.openclaw.ai/channels/synology-chat#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Synology Chat

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Synology Chat (plugin)](https://docs.openclaw.ai/channels/synology-chat#synology-chat-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/synology-chat#plugin-required)
- [Quick setup](https://docs.openclaw.ai/channels/synology-chat#quick-setup)
- [Environment variables](https://docs.openclaw.ai/channels/synology-chat#environment-variables)
- [DM policy and access control](https://docs.openclaw.ai/channels/synology-chat#dm-policy-and-access-control)
- [Outbound delivery](https://docs.openclaw.ai/channels/synology-chat#outbound-delivery)
- [Multi-account](https://docs.openclaw.ai/channels/synology-chat#multi-account)
- [Security notes](https://docs.openclaw.ai/channels/synology-chat#security-notes)

# [​](https://docs.openclaw.ai/channels/synology-chat\#synology-chat-plugin)  Synology Chat (plugin)

Status: supported via plugin as a direct-message channel using Synology Chat webhooks.
The plugin accepts inbound messages from Synology Chat outgoing webhooks and sends replies
through a Synology Chat incoming webhook.

## [​](https://docs.openclaw.ai/channels/synology-chat\#plugin-required)  Plugin required

Synology Chat is plugin-based and not part of the default core channel install.Install from a local checkout:

Copy

```
openclaw plugins install ./extensions/synology-chat
```

Details: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/channels/synology-chat\#quick-setup)  Quick setup

1. Install and enable the Synology Chat plugin.
2. In Synology Chat integrations:
   - Create an incoming webhook and copy its URL.
   - Create an outgoing webhook with your secret token.
3. Point the outgoing webhook URL to your OpenClaw gateway:
   - `https://gateway-host/webhook/synology` by default.
   - Or your custom `channels.synology-chat.webhookPath`.
4. Configure `channels.synology-chat` in OpenClaw.
5. Restart gateway and send a DM to the Synology Chat bot.

Minimal config:

Copy

```
{
  channels: {
    "synology-chat": {
      enabled: true,
      token: "synology-outgoing-token",
      incomingUrl: "https://nas.example.com/webapi/entry.cgi?api=SYNO.Chat.External&method=incoming&version=2&token=...",
      webhookPath: "/webhook/synology",
      dmPolicy: "allowlist",
      allowedUserIds: ["123456"],
      rateLimitPerMinute: 30,
      allowInsecureSsl: false,
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/synology-chat\#environment-variables)  Environment variables

For the default account, you can use env vars:

- `SYNOLOGY_CHAT_TOKEN`
- `SYNOLOGY_CHAT_INCOMING_URL`
- `SYNOLOGY_NAS_HOST`
- `SYNOLOGY_ALLOWED_USER_IDS` (comma-separated)
- `SYNOLOGY_RATE_LIMIT`
- `OPENCLAW_BOT_NAME`

Config values override env vars.

## [​](https://docs.openclaw.ai/channels/synology-chat\#dm-policy-and-access-control)  DM policy and access control

- `dmPolicy: "allowlist"` is the recommended default.
- `allowedUserIds` accepts a list (or comma-separated string) of Synology user IDs.
- In `allowlist` mode, an empty `allowedUserIds` list is treated as misconfiguration and the webhook route will not start (use `dmPolicy: "open"` for allow-all).
- `dmPolicy: "open"` allows any sender.
- `dmPolicy: "disabled"` blocks DMs.
- Pairing approvals work with:
  - `openclaw pairing list synology-chat`
  - `openclaw pairing approve synology-chat <CODE>`

## [​](https://docs.openclaw.ai/channels/synology-chat\#outbound-delivery)  Outbound delivery

Use numeric Synology Chat user IDs as targets.Examples:

Copy

```
openclaw message send --channel synology-chat --target 123456 --text "Hello from OpenClaw"
openclaw message send --channel synology-chat --target synology-chat:123456 --text "Hello again"
```

Media sends are supported by URL-based file delivery.

## [​](https://docs.openclaw.ai/channels/synology-chat\#multi-account)  Multi-account

Multiple Synology Chat accounts are supported under `channels.synology-chat.accounts`.
Each account can override token, incoming URL, webhook path, DM policy, and limits.

Copy

```
{
  channels: {
    "synology-chat": {
      enabled: true,
      accounts: {
        default: {
          token: "token-a",
          incomingUrl: "https://nas-a.example.com/...token=...",
        },
        alerts: {
          token: "token-b",
          incomingUrl: "https://nas-b.example.com/...token=...",
          webhookPath: "/webhook/synology-alerts",
          dmPolicy: "allowlist",
          allowedUserIds: ["987654"],
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/synology-chat\#security-notes)  Security notes

- Keep `token` secret and rotate it if leaked.
- Keep `allowInsecureSsl: false` unless you explicitly trust a self-signed local NAS cert.
- Inbound webhook requests are token-verified and rate-limited per sender.
- Prefer `dmPolicy: "allowlist"` for production.

[Microsoft Teams](https://docs.openclaw.ai/channels/msteams) [LINE](https://docs.openclaw.ai/channels/line)

Ctrl+I

---
