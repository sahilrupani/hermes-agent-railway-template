# Deploy and Host Hermes Agent on Railway — Self-Improving AI Agent with Persistent Memory

Hermes Agent is an open-source, self-improving AI agent from Nous Research. It has a built-in learning loop that creates skills from experience, refines them during use, and builds a deepening model of the user across sessions using Honcho user modeling. It supports 200+ LLM models and connects to Telegram, Discord, Slack, WhatsApp and WeChat, with a cron scheduler for unattended automation.

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/ai-agent-persistent-memory?referralCode=zxcgoT&utm_medium=integration&utm_source=template&utm_campaign=generic)

## 🚀 Quick Start Deployment Guide

### Step 1: Deploy on Railway
1. Click **Deploy on Railway** above
2. Wait for the container to build and start

### Step 2: Mount a volume
1. Add a Railway Volume mounted at `/opt/data`
2. Configuration, conversation history, learned skills and Honcho memory databases all live there

### Step 3: Set dashboard credentials — required
1. Set `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` to a username of your choice
2. Generate a password with `openssl rand -hex 32` and set `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD`
3. Both are mandatory: the auth gate engages on every non-loopback bind, and without them the dashboard fails closed and takes the container down with it

### Step 4: Log in to the dashboard
1. Open the URL Railway generated for the service
2. Enter the basic-auth username and password you just set

### Step 5: Configure your model provider
1. LLM provider keys and channel tokens are set in the WebUI after login, not as deploy-time variables
2. Add a provider key — see the guides below — and pick a model
3. Hermes routes to 200+ models via OpenRouter, Nous Portal, NVIDIA NIM, OpenAI, or a custom endpoint

### Step 6: Connect a channel and start
1. Add a Telegram, Discord, Slack, WhatsApp or WeChat token in the WebUI if you want the agent reachable from chat apps
2. Send your first message; the learning loop begins building skills and user context from there

## About Hosting Hermes Agent

This Railway template runs the official `nousresearch/hermes-agent:v2026.8.3` Docker image as a single container. All dependencies are bundled — Python 3, Node.js, and Playwright with Chromium for browser tools. The image supervises the gateway and the WebUI dashboard under s6; the dashboard serves on the configured `PORT` and requires a login.

A persistent volume at `/opt/data` stores configuration, conversation history, learned skills and Honcho memory databases. Since the June 2026 hardening, the dashboard auth gate engages on every non-loopback bind: `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` must be set, and `--insecure` / `HERMES_DASHBOARD_INSECURE` are accepted but ignored. LLM keys and channel tokens are configured in the WebUI after deployment rather than as deploy-time variables. Hosting it on Railway means the container, volume and network exposure are all under your own account, with no vendor sitting between you and your model provider.

## Common Use Cases

- **Persistent chat assistant** — a Telegram, Discord, Slack, WhatsApp or WeChat bot that carries context between sessions instead of resetting each time
- **Unattended automation** — recurring research, monitoring and reporting jobs run by the built-in cron scheduler
- **Custom skill building** — the agent creates skills from experience and refines them the more you use it
- **Multi-model routing** — route to 200+ models through OpenRouter, Nous Portal, NVIDIA NIM, OpenAI or a custom OpenAI-compatible endpoint
- **Data ownership** — keep conversation history, learned skills and API keys on infrastructure you control instead of a vendor's servers

## Dependencies for Hermes Agent Hosting

### Deployment Dependencies

