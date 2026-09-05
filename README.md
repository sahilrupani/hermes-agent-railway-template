# Hermes Agent — Self-Improving AI Agent with Persistent Memory on Railway (One-Click Deploy)

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/ai-agent-persistent-memory?referralCode=zxcgoT)

Self-host **Hermes Agent** on Railway — a self-improving AI agent from Nous Research with persistent memory, no per-call platform fees, and no per-seat subscription.

## Contents

- [What This Railway Template Deploys](#what-this-railway-template-deploys)
- [Why Self-Host Hermes Agent Instead of a Paid Alternative](#why-self-host-hermes-agent-instead-of-a-paid-alternative)
- [Deploy to Railway](#deploy-to-railway)
- [Self-host with Docker Compose](#self-host-with-docker-compose)
- [Configuration](#configuration)
- [What You Can Build with This Hermes Agent Template](#what-you-can-build-with-this-hermes-agent-template)
- [Troubleshooting Hermes Agent](#troubleshooting-hermes-agent)
- [FAQ](#faq)

Hermes Agent is an open-source, self-improving AI agent from Nous Research. It has a built-in learning loop that creates skills from experience, refines them during use, and builds a deepening model of the user across sessions using Honcho user modeling. It supports 200+ LLM models and connects to Telegram, Discord, Slack, WhatsApp and WeChat, with a cron scheduler for unattended automation.

## What This Railway Template Deploys

| Service | Image | Purpose |
|---|---|---|
| Hermes Agent | `nousresearch/hermes-agent:v2026.8.3` | Single container bundling the agent gateway and WebUI dashboard under s6 supervision, with Python 3, Node.js and Playwright/Chromium for browser tools |

**Persistent volume:** `/opt/data` — configuration, conversation history, learned skills and Honcho memory databases.

The dashboard serves on the configured `PORT` and requires a login. LLM keys and channel tokens (Telegram, Discord, Slack, WhatsApp, WeChat) are configured in the WebUI after deployment rather than as deploy-time variables.

## Why Self-Host Hermes Agent Instead of a Paid Alternative

One always-on container on Railway, plus whatever your chosen LLM provider charges through your own key. There are no per-call platform fees and no per-seat subscription. Railway gives new GitHub signups a $5 trial credit.

| | Hermes Agent (self-hosted) | ChatGPT Plus | Open WebUI | Managed agent SaaS |
|---|---|---|---|---|
| Pricing model | Infrastructure cost + your own LLM key, no per-seat fee | Flat monthly fee per seat | Self-hosted, infrastructure only | Per-seat or per-task with a markup on model calls |
| Control | You control conversation history, learned skills and API keys | OpenAI-hosted, no persistent learning between sessions | Fully self-hosted | Vendor-hosted memory and credentials |
| Where it's stronger | — | No setup, official support | Simpler to run as a straightforward chat interface | Turnkey setup and support |

Honest tradeoff: if you just want a chat UI with zero configuration, ChatGPT Plus or Open WebUI will get you there faster. Hermes Agent is for when you want a persistent, self-improving agent with memory and skills that live on infrastructure you own.

## Deploy to Railway

1. Click **Deploy on Railway** above.
2. Set `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` — required, the dashboard fails closed without both.
3. Confirm the persistent volume is mounted at `/opt/data` so memory and skills survive redeploys.
4. Deploy and open the service URL; `PORT` is injected automatically by Railway.
5. Log in with your basic auth credentials, then configure LLM keys and channel tokens (Telegram, Discord, Slack, WhatsApp, WeChat) inside the WebUI.
6. If the gateway stays down on a fresh volume, set the profile's desired state to running on first boot.

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/ai-agent-persistent-memory?referralCode=zxcgoT)

## Self-host with Docker Compose

```bash
git clone https://github.com/sahilrupani/hermes-agent-railway-template.git
cd hermes-agent-railway-template
cp .env.example .env
docker compose up -d
```

Set `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` in `.env` before starting — the container comes down with the dashboard if either is missing. Once running, open the dashboard at the configured host/port and log in.

## Configuration

| Variable | Required | Description |
|---|---|---|
| `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` | Yes | Dashboard username. Since the June 2026 hardening the auth gate engages on every non-loopback bind — without it the dashboard fails closed and takes the container down |
| `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` | Yes | Dashboard password, paired with the username above |
| `PORT` | Yes | Port the dashboard binds to. Railway injects this automatically |
| `HERMES_HOME` | No | Agent home directory. Point it inside the volume so state persists |
| `HERMES_DASHBOARD` | No | Gates the supervised dashboard service shipped in the image |
| `HERMES_DASHBOARD_HOST` | No | Host the dashboard binds to |

## What You Can Build with This Hermes Agent Template

- A persistent assistant on Telegram, Discord, Slack, WhatsApp or WeChat that carries context between sessions
- Automating recurring research, monitoring and reporting with the built-in cron scheduler
- Building custom skills the agent creates from experience and refines during use
- Routing to 200+ models through OpenRouter, Nous Portal, NVIDIA NIM, OpenAI or a custom endpoint
- Keeping conversation history, learned skills and API keys on infrastructure you control

## Troubleshooting Hermes Agent

**Container starts then immediately exits or crash-loops** — The dashboard auth gate failed closed. `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `_PASSWORD` must both be set on any non-loopback bind; the container comes down with the dashboard if they are missing.

**`--insecure` or `HERMES_DASHBOARD_INSECURE` has no effect** — Since the June 2026 hardening both flags are accepted but ignored. Basic auth credentials are the only supported way to open the dashboard.

**Memories, skills and configuration reset after a redeploy** — No persistent volume at `/opt/data`. Honcho memory databases, learned skills and config all live there.

**Gateway never comes up on a fresh volume** — A never-started profile is only registered, not started, so the gateway stays down until a profile's desired state is set to running on first boot.

## FAQ

### What does deploying Hermes Agent on Railway cost?

One always-on container on Railway, plus whatever your chosen LLM provider charges through your own key. There are no per-call platform fees and no per-seat subscription. Railway gives new GitHub signups a $5 trial credit.

### Where do I configure LLM keys and channel tokens?

LLM keys and channel tokens (Telegram, Discord, Slack, WhatsApp, WeChat) are configured in the WebUI after deployment rather than as deploy-time variables.

### Why does the container keep crash-looping?

The dashboard auth gate fails closed on any non-loopback bind if `HERMES_DASHBOARD_BASIC_AUTH_USERNAME` and `HERMES_DASHBOARD_BASIC_AUTH_PASSWORD` are not both set.

### Can I open the dashboard without setting up basic auth?

No. Since the June 2026 hardening, `--insecure` and `HERMES_DASHBOARD_INSECURE` are accepted but ignored — basic auth credentials are the only supported way to open the dashboard.

### Will my memories and skills survive a redeploy?

Only if a persistent volume is mounted at `/opt/data`, which stores conversation history, learned skills and Honcho memory databases.

### Which models can Hermes Agent use?

It supports 200+ LLM models, routed through OpenRouter, Nous Portal, NVIDIA NIM, OpenAI or a custom endpoint.

---

*This is a community-maintained Railway template for [Hermes Agent](https://github.com/Shinyduo/hermes-agent). It is not affiliated with Nous Research, the Hermes Agent maintainers, or Railway. See the upstream repository for licensing terms.*