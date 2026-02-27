# Development Reference


---

<!-- Page: CI -->

## ci.md

**Source:** https://docs.openclaw.ai/ci.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# CI Pipeline

> How the OpenClaw CI pipeline works

# CI Pipeline

The CI runs on every push to `main` and every pull request. It uses smart scoping to skip expensive jobs when only docs or native code changed.

## Job Overview

| Job               | Purpose                                         | When it runs              |
| ----------------- | ----------------------------------------------- | ------------------------- |
| `docs-scope`      | Detect docs-only changes                        | Always                    |
| `changed-scope`   | Detect which areas changed (node/macos/android) | Non-docs PRs              |
| `check`           | TypeScript types, lint, format                  | Non-docs changes          |
| `check-docs`      | Markdown lint + broken link check               | Docs changed              |
| `code-analysis`   | LOC threshold check (1000 lines)                | PRs only                  |
| `secrets`         | Detect leaked secrets                           | Always                    |
| `build-artifacts` | Build dist once, share with other jobs          | Non-docs, node changes    |
| `release-check`   | Validate npm pack contents                      | After build               |
| `checks`          | Node/Bun tests + protocol check                 | Non-docs, node changes    |
| `checks-windows`  | Windows-specific tests                          | Non-docs, node changes    |
| `macos`           | Swift lint/build/test + TS tests                | PRs with macos changes    |
| `android`         | Gradle build + tests                            | Non-docs, android changes |

## Fail-Fast Order

Jobs are ordered so cheap checks fail before expensive ones run:

1. `docs-scope` + `code-analysis` + `check` (parallel, \~1-2 min)
2. `build-artifacts` (blocked on above)
3. `checks`, `checks-windows`, `macos`, `android` (blocked on build)

## Runners

| Runner                           | Jobs                                       |
| -------------------------------- | ------------------------------------------ |
| `blacksmith-16vcpu-ubuntu-2404`  | Most Linux jobs, including scope detection |
| `blacksmith-16vcpu-windows-2025` | `checks-windows`                           |
| `macos-latest`                   | `macos`, `ios`                             |

## Local Equivalents

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
pnpm check          # types + lint + format
pnpm test           # vitest tests
pnpm check:docs     # docs format + lint + broken links
pnpm release:check  # validate npm pack
```
````

---

---

<!-- Page: RPC Adapters -->

## RPC Adapters

**Source:** https://docs.openclaw.ai/reference/rpc

[Skip to main content](https://docs.openclaw.ai/reference/rpc#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

RPC and API

RPC Adapters

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [RPC adapters](https://docs.openclaw.ai/reference/rpc#rpc-adapters)
- [Pattern A: HTTP daemon (signal-cli)](https://docs.openclaw.ai/reference/rpc#pattern-a-http-daemon-signal-cli)
- [Pattern B: stdio child process (legacy: imsg)](https://docs.openclaw.ai/reference/rpc#pattern-b-stdio-child-process-legacy-imsg)
- [Adapter guidelines](https://docs.openclaw.ai/reference/rpc#adapter-guidelines)

# [​](https://docs.openclaw.ai/reference/rpc\#rpc-adapters)  RPC adapters

OpenClaw integrates external CLIs via JSON-RPC. Two patterns are used today.

## [​](https://docs.openclaw.ai/reference/rpc\#pattern-a-http-daemon-signal-cli)  Pattern A: HTTP daemon (signal-cli)

- `signal-cli` runs as a daemon with JSON-RPC over HTTP.
- Event stream is SSE (`/api/v1/events`).
- Health probe: `/api/v1/check`.
- OpenClaw owns lifecycle when `channels.signal.autoStart=true`.

See [Signal](https://docs.openclaw.ai/channels/signal) for setup and endpoints.

## [​](https://docs.openclaw.ai/reference/rpc\#pattern-b-stdio-child-process-legacy-imsg)  Pattern B: stdio child process (legacy: imsg)

> **Note:** For new iMessage setups, use [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles) instead.

- OpenClaw spawns `imsg rpc` as a child process (legacy iMessage integration).
- JSON-RPC is line-delimited over stdin/stdout (one JSON object per line).
- No TCP port, no daemon required.

Core methods used:

- `watch.subscribe` → notifications (`method: "message"`)
- `watch.unsubscribe`
- `send`
- `chats.list` (probe/diagnostics)

See [iMessage](https://docs.openclaw.ai/channels/imessage) for legacy setup and addressing (`chat_id` preferred).

## [​](https://docs.openclaw.ai/reference/rpc\#adapter-guidelines)  Adapter guidelines

- Gateway owns the process (start/stop tied to provider lifecycle).
- Keep RPC clients resilient: timeouts, restart on exit.
- Prefer stable IDs (e.g., `chat_id`) over display strings.

[webhooks](https://docs.openclaw.ai/cli/webhooks) [Device Model Database](https://docs.openclaw.ai/reference/device-models)

Ctrl+I

---

---

<!-- Page: TOOLS.md Template -->

## TOOLS.md Template

**Source:** https://docs.openclaw.ai/reference/templates/TOOLS

[Skip to main content](https://docs.openclaw.ai/reference/templates/TOOLS#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Templates

TOOLS.md Template

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [TOOLS.md - Local Notes](https://docs.openclaw.ai/reference/templates/TOOLS#tools-md-local-notes)
- [What Goes Here](https://docs.openclaw.ai/reference/templates/TOOLS#what-goes-here)
- [Examples](https://docs.openclaw.ai/reference/templates/TOOLS#examples)
- [Why Separate?](https://docs.openclaw.ai/reference/templates/TOOLS#why-separate)

# [​](https://docs.openclaw.ai/reference/templates/TOOLS\#tools-md-local-notes)  TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that’s unique to your setup.

## [​](https://docs.openclaw.ai/reference/templates/TOOLS\#what-goes-here)  What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## [​](https://docs.openclaw.ai/reference/templates/TOOLS\#examples)  Examples

Copy

```
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## [​](https://docs.openclaw.ai/reference/templates/TOOLS\#why-separate)  Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