- [Hermes Agent (template source)](https://github.com/Shinyduo/hermes-agent)
- [Official Docker image](https://hub.docker.com/r/nousresearch/hermes-agent)
- [OpenRouter API keys — 200+ models through one key](https://openrouter.ai/keys)
- [OpenAI API keys (GPT)](https://platform.openai.com/)
- [Telegram BotFather](https://t.me/botfather)
- [Discord Developer Portal](https://discord.com/developers/applications)

## 🔑 How to Get API Keys for Different AI Providers

### How to Get an OpenRouter API Key?
1. Go to [https://openrouter.ai/keys](https://openrouter.ai/keys)
2. Sign in, then click **Create Key**
3. Name the key and copy it
4. One OpenRouter key gives you routing across many providers

### How to Get an OpenAI API Key?
1. Go to the OpenAI Platform at [https://platform.openai.com/](https://platform.openai.com/)
2. Create an account or sign in, then open **API keys** from the profile menu
3. Click **Create new secret key** and name it
4. Copy the key — it is shown only once

### How to Get an Anthropic Claude API Key? (recommended)
1. Visit the Anthropic Console at [https://platform.claude.com/](https://platform.claude.com/)
2. Sign up or log in, then open **API Keys** in the left sidebar
3. Click **Create Key** and give it a name
4. Copy the key — it is shown only once

## 💬 How to Add Messaging Channels to Hermes Agent

### How to Add a Telegram Bot?
**Step 1: Create your bot**
1. Open Telegram and search for `@BotFather`
2. Send `/newbot`
3. Choose a display name, then a username ending in `bot`
4. BotFather returns a token in the form `123456789:ABCdef...` — copy it

**Step 2: Add it to the agent**
1. Paste the token into the Telegram bot token field in the setup UI
2. Save / re-run setup and wait for it to finish

**Step 3: Start chatting**
1. Search your bot's username in Telegram
2. Press **Start** or send `/start`
3. If a pairing code is required, send any message and enter the code the bot replies with

### How to Add a Discord Bot?
**Step 1: Create the application**
1. Open the Discord Developer Portal at [https://discord.com/developers/applications](https://discord.com/developers/applications)
2. Click **New Application** and name it
3. Open the **Bot** tab and add a bot

**Step 2: Configure the bot**
1. Under **Privileged Gateway Intents**, enable **MESSAGE CONTENT INTENT** — required
2. Click **Reset Token**, copy the token, and store it securely

**Step 3: Invite it to your server**
1. Go to **OAuth2 → URL Generator**
2. Select the `bot` and `applications.commands` scopes
3. Select permissions: Read Messages/View Channels, Send Messages, Read Message History, Embed Links
4. Open the generated URL, pick your server, and authorize

**Step 4: Add it to the agent**
1. Paste the token into the Discord bot token field in the setup UI
2. Save / re-run setup, then mention the bot in a channel to chat

### How to Add a Slack Bot?
**Step 1: Create the app**
1. Go to [https://api.slack.com/apps](https://api.slack.com/apps) and click **Create New App**
2. Add the bot token scopes your workspace needs, then install the app to the workspace
3. Copy the Bot User OAuth token (starts with `xoxb-`)

**Step 2: Add it to the agent**
1. Paste the token into the Slack bot token field in the setup UI
2. Save / re-run setup, then invite the bot to a channel

## ⚙️ Configuration

| Variable | Required | Description |
|---|---|---|
| `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` | Yes | Dashboard username. Since the June 2026 hardening the auth gate engages on every non-loopback bind — without it the dashboard fails closed and takes the container down |
| `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` | Yes | Dashboard password, paired with the username above |
| `PORT` | Yes | Port the dashboard binds to. Railway injects this automatically |
| `HERMES_HOME` | No | Agent home directory. Point it inside the volume so state persists |
| `HERMES_DASHBOARD` | No | Gates the supervised dashboard service shipped in the image |
| `HERMES_DASHBOARD_HOST` | No | Host the dashboard binds to |

## 🐳 Self-Host with Docker Compose

```bash
git clone https://github.com/sahilrupani/hermes-agent-railway-template
cd hermes-agent-railway-template
cp .env.example .env
```

Generate a dashboard password and set both auth variables in `.env`:

```bash
openssl rand -hex 32
```

Set `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` in `.env`, then start the stack:

```bash
docker compose up -d
```

Open the dashboard at the URL/port configured in your `.env` and log in with the basic-auth credentials you set.

## ❓ Frequently Asked Questions (FAQ)

### How much does it cost to run Hermes Agent on Railway?
One always-on container on Railway, plus whatever your model provider charges against your own key. There are no per-call platform fees and no per-seat subscription. Railway gives new GitHub signups a $5 trial credit.

### Is my data private and secure?
Yes. Conversations, learned skills and Honcho memory stay in your `/opt/data` volume on Railway, and the dashboard is behind basic auth. Prompts still reach whichever model provider you configure.

### What makes Hermes "self-improving"?
A built-in learning loop creates skills from experience, refines them during use, and builds a deepening model of you across sessions through Honcho user modeling — so context carries between conversations rather than resetting.

### Which models can I use?
200+ across OpenRouter, Nous Portal (Nous-Hermes models), NVIDIA NIM, OpenAI, and custom OpenAI-compatible endpoints. Keys are configured in the WebUI after deployment.

### Can I migrate off Railway later?
Yes. It runs the official `nousresearch/hermes-agent` image, which works on any Docker host — this repo ships a compose file. Carry the `/opt/data` volume across and memories and skills come with it.

### Why does the container start then immediately exit or crash-loop?
The dashboard auth gate failed closed. `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `_PASSWORD` must both be set on any non-loopback bind — the container comes down with the dashboard if they are missing.

### Why does `--insecure` or `HERMES_DASHBOARD_INSECURE` have no effect?
Since the June 2026 hardening both flags are accepted but ignored. Basic auth credentials are the only supported way to open the dashboard.

### Why do memories, skills and configuration reset after a redeploy?
There's no persistent volume at `/opt/data`. Honcho memory databases, learned skills and config all live there — mount a Railway Volume at that path to keep them across deploys.

### Why does the gateway never come up on a fresh volume?
A never-started profile is only registered, not started, so the gateway stays down until a profile's desired state is set to running on first boot.

## 🛠️ Support & Issues

If you hit a problem, open an issue at [https://github.com/sahilrupani/hermes-agent-railway-template/issues](https://github.com/sahilrupani/hermes-agent-railway-template/issues) with a description of the problem, the steps to reproduce it, and any relevant container logs.

---

*This template packages [Hermes Agent](https://github.com/Shinyduo/hermes-agent) for one-click deployment on Railway. It is community-maintained and not affiliated with Nous Research, Shinyduo, or Railway.*