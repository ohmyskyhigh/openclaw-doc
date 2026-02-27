# Channels Messaging Apps


---

<!-- Page: BlueBubbles -->

## BlueBubbles

**Source:** https://docs.openclaw.ai/channels/bluebubbles

[Skip to main content](https://docs.openclaw.ai/channels/bluebubbles#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

BlueBubbles

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [BlueBubbles (macOS REST)](https://docs.openclaw.ai/channels/bluebubbles#bluebubbles-macos-rest)
- [Overview](https://docs.openclaw.ai/channels/bluebubbles#overview)
- [Quick start](https://docs.openclaw.ai/channels/bluebubbles#quick-start)
- [Keeping Messages.app alive (VM / headless setups)](https://docs.openclaw.ai/channels/bluebubbles#keeping-messages-app-alive-vm-%2F-headless-setups)
- [1) Save the AppleScript](https://docs.openclaw.ai/channels/bluebubbles#1-save-the-applescript)
- [2) Install a LaunchAgent](https://docs.openclaw.ai/channels/bluebubbles#2-install-a-launchagent)
- [Onboarding](https://docs.openclaw.ai/channels/bluebubbles#onboarding)
- [Access control (DMs + groups)](https://docs.openclaw.ai/channels/bluebubbles#access-control-dms-%2B-groups)
- [Mention gating (groups)](https://docs.openclaw.ai/channels/bluebubbles#mention-gating-groups)
- [Command gating](https://docs.openclaw.ai/channels/bluebubbles#command-gating)
- [Typing + read receipts](https://docs.openclaw.ai/channels/bluebubbles#typing-%2B-read-receipts)
- [Advanced actions](https://docs.openclaw.ai/channels/bluebubbles#advanced-actions)
- [Message IDs (short vs full)](https://docs.openclaw.ai/channels/bluebubbles#message-ids-short-vs-full)
- [Block streaming](https://docs.openclaw.ai/channels/bluebubbles#block-streaming)
- [Media + limits](https://docs.openclaw.ai/channels/bluebubbles#media-%2B-limits)
- [Configuration reference](https://docs.openclaw.ai/channels/bluebubbles#configuration-reference)
- [Addressing / delivery targets](https://docs.openclaw.ai/channels/bluebubbles#addressing-%2F-delivery-targets)
- [Security](https://docs.openclaw.ai/channels/bluebubbles#security)
- [Troubleshooting](https://docs.openclaw.ai/channels/bluebubbles#troubleshooting)

# [​](https://docs.openclaw.ai/channels/bluebubbles\#bluebubbles-macos-rest)  BlueBubbles (macOS REST)

Status: bundled plugin that talks to the BlueBubbles macOS server over HTTP. **Recommended for iMessage integration** due to its richer API and easier setup compared to the legacy imsg channel.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#overview)  Overview

- Runs on macOS via the BlueBubbles helper app ( [bluebubbles.app](https://bluebubbles.app/)).
- Recommended/tested: macOS Sequoia (15). macOS Tahoe (26) works; edit is currently broken on Tahoe, and group icon updates may report success but not sync.
- OpenClaw talks to it through its REST API (`GET /api/v1/ping`, `POST /message/text`, `POST /chat/:id/*`).
- Incoming messages arrive via webhooks; outgoing replies, typing indicators, read receipts, and tapbacks are REST calls.
- Attachments and stickers are ingested as inbound media (and surfaced to the agent when possible).
- Pairing/allowlist works the same way as other channels (`/channels/pairing` etc) with `channels.bluebubbles.allowFrom` \+ pairing codes.
- Reactions are surfaced as system events just like Slack/Telegram so agents can “mention” them before replying.
- Advanced features: edit, unsend, reply threading, message effects, group management.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#quick-start)  Quick start

1. Install the BlueBubbles server on your Mac (follow the instructions at [bluebubbles.app/install](https://bluebubbles.app/install)).
2. In the BlueBubbles config, enable the web API and set a password.
3. Run `openclaw onboard` and select BlueBubbles, or configure manually:






Copy











```
{
     channels: {
       bluebubbles: {
         enabled: true,
         serverUrl: "http://192.168.1.100:1234",
         password: "example-password",
         webhookPath: "/bluebubbles-webhook",
       },
     },
}
```

4. Point BlueBubbles webhooks to your gateway (example: `https://your-gateway-host:3000/bluebubbles-webhook?password=<password>`).
5. Start the gateway; it will register the webhook handler and start pairing.

Security note:

- Always set a webhook password.
- Webhook authentication is always required. OpenClaw rejects BlueBubbles webhook requests unless they include a password/guid that matches `channels.bluebubbles.password` (for example `?password=<password>` or `x-password`), regardless of loopback/proxy topology.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#keeping-messages-app-alive-vm-/-headless-setups)  Keeping Messages.app alive (VM / headless setups)

Some macOS VM / always-on setups can end up with Messages.app going “idle” (incoming events stop until the app is opened/foregrounded). A simple workaround is to **poke Messages every 5 minutes** using an AppleScript + LaunchAgent.

### [​](https://docs.openclaw.ai/channels/bluebubbles\#1-save-the-applescript)  1) Save the AppleScript

Save this as:

- `~/Scripts/poke-messages.scpt`

Example script (non-interactive; does not steal focus):

Copy

```
try
  tell application "Messages"
    if not running then
      launch
    end if

    -- Touch the scripting interface to keep the process responsive.
    set _chatCount to (count of chats)
  end tell
on error
  -- Ignore transient failures (first-run prompts, locked session, etc).
end try
```

### [​](https://docs.openclaw.ai/channels/bluebubbles\#2-install-a-launchagent)  2) Install a LaunchAgent

Save this as:

- `~/Library/LaunchAgents/com.user.poke-messages.plist`

Copy

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>com.user.poke-messages</string>

    <key>ProgramArguments</key>
    <array>
      <string>/bin/bash</string>
      <string>-lc</string>
      <string>/usr/bin/osascript &quot;$HOME/Scripts/poke-messages.scpt&quot;</string>
    </array>

    <key>RunAtLoad</key>
    <true/>

    <key>StartInterval</key>
    <integer>300</integer>

    <key>StandardOutPath</key>
    <string>/tmp/poke-messages.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/poke-messages.err</string>
  </dict>
</plist>
```

Notes:

- This runs **every 300 seconds** and **on login**.
- The first run may trigger macOS **Automation** prompts (`osascript` → Messages). Approve them in the same user session that runs the LaunchAgent.

Load it:

Copy

```
launchctl unload ~/Library/LaunchAgents/com.user.poke-messages.plist 2>/dev/null || true
launchctl load ~/Library/LaunchAgents/com.user.poke-messages.plist
```

## [​](https://docs.openclaw.ai/channels/bluebubbles\#onboarding)  Onboarding

BlueBubbles is available in the interactive setup wizard:

Copy

```
openclaw onboard
```

The wizard prompts for:

- **Server URL** (required): BlueBubbles server address (e.g., `http://192.168.1.100:1234`)
- **Password** (required): API password from BlueBubbles Server settings
- **Webhook path** (optional): Defaults to `/bluebubbles-webhook`
- **DM policy**: pairing, allowlist, open, or disabled
- **Allow list**: Phone numbers, emails, or chat targets

You can also add BlueBubbles via CLI:

Copy

```
openclaw channels add bluebubbles --http-url http://192.168.1.100:1234 --password <password>
```

## [​](https://docs.openclaw.ai/channels/bluebubbles\#access-control-dms-+-groups)  Access control (DMs + groups)

DMs:

- Default: `channels.bluebubbles.dmPolicy = "pairing"`.
- Unknown senders receive a pairing code; messages are ignored until approved (codes expire after 1 hour).
- Approve via:
  - `openclaw pairing list bluebubbles`
  - `openclaw pairing approve bluebubbles <CODE>`
- Pairing is the default token exchange. Details: [Pairing](https://docs.openclaw.ai/channels/pairing)

Groups:

- `channels.bluebubbles.groupPolicy = open | allowlist | disabled` (default: `allowlist`).
- `channels.bluebubbles.groupAllowFrom` controls who can trigger in groups when `allowlist` is set.

### [​](https://docs.openclaw.ai/channels/bluebubbles\#mention-gating-groups)  Mention gating (groups)

BlueBubbles supports mention gating for group chats, matching iMessage/WhatsApp behavior:

- Uses `agents.list[].groupChat.mentionPatterns` (or `messages.groupChat.mentionPatterns`) to detect mentions.
- When `requireMention` is enabled for a group, the agent only responds when mentioned.
- Control commands from authorized senders bypass mention gating.

Per-group configuration:

Copy

```
{
  channels: {
    bluebubbles: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["+15555550123"],
      groups: {
        "*": { requireMention: true }, // default for all groups
        "iMessage;-;chat123": { requireMention: false }, // override for specific group
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/bluebubbles\#command-gating)  Command gating

- Control commands (e.g., `/config`, `/model`) require authorization.
- Uses `allowFrom` and `groupAllowFrom` to determine command authorization.
- Authorized senders can run control commands even without mentioning in groups.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#typing-+-read-receipts)  Typing + read receipts

- **Typing indicators**: Sent automatically before and during response generation.
- **Read receipts**: Controlled by `channels.bluebubbles.sendReadReceipts` (default: `true`).
- **Typing indicators**: OpenClaw sends typing start events; BlueBubbles clears typing automatically on send or timeout (manual stop via DELETE is unreliable).

Copy

```
{
  channels: {
    bluebubbles: {
      sendReadReceipts: false, // disable read receipts
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/bluebubbles\#advanced-actions)  Advanced actions

BlueBubbles supports advanced message actions when enabled in config:

Copy

```
{
  channels: {
    bluebubbles: {
      actions: {
        reactions: true, // tapbacks (default: true)
        edit: true, // edit sent messages (macOS 13+, broken on macOS 26 Tahoe)
        unsend: true, // unsend messages (macOS 13+)
        reply: true, // reply threading by message GUID
        sendWithEffect: true, // message effects (slam, loud, etc.)
        renameGroup: true, // rename group chats
        setGroupIcon: true, // set group chat icon/photo (flaky on macOS 26 Tahoe)
        addParticipant: true, // add participants to groups
        removeParticipant: true, // remove participants from groups
        leaveGroup: true, // leave group chats
        sendAttachment: true, // send attachments/media
      },
    },
  },
}
```

Available actions:

- **react**: Add/remove tapback reactions (`messageId`, `emoji`, `remove`)
- **edit**: Edit a sent message (`messageId`, `text`)
- **unsend**: Unsend a message (`messageId`)
- **reply**: Reply to a specific message (`messageId`, `text`, `to`)
- **sendWithEffect**: Send with iMessage effect (`text`, `to`, `effectId`)
- **renameGroup**: Rename a group chat (`chatGuid`, `displayName`)
- **setGroupIcon**: Set a group chat’s icon/photo (`chatGuid`, `media`) — flaky on macOS 26 Tahoe (API may return success but the icon does not sync).
- **addParticipant**: Add someone to a group (`chatGuid`, `address`)
- **removeParticipant**: Remove someone from a group (`chatGuid`, `address`)
- **leaveGroup**: Leave a group chat (`chatGuid`)
- **sendAttachment**: Send media/files (`to`, `buffer`, `filename`, `asVoice`)

  - Voice memos: set `asVoice: true` with **MP3** or **CAF** audio to send as an iMessage voice message. BlueBubbles converts MP3 → CAF when sending voice memos.

### [​](https://docs.openclaw.ai/channels/bluebubbles\#message-ids-short-vs-full)  Message IDs (short vs full)

OpenClaw may surface _short_ message IDs (e.g., `1`, `2`) to save tokens.

- `MessageSid` / `ReplyToId` can be short IDs.
- `MessageSidFull` / `ReplyToIdFull` contain the provider full IDs.
- Short IDs are in-memory; they can expire on restart or cache eviction.
- Actions accept short or full `messageId`, but short IDs will error if no longer available.

Use full IDs for durable automations and storage:

- Templates: `{{MessageSidFull}}`, `{{ReplyToIdFull}}`
- Context: `MessageSidFull` / `ReplyToIdFull` in inbound payloads

See [Configuration](https://docs.openclaw.ai/gateway/configuration) for template variables.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#block-streaming)  Block streaming

Control whether responses are sent as a single message or streamed in blocks:

Copy

```
{
  channels: {
    bluebubbles: {
      blockStreaming: true, // enable block streaming (off by default)
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/bluebubbles\#media-+-limits)  Media + limits

- Inbound attachments are downloaded and stored in the media cache.
- Media cap via `channels.bluebubbles.mediaMaxMb` (default: 8 MB).
- Outbound text is chunked to `channels.bluebubbles.textChunkLimit` (default: 4000 chars).

## [​](https://docs.openclaw.ai/channels/bluebubbles\#configuration-reference)  Configuration reference

Full configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)Provider options:

- `channels.bluebubbles.enabled`: Enable/disable the channel.
- `channels.bluebubbles.serverUrl`: BlueBubbles REST API base URL.
- `channels.bluebubbles.password`: API password.
- `channels.bluebubbles.webhookPath`: Webhook endpoint path (default: `/bluebubbles-webhook`).
- `channels.bluebubbles.dmPolicy`: `pairing | allowlist | open | disabled` (default: `pairing`).
- `channels.bluebubbles.allowFrom`: DM allowlist (handles, emails, E.164 numbers, `chat_id:*`, `chat_guid:*`).
- `channels.bluebubbles.groupPolicy`: `open | allowlist | disabled` (default: `allowlist`).
- `channels.bluebubbles.groupAllowFrom`: Group sender allowlist.
- `channels.bluebubbles.groups`: Per-group config (`requireMention`, etc.).
- `channels.bluebubbles.sendReadReceipts`: Send read receipts (default: `true`).
- `channels.bluebubbles.blockStreaming`: Enable block streaming (default: `false`; required for streaming replies).
- `channels.bluebubbles.textChunkLimit`: Outbound chunk size in chars (default: 4000).
- `channels.bluebubbles.chunkMode`: `length` (default) splits only when exceeding `textChunkLimit`; `newline` splits on blank lines (paragraph boundaries) before length chunking.
- `channels.bluebubbles.mediaMaxMb`: Inbound media cap in MB (default: 8).
- `channels.bluebubbles.mediaLocalRoots`: Explicit allowlist of absolute local directories permitted for outbound local media paths. Local path sends are denied by default unless this is configured. Per-account override: `channels.bluebubbles.accounts.<accountId>.mediaLocalRoots`.
- `channels.bluebubbles.historyLimit`: Max group messages for context (0 disables).
- `channels.bluebubbles.dmHistoryLimit`: DM history limit.
- `channels.bluebubbles.actions`: Enable/disable specific actions.
- `channels.bluebubbles.accounts`: Multi-account configuration.

Related global options:

- `agents.list[].groupChat.mentionPatterns` (or `messages.groupChat.mentionPatterns`).
- `messages.responsePrefix`.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#addressing-/-delivery-targets)  Addressing / delivery targets

Prefer `chat_guid` for stable routing:

- `chat_guid:iMessage;-;+15555550123` (preferred for groups)
- `chat_id:123`
- `chat_identifier:...`
- Direct handles: `+15555550123`, `user@example.com`
  - If a direct handle does not have an existing DM chat, OpenClaw will create one via `POST /api/v1/chat/new`. This requires the BlueBubbles Private API to be enabled.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#security)  Security

- Webhook requests are authenticated by comparing `guid`/`password` query params or headers against `channels.bluebubbles.password`. Requests from `localhost` are also accepted.
- Keep the API password and webhook endpoint secret (treat them like credentials).
- Localhost trust means a same-host reverse proxy can unintentionally bypass the password. If you proxy the gateway, require auth at the proxy and configure `gateway.trustedProxies`. See [Gateway security](https://docs.openclaw.ai/gateway/security#reverse-proxy-configuration).
- Enable HTTPS + firewall rules on the BlueBubbles server if exposing it outside your LAN.

## [​](https://docs.openclaw.ai/channels/bluebubbles\#troubleshooting)  Troubleshooting

- If typing/read events stop working, check the BlueBubbles webhook logs and verify the gateway path matches `channels.bluebubbles.webhookPath`.
- Pairing codes expire after one hour; use `openclaw pairing list bluebubbles` and `openclaw pairing approve bluebubbles <code>`.
- Reactions require the BlueBubbles private API (`POST /api/v1/message/react`); ensure the server version exposes it.
- Edit/unsend require macOS 13+ and a compatible BlueBubbles server version. On macOS 26 (Tahoe), edit is currently broken due to private API changes.
- Group icon updates can be flaky on macOS 26 (Tahoe): the API may return success but the new icon does not sync.
- OpenClaw auto-hides known-broken actions based on the BlueBubbles server’s macOS version. If edit still appears on macOS 26 (Tahoe), disable it manually with `channels.bluebubbles.actions.edit=false`.
- For status/health info: `openclaw status --all` or `openclaw status --deep`.

For general channel workflow reference, see [Channels](https://docs.openclaw.ai/channels) and the [Plugins](https://docs.openclaw.ai/tools/plugin) guide.

[iMessage](https://docs.openclaw.ai/channels/imessage) [Microsoft Teams](https://docs.openclaw.ai/channels/msteams)

Ctrl+I

---

---

<!-- Page: iMessage -->

## iMessage

**Source:** https://docs.openclaw.ai/channels/imessage

[Skip to main content](https://docs.openclaw.ai/channels/imessage#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

iMessage

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [iMessage (legacy: imsg)](https://docs.openclaw.ai/channels/imessage#imessage-legacy-imsg)
- [Quick setup](https://docs.openclaw.ai/channels/imessage#quick-setup)
- [Requirements and permissions (macOS)](https://docs.openclaw.ai/channels/imessage#requirements-and-permissions-macos)
- [Access control and routing](https://docs.openclaw.ai/channels/imessage#access-control-and-routing)
- [Deployment patterns](https://docs.openclaw.ai/channels/imessage#deployment-patterns)
- [Media, chunking, and delivery targets](https://docs.openclaw.ai/channels/imessage#media-chunking-and-delivery-targets)
- [Config writes](https://docs.openclaw.ai/channels/imessage#config-writes)
- [Troubleshooting](https://docs.openclaw.ai/channels/imessage#troubleshooting)
- [Configuration reference pointers](https://docs.openclaw.ai/channels/imessage#configuration-reference-pointers)

# [​](https://docs.openclaw.ai/channels/imessage\#imessage-legacy-imsg)  iMessage (legacy: imsg)

For new iMessage deployments, use [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles).The `imsg` integration is legacy and may be removed in a future release.

Status: legacy external CLI integration. Gateway spawns `imsg rpc` and communicates over JSON-RPC on stdio (no separate daemon/port).

[**BlueBubbles (recommended)** \\
\\
Preferred iMessage path for new setups.](https://docs.openclaw.ai/channels/bluebubbles) [**Pairing** \\
\\
iMessage DMs default to pairing mode.](https://docs.openclaw.ai/channels/pairing) [**Configuration reference** \\
\\
Full iMessage field reference.](https://docs.openclaw.ai/gateway/configuration-reference#imessage)

## [​](https://docs.openclaw.ai/channels/imessage\#quick-setup)  Quick setup

- Local Mac (fast path)

- Remote Mac over SSH


1

[Navigate to header](https://docs.openclaw.ai/channels/imessage#)

Install and verify imsg

Copy

```
brew install steipete/tap/imsg
imsg rpc --help
```

2

[Navigate to header](https://docs.openclaw.ai/channels/imessage#)

Configure OpenClaw

Copy

```
{
  channels: {
    imessage: {
      enabled: true,
      cliPath: "/usr/local/bin/imsg",
      dbPath: "/Users/<you>/Library/Messages/chat.db",
    },
  },
}
```

3

[Navigate to header](https://docs.openclaw.ai/channels/imessage#)

Start gateway

Copy

```
openclaw gateway
```

4

[Navigate to header](https://docs.openclaw.ai/channels/imessage#)

Approve first DM pairing (default dmPolicy)

Copy

```
openclaw pairing list imessage
openclaw pairing approve imessage <CODE>
```

Pairing requests expire after 1 hour.

OpenClaw only requires a stdio-compatible `cliPath`, so you can point `cliPath` at a wrapper script that SSHes to a remote Mac and runs `imsg`.

Copy

```
#!/usr/bin/env bash
exec ssh -T gateway-host imsg "$@"
```

Recommended config when attachments are enabled:

Copy

```
{
  channels: {
    imessage: {
      enabled: true,
      cliPath: "~/.openclaw/scripts/imsg-ssh",
      remoteHost: "user@gateway-host", // used for SCP attachment fetches
      includeAttachments: true,
      // Optional: override allowed attachment roots.
      // Defaults include /Users/*/Library/Messages/Attachments
      attachmentRoots: ["/Users/*/Library/Messages/Attachments"],
      remoteAttachmentRoots: ["/Users/*/Library/Messages/Attachments"],
    },
  },
}
```

If `remoteHost` is not set, OpenClaw attempts to auto-detect it by parsing the SSH wrapper script.
`remoteHost` must be `host` or `user@host` (no spaces or SSH options).
OpenClaw uses strict host-key checking for SCP, so the relay host key must already exist in `~/.ssh/known_hosts`.
Attachment paths are validated against allowed roots (`attachmentRoots` / `remoteAttachmentRoots`).

## [​](https://docs.openclaw.ai/channels/imessage\#requirements-and-permissions-macos)  Requirements and permissions (macOS)

- Messages must be signed in on the Mac running `imsg`.
- Full Disk Access is required for the process context running OpenClaw/`imsg` (Messages DB access).
- Automation permission is required to send messages through Messages.app.

Permissions are granted per process context. If gateway runs headless (LaunchAgent/SSH), run a one-time interactive command in that same context to trigger prompts:

Copy

```
imsg chats --limit 1
# or
imsg send <handle> "test"
```

## [​](https://docs.openclaw.ai/channels/imessage\#access-control-and-routing)  Access control and routing

- DM policy

- Group policy + mentions

- Sessions and deterministic replies


`channels.imessage.dmPolicy` controls direct messages:

- `pairing` (default)
- `allowlist`
- `open` (requires `allowFrom` to include `"*"`)
- `disabled`

Allowlist field: `channels.imessage.allowFrom`.Allowlist entries can be handles or chat targets (`chat_id:*`, `chat_guid:*`, `chat_identifier:*`).

`channels.imessage.groupPolicy` controls group handling:

- `allowlist` (default when configured)
- `open`
- `disabled`

Group sender allowlist: `channels.imessage.groupAllowFrom`.Runtime fallback: if `groupAllowFrom` is unset, iMessage group sender checks fall back to `allowFrom` when available.
Runtime note: if `channels.imessage` is completely missing, runtime falls back to `groupPolicy="allowlist"` and logs a warning (even if `channels.defaults.groupPolicy` is set).Mention gating for groups:

- iMessage has no native mention metadata
- mention detection uses regex patterns (`agents.list[].groupChat.mentionPatterns`, fallback `messages.groupChat.mentionPatterns`)
- with no configured patterns, mention gating cannot be enforced

Control commands from authorized senders can bypass mention gating in groups.

- DMs use direct routing; groups use group routing.
- With default `session.dmScope=main`, iMessage DMs collapse into the agent main session.
- Group sessions are isolated (`agent:<agentId>:imessage:group:<chat_id>`).
- Replies route back to iMessage using originating channel/target metadata.

Group-ish thread behavior:Some multi-participant iMessage threads can arrive with `is_group=false`.
If that `chat_id` is explicitly configured under `channels.imessage.groups`, OpenClaw treats it as group traffic (group gating + group session isolation).

## [​](https://docs.openclaw.ai/channels/imessage\#deployment-patterns)  Deployment patterns

Dedicated bot macOS user (separate iMessage identity)

Use a dedicated Apple ID and macOS user so bot traffic is isolated from your personal Messages profile.Typical flow:

1. Create/sign in a dedicated macOS user.
2. Sign into Messages with the bot Apple ID in that user.
3. Install `imsg` in that user.
4. Create SSH wrapper so OpenClaw can run `imsg` in that user context.
5. Point `channels.imessage.accounts.<id>.cliPath` and `.dbPath` to that user profile.

First run may require GUI approvals (Automation + Full Disk Access) in that bot user session.

Remote Mac over Tailscale (example)

Common topology:

- gateway runs on Linux/VM
- iMessage + `imsg` runs on a Mac in your tailnet
- `cliPath` wrapper uses SSH to run `imsg`
- `remoteHost` enables SCP attachment fetches

Example:

Copy

```
{
  channels: {
    imessage: {
      enabled: true,
      cliPath: "~/.openclaw/scripts/imsg-ssh",
      remoteHost: "bot@mac-mini.tailnet-1234.ts.net",
      includeAttachments: true,
      dbPath: "/Users/bot/Library/Messages/chat.db",
    },
  },
}
```

Copy

```
#!/usr/bin/env bash
exec ssh -T bot@mac-mini.tailnet-1234.ts.net imsg "$@"
```

Use SSH keys so both SSH and SCP are non-interactive.
Ensure the host key is trusted first (for example `ssh bot@mac-mini.tailnet-1234.ts.net`) so `known_hosts` is populated.

Multi-account pattern

iMessage supports per-account config under `channels.imessage.accounts`.Each account can override fields such as `cliPath`, `dbPath`, `allowFrom`, `groupPolicy`, `mediaMaxMb`, history settings, and attachment root allowlists.

## [​](https://docs.openclaw.ai/channels/imessage\#media-chunking-and-delivery-targets)  Media, chunking, and delivery targets

Attachments and media

- inbound attachment ingestion is optional: `channels.imessage.includeAttachments`
- remote attachment paths can be fetched via SCP when `remoteHost` is set
- attachment paths must match allowed roots:
  - `channels.imessage.attachmentRoots` (local)
  - `channels.imessage.remoteAttachmentRoots` (remote SCP mode)
  - default root pattern: `/Users/*/Library/Messages/Attachments`
- SCP uses strict host-key checking (`StrictHostKeyChecking=yes`)
- outbound media size uses `channels.imessage.mediaMaxMb` (default 16 MB)

Outbound chunking

- text chunk limit: `channels.imessage.textChunkLimit` (default 4000)
- chunk mode: `channels.imessage.chunkMode`
  - `length` (default)
  - `newline` (paragraph-first splitting)

Addressing formats

Preferred explicit targets:

- `chat_id:123` (recommended for stable routing)
- `chat_guid:...`
- `chat_identifier:...`

Handle targets are also supported:

- `imessage:+1555...`
- `sms:+1555...`
- `user@example.com`

Copy

```
imsg chats --limit 20
```

## [​](https://docs.openclaw.ai/channels/imessage\#config-writes)  Config writes

iMessage allows channel-initiated config writes by default (for `/config set|unset` when `commands.config: true`).Disable:

Copy

```
{
  channels: {
    imessage: {
      configWrites: false,
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/imessage\#troubleshooting)  Troubleshooting

imsg not found or RPC unsupported

Validate the binary and RPC support:

Copy

```
imsg rpc --help
openclaw channels status --probe
```

If probe reports RPC unsupported, update `imsg`.

DMs are ignored

Check:

- `channels.imessage.dmPolicy`
- `channels.imessage.allowFrom`
- pairing approvals (`openclaw pairing list imessage`)

Group messages are ignored

Check:

- `channels.imessage.groupPolicy`
- `channels.imessage.groupAllowFrom`
- `channels.imessage.groups` allowlist behavior
- mention pattern configuration (`agents.list[].groupChat.mentionPatterns`)

Remote attachments fail

Check:

- `channels.imessage.remoteHost`
- `channels.imessage.remoteAttachmentRoots`
- SSH/SCP key auth from the gateway host
- host key exists in `~/.ssh/known_hosts` on the gateway host
- remote path readability on the Mac running Messages

macOS permission prompts were missed

Re-run in an interactive GUI terminal in the same user/session context and approve prompts:

Copy

```
imsg chats --limit 1
imsg send <handle> "test"
```

Confirm Full Disk Access + Automation are granted for the process context that runs OpenClaw/`imsg`.

## [​](https://docs.openclaw.ai/channels/imessage\#configuration-reference-pointers)  Configuration reference pointers

- [Configuration reference - iMessage](https://docs.openclaw.ai/gateway/configuration-reference#imessage)
- [Gateway configuration](https://docs.openclaw.ai/gateway/configuration)
- [Pairing](https://docs.openclaw.ai/channels/pairing)
- [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles)

[Signal](https://docs.openclaw.ai/channels/signal) [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles)

Ctrl+I

---

---

<!-- Page: Signal -->

## Signal

**Source:** https://docs.openclaw.ai/channels/signal

[Skip to main content](https://docs.openclaw.ai/channels/signal#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Signal

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Signal (signal-cli)](https://docs.openclaw.ai/channels/signal#signal-signal-cli)
- [Prerequisites](https://docs.openclaw.ai/channels/signal#prerequisites)
- [Quick setup (beginner)](https://docs.openclaw.ai/channels/signal#quick-setup-beginner)
- [What it is](https://docs.openclaw.ai/channels/signal#what-it-is)
- [Config writes](https://docs.openclaw.ai/channels/signal#config-writes)
- [The number model (important)](https://docs.openclaw.ai/channels/signal#the-number-model-important)
- [Setup path A: link existing Signal account (QR)](https://docs.openclaw.ai/channels/signal#setup-path-a-link-existing-signal-account-qr)
- [Setup path B: register dedicated bot number (SMS, Linux)](https://docs.openclaw.ai/channels/signal#setup-path-b-register-dedicated-bot-number-sms-linux)
- [External daemon mode (httpUrl)](https://docs.openclaw.ai/channels/signal#external-daemon-mode-httpurl)
- [Access control (DMs + groups)](https://docs.openclaw.ai/channels/signal#access-control-dms-%2B-groups)
- [How it works (behavior)](https://docs.openclaw.ai/channels/signal#how-it-works-behavior)
- [Media + limits](https://docs.openclaw.ai/channels/signal#media-%2B-limits)
- [Typing + read receipts](https://docs.openclaw.ai/channels/signal#typing-%2B-read-receipts)
- [Reactions (message tool)](https://docs.openclaw.ai/channels/signal#reactions-message-tool)
- [Delivery targets (CLI/cron)](https://docs.openclaw.ai/channels/signal#delivery-targets-cli%2Fcron)
- [Troubleshooting](https://docs.openclaw.ai/channels/signal#troubleshooting)
- [Security notes](https://docs.openclaw.ai/channels/signal#security-notes)
- [Configuration reference (Signal)](https://docs.openclaw.ai/channels/signal#configuration-reference-signal)

# [​](https://docs.openclaw.ai/channels/signal\#signal-signal-cli)  Signal (signal-cli)

Status: external CLI integration. Gateway talks to `signal-cli` over HTTP JSON-RPC + SSE.

## [​](https://docs.openclaw.ai/channels/signal\#prerequisites)  Prerequisites

- OpenClaw installed on your server (Linux flow below tested on Ubuntu 24).
- `signal-cli` available on the host where the gateway runs.
- A phone number that can receive one verification SMS (for SMS registration path).
- Browser access for Signal captcha (`signalcaptchas.org`) during registration.

## [​](https://docs.openclaw.ai/channels/signal\#quick-setup-beginner)  Quick setup (beginner)

1. Use a **separate Signal number** for the bot (recommended).
2. Install `signal-cli` (Java required if you use the JVM build).
3. Choose one setup path:
   - **Path A (QR link):**`signal-cli link -n "OpenClaw"` and scan with Signal.
   - **Path B (SMS register):** register a dedicated number with captcha + SMS verification.
4. Configure OpenClaw and restart the gateway.
5. Send a first DM and approve pairing (`openclaw pairing approve signal <CODE>`).

Minimal config:

Copy

```
{
  channels: {
    signal: {
      enabled: true,
      account: "+15551234567",
      cliPath: "signal-cli",
      dmPolicy: "pairing",
      allowFrom: ["+15557654321"],
    },
  },
}
```

Field reference:

| Field | Description |
| --- | --- |
| `account` | Bot phone number in E.164 format (`+15551234567`) |
| `cliPath` | Path to `signal-cli` (`signal-cli` if on `PATH`) |
| `dmPolicy` | DM access policy (`pairing` recommended) |
| `allowFrom` | Phone numbers or `uuid:<id>` values allowed to DM |

## [​](https://docs.openclaw.ai/channels/signal\#what-it-is)  What it is

- Signal channel via `signal-cli` (not embedded libsignal).
- Deterministic routing: replies always go back to Signal.
- DMs share the agent’s main session; groups are isolated (`agent:<agentId>:signal:group:<groupId>`).

## [​](https://docs.openclaw.ai/channels/signal\#config-writes)  Config writes

By default, Signal is allowed to write config updates triggered by `/config set|unset` (requires `commands.config: true`).Disable with:

Copy

```
{
  channels: { signal: { configWrites: false } },
}
```

## [​](https://docs.openclaw.ai/channels/signal\#the-number-model-important)  The number model (important)

- The gateway connects to a **Signal device** (the `signal-cli` account).
- If you run the bot on **your personal Signal account**, it will ignore your own messages (loop protection).
- For “I text the bot and it replies,” use a **separate bot number**.

## [​](https://docs.openclaw.ai/channels/signal\#setup-path-a-link-existing-signal-account-qr)  Setup path A: link existing Signal account (QR)

1. Install `signal-cli` (JVM or native build).
2. Link a bot account:
   - `signal-cli link -n "OpenClaw"` then scan the QR in Signal.
3. Configure Signal and start the gateway.

Example:

Copy

```
{
  channels: {
    signal: {
      enabled: true,
      account: "+15551234567",
      cliPath: "signal-cli",
      dmPolicy: "pairing",
      allowFrom: ["+15557654321"],
    },
  },
}
```

Multi-account support: use `channels.signal.accounts` with per-account config and optional `name`. See [`gateway/configuration`](https://docs.openclaw.ai/gateway/configuration#telegramaccounts--discordaccounts--slackaccounts--signalaccounts--imessageaccounts) for the shared pattern.

## [​](https://docs.openclaw.ai/channels/signal\#setup-path-b-register-dedicated-bot-number-sms-linux)  Setup path B: register dedicated bot number (SMS, Linux)

Use this when you want a dedicated bot number instead of linking an existing Signal app account.

1. Get a number that can receive SMS (or voice verification for landlines).
   - Use a dedicated bot number to avoid account/session conflicts.
2. Install `signal-cli` on the gateway host:

Copy

```
VERSION=$(curl -Ls -o /dev/null -w %{url_effective} https://github.com/AsamK/signal-cli/releases/latest | sed -e 's/^.*\/v//')
curl -L -O "https://github.com/AsamK/signal-cli/releases/download/v${VERSION}/signal-cli-${VERSION}-Linux-native.tar.gz"
sudo tar xf "signal-cli-${VERSION}-Linux-native.tar.gz" -C /opt
sudo ln -sf /opt/signal-cli /usr/local/bin/
signal-cli --version
```

If you use the JVM build (`signal-cli-${VERSION}.tar.gz`), install JRE 25+ first.
Keep `signal-cli` updated; upstream notes that old releases can break as Signal server APIs change.

3. Register and verify the number:

Copy

```
signal-cli -a +<BOT_PHONE_NUMBER> register
```

If captcha is required:

1. Open `https://signalcaptchas.org/registration/generate.html`.
2. Complete captcha, copy the `signalcaptcha://...` link target from “Open Signal”.
3. Run from the same external IP as the browser session when possible.
4. Run registration again immediately (captcha tokens expire quickly):

Copy

```
signal-cli -a +<BOT_PHONE_NUMBER> register --captcha '<SIGNALCAPTCHA_URL>'
signal-cli -a +<BOT_PHONE_NUMBER> verify <VERIFICATION_CODE>
```

4. Configure OpenClaw, restart gateway, verify channel:

Copy

```
# If you run the gateway as a user systemd service:
systemctl --user restart openclaw-gateway

# Then verify:
openclaw doctor
openclaw channels status --probe
```

5. Pair your DM sender:
   - Send any message to the bot number.
   - Approve code on the server: `openclaw pairing approve signal <PAIRING_CODE>`.
   - Save the bot number as a contact on your phone to avoid “Unknown contact”.

Important: registering a phone number account with `signal-cli` can de-authenticate the main Signal app session for that number. Prefer a dedicated bot number, or use QR link mode if you need to keep your existing phone app setup.Upstream references:

- `signal-cli` README: `https://github.com/AsamK/signal-cli`
- Captcha flow: `https://github.com/AsamK/signal-cli/wiki/Registration-with-captcha`
- Linking flow: `https://github.com/AsamK/signal-cli/wiki/Linking-other-devices-(Provisioning)`

## [​](https://docs.openclaw.ai/channels/signal\#external-daemon-mode-httpurl)  External daemon mode (httpUrl)

If you want to manage `signal-cli` yourself (slow JVM cold starts, container init, or shared CPUs), run the daemon separately and point OpenClaw at it:

Copy

```
{
  channels: {
    signal: {
      httpUrl: "http://127.0.0.1:8080",
      autoStart: false,
    },
  },
}
```

This skips auto-spawn and the startup wait inside OpenClaw. For slow starts when auto-spawning, set `channels.signal.startupTimeoutMs`.

## [​](https://docs.openclaw.ai/channels/signal\#access-control-dms-+-groups)  Access control (DMs + groups)

DMs:

- Default: `channels.signal.dmPolicy = "pairing"`.
- Unknown senders receive a pairing code; messages are ignored until approved (codes expire after 1 hour).
- Approve via:
  - `openclaw pairing list signal`
  - `openclaw pairing approve signal <CODE>`
- Pairing is the default token exchange for Signal DMs. Details: [Pairing](https://docs.openclaw.ai/channels/pairing)
- UUID-only senders (from `sourceUuid`) are stored as `uuid:<id>` in `channels.signal.allowFrom`.

Groups:

- `channels.signal.groupPolicy = open | allowlist | disabled`.
- `channels.signal.groupAllowFrom` controls who can trigger in groups when `allowlist` is set.
- Runtime note: if `channels.signal` is completely missing, runtime falls back to `groupPolicy="allowlist"` for group checks (even if `channels.defaults.groupPolicy` is set).

## [​](https://docs.openclaw.ai/channels/signal\#how-it-works-behavior)  How it works (behavior)

- `signal-cli` runs as a daemon; the gateway reads events via SSE.
- Inbound messages are normalized into the shared channel envelope.
- Replies always route back to the same number or group.

## [​](https://docs.openclaw.ai/channels/signal\#media-+-limits)  Media + limits

- Outbound text is chunked to `channels.signal.textChunkLimit` (default 4000).
- Optional newline chunking: set `channels.signal.chunkMode="newline"` to split on blank lines (paragraph boundaries) before length chunking.
- Attachments supported (base64 fetched from `signal-cli`).
- Default media cap: `channels.signal.mediaMaxMb` (default 8).
- Use `channels.signal.ignoreAttachments` to skip downloading media.
- Group history context uses `channels.signal.historyLimit` (or `channels.signal.accounts.*.historyLimit`), falling back to `messages.groupChat.historyLimit`. Set `0` to disable (default 50).

## [​](https://docs.openclaw.ai/channels/signal\#typing-+-read-receipts)  Typing + read receipts

- **Typing indicators**: OpenClaw sends typing signals via `signal-cli sendTyping` and refreshes them while a reply is running.
- **Read receipts**: when `channels.signal.sendReadReceipts` is true, OpenClaw forwards read receipts for allowed DMs.
- Signal-cli does not expose read receipts for groups.

## [​](https://docs.openclaw.ai/channels/signal\#reactions-message-tool)  Reactions (message tool)

- Use `message action=react` with `channel=signal`.
- Targets: sender E.164 or UUID (use `uuid:<id>` from pairing output; bare UUID works too).
- `messageId` is the Signal timestamp for the message you’re reacting to.
- Group reactions require `targetAuthor` or `targetAuthorUuid`.

Examples:

Copy

```
message action=react channel=signal target=uuid:123e4567-e89b-12d3-a456-426614174000 messageId=1737630212345 emoji=🔥
message action=react channel=signal target=+15551234567 messageId=1737630212345 emoji=🔥 remove=true
message action=react channel=signal target=signal:group:<groupId> targetAuthor=uuid:<sender-uuid> messageId=1737630212345 emoji=✅
```

Config:

- `channels.signal.actions.reactions`: enable/disable reaction actions (default true).
- `channels.signal.reactionLevel`: `off | ack | minimal | extensive`.

  - `off`/`ack` disables agent reactions (message tool `react` will error).
  - `minimal`/`extensive` enables agent reactions and sets the guidance level.
- Per-account overrides: `channels.signal.accounts.<id>.actions.reactions`, `channels.signal.accounts.<id>.reactionLevel`.

## [​](https://docs.openclaw.ai/channels/signal\#delivery-targets-cli/cron)  Delivery targets (CLI/cron)

- DMs: `signal:+15551234567` (or plain E.164).
- UUID DMs: `uuid:<id>` (or bare UUID).
- Groups: `signal:group:<groupId>`.
- Usernames: `username:<name>` (if supported by your Signal account).

## [​](https://docs.openclaw.ai/channels/signal\#troubleshooting)  Troubleshooting

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
openclaw pairing list signal
```

Common failures:

- Daemon reachable but no replies: verify account/daemon settings (`httpUrl`, `account`) and receive mode.
- DMs ignored: sender is pending pairing approval.
- Group messages ignored: group sender/mention gating blocks delivery.
- Config validation errors after edits: run `openclaw doctor --fix`.
- Signal missing from diagnostics: confirm `channels.signal.enabled: true`.

Extra checks:

Copy

```
openclaw pairing list signal
pgrep -af signal-cli
grep -i "signal" "/tmp/openclaw/openclaw-$(date +%Y-%m-%d).log" | tail -20
```

For triage flow: [/channels/troubleshooting](https://docs.openclaw.ai/channels/troubleshooting).

## [​](https://docs.openclaw.ai/channels/signal\#security-notes)  Security notes

- `signal-cli` stores account keys locally (typically `~/.local/share/signal-cli/data/`).
- Back up Signal account state before server migration or rebuild.
- Keep `channels.signal.dmPolicy: "pairing"` unless you explicitly want broader DM access.
- SMS verification is only needed for registration or recovery flows, but losing control of the number/account can complicate re-registration.

## [​](https://docs.openclaw.ai/channels/signal\#configuration-reference-signal)  Configuration reference (Signal)

Full configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)Provider options:

- `channels.signal.enabled`: enable/disable channel startup.
- `channels.signal.account`: E.164 for the bot account.
- `channels.signal.cliPath`: path to `signal-cli`.
- `channels.signal.httpUrl`: full daemon URL (overrides host/port).
- `channels.signal.httpHost`, `channels.signal.httpPort`: daemon bind (default 127.0.0.1:8080).
- `channels.signal.autoStart`: auto-spawn daemon (default true if `httpUrl` unset).
- `channels.signal.startupTimeoutMs`: startup wait timeout in ms (cap 120000).
- `channels.signal.receiveMode`: `on-start | manual`.
- `channels.signal.ignoreAttachments`: skip attachment downloads.
- `channels.signal.ignoreStories`: ignore stories from the daemon.
- `channels.signal.sendReadReceipts`: forward read receipts.
- `channels.signal.dmPolicy`: `pairing | allowlist | open | disabled` (default: pairing).
- `channels.signal.allowFrom`: DM allowlist (E.164 or `uuid:<id>`). `open` requires `"*"`. Signal has no usernames; use phone/UUID ids.
- `channels.signal.groupPolicy`: `open | allowlist | disabled` (default: allowlist).
- `channels.signal.groupAllowFrom`: group sender allowlist.
- `channels.signal.historyLimit`: max group messages to include as context (0 disables).
- `channels.signal.dmHistoryLimit`: DM history limit in user turns. Per-user overrides: `channels.signal.dms["<phone_or_uuid>"].historyLimit`.
- `channels.signal.textChunkLimit`: outbound chunk size (chars).
- `channels.signal.chunkMode`: `length` (default) or `newline` to split on blank lines (paragraph boundaries) before length chunking.
- `channels.signal.mediaMaxMb`: inbound/outbound media cap (MB).

Related global options:

- `agents.list[].groupChat.mentionPatterns` (Signal does not support native mentions).
- `messages.groupChat.mentionPatterns` (global fallback).
- `messages.responsePrefix`.

[Mattermost](https://docs.openclaw.ai/channels/mattermost) [iMessage](https://docs.openclaw.ai/channels/imessage)

Ctrl+I

---

---

<!-- Page: LINE -->

## LINE

**Source:** https://docs.openclaw.ai/channels/line

[Skip to main content](https://docs.openclaw.ai/channels/line#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

LINE

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [LINE (plugin)](https://docs.openclaw.ai/channels/line#line-plugin)
- [Plugin required](https://docs.openclaw.ai/channels/line#plugin-required)
- [Setup](https://docs.openclaw.ai/channels/line#setup)
- [Configure](https://docs.openclaw.ai/channels/line#configure)
- [Access control](https://docs.openclaw.ai/channels/line#access-control)
- [Message behavior](https://docs.openclaw.ai/channels/line#message-behavior)
- [Channel data (rich messages)](https://docs.openclaw.ai/channels/line#channel-data-rich-messages)
- [Troubleshooting](https://docs.openclaw.ai/channels/line#troubleshooting)

# [​](https://docs.openclaw.ai/channels/line\#line-plugin)  LINE (plugin)

LINE connects to OpenClaw via the LINE Messaging API. The plugin runs as a webhook
receiver on the gateway and uses your channel access token + channel secret for
authentication.Status: supported via plugin. Direct messages, group chats, media, locations, Flex
messages, template messages, and quick replies are supported. Reactions and threads
are not supported.

## [​](https://docs.openclaw.ai/channels/line\#plugin-required)  Plugin required

Install the LINE plugin:

Copy

```
openclaw plugins install @openclaw/line
```

Local checkout (when running from a git repo):

Copy

```
openclaw plugins install ./extensions/line
```

## [​](https://docs.openclaw.ai/channels/line\#setup)  Setup

1. Create a LINE Developers account and open the Console:
[https://developers.line.biz/console/](https://developers.line.biz/console/)
2. Create (or pick) a Provider and add a **Messaging API** channel.
3. Copy the **Channel access token** and **Channel secret** from the channel settings.
4. Enable **Use webhook** in the Messaging API settings.
5. Set the webhook URL to your gateway endpoint (HTTPS required):

Copy

```
https://gateway-host/line/webhook
```

The gateway responds to LINE’s webhook verification (GET) and inbound events (POST).
If you need a custom path, set `channels.line.webhookPath` or
`channels.line.accounts.<id>.webhookPath` and update the URL accordingly.

## [​](https://docs.openclaw.ai/channels/line\#configure)  Configure

Minimal config:

Copy

```
{
  channels: {
    line: {
      enabled: true,
      channelAccessToken: "LINE_CHANNEL_ACCESS_TOKEN",
      channelSecret: "LINE_CHANNEL_SECRET",
      dmPolicy: "pairing",
    },
  },
}
```

Env vars (default account only):

- `LINE_CHANNEL_ACCESS_TOKEN`
- `LINE_CHANNEL_SECRET`

Token/secret files:

Copy

```
{
  channels: {
    line: {
      tokenFile: "/path/to/line-token.txt",
      secretFile: "/path/to/line-secret.txt",
    },
  },
}
```

Multiple accounts:

Copy

```
{
  channels: {
    line: {
      accounts: {
        marketing: {
          channelAccessToken: "...",
          channelSecret: "...",
          webhookPath: "/line/marketing",
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/line\#access-control)  Access control

Direct messages default to pairing. Unknown senders get a pairing code and their
messages are ignored until approved.

Copy

```
openclaw pairing list line
openclaw pairing approve line <CODE>
```

Allowlists and policies:

- `channels.line.dmPolicy`: `pairing | allowlist | open | disabled`
- `channels.line.allowFrom`: allowlisted LINE user IDs for DMs
- `channels.line.groupPolicy`: `allowlist | open | disabled`
- `channels.line.groupAllowFrom`: allowlisted LINE user IDs for groups
- Per-group overrides: `channels.line.groups.<groupId>.allowFrom`
- Runtime note: if `channels.line` is completely missing, runtime falls back to `groupPolicy="allowlist"` for group checks (even if `channels.defaults.groupPolicy` is set).

LINE IDs are case-sensitive. Valid IDs look like:

- User: `U` \+ 32 hex chars
- Group: `C` \+ 32 hex chars
- Room: `R` \+ 32 hex chars

## [​](https://docs.openclaw.ai/channels/line\#message-behavior)  Message behavior

- Text is chunked at 5000 characters.
- Markdown formatting is stripped; code blocks and tables are converted into Flex
cards when possible.
- Streaming responses are buffered; LINE receives full chunks with a loading
animation while the agent works.
- Media downloads are capped by `channels.line.mediaMaxMb` (default 10).

## [​](https://docs.openclaw.ai/channels/line\#channel-data-rich-messages)  Channel data (rich messages)

Use `channelData.line` to send quick replies, locations, Flex cards, or template
messages.

Copy

```
{
  text: "Here you go",
  channelData: {
    line: {
      quickReplies: ["Status", "Help"],
      location: {
        title: "Office",
        address: "123 Main St",
        latitude: 35.681236,
        longitude: 139.767125,
      },
      flexMessage: {
        altText: "Status card",
        contents: {
          /* Flex payload */
        },
      },
      templateMessage: {
        type: "confirm",
        text: "Proceed?",
        confirmLabel: "Yes",
        confirmData: "yes",
        cancelLabel: "No",
        cancelData: "no",
      },
    },
  },
}
```

The LINE plugin also ships a `/card` command for Flex message presets:

Copy

```
/card info "Welcome" "Thanks for joining!"
```

## [​](https://docs.openclaw.ai/channels/line\#troubleshooting)  Troubleshooting

- **Webhook verification fails:** ensure the webhook URL is HTTPS and the
`channelSecret` matches the LINE console.
- **No inbound events:** confirm the webhook path matches `channels.line.webhookPath`
and that the gateway is reachable from LINE.
- **Media download errors:** raise `channels.line.mediaMaxMb` if media exceeds the
default limit.

[Synology Chat](https://docs.openclaw.ai/channels/synology-chat) [Matrix](https://docs.openclaw.ai/channels/matrix)

Ctrl+I

---

---

<!-- Page: grammY (Telegram) -->

## grammY

**Source:** https://docs.openclaw.ai/channels/grammy

[Skip to main content](https://docs.openclaw.ai/channels/grammy#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Technical reference

grammY

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [grammY Integration (Telegram Bot API)](https://docs.openclaw.ai/channels/grammy#grammy-integration-telegram-bot-api)
- [Why grammY](https://docs.openclaw.ai/channels/grammy#why-grammy)
- [What we shipped](https://docs.openclaw.ai/channels/grammy#what-we-shipped)

# [​](https://docs.openclaw.ai/channels/grammy\#grammy-integration-telegram-bot-api)  grammY Integration (Telegram Bot API)

# [​](https://docs.openclaw.ai/channels/grammy\#why-grammy)  Why grammY

- TS-first Bot API client with built-in long-poll + webhook helpers, middleware, error handling, rate limiter.
- Cleaner media helpers than hand-rolling fetch + FormData; supports all Bot API methods.
- Extensible: proxy support via custom fetch, session middleware (optional), type-safe context.

# [​](https://docs.openclaw.ai/channels/grammy\#what-we-shipped)  What we shipped

- **Single client path:** fetch-based implementation removed; grammY is now the sole Telegram client (send + gateway) with the grammY throttler enabled by default.
- **Gateway:**`monitorTelegramProvider` builds a grammY `Bot`, wires mention/allowlist gating, media download via `getFile`/`download`, and delivers replies with `sendMessage/sendPhoto/sendVideo/sendAudio/sendDocument`. Supports long-poll or webhook via `webhookCallback`.
- **Proxy:** optional `channels.telegram.proxy` uses `undici.ProxyAgent` through grammY’s `client.baseFetch`.
- **Webhook support:**`webhook-set.ts` wraps `setWebhook/deleteWebhook`; `webhook.ts` hosts the callback with health + graceful shutdown. Gateway enables webhook mode when `channels.telegram.webhookUrl` \+ `channels.telegram.webhookSecret` are set (otherwise it long-polls).
- **Sessions:** direct chats collapse into the agent main session (`agent:<agentId>:<mainKey>`); groups use `agent:<agentId>:telegram:group:<chatId>`; replies route back to the same channel.
- **Config knobs:**`channels.telegram.botToken`, `channels.telegram.dmPolicy`, `channels.telegram.groups` (allowlist + mention defaults), `channels.telegram.allowFrom`, `channels.telegram.groupAllowFrom`, `channels.telegram.groupPolicy`, `channels.telegram.mediaMaxMb`, `channels.telegram.linkPreview`, `channels.telegram.proxy`, `channels.telegram.webhookSecret`, `channels.telegram.webhookUrl`, `channels.telegram.webhookHost`.
- **Live stream preview:**`channels.telegram.streaming` (`off | partial | block | progress`) sends a temporary message and updates it with `editMessageText`. This is separate from channel block streaming.
- **Tests:** grammy mocks cover DM + group mention gating and outbound send; more media/webhook fixtures still welcome.

Open questions

- Optional grammY plugins (throttler) if we hit Bot API 429s.
- Add more structured media tests (stickers, voice notes).
- Make webhook listen port configurable (currently fixed to 8787 unless wired through the gateway).

[Prompt Caching](https://docs.openclaw.ai/reference/prompt-caching) [TypeBox](https://docs.openclaw.ai/concepts/typebox)

Ctrl+I

---

---

<!-- Page: Nostr -->

## Nostr

**Source:** https://docs.openclaw.ai/channels/nostr

[Skip to main content](https://docs.openclaw.ai/channels/nostr#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Messaging platforms

Nostr

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Nostr](https://docs.openclaw.ai/channels/nostr#nostr)
- [Install (on demand)](https://docs.openclaw.ai/channels/nostr#install-on-demand)
- [Onboarding (recommended)](https://docs.openclaw.ai/channels/nostr#onboarding-recommended)
- [Manual install](https://docs.openclaw.ai/channels/nostr#manual-install)
- [Quick setup](https://docs.openclaw.ai/channels/nostr#quick-setup)
- [Configuration reference](https://docs.openclaw.ai/channels/nostr#configuration-reference)
- [Profile metadata](https://docs.openclaw.ai/channels/nostr#profile-metadata)
- [Access control](https://docs.openclaw.ai/channels/nostr#access-control)
- [DM policies](https://docs.openclaw.ai/channels/nostr#dm-policies)
- [Allowlist example](https://docs.openclaw.ai/channels/nostr#allowlist-example)
- [Key formats](https://docs.openclaw.ai/channels/nostr#key-formats)
- [Relays](https://docs.openclaw.ai/channels/nostr#relays)
- [Protocol support](https://docs.openclaw.ai/channels/nostr#protocol-support)
- [Testing](https://docs.openclaw.ai/channels/nostr#testing)
- [Local relay](https://docs.openclaw.ai/channels/nostr#local-relay)
- [Manual test](https://docs.openclaw.ai/channels/nostr#manual-test)
- [Troubleshooting](https://docs.openclaw.ai/channels/nostr#troubleshooting)
- [Not receiving messages](https://docs.openclaw.ai/channels/nostr#not-receiving-messages)
- [Not sending responses](https://docs.openclaw.ai/channels/nostr#not-sending-responses)
- [Duplicate responses](https://docs.openclaw.ai/channels/nostr#duplicate-responses)
- [Security](https://docs.openclaw.ai/channels/nostr#security)
- [Limitations (MVP)](https://docs.openclaw.ai/channels/nostr#limitations-mvp)

# [​](https://docs.openclaw.ai/channels/nostr\#nostr)  Nostr

**Status:** Optional plugin (disabled by default).Nostr is a decentralized protocol for social networking. This channel enables OpenClaw to receive and respond to encrypted direct messages (DMs) via NIP-04.

## [​](https://docs.openclaw.ai/channels/nostr\#install-on-demand)  Install (on demand)

### [​](https://docs.openclaw.ai/channels/nostr\#onboarding-recommended)  Onboarding (recommended)

- The onboarding wizard (`openclaw onboard`) and `openclaw channels add` list optional channel plugins.
- Selecting Nostr prompts you to install the plugin on demand.

Install defaults:

- **Dev channel + git checkout available:** uses the local plugin path.
- **Stable/Beta:** downloads from npm.

You can always override the choice in the prompt.

### [​](https://docs.openclaw.ai/channels/nostr\#manual-install)  Manual install

Copy

```
openclaw plugins install @openclaw/nostr
```

Use a local checkout (dev workflows):

Copy

```
openclaw plugins install --link <path-to-openclaw>/extensions/nostr
```

Restart the Gateway after installing or enabling plugins.

## [​](https://docs.openclaw.ai/channels/nostr\#quick-setup)  Quick setup

1. Generate a Nostr keypair (if needed):

Copy

```
# Using nak
nak key generate
```

2. Add to config:

Copy

```
{
  "channels": {
    "nostr": {
      "privateKey": "${NOSTR_PRIVATE_KEY}"
    }
  }
}
```

3. Export the key:

Copy

```
export NOSTR_PRIVATE_KEY="nsec1..."
```

4. Restart the Gateway.

## [​](https://docs.openclaw.ai/channels/nostr\#configuration-reference)  Configuration reference

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `privateKey` | string | required | Private key in `nsec` or hex format |
| `relays` | string\[\] | `['wss://relay.damus.io', 'wss://nos.lol']` | Relay URLs (WebSocket) |
| `dmPolicy` | string | `pairing` | DM access policy |
| `allowFrom` | string\[\] | `[]` | Allowed sender pubkeys |
| `enabled` | boolean | `true` | Enable/disable channel |
| `name` | string | - | Display name |
| `profile` | object | - | NIP-01 profile metadata |

## [​](https://docs.openclaw.ai/channels/nostr\#profile-metadata)  Profile metadata

Profile data is published as a NIP-01 `kind:0` event. You can manage it from the Control UI (Channels -> Nostr -> Profile) or set it directly in config.Example:

Copy

```
{
  "channels": {
    "nostr": {
      "privateKey": "${NOSTR_PRIVATE_KEY}",
      "profile": {
        "name": "openclaw",
        "displayName": "OpenClaw",
        "about": "Personal assistant DM bot",
        "picture": "https://example.com/avatar.png",
        "banner": "https://example.com/banner.png",
        "website": "https://example.com",
        "nip05": "openclaw@example.com",
        "lud16": "openclaw@example.com"
      }
    }
  }
}
```

Notes:

- Profile URLs must use `https://`.
- Importing from relays merges fields and preserves local overrides.

## [​](https://docs.openclaw.ai/channels/nostr\#access-control)  Access control

### [​](https://docs.openclaw.ai/channels/nostr\#dm-policies)  DM policies

- **pairing** (default): unknown senders get a pairing code.
- **allowlist**: only pubkeys in `allowFrom` can DM.
- **open**: public inbound DMs (requires `allowFrom: ["*"]`).
- **disabled**: ignore inbound DMs.

### [​](https://docs.openclaw.ai/channels/nostr\#allowlist-example)  Allowlist example

Copy

```
{
  "channels": {
    "nostr": {
      "privateKey": "${NOSTR_PRIVATE_KEY}",
      "dmPolicy": "allowlist",
      "allowFrom": ["npub1abc...", "npub1xyz..."]
    }
  }
}
```

## [​](https://docs.openclaw.ai/channels/nostr\#key-formats)  Key formats

Accepted formats:

- **Private key:**`nsec...` or 64-char hex
- **Pubkeys (`allowFrom`):**`npub...` or hex

## [​](https://docs.openclaw.ai/channels/nostr\#relays)  Relays

Defaults: `relay.damus.io` and `nos.lol`.

Copy

```
{
  "channels": {
    "nostr": {
      "privateKey": "${NOSTR_PRIVATE_KEY}",
      "relays": ["wss://relay.damus.io", "wss://relay.primal.net", "wss://nostr.wine"]
    }
  }
}
```

Tips:

- Use 2-3 relays for redundancy.
- Avoid too many relays (latency, duplication).
- Paid relays can improve reliability.
- Local relays are fine for testing (`ws://localhost:7777`).

## [​](https://docs.openclaw.ai/channels/nostr\#protocol-support)  Protocol support

| NIP | Status | Description |
| --- | --- | --- |
| NIP-01 | Supported | Basic event format + profile metadata |
| NIP-04 | Supported | Encrypted DMs (`kind:4`) |
| NIP-17 | Planned | Gift-wrapped DMs |
| NIP-44 | Planned | Versioned encryption |

## [​](https://docs.openclaw.ai/channels/nostr\#testing)  Testing

### [​](https://docs.openclaw.ai/channels/nostr\#local-relay)  Local relay

Copy

```
# Start strfry
docker run -p 7777:7777 ghcr.io/hoytech/strfry
```

Copy

```
{
  "channels": {
    "nostr": {
      "privateKey": "${NOSTR_PRIVATE_KEY}",
      "relays": ["ws://localhost:7777"]
    }
  }
}
```

### [​](https://docs.openclaw.ai/channels/nostr\#manual-test)  Manual test

1. Note the bot pubkey (npub) from logs.
2. Open a Nostr client (Damus, Amethyst, etc.).
3. DM the bot pubkey.
4. Verify the response.

## [​](https://docs.openclaw.ai/channels/nostr\#troubleshooting)  Troubleshooting

### [​](https://docs.openclaw.ai/channels/nostr\#not-receiving-messages)  Not receiving messages

- Verify the private key is valid.
- Ensure relay URLs are reachable and use `wss://` (or `ws://` for local).
- Confirm `enabled` is not `false`.
- Check Gateway logs for relay connection errors.

### [​](https://docs.openclaw.ai/channels/nostr\#not-sending-responses)  Not sending responses

- Check relay accepts writes.
- Verify outbound connectivity.
- Watch for relay rate limits.

### [​](https://docs.openclaw.ai/channels/nostr\#duplicate-responses)  Duplicate responses

- Expected when using multiple relays.
- Messages are deduplicated by event ID; only the first delivery triggers a response.

## [​](https://docs.openclaw.ai/channels/nostr\#security)  Security

- Never commit private keys.
- Use environment variables for keys.
- Consider `allowlist` for production bots.

## [​](https://docs.openclaw.ai/channels/nostr\#limitations-mvp)  Limitations (MVP)

- Direct messages only (no group chats).
- No media attachments.
- NIP-04 only (NIP-17 gift-wrap planned).

[Nextcloud Talk](https://docs.openclaw.ai/channels/nextcloud-talk) [Tlon](https://docs.openclaw.ai/channels/tlon)

Ctrl+I

---
