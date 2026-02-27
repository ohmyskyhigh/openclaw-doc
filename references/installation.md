# Installation


---

<!-- Page: Install -->

## Install

**Source:** https://docs.openclaw.ai/install

[Skip to main content](https://docs.openclaw.ai/install#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Install overview

Install

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [Install](https://docs.openclaw.ai/install#install)
- [System requirements](https://docs.openclaw.ai/install#system-requirements)
- [Install methods](https://docs.openclaw.ai/install#install-methods)
- [Other install methods](https://docs.openclaw.ai/install#other-install-methods)
- [After install](https://docs.openclaw.ai/install#after-install)
- [Troubleshooting: openclaw not found](https://docs.openclaw.ai/install#troubleshooting-openclaw-not-found)
- [Update / uninstall](https://docs.openclaw.ai/install#update-%2F-uninstall)

# [​](https://docs.openclaw.ai/install\#install)  Install

Already followed [Getting Started](https://docs.openclaw.ai/start/getting-started)? You’re all set — this page is for alternative install methods, platform-specific instructions, and maintenance.

## [​](https://docs.openclaw.ai/install\#system-requirements)  System requirements

- **[Node 22+](https://docs.openclaw.ai/install/node)** (the [installer script](https://docs.openclaw.ai/install#install-methods) will install it if missing)
- macOS, Linux, or Windows
- `pnpm` only if you build from source

On Windows, we strongly recommend running OpenClaw under [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install).

## [​](https://docs.openclaw.ai/install\#install-methods)  Install methods

The **installer script** is the recommended way to install OpenClaw. It handles Node detection, installation, and onboarding in one step.

For VPS/cloud hosts, avoid third-party “1-click” marketplace images when possible. Prefer a clean base OS image (for example Ubuntu LTS), then install OpenClaw yourself with the installer script.

Installer script

Downloads the CLI, installs it globally via npm, and launches the onboarding wizard.

- macOS / Linux / WSL2

- Windows (PowerShell)


Copy

```
curl -fsSL https://openclaw.ai/install.sh | bash
```

Copy

```
iwr -useb https://openclaw.ai/install.ps1 | iex
```

That’s it — the script handles Node detection, installation, and onboarding.To skip onboarding and just install the binary:

- macOS / Linux / WSL2

- Windows (PowerShell)


Copy

```
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --no-onboard
```

Copy

```
& ([scriptblock]::Create((iwr -useb https://openclaw.ai/install.ps1))) -NoOnboard
```

For all flags, env vars, and CI/automation options, see [Installer internals](https://docs.openclaw.ai/install/installer).

npm / pnpm

If you already have Node 22+ and prefer to manage the install yourself:

- npm

- pnpm


Copy

```
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

sharp build errors?

If you have libvips installed globally (common on macOS via Homebrew) and `sharp` fails, force prebuilt binaries:

Copy

```
SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm install -g openclaw@latest
```

If you see `sharp: Please add node-gyp to your dependencies`, either install build tooling (macOS: Xcode CLT + `npm install -g node-gyp`) or use the env var above.

Copy

```
pnpm add -g openclaw@latest
pnpm approve-builds -g        # approve openclaw, node-llama-cpp, sharp, etc.
openclaw onboard --install-daemon
```

pnpm requires explicit approval for packages with build scripts. After the first install shows the “Ignored build scripts” warning, run `pnpm approve-builds -g` and select the listed packages.

From source

For contributors or anyone who wants to run from a local checkout.

1

[Navigate to header](https://docs.openclaw.ai/install#)

Clone and build

Clone the [OpenClaw repo](https://github.com/openclaw/openclaw) and build:

Copy

```
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm ui:build
pnpm build
```

2

[Navigate to header](https://docs.openclaw.ai/install#)

Link the CLI

Make the `openclaw` command available globally:

Copy

```
pnpm link --global
```

Alternatively, skip the link and run commands via `pnpm openclaw ...` from inside the repo.

3

[Navigate to header](https://docs.openclaw.ai/install#)

Run onboarding

Copy

```
openclaw onboard --install-daemon
```

For deeper development workflows, see [Setup](https://docs.openclaw.ai/start/setup).

## [​](https://docs.openclaw.ai/install\#other-install-methods)  Other install methods

[**Docker** \\
\\
Containerized or headless deployments.](https://docs.openclaw.ai/install/docker) [**Podman** \\
\\
Rootless container: run `setup-podman.sh` once, then the launch script.](https://docs.openclaw.ai/install/podman) [**Nix** \\
\\
Declarative install via Nix.](https://docs.openclaw.ai/install/nix) [**Ansible** \\
\\
Automated fleet provisioning.](https://docs.openclaw.ai/install/ansible) [**Bun** \\
\\
CLI-only usage via the Bun runtime.](https://docs.openclaw.ai/install/bun)

## [​](https://docs.openclaw.ai/install\#after-install)  After install

Verify everything is working:

Copy

```
openclaw doctor         # check for config issues
openclaw status         # gateway status
openclaw dashboard      # open the browser UI
```

If you need custom runtime paths, use:

- `OPENCLAW_HOME` for home-directory based internal paths
- `OPENCLAW_STATE_DIR` for mutable state location
- `OPENCLAW_CONFIG_PATH` for config file location

See [Environment vars](https://docs.openclaw.ai/help/environment) for precedence and full details.

## [​](https://docs.openclaw.ai/install\#troubleshooting-openclaw-not-found)  Troubleshooting: `openclaw` not found

PATH diagnosis and fix

Quick diagnosis:

Copy

```
node -v
npm -v
npm prefix -g
echo "$PATH"
```

If `$(npm prefix -g)/bin` (macOS/Linux) or `$(npm prefix -g)` (Windows) is **not** in your `$PATH`, your shell can’t find global npm binaries (including `openclaw`).Fix — add it to your shell startup file (`~/.zshrc` or `~/.bashrc`):

Copy

```
export PATH="$(npm prefix -g)/bin:$PATH"
```

On Windows, add the output of `npm prefix -g` to your PATH.Then open a new terminal (or `rehash` in zsh / `hash -r` in bash).

## [​](https://docs.openclaw.ai/install\#update-/-uninstall)  Update / uninstall

[**Updating** \\
\\
Keep OpenClaw up to date.](https://docs.openclaw.ai/install/updating) [**Migrating** \\
\\
Move to a new machine.](https://docs.openclaw.ai/install/migrating) [**Uninstall** \\
\\
Remove OpenClaw completely.](https://docs.openclaw.ai/install/uninstall)

[Installer Internals](https://docs.openclaw.ai/install/installer)

Ctrl+I

---

---

<!-- Page: Docker -->

## docker.md

**Source:** https://docs.openclaw.ai/install/docker.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Docker

# Docker (optional)

Docker is **optional**. Use it only if you want a containerized gateway or to validate the Docker flow.

## Is Docker right for me?

* **Yes**: you want an isolated, throwaway gateway environment or to run OpenClaw on a host without local installs.
* **No**: you’re running on your own machine and just want the fastest dev loop. Use the normal install flow instead.
* **Sandboxing note**: agent sandboxing uses Docker too, but it does **not** require the full gateway to run in Docker. See [Sandboxing](/gateway/sandboxing).

This guide covers:

* Containerized Gateway (full OpenClaw in Docker)
* Per-session Agent Sandbox (host gateway + Docker-isolated agent tools)

Sandboxing details: [Sandboxing](/gateway/sandboxing)

## Requirements

* Docker Desktop (or Docker Engine) + Docker Compose v2
* At least 2 GB RAM for image build (`pnpm install` may be OOM-killed on 1 GB hosts with exit 137)
* Enough disk for images + logs

## Containerized Gateway (Docker Compose)

### Quick start (recommended)

From repo root:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
./docker-setup.sh
```

This script:

* builds the gateway image
* runs the onboarding wizard
* prints optional provider setup hints
* starts the gateway via Docker Compose
* generates a gateway token and writes it to `.env`

Optional env vars:

* `OPENCLAW_DOCKER_APT_PACKAGES` — install extra apt packages during build
* `OPENCLAW_EXTRA_MOUNTS` — add extra host bind mounts
* `OPENCLAW_HOME_VOLUME` — persist `/home/node` in a named volume

After it finishes:

* Open `http://127.0.0.1:18789/` in your browser.
* Paste the token into the Control UI (Settings → token).
* Need the URL again? Run `docker compose run --rm openclaw-cli dashboard --no-open`.

It writes config/workspace on the host:

* `~/.openclaw/`
* `~/.openclaw/workspace`

Running on a VPS? See [Hetzner (Docker VPS)](/install/hetzner).

### Shell Helpers (optional)

For easier day-to-day Docker management, install `ClawDock`:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
mkdir -p ~/.clawdock && curl -sL https://raw.githubusercontent.com/openclaw/openclaw/main/scripts/shell-helpers/clawdock-helpers.sh -o ~/.clawdock/clawdock-helpers.sh
```

**Add to your shell config (zsh):**

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
echo 'source ~/.clawdock/clawdock-helpers.sh' >> ~/.zshrc && source ~/.zshrc
```

Then use `clawdock-start`, `clawdock-stop`, `clawdock-dashboard`, etc. Run `clawdock-help` for all commands.

See [`ClawDock` Helper README](https://github.com/openclaw/openclaw/blob/main/scripts/shell-helpers/README.md) for details.

### Manual flow (compose)

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker build -t openclaw:local -f Dockerfile .
docker compose run --rm openclaw-cli onboard
docker compose up -d openclaw-gateway
```

Note: run `docker compose ...` from the repo root. If you enabled
`OPENCLAW_EXTRA_MOUNTS` or `OPENCLAW_HOME_VOLUME`, the setup script writes
`docker-compose.extra.yml`; include it when running Compose elsewhere:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose -f docker-compose.yml -f docker-compose.extra.yml <command>
```

### Control UI token + pairing (Docker)

If you see “unauthorized” or “disconnected (1008): pairing required”, fetch a
fresh dashboard link and approve the browser device:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose run --rm openclaw-cli dashboard --no-open
docker compose run --rm openclaw-cli devices list
docker compose run --rm openclaw-cli devices approve <requestId>
```

More detail: [Dashboard](/web/dashboard), [Devices](/cli/devices).

### Extra mounts (optional)

If you want to mount additional host directories into the containers, set
`OPENCLAW_EXTRA_MOUNTS` before running `docker-setup.sh`. This accepts a
comma-separated list of Docker bind mounts and applies them to both
`openclaw-gateway` and `openclaw-cli` by generating `docker-compose.extra.yml`.

Example:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_EXTRA_MOUNTS="$HOME/.codex:/home/node/.codex:ro,$HOME/github:/home/node/github:rw"
./docker-setup.sh
```

Notes:

* Paths must be shared with Docker Desktop on macOS/Windows.
* Each entry must be `source:target[:options]` with no spaces, tabs, or newlines.
* If you edit `OPENCLAW_EXTRA_MOUNTS`, rerun `docker-setup.sh` to regenerate the
  extra compose file.
* `docker-compose.extra.yml` is generated. Don’t hand-edit it.

### Persist the entire container home (optional)

If you want `/home/node` to persist across container recreation, set a named
volume via `OPENCLAW_HOME_VOLUME`. This creates a Docker volume and mounts it at
`/home/node`, while keeping the standard config/workspace bind mounts. Use a
named volume here (not a bind path); for bind mounts, use
`OPENCLAW_EXTRA_MOUNTS`.

Example:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_HOME_VOLUME="openclaw_home"
./docker-setup.sh
```

You can combine this with extra mounts:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_HOME_VOLUME="openclaw_home"
export OPENCLAW_EXTRA_MOUNTS="$HOME/.codex:/home/node/.codex:ro,$HOME/github:/home/node/github:rw"
./docker-setup.sh
```

Notes:

* Named volumes must match `^[A-Za-z0-9][A-Za-z0-9_.-]*$`.
* If you change `OPENCLAW_HOME_VOLUME`, rerun `docker-setup.sh` to regenerate the
  extra compose file.
* The named volume persists until removed with `docker volume rm <name>`.

### Install extra apt packages (optional)

If you need system packages inside the image (for example, build tools or media
libraries), set `OPENCLAW_DOCKER_APT_PACKAGES` before running `docker-setup.sh`.
This installs the packages during the image build, so they persist even if the
container is deleted.

Example:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_DOCKER_APT_PACKAGES="ffmpeg build-essential"
./docker-setup.sh
```

Notes:

* This accepts a space-separated list of apt package names.
* If you change `OPENCLAW_DOCKER_APT_PACKAGES`, rerun `docker-setup.sh` to rebuild
  the image.

### Power-user / full-featured container (opt-in)

The default Docker image is **security-first** and runs as the non-root `node`
user. This keeps the attack surface small, but it means:

* no system package installs at runtime
* no Homebrew by default
* no bundled Chromium/Playwright browsers

If you want a more full-featured container, use these opt-in knobs:

1. **Persist `/home/node`** so browser downloads and tool caches survive:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_HOME_VOLUME="openclaw_home"
./docker-setup.sh
```

2. **Bake system deps into the image** (repeatable + persistent):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
export OPENCLAW_DOCKER_APT_PACKAGES="git curl jq"
./docker-setup.sh
```

3. **Install Playwright browsers without `npx`** (avoids npm override conflicts):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose run --rm openclaw-cli \
  node /app/node_modules/playwright-core/cli.js install chromium
```

If you need Playwright to install system deps, rebuild the image with
`OPENCLAW_DOCKER_APT_PACKAGES` instead of using `--with-deps` at runtime.

4. **Persist Playwright browser downloads**:

* Set `PLAYWRIGHT_BROWSERS_PATH=/home/node/.cache/ms-playwright` in
  `docker-compose.yml`.
* Ensure `/home/node` persists via `OPENCLAW_HOME_VOLUME`, or mount
  `/home/node/.cache/ms-playwright` via `OPENCLAW_EXTRA_MOUNTS`.

### Permissions + EACCES

The image runs as `node` (uid 1000). If you see permission errors on
`/home/node/.openclaw`, make sure your host bind mounts are owned by uid 1000.

Example (Linux host):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
sudo chown -R 1000:1000 /path/to/openclaw-config /path/to/openclaw-workspace
```

If you choose to run as root for convenience, you accept the security tradeoff.

### Faster rebuilds (recommended)

To speed up rebuilds, order your Dockerfile so dependency layers are cached.
This avoids re-running `pnpm install` unless lockfiles change:

```dockerfile  theme={"theme":{"light":"min-light","dark":"min-dark"}}
FROM node:22-bookworm

# Install Bun (required for build scripts)
RUN curl -fsSL https://bun.sh/install | bash
ENV PATH="/root/.bun/bin:${PATH}"

RUN corepack enable

WORKDIR /app

# Cache dependencies unless package metadata changes
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml .npmrc ./
COPY ui/package.json ./ui/package.json
COPY scripts ./scripts

RUN pnpm install --frozen-lockfile

COPY . .
RUN pnpm build
RUN pnpm ui:install
RUN pnpm ui:build

ENV NODE_ENV=production

CMD ["node","dist/index.js"]
```

### Channel setup (optional)

Use the CLI container to configure channels, then restart the gateway if needed.

WhatsApp (QR):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose run --rm openclaw-cli channels login
```

Telegram (bot token):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose run --rm openclaw-cli channels add --channel telegram --token "<token>"
```

Discord (bot token):

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose run --rm openclaw-cli channels add --channel discord --token "<token>"
```

Docs: [WhatsApp](/channels/whatsapp), [Telegram](/channels/telegram), [Discord](/channels/discord)

### OpenAI Codex OAuth (headless Docker)

If you pick OpenAI Codex OAuth in the wizard, it opens a browser URL and tries
to capture a callback on `http://127.0.0.1:1455/auth/callback`. In Docker or
headless setups that callback can show a browser error. Copy the full redirect
URL you land on and paste it back into the wizard to finish auth.

### Health check

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker compose exec openclaw-gateway node dist/index.js health --token "$OPENCLAW_GATEWAY_TOKEN"
```

### E2E smoke test (Docker)

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
scripts/e2e/onboard-docker.sh
```

### QR import smoke test (Docker)

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
pnpm test:docker:qr
```

### Notes

* Gateway bind defaults to `lan` for container use.
* Dockerfile CMD uses `--allow-unconfigured`; mounted config with `gateway.mode` not `local` will still start. Override CMD to enforce the guard.
* The gateway container is the source of truth for sessions (`~/.openclaw/agents/<agentId>/sessions/`).

## Agent Sandbox (host gateway + Docker tools)

Deep dive: [Sandboxing](/gateway/sandboxing)

### What it does

When `agents.defaults.sandbox` is enabled, **non-main sessions** run tools inside a Docker
container. The gateway stays on your host, but the tool execution is isolated:

* scope: `"agent"` by default (one container + workspace per agent)
* scope: `"session"` for per-session isolation
* per-scope workspace folder mounted at `/workspace`
* optional agent workspace access (`agents.defaults.sandbox.workspaceAccess`)
* allow/deny tool policy (deny wins)
* inbound media is copied into the active sandbox workspace (`media/inbound/*`) so tools can read it (with `workspaceAccess: "rw"`, this lands in the agent workspace)

Warning: `scope: "shared"` disables cross-session isolation. All sessions share
one container and one workspace.

### Per-agent sandbox profiles (multi-agent)

If you use multi-agent routing, each agent can override sandbox + tool settings:
`agents.list[].sandbox` and `agents.list[].tools` (plus `agents.list[].tools.sandbox.tools`). This lets you run
mixed access levels in one gateway:

* Full access (personal agent)
* Read-only tools + read-only workspace (family/work agent)
* No filesystem/shell tools (public agent)

See [Multi-Agent Sandbox & Tools](/tools/multi-agent-sandbox-tools) for examples,
precedence, and troubleshooting.

### Default behavior

* Image: `openclaw-sandbox:bookworm-slim`
* One container per agent
* Agent workspace access: `workspaceAccess: "none"` (default) uses `~/.openclaw/sandboxes`
  * `"ro"` keeps the sandbox workspace at `/workspace` and mounts the agent workspace read-only at `/agent` (disables `write`/`edit`/`apply_patch`)
  * `"rw"` mounts the agent workspace read/write at `/workspace`
* Auto-prune: idle > 24h OR age > 7d
* Network: `none` by default (explicitly opt-in if you need egress)
  * `host` is blocked.
  * `container:<id>` is blocked by default (namespace-join risk).
* Default allow: `exec`, `process`, `read`, `write`, `edit`, `sessions_list`, `sessions_history`, `sessions_send`, `sessions_spawn`, `session_status`
* Default deny: `browser`, `canvas`, `nodes`, `cron`, `discord`, `gateway`

### Enable sandboxing

If you plan to install packages in `setupCommand`, note:

* Default `docker.network` is `"none"` (no egress).
* `docker.network: "host"` is blocked.
* `docker.network: "container:<id>"` is blocked by default.
* Break-glass override: `agents.defaults.sandbox.docker.dangerouslyAllowContainerNamespaceJoin: true`.
* `readOnlyRoot: true` blocks package installs.
* `user` must be root for `apt-get` (omit `user` or set `user: "0:0"`).
  OpenClaw auto-recreates containers when `setupCommand` (or docker config) changes
  unless the container was **recently used** (within \~5 minutes). Hot containers
  log a warning with the exact `openclaw sandbox recreate ...` command.

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main", // off | non-main | all
        scope: "agent", // session | agent | shared (agent is default)
        workspaceAccess: "none", // none | ro | rw
        workspaceRoot: "~/.openclaw/sandboxes",
        docker: {
          image: "openclaw-sandbox:bookworm-slim",
          workdir: "/workspace",
          readOnlyRoot: true,
          tmpfs: ["/tmp", "/var/tmp", "/run"],
          network: "none",
          user: "1000:1000",
          capDrop: ["ALL"],
          env: { LANG: "C.UTF-8" },
          setupCommand: "apt-get update && apt-get install -y git curl jq",
          pidsLimit: 256,
          memory: "1g",
          memorySwap: "2g",
          cpus: 1,
          ulimits: {
            nofile: { soft: 1024, hard: 2048 },
            nproc: 256,
          },
          seccompProfile: "/path/to/seccomp.json",
          apparmorProfile: "openclaw-sandbox",
          dns: ["1.1.1.1", "8.8.8.8"],
          extraHosts: ["internal.service:10.0.0.5"],
        },
        prune: {
          idleHours: 24, // 0 disables idle pruning
          maxAgeDays: 7, // 0 disables max-age pruning
        },
      },
    },
  },
  tools: {
    sandbox: {
      tools: {
        allow: [\
          "exec",\
          "process",\
          "read",\
          "write",\
          "edit",\
          "sessions_list",\
          "sessions_history",\
          "sessions_send",\
          "sessions_spawn",\
          "session_status",\
        ],
        deny: ["browser", "canvas", "nodes", "cron", "discord", "gateway"],
      },
    },
  },
}
```

Hardening knobs live under `agents.defaults.sandbox.docker`:
`network`, `user`, `pidsLimit`, `memory`, `memorySwap`, `cpus`, `ulimits`,
`seccompProfile`, `apparmorProfile`, `dns`, `extraHosts`,
`dangerouslyAllowContainerNamespaceJoin` (break-glass only).

Multi-agent: override `agents.defaults.sandbox.{docker,browser,prune}.*` per agent via `agents.list[].sandbox.{docker,browser,prune}.*`
(ignored when `agents.defaults.sandbox.scope` / `agents.list[].sandbox.scope` is `"shared"`).

### Build the default sandbox image

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
scripts/sandbox-setup.sh
```

This builds `openclaw-sandbox:bookworm-slim` using `Dockerfile.sandbox`.

### Sandbox common image (optional)

If you want a sandbox image with common build tooling (Node, Go, Rust, etc.), build the common image:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
scripts/sandbox-common-setup.sh
```

This builds `openclaw-sandbox-common:bookworm-slim`. To use it:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agents: {
    defaults: {
      sandbox: { docker: { image: "openclaw-sandbox-common:bookworm-slim" } },
    },
  },
}
```

### Sandbox browser image

To run the browser tool inside the sandbox, build the browser image:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
scripts/sandbox-browser-setup.sh
```

This builds `openclaw-sandbox-browser:bookworm-slim` using
`Dockerfile.sandbox-browser`. The container runs Chromium with CDP enabled and
an optional noVNC observer (headful via Xvfb).

Notes:

* Headful (Xvfb) reduces bot blocking vs headless.
* Headless can still be used by setting `agents.defaults.sandbox.browser.headless=true`.
* No full desktop environment (GNOME) is needed; Xvfb provides the display.
* Browser containers default to a dedicated Docker network (`openclaw-sandbox-browser`) instead of global `bridge`.
* Optional `agents.defaults.sandbox.browser.cdpSourceRange` restricts container-edge CDP ingress by CIDR (for example `172.21.0.1/32`).
* noVNC observer access is password-protected by default; OpenClaw provides a short-lived observer token URL instead of sharing the raw password in the URL.

Use config:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agents: {
    defaults: {
      sandbox: {
        browser: { enabled: true },
      },
    },
  },
}
```

Custom browser image:

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agents: {
    defaults: {
      sandbox: { browser: { image: "my-openclaw-browser" } },
    },
  },
}
```

When enabled, the agent receives:

* a sandbox browser control URL (for the `browser` tool)
* a noVNC URL (if enabled and headless=false)

Remember: if you use an allowlist for tools, add `browser` (and remove it from
deny) or the tool remains blocked.
Prune rules (`agents.defaults.sandbox.prune`) apply to browser containers too.

### Custom sandbox image

Build your own image and point config to it:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
docker build -t my-openclaw-sbx -f Dockerfile.sandbox .
```

```json5  theme={"theme":{"light":"min-light","dark":"min-dark"}}
{
  agents: {
    defaults: {
      sandbox: { docker: { image: "my-openclaw-sbx" } },
    },
  },
}
```

### Tool policy (allow/deny)

* `deny` wins over `allow`.
* If `allow` is empty: all tools (except deny) are available.
* If `allow` is non-empty: only tools in `allow` are available (minus deny).

### Pruning strategy

Two knobs:

* `prune.idleHours`: remove containers not used in X hours (0 = disable)
* `prune.maxAgeDays`: remove containers older than X days (0 = disable)

Example:

* Keep busy sessions but cap lifetime:
  `idleHours: 24`, `maxAgeDays: 7`
* Never prune:
  `idleHours: 0`, `maxAgeDays: 0`

### Security notes

* Hard wall only applies to **tools** (exec/read/write/edit/apply\_patch).
* Host-only tools like browser/camera/canvas are blocked by default.
* Allowing `browser` in sandbox **breaks isolation** (browser runs on host).

## Troubleshooting

* Image missing: build with [`scripts/sandbox-setup.sh`](https://github.com/openclaw/openclaw/blob/main/scripts/sandbox-setup.sh) or set `agents.defaults.sandbox.docker.image`.
* Container not running: it will auto-create per session on demand.
* Permission errors in sandbox: set `docker.user` to a UID:GID that matches your
  mounted workspace ownership (or chown the workspace folder).
* Custom tools not found: OpenClaw runs commands with `sh -lc` (login shell), which
  sources `/etc/profile` and may reset PATH. Set `docker.env.PATH` to prepend your
  custom tool paths (e.g., `/custom/bin:/usr/local/share/npm-global/bin`), or add
  a script under `/etc/profile.d/` in your Dockerfile.
````

---

---

<!-- Page: Node.js -->

## node.md

**Source:** https://docs.openclaw.ai/install/node.md

````
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Node.js

# Node.js

OpenClaw requires **Node 22 or newer**. The [installer script](/install#install-methods) will detect and install Node automatically — this page is for when you want to set up Node yourself and make sure everything is wired up correctly (versions, PATH, global installs).

## Check your version

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
node -v
```

If this prints `v22.x.x` or higher, you're good. If Node isn't installed or the version is too old, pick an install method below.

## Install Node

<Tabs>
  <Tab title="macOS">
    **Homebrew** (recommended):

    ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    brew install node
    ```

    Or download the macOS installer from [nodejs.org](https://nodejs.org/).
  </Tab>

  <Tab title="Linux">
    **Ubuntu / Debian:**

    ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
    sudo apt-get install -y nodejs
    ```

    **Fedora / RHEL:**

    ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    sudo dnf install nodejs
    ```

    Or use a version manager (see below).
  </Tab>

  <Tab title="Windows">
    **winget** (recommended):

    ```powershell  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    winget install OpenJS.NodeJS.LTS
    ```

    **Chocolatey:**

    ```powershell  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    choco install nodejs-lts
    ```

    Or download the Windows installer from [nodejs.org](https://nodejs.org/).
  </Tab>
</Tabs>

<Accordion title="Using a version manager (nvm, fnm, mise, asdf)">
  Version managers let you switch between Node versions easily. Popular options:

  * [**fnm**](https://github.com/Schniz/fnm) — fast, cross-platform
  * [**nvm**](https://github.com/nvm-sh/nvm) — widely used on macOS/Linux
  * [**mise**](https://mise.jdx.dev/) — polyglot (Node, Python, Ruby, etc.)

  Example with fnm:

  ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
  fnm install 22
  fnm use 22
  ```

  <Warning>
    Make sure your version manager is initialized in your shell startup file (`~/.zshrc` or `~/.bashrc`). If it isn't, `openclaw` may not be found in new terminal sessions because the PATH won't include Node's bin directory.
  </Warning>
</Accordion>

## Troubleshooting

### `openclaw: command not found`

This almost always means npm's global bin directory isn't on your PATH.

<Steps>
  <Step title="Find your global npm prefix">
    ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    npm prefix -g
    ```
  </Step>

  <Step title="Check if it's on your PATH">
    ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
    echo "$PATH"
    ```

    Look for `<npm-prefix>/bin` (macOS/Linux) or `<npm-prefix>` (Windows) in the output.
  </Step>

  <Step title="Add it to your shell startup file">
    <Tabs>
      <Tab title="macOS / Linux">
        Add to `~/.zshrc` or `~/.bashrc`:

        ```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
        export PATH="$(npm prefix -g)/bin:$PATH"
        ```

        Then open a new terminal (or run `rehash` in zsh / `hash -r` in bash).
      </Tab>

      <Tab title="Windows">
        Add the output of `npm prefix -g` to your system PATH via Settings → System → Environment Variables.
      </Tab>
    </Tabs>
  </Step>
</Steps>

### Permission errors on `npm install -g` (Linux)

If you see `EACCES` errors, switch npm's global prefix to a user-writable directory:

```bash  theme={"theme":{"light":"min-light","dark":"min-dark"}}
mkdir -p "$HOME/.npm-global"
npm config set prefix "$HOME/.npm-global"
export PATH="$HOME/.npm-global/bin:$PATH"
```

Add the `export PATH=...` line to your `~/.bashrc` or `~/.zshrc` to make it permanent.
````

---
