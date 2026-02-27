# Deployment


---

<!-- Page: exe.dev -->

## exe.dev

**Source:** https://docs.openclaw.ai/install/exe-dev

[Skip to main content](https://docs.openclaw.ai/install/exe-dev#content-area)

[OpenClaw home page![light logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)![dark logo](https://mintcdn.com/clawdhub/A8OxQpxR3DcyCCHY/assets/pixel-lobster.svg?fit=max&auto=format&n=A8OxQpxR3DcyCCHY&q=85&s=7d28d01258a677dc2c3e3ad383948e91)](https://docs.openclaw.ai/)

![US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

Search...

Navigation

Hosting and deployment

exe.dev

[Get started](https://docs.openclaw.ai/) [Install](https://docs.openclaw.ai/install) [Channels](https://docs.openclaw.ai/channels) [Agents](https://docs.openclaw.ai/concepts/architecture) [Tools](https://docs.openclaw.ai/tools) [Models](https://docs.openclaw.ai/providers) [Platforms](https://docs.openclaw.ai/platforms) [Gateway & Ops](https://docs.openclaw.ai/gateway) [Reference](https://docs.openclaw.ai/cli) [Help](https://docs.openclaw.ai/help)

On this page

- [exe.dev](https://docs.openclaw.ai/install/exe-dev#exe-dev)
- [Beginner quick path](https://docs.openclaw.ai/install/exe-dev#beginner-quick-path)
- [What you need](https://docs.openclaw.ai/install/exe-dev#what-you-need)
- [Automated Install with Shelley](https://docs.openclaw.ai/install/exe-dev#automated-install-with-shelley)
- [Manual installation](https://docs.openclaw.ai/install/exe-dev#manual-installation)
- [1) Create the VM](https://docs.openclaw.ai/install/exe-dev#1-create-the-vm)
- [2) Install prerequisites (on the VM)](https://docs.openclaw.ai/install/exe-dev#2-install-prerequisites-on-the-vm)
- [3) Install OpenClaw](https://docs.openclaw.ai/install/exe-dev#3-install-openclaw)
- [4) Setup nginx to proxy OpenClaw to port 8000](https://docs.openclaw.ai/install/exe-dev#4-setup-nginx-to-proxy-openclaw-to-port-8000)
- [5) Access OpenClaw and grant privileges](https://docs.openclaw.ai/install/exe-dev#5-access-openclaw-and-grant-privileges)
- [Remote Access](https://docs.openclaw.ai/install/exe-dev#remote-access)
- [Updating](https://docs.openclaw.ai/install/exe-dev#updating)

# [​](https://docs.openclaw.ai/install/exe-dev\#exe-dev)  exe.dev

Goal: OpenClaw Gateway running on an exe.dev VM, reachable from your laptop via: `https://<vm-name>.exe.xyz`This page assumes exe.dev’s default **exeuntu** image. If you picked a different distro, map packages accordingly.

## [​](https://docs.openclaw.ai/install/exe-dev\#beginner-quick-path)  Beginner quick path

1. [https://exe.new/openclaw](https://exe.new/openclaw)
2. Fill in your auth key/token as needed
3. Click on “Agent” next to your VM, and wait…
4. ???
5. Profit

## [​](https://docs.openclaw.ai/install/exe-dev\#what-you-need)  What you need

- exe.dev account
- `ssh exe.dev` access to [exe.dev](https://exe.dev/) virtual machines (optional)

## [​](https://docs.openclaw.ai/install/exe-dev\#automated-install-with-shelley)  Automated Install with Shelley

Shelley, [exe.dev](https://exe.dev/)’s agent, can install OpenClaw instantly with our
prompt. The prompt used is as below:

Copy

```
Set up OpenClaw (https://docs.openclaw.ai/install) on this VM. Use the non-interactive and accept-risk flags for openclaw onboarding. Add the supplied auth or token as needed. Configure nginx to forward from the default port 18789 to the root location on the default enabled site config, making sure to enable Websocket support. Pairing is done by "openclaw devices list" and "openclaw devices approve <request id>". Make sure the dashboard shows that OpenClaw's health is OK. exe.dev handles forwarding from port 8000 to port 80/443 and HTTPS for us, so the final "reachable" should be <vm-name>.exe.xyz, without port specification.
```

## [​](https://docs.openclaw.ai/install/exe-dev\#manual-installation)  Manual installation

## [​](https://docs.openclaw.ai/install/exe-dev\#1-create-the-vm)  1) Create the VM

From your device:

Copy

```
ssh exe.dev new
```

Then connect:

Copy

```
ssh <vm-name>.exe.xyz
```

Tip: keep this VM **stateful**. OpenClaw stores state under `~/.openclaw/` and `~/.openclaw/workspace/`.

## [​](https://docs.openclaw.ai/install/exe-dev\#2-install-prerequisites-on-the-vm)  2) Install prerequisites (on the VM)

Copy

```
sudo apt-get update
sudo apt-get install -y git curl jq ca-certificates openssl
```

## [​](https://docs.openclaw.ai/install/exe-dev\#3-install-openclaw)  3) Install OpenClaw

Run the OpenClaw install script:

Copy

```
curl -fsSL https://openclaw.ai/install.sh | bash
```

## [​](https://docs.openclaw.ai/install/exe-dev\#4-setup-nginx-to-proxy-openclaw-to-port-8000)  4) Setup nginx to proxy OpenClaw to port 8000

Edit `/etc/nginx/sites-enabled/default` with

Copy

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    listen 8000;
    listen [::]:8000;

    server_name _;

    location / {
        proxy_pass http://127.0.0.1:18789;
        proxy_http_version 1.1;

        # WebSocket support
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Standard proxy headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeout settings for long-lived connections
        proxy_read_timeout 86400s;
        proxy_send_timeout 86400s;
    }
}
```

## [​](https://docs.openclaw.ai/install/exe-dev\#5-access-openclaw-and-grant-privileges)  5) Access OpenClaw and grant privileges

Access `https://<vm-name>.exe.xyz/` (see the Control UI output from onboarding). If it prompts for auth, paste the
token from `gateway.auth.token` on the VM (retrieve with `openclaw config get gateway.auth.token`, or generate one
with `openclaw doctor --generate-gateway-token`). Approve devices with `openclaw devices list` and
`openclaw devices approve <requestId>`. When in doubt, use Shelley from your browser!

## [​](https://docs.openclaw.ai/install/exe-dev\#remote-access)  Remote Access

Remote access is handled by [exe.dev](https://exe.dev/)’s authentication. By
default, HTTP traffic from port 8000 is forwarded to `https://<vm-name>.exe.xyz`
with email auth.

## [​](https://docs.openclaw.ai/install/exe-dev\#updating)  Updating

Copy

```
npm i -g openclaw@latest
openclaw doctor
openclaw gateway restart
openclaw health
```

Guide: [Updating](https://docs.openclaw.ai/install/updating)

[macOS VMs](https://docs.openclaw.ai/install/macos-vm) [Deploy on Railway](https://docs.openclaw.ai/install/railway)

Ctrl+I

---

---

<!-- Page: Northflank -->

## northflank.md

**Source:** https://docs.openclaw.ai/install/northflank.md

```
> ## Documentation Index
> Fetch the complete documentation index at: https://docs.openclaw.ai/llms.txt
> Use this file to discover all available pages before exploring further.

# Deploy on Northflank

Deploy OpenClaw on Northflank with a one-click template and finish setup in your browser.
This is the easiest “no terminal on the server” path: Northflank runs the Gateway for you,
and you configure everything via the `/setup` web wizard.

## How to get started

1. Click [Deploy OpenClaw](https://northflank.com/stacks/deploy-openclaw) to open the template.
2. Create an [account on Northflank](https://app.northflank.com/signup) if you don’t already have one.
3. Click **Deploy OpenClaw now**.
4. Set the required environment variable: `SETUP_PASSWORD`.
5. Click **Deploy stack** to build and run the OpenClaw template.
6. Wait for the deployment to complete, then click **View resources**.
7. Open the OpenClaw service.
8. Open the public OpenClaw URL and complete setup at `/setup`.
9. Open the Control UI at `/openclaw`.

## What you get

* Hosted OpenClaw Gateway + Control UI
* Web setup wizard at `/setup` (no terminal commands)
* Persistent storage via Northflank Volume (`/data`) so config/credentials/workspace survive redeploys

## Setup flow

1. Visit `https://<your-northflank-domain>/setup` and enter your `SETUP_PASSWORD`.
2. Choose a model/auth provider and paste your key.
3. (Optional) Add Telegram/Discord/Slack tokens.
4. Click **Run setup**.
5. Open the Control UI at `https://<your-northflank-domain>/openclaw`

If Telegram DMs are set to pairing, the setup wizard can approve the pairing code.

## Getting chat tokens

### Telegram bot token

1. Message `@BotFather` in Telegram
2. Run `/newbot`
3. Copy the token (looks like `123456789:AA...`)
4. Paste it into `/setup`

### Discord bot token

1. Go to [https://discord.com/developers/applications](https://discord.com/developers/applications)
2. **New Application** → choose a name
3. **Bot** → **Add Bot**
4. **Enable MESSAGE CONTENT INTENT** under Bot → Privileged Gateway Intents (required or the bot will crash on startup)
5. Copy the **Bot Token** and paste into `/setup`
6. Invite the bot to your server (OAuth2 URL Generator; scopes: `bot`, `applications.commands`)
```

---
