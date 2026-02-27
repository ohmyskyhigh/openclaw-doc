# Platforms


---

<!-- Page: macOS Skills -->

## skills.md

**Source:** https://docs.openclaw.ai/platforms/mac/skills.md

```
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Skills

# Skills (macOS)

The macOS app surfaces OpenClaw skills via the gateway; it does not parse skills locally.

## Data source

* `skills.status` (gateway) returns all skills plus eligibility and missing requirements
  (including allowlist blocks for bundled skills).
* Requirements are derived from `metadata.openclaw.requires` in each `SKILL.md`.

## Install actions

* `metadata.openclaw.install` defines install options (brew/node/go/uv).
* The app calls `skills.install` to run installers on the gateway host.
* The gateway surfaces only one preferred installer when multiple are provided
  (brew when available, otherwise node manager from `skills.install`, default npm).

## Env/API keys

* The app stores keys in `~/.openclaw/openclaw.json` under `skills.entries.<skillKey>`.
* `skills.update` patches `enabled`, `apiKey`, and `env`.

## Remote mode

* Install + config updates happen on the gateway host (not the local Mac).
```

---

---

<!-- Page: Voice Wake -->

## Voice Wake

**Source:** https://docs.openclaw.ai/platforms/mac/voicewake

[Skip to main content](https://docs.openclaw.ai/platforms/mac/voicewake#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

macOS companion app

Voice Wake

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Voice Wake & Push-to-Talk](https://docs.openclaw.ai/platforms/mac/voicewake#voice-wake-%26-push-to-talk)
- [Modes](https://docs.openclaw.ai/platforms/mac/voicewake#modes)
- [Runtime behavior (wake-word)](https://docs.openclaw.ai/platforms/mac/voicewake#runtime-behavior-wake-word)
- [Lifecycle invariants](https://docs.openclaw.ai/platforms/mac/voicewake#lifecycle-invariants)
- [Sticky overlay failure mode (previous)](https://docs.openclaw.ai/platforms/mac/voicewake#sticky-overlay-failure-mode-previous)
- [Push-to-talk specifics](https://docs.openclaw.ai/platforms/mac/voicewake#push-to-talk-specifics)
- [User-facing settings](https://docs.openclaw.ai/platforms/mac/voicewake#user-facing-settings)
- [Forwarding behavior](https://docs.openclaw.ai/platforms/mac/voicewake#forwarding-behavior)
- [Forwarding payload](https://docs.openclaw.ai/platforms/mac/voicewake#forwarding-payload)
- [Quick verification](https://docs.openclaw.ai/platforms/mac/voicewake#quick-verification)

# [​](https://docs.openclaw.ai/platforms/mac/voicewake\#voice-wake-&-push-to-talk)  Voice Wake & Push-to-Talk

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#modes)  Modes

- **Wake-word mode** (default): always-on Speech recognizer waits for trigger tokens (`swabbleTriggerWords`). On match it starts capture, shows the overlay with partial text, and auto-sends after silence.
- **Push-to-talk (Right Option hold)**: hold the right Option key to capture immediately—no trigger needed. The overlay appears while held; releasing finalizes and forwards after a short delay so you can tweak text.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#runtime-behavior-wake-word)  Runtime behavior (wake-word)

- Speech recognizer lives in `VoiceWakeRuntime`.
- Trigger only fires when there’s a **meaningful pause** between the wake word and the next word (~0.55s gap). The overlay/chime can start on the pause even before the command begins.
- Silence windows: 2.0s when speech is flowing, 5.0s if only the trigger was heard.
- Hard stop: 120s to prevent runaway sessions.
- Debounce between sessions: 350ms.
- Overlay is driven via `VoiceWakeOverlayController` with committed/volatile coloring.
- After send, recognizer restarts cleanly to listen for the next trigger.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#lifecycle-invariants)  Lifecycle invariants

- If Voice Wake is enabled and permissions are granted, the wake-word recognizer should be listening (except during an explicit push-to-talk capture).
- Overlay visibility (including manual dismiss via the X button) must never prevent the recognizer from resuming.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#sticky-overlay-failure-mode-previous)  Sticky overlay failure mode (previous)

Previously, if the overlay got stuck visible and you manually closed it, Voice Wake could appear “dead” because the runtime’s restart attempt could be blocked by overlay visibility and no subsequent restart was scheduled.Hardening:

- Wake runtime restart is no longer blocked by overlay visibility.
- Overlay dismiss completion triggers a `VoiceWakeRuntime.refresh(...)` via `VoiceSessionCoordinator`, so manual X-dismiss always resumes listening.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#push-to-talk-specifics)  Push-to-talk specifics

- Hotkey detection uses a global `.flagsChanged` monitor for **right Option** (`keyCode 61` \+ `.option`). We only observe events (no swallowing).
- Capture pipeline lives in `VoicePushToTalk`: starts Speech immediately, streams partials to the overlay, and calls `VoiceWakeForwarder` on release.
- When push-to-talk starts we pause the wake-word runtime to avoid dueling audio taps; it restarts automatically after release.
- Permissions: requires Microphone + Speech; seeing events needs Accessibility/Input Monitoring approval.
- External keyboards: some may not expose right Option as expected—offer a fallback shortcut if users report misses.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#user-facing-settings)  User-facing settings

- **Voice Wake** toggle: enables wake-word runtime.
- **Hold Cmd+Fn to talk**: enables the push-to-talk monitor. Disabled on macOS < 26.
- Language & mic pickers, live level meter, trigger-word table, tester (local-only; does not forward).
- Mic picker preserves the last selection if a device disconnects, shows a disconnected hint, and temporarily falls back to the system default until it returns.
- **Sounds**: chimes on trigger detect and on send; defaults to the macOS “Glass” system sound. You can pick any `NSSound`-loadable file (e.g. MP3/WAV/AIFF) for each event or choose **No Sound**.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#forwarding-behavior)  Forwarding behavior

- When Voice Wake is enabled, transcripts are forwarded to the active gateway/agent (the same local vs remote mode used by the rest of the mac app).
- Replies are delivered to the **last-used main provider** (WhatsApp/Telegram/Discord/WebChat). If delivery fails, the error is logged and the run is still visible via WebChat/session logs.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#forwarding-payload)  Forwarding payload

- `VoiceWakeForwarder.prefixedTranscript(_:)` prepends the machine hint before sending. Shared between wake-word and push-to-talk paths.

## [​](https://docs.openclaw.ai/platforms/mac/voicewake\#quick-verification)  Quick verification

- Toggle push-to-talk on, hold Cmd+Fn, speak, release: overlay should show partials then send.
- While holding, menu-bar ears should stay enlarged (uses `triggerVoiceEars(ttl:nil)`); they drop after release.

[Menu Bar](https://docs.openclaw.ai/platforms/mac/menu-bar) [Voice Overlay](https://docs.openclaw.ai/platforms/mac/voice-overlay)

Ctrl+I

---
