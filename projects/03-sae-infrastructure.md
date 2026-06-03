# SAE — AI-Native Sales Infrastructure

> **TL;DR** — Built the infrastructure for a fully AI-native sales process, designed AI-first from the ground up. The goal: automate the entire sales pipeline end-to-end, from lead identification to proposal delivery, with humans reviewing decisions rather than executing tasks. Every technical choice — self-hosted CRM, n8n, MCP integration — exists to serve that vision.

---

## The Idea: AI-First Sales

Most companies bolt AI onto existing sales processes. We designed the process around AI from the start.

The question wasn't "how do we use AI to help with sales?" It was "if AI could handle the entire pipeline autonomously, what infrastructure would it need?" Then we built that infrastructure.

The sales pipeline runs through five stages: Pool → Lead → Prospect → Deal → Win/Loss. At each stage, AI agents handle the heavy lifting — research, outreach, qualification, proposal generation — and humans make the key decisions. The server stack is the foundation that makes this possible.

---

## Problem

Running AI agents across a sales pipeline requires:
- A CRM the AI can read and write natively (not via scraping or workarounds)
- An automation engine that connects signals to actions
- Data that stays in EU jurisdiction (GDPR)
- Infrastructure cheap enough to run permanently without justifying ROI upfront

No off-the-shelf solution satisfied all four. Cloud CRMs don't expose MCP endpoints. GDPR-compliant hosting rules out most US providers. So we built it.

---

## Solution

Self-hosted server stack on Hetzner Finland. Twenty CRM as the AI-readable data layer, n8n as the automation backbone that wires everything together, Caddy for zero-config HTTPS. The entire stack exposes native MCP endpoints — Claude can read pipeline state, create records, and trigger workflows without any API wrappers.

---

## Architecture

```
Internet
    │
    ▼
Caddy (reverse proxy + auto HTTPS)
    │
    ▼
┌──────────────────────────────┐
│     Docker Compose Stack     │
│                              │
│  Twenty CRM  ←→  PostgreSQL  │
│      │            Redis      │
│      │                       │
│     n8n  (internal only)     │
└──────────────────────────────┘
    │
    ▼
Tailscale VPN (admin access)
```

---

## How the AI Pipeline Uses This Stack

```
MailerLite / events / LinkedIn
    │  (webhook)
    ▼
n8n — lead ingestion & routing
    │
    ▼
Twenty CRM — structured pipeline data
    │  (MCP)
    ▼
Claude — AI agents run against CRM context
    │  anna-liisa (research) → matti (outreach)
    │  → prospektilinko (qualification)
    │  → tarja (proposal generation)
    ▼
Human review & approval
```

Every stage produces structured output that feeds the next. Claude doesn't operate on scattered documents — it operates on clean CRM records with defined fields and relationships.

---

## Server Spec

| Component | Choice | Reason |
|-----------|--------|--------|
| Provider | Hetzner Cloud Finland | EU jurisdiction, GDPR, low cost |
| Instance | CX32 (4 vCPU, 8 GB RAM, 80 GB NVMe) | Headroom for future services |
| OS | Ubuntu 24.04 LTS | Long-term support |
| Cost | ~10€/month | 10x cheaper than equivalent cloud CRM |

---

## Stack Components

**Twenty CRM**
Open-source CRM with a native MCP server at `/mcp`. Custom fields built for the AI pipeline: lead source, ICP role, segment, fit score, recommended product, outreach angle, next action. Claude writes directly to these fields after running analysis — no human data entry needed.

**n8n**
The automation glue. Connects MailerLite webhooks to CRM records, routes Fireflies transcripts to Claude for processing, triggers agent chains when pipeline stages change. Runs internally — accessible only via Tailscale.

**Caddy**
Reverse proxy with automatic Let's Encrypt. Single config file, zero manual certificate management.

**Tailscale**
Zero-config VPN. n8n dashboard and SSH accessible only through the Tailscale tunnel — attack surface stays minimal.

---

## Security

- Root SSH login disabled, password auth disabled
- Hetzner firewall: only 80, 443, 22 open publicly
- PostgreSQL, Redis, n8n not exposed to internet
- Unattended security upgrades + fail2ban
- Anthropic DPA signed for Claude API usage
- All data in EU jurisdiction (Helsinki DC)

---

## Stack

- **Hetzner Cloud** — VPS (Helsinki, Finland)
- **Ubuntu 24.04 LTS** — OS
- **Docker + Docker Compose** — container orchestration
- **Twenty CRM** — open-source CRM with native MCP
- **n8n** — workflow automation
- **Caddy** — reverse proxy + automatic HTTPS
- **PostgreSQL 16** — primary database
- **Redis 7** — caching layer
- **Tailscale** — admin VPN

---

## Results

- Full stack running in production
- Claude reads and writes CRM data natively via MCP
- All data EU-jurisdicted and GDPR-compliant
- 10€/month infrastructure cost
- Foundation for a fully automated AI sales pipeline