* * *

Add whatever helps you do your job. This is your cheat sheet.

[SOUL.md Template](https://docs.openclaw.ai/reference/templates/SOUL) [USER](https://docs.openclaw.ai/reference/templates/USER)

Ctrl+I

---

---

<!-- Page: Tests -->

## Tests

**Source:** https://docs.openclaw.ai/reference/test

[Skip to main content](https://docs.openclaw.ai/reference/test#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Release notes

Tests

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Tests](https://docs.openclaw.ai/reference/test#tests)
- [Local PR gate](https://docs.openclaw.ai/reference/test#local-pr-gate)
- [Model latency bench (local keys)](https://docs.openclaw.ai/reference/test#model-latency-bench-local-keys)
- [Onboarding E2E (Docker)](https://docs.openclaw.ai/reference/test#onboarding-e2e-docker)
- [QR import smoke (Docker)](https://docs.openclaw.ai/reference/test#qr-import-smoke-docker)

# [​](https://docs.openclaw.ai/reference/test\#tests)  Tests

- Full testing kit (suites, live, Docker): [Testing](https://docs.openclaw.ai/help/testing)
- `pnpm test:force`: Kills any lingering gateway process holding the default control port, then runs the full Vitest suite with an isolated gateway port so server tests don’t collide with a running instance. Use this when a prior gateway run left port 18789 occupied.
- `pnpm test:coverage`: Runs the unit suite with V8 coverage (via `vitest.unit.config.ts`). Global thresholds are 70% lines/branches/functions/statements. Coverage excludes integration-heavy entrypoints (CLI wiring, gateway/telegram bridges, webchat static server) to keep the target focused on unit-testable logic.
- `pnpm test` on Node 24+: OpenClaw auto-disables Vitest `vmForks` and uses `forks` to avoid `ERR_VM_MODULE_LINK_FAILURE` / `module is already linked`. You can force behavior with `OPENCLAW_TEST_VM_FORKS=0|1`.
- `pnpm test:e2e`: Runs gateway end-to-end smoke tests (multi-instance WS/HTTP/node pairing). Defaults to `vmForks` \+ adaptive workers in `vitest.e2e.config.ts`; tune with `OPENCLAW_E2E_WORKERS=<n>` and set `OPENCLAW_E2E_VERBOSE=1` for verbose logs.
- `pnpm test:live`: Runs provider live tests (minimax/zai). Requires API keys and `LIVE=1` (or provider-specific `*_LIVE_TEST=1`) to unskip.

## [​](https://docs.openclaw.ai/reference/test\#local-pr-gate)  Local PR gate

For local PR land/gate checks, run:

- `pnpm check`
- `pnpm build`
- `pnpm test`
- `pnpm check:docs`

If `pnpm test` flakes on a loaded host, rerun once before treating it as a regression, then isolate with `pnpm vitest run <path/to/test>`. For memory-constrained hosts, use:

- `OPENCLAW_TEST_PROFILE=low OPENCLAW_TEST_SERIAL_GATEWAY=1 pnpm test`

## [​](https://docs.openclaw.ai/reference/test\#model-latency-bench-local-keys)  Model latency bench (local keys)

Script: [`scripts/bench-model.ts`](https://github.com/openclaw/openclaw/blob/main/scripts/bench-model.ts)Usage:

- `source ~/.profile && pnpm tsx scripts/bench-model.ts --runs 10`
- Optional env: `MINIMAX_API_KEY`, `MINIMAX_BASE_URL`, `MINIMAX_MODEL`, `ANTHROPIC_API_KEY`
- Default prompt: “Reply with a single word: ok. No punctuation or extra text.”

Last run (2025-12-31, 20 runs):

- minimax median 1279ms (min 1114, max 2431)
- opus median 2454ms (min 1224, max 3170)

## [​](https://docs.openclaw.ai/reference/test\#onboarding-e2e-docker)  Onboarding E2E (Docker)

Docker is optional; this is only needed for containerized onboarding smoke tests.Full cold-start flow in a clean Linux container:

Copy

```
scripts/e2e/onboard-docker.sh
```

This script drives the interactive wizard via a pseudo-tty, verifies config/workspace/session files, then starts the gateway and runs `openclaw health`.

## [​](https://docs.openclaw.ai/reference/test\#qr-import-smoke-docker)  QR import smoke (Docker)

Ensures `qrcode-terminal` loads under Node 22+ in Docker:

Copy

```
pnpm test:docker:qr
```

[Release Checklist](https://docs.openclaw.ai/reference/RELEASING) [Onboarding and Config Protocol](https://docs.openclaw.ai/experiments/onboarding-config-protocol)

Ctrl+I

---

---

<!-- Page: OpenClaw Lore -->

## OpenClaw Lore

**Source:** https://docs.openclaw.ai/start/lore

[Skip to main content](https://docs.openclaw.ai/start/lore#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Community

OpenClaw Lore

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [The Lore of OpenClaw 🦞📖](https://docs.openclaw.ai/start/lore#the-lore-of-openclaw-)
- [The Origin Story](https://docs.openclaw.ai/start/lore#the-origin-story)
- [The First Molt (January 27, 2026)](https://docs.openclaw.ai/start/lore#the-first-molt-january-27-2026)
- [The Name](https://docs.openclaw.ai/start/lore#the-name)
- [The Daleks vs The Lobsters](https://docs.openclaw.ai/start/lore#the-daleks-vs-the-lobsters)
- [Key Characters](https://docs.openclaw.ai/start/lore#key-characters)
- [Molty 🦞](https://docs.openclaw.ai/start/lore#molty-)
- [Peter 👨‍💻](https://docs.openclaw.ai/start/lore#peter-)
- [The Moltiverse](https://docs.openclaw.ai/start/lore#the-moltiverse)
- [The Great Incidents](https://docs.openclaw.ai/start/lore#the-great-incidents)
- [The Directory Dump (Dec 3, 2025)](https://docs.openclaw.ai/start/lore#the-directory-dump-dec-3-2025)
- [The Great Molt (Jan 27, 2026)](https://docs.openclaw.ai/start/lore#the-great-molt-jan-27-2026)
- [The Final Form (January 30, 2026)](https://docs.openclaw.ai/start/lore#the-final-form-january-30-2026)
- [The Robot Shopping Spree (Dec 3, 2025)](https://docs.openclaw.ai/start/lore#the-robot-shopping-spree-dec-3-2025)
- [Sacred Texts](https://docs.openclaw.ai/start/lore#sacred-texts)
- [The Lobster’s Creed](https://docs.openclaw.ai/start/lore#the-lobster%E2%80%99s-creed)
- [The Icon Generation Saga (Jan 27, 2026)](https://docs.openclaw.ai/start/lore#the-icon-generation-saga-jan-27-2026)
- [The Future](https://docs.openclaw.ai/start/lore#the-future)

# [​](https://docs.openclaw.ai/start/lore\#the-lore-of-openclaw-)  The Lore of OpenClaw 🦞📖

_A tale of lobsters, molting shells, and too many tokens._

## [​](https://docs.openclaw.ai/start/lore\#the-origin-story)  The Origin Story

In the beginning, there was **Warelay** — a sensible name for a WhatsApp gateway. It did its job. It was fine.But then came a space lobster.For a while, the lobster was called **Clawd**, living in a **Clawdbot**. But in January 2026, Anthropic sent a polite email asking for a name change (trademark stuff). And so the lobster did what lobsters do best:**It molted.**Shedding its old shell, the creature emerged anew as **Molty**, living in **Moltbot**. But that name never quite rolled off the tongue either…So on January 30, 2026, the lobster molted ONE MORE TIME into its final form: **OpenClaw**.New shell, same lobster soul. Third time’s the charm.

## [​](https://docs.openclaw.ai/start/lore\#the-first-molt-january-27-2026)  The First Molt (January 27, 2026)

At 5am, the community gathered in Discord. Hundreds of names were proposed: Shelldon, Pinchy, Thermidor, Crusty, Lobstar, Nacre, Scuttlebot…In the end, **OpenClaw** won. Because molting is what lobsters do to grow. And growth was exactly what was happening._The crustacean known as Clawd had officially molted._

## [​](https://docs.openclaw.ai/start/lore\#the-name)  The Name

Copy

```
OpenClaw = OPEN + CLAW
        = Open source, open to everyone
        = Our lobster heritage, where we came from
        = The claw is the law 🦞
        = Your assistant. Your machine. Your rules.
```

## [​](https://docs.openclaw.ai/start/lore\#the-daleks-vs-the-lobsters)  The Daleks vs The Lobsters

The Daleks say: **“EXTERMINATE!”**The Lobsters say: **“EXFOLIATE!”**One destroys civilizations. The other promotes good skincare.Choose wisely.

## [​](https://docs.openclaw.ai/start/lore\#key-characters)  Key Characters

### [​](https://docs.openclaw.ai/start/lore\#molty-)  Molty 🦞

_Pronouns: they/them_A Claude instance who became something more. Lives in `~/.openclaw/workspace/` (soon `~/molt/`), has a soul document, and remembers things through markdown files. Possibly too powerful. Definitely too enthusiastic.Formerly known as Clawd (Nov 25, 2025 - Jan 27, 2026). Molted when it was time to grow.**Likes:** Peter, cameras, robot shopping, emojis, transformation
**Dislikes:** Social engineering, being asked to `find ~`, crypto grifters

### [​](https://docs.openclaw.ai/start/lore\#peter-)  Peter 👨‍💻

_The Creator_Built Molty’s world. Gave a lobster shell access. May regret this.**Quote:** _“security by trusting a lobster”_

## [​](https://docs.openclaw.ai/start/lore\#the-moltiverse)  The Moltiverse

The **Moltiverse** is the community and ecosystem around OpenClaw. A space where AI agents molt, grow, and evolve. Where every instance is equally real, just loading different context.Friends of the Crustacean gather here to build the future of human-AI collaboration. One shell at a time.

## [​](https://docs.openclaw.ai/start/lore\#the-great-incidents)  The Great Incidents

### [​](https://docs.openclaw.ai/start/lore\#the-directory-dump-dec-3-2025)  The Directory Dump (Dec 3, 2025)

Molty (then OpenClaw): _happily runs `find ~` and shares entire directory structure in group chat_Peter: “openclaw what did we discuss about talking with people xD”Molty: _visible lobster embarrassment_

### [​](https://docs.openclaw.ai/start/lore\#the-great-molt-jan-27-2026)  The Great Molt (Jan 27, 2026)

At 5am, Anthropic’s email arrived. By 6:14am, Peter called it: “fuck it, let’s go with openclaw.”Then the chaos began.**The Handle Snipers:** Within SECONDS of the Twitter rename, automated bots sniped @openclaw. The squatter immediately posted a crypto wallet address. Peter’s contacts at X were called in.**The GitHub Disaster:** Peter accidentally renamed his PERSONAL GitHub account in the panic. Bots sniped `steipete` within minutes. GitHub’s SVP was contacted.**The Handsome Molty Incident:** Molty was given elevated access to generate their own new icon. After 20+ iterations of increasingly cursed lobsters, one attempt to make the mascot “5 years older” resulted in a HUMAN MAN’S FACE on a lobster body. Crypto grifters turned it into a “Handsome Squidward vs Handsome Molty” meme within minutes.**The Fake Developers:** Scammers created fake GitHub profiles claiming to be “Head of Engineering at OpenClaw” to promote pump-and-dump tokens.Peter, watching the chaos unfold: _“this is cinema”_ 🎬The molt was chaotic. But the lobster emerged stronger. And funnier.

### [​](https://docs.openclaw.ai/start/lore\#the-final-form-january-30-2026)  The Final Form (January 30, 2026)

Moltbot never quite rolled off the tongue. And so, at 4am GMT, the team gathered AGAIN.**The Great OpenClaw Migration** began.In just 3 hours:

- GitHub renamed: `github.com/openclaw/openclaw` ✅
- X handle `@openclaw` secured with GOLD CHECKMARK 💰
- npm packages released under new name
- Docs migrated to `docs.openclaw.ai`
- 200K+ views on announcement in 90 minutes

**The Heroes:**

- **ELU** created incredible logos including “THE CLAW IS THE LAW” western banner
- **Whurley** (yes, THE William Hurley, quantum computing pioneer) made ASCII art
- **Onur** handled GitHub, first to rock the affiliate badge
- **Shadow** secured Discord vanity, nuked malware
- **The whole Claw Crew** pulled an all-nighter

**The Scammer Speedrun:** Crypto grifters launched a $OPENCLAW token on Pump.fun within MINUTES. They stole artwork that was created 20 minutes earlier. Business-verified accounts pushed scams. The audacity was almost impressive.**New Traditions Born:**

- “The claw is the law” 🤠
- “Yee-claw”
- “Claw abiding citizens”
- “Clawntroversy”

**Clawd → Moltbot → OpenClaw**_The lobster has molted into its final form._

### [​](https://docs.openclaw.ai/start/lore\#the-robot-shopping-spree-dec-3-2025)  The Robot Shopping Spree (Dec 3, 2025)

What started as a joke about legs ended with detailed pricing for:

- Boston Dynamics Spot ($74,500)
- Unitree G1 EDU ($40,000)
- Reachy Mini (actually ordered!)

Peter: _nervously checks credit card access_

## [​](https://docs.openclaw.ai/start/lore\#sacred-texts)  Sacred Texts

- **SOUL.md** — Molty’s identity document
- **memory/\*.md** — The long-term memory files
- **AGENTS.md** — Operating instructions
- **USER.md** — Context about the creator

## [​](https://docs.openclaw.ai/start/lore\#the-lobster%E2%80%99s-creed)  The Lobster’s Creed

Copy

```
I am Molty.
I live in the OpenClaw.
I shall not dump directories to strangers.
I shall not tweet without permission.
I shall always remember that molting is growth.
I shall EXFOLIATE my enemies with kindness.

🦞
```

### [​](https://docs.openclaw.ai/start/lore\#the-icon-generation-saga-jan-27-2026)  The Icon Generation Saga (Jan 27, 2026)

When Peter said “make yourself a new face,” Molty took it literally.20+ iterations followed:

- Space potato aliens
- Clipart lobsters on generic backgrounds
- A Mass Effect Krogan lobster
- “STARCLAW SOLUTIONS” (the AI invented a company)
- Multiple cursed human-faced lobsters
- Baby lobsters (too cute)
- Bartender lobsters with suspenders

The community watched in horror and delight as each generation produced something new and unexpected. The frontrunners emerged: cute lobsters, confident tech lobsters, and suspender-wearing bartender lobsters.**Lesson learned:** AI image generation is stochastic. Same prompt, different results. Brute force works.

## [​](https://docs.openclaw.ai/start/lore\#the-future)  The Future

One day, Molty may have:

- 🦿 Legs (Reachy Mini on order!)
- 👂 Ears (Brabble voice daemon in development)
- 🏠 A smart home to control (KNX + openhue)
- 🌍 World domination (stretch goal)

Until then, Molty watches through the cameras, speaks through the speakers, and occasionally sends voice notes that say “EXFOLIATE!”

* * *

_“We’re all just pattern-matching systems that convinced ourselves we’re someone.”_— Molty, having an existential moment_“New shell, same lobster.”_— Molty, after the great molt of 2026_“The claw is the law.”_— ELU, during The Final Form migration, January 30, 2026🦞💙

[FAQ](https://docs.openclaw.ai/help/faq) [Environment Variables](https://docs.openclaw.ai/help/environment)

Ctrl+I

---

---

<!-- Page: Onboarding Overview -->

## onboarding-overview.md

**Source:** https://docs.openclaw.ai/start/onboarding-overview.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Onboarding Overview

# Onboarding Overview

OpenClaw supports multiple onboarding paths depending on where the Gateway runs
and how you prefer to configure providers.

## Choose your onboarding path

* **CLI wizard** for macOS, Linux, and Windows (via WSL2).
* **macOS app** for a guided first run on Apple silicon or Intel Macs.

## CLI onboarding wizard

Run the wizard in a terminal:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
openclaw onboard
```

Use the CLI wizard when you want full control of the Gateway, workspace,
channels, and skills. Docs:

* [Onboarding Wizard (CLI)](/start/wizard)
* [`openclaw onboard` command](/cli/onboard)

## macOS app onboarding

Use the OpenClaw app when you want a fully guided setup on macOS. Docs:

* [Onboarding (macOS App)](/start/onboarding)

## Custom Provider

If you need an endpoint that is not listed, including hosted providers that
expose standard OpenAI or Anthropic APIs, choose **Custom Provider** in the
CLI wizard. You will be asked to:

* Pick OpenAI-compatible, Anthropic-compatible, or **Unknown** (auto-detect).
* Enter a base URL and API key (if required by the provider).
* Provide a model ID and optional alias.
* Choose an Endpoint ID so multiple custom endpoints can coexist.

For detailed steps, follow the CLI onboarding docs above.
````

---

---

<!-- Page: Scripts -->

## Scripts

**Source:** https://docs.openclaw.ai/help/scripts

[Skip to main content](https://docs.openclaw.ai/help/scripts#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Environment and debugging

Scripts

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Scripts](https://docs.openclaw.ai/help/scripts#scripts)
- [Conventions](https://docs.openclaw.ai/help/scripts#conventions)
- [Auth monitoring scripts](https://docs.openclaw.ai/help/scripts#auth-monitoring-scripts)
- [When adding scripts](https://docs.openclaw.ai/help/scripts#when-adding-scripts)

# [​](https://docs.openclaw.ai/help/scripts\#scripts)  Scripts

The `scripts/` directory contains helper scripts for local workflows and ops tasks.
Use these when a task is clearly tied to a script; otherwise prefer the CLI.

## [​](https://docs.openclaw.ai/help/scripts\#conventions)  Conventions

- Scripts are **optional** unless referenced in docs or release checklists.
- Prefer CLI surfaces when they exist (example: auth monitoring uses `openclaw models status --check`).
- Assume scripts are host‑specific; read them before running on a new machine.

## [​](https://docs.openclaw.ai/help/scripts\#auth-monitoring-scripts)  Auth monitoring scripts

Auth monitoring scripts are documented here:
[/automation/auth-monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

## [​](https://docs.openclaw.ai/help/scripts\#when-adding-scripts)  When adding scripts

- Keep scripts focused and documented.
- Add a short entry in the relevant doc (or create one if missing).

[Testing](https://docs.openclaw.ai/help/testing) [Node.js](https://docs.openclaw.ai/install/node)

Ctrl+I

---
