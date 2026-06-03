# SAE Server Infrastructure

> **TL;DR** — Designed and co-built a self-hosted AI sales stack on a Hetzner Finland VPS. Runs Twenty CRM, n8n automation, and Caddy reverse proxy in Docker Compose. The server exposes a native MCP endpoint, making it directly accessible to Claude. GDPR-compliant, EU-jurisdicted, ~10€/month.

---

## Problem

Cloud CRM tools are expensive, store data outside EU jurisdiction, and don't natively integrate with Claude. Building an AI-native sales pipeline requires a stack you fully control.

## Solution

Self-hosted server stack on Hetzner Finland (Helsinki datacenter, EU jurisdiction). Twenty CRM as the structured data layer, n8n as the automation backbone, Caddy handling HTTPS automatically. The full stack is accessible to Claude via MCP — Claude can read and write CRM data directly without any middleware.

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
Open-source CRM with a native MCP server at `/mcp`. Custom fields built for the sales pipeline. All demo data deleted, production data imported via CSV.

**n8n**
Workflow automation engine. Handles webhook ingestion, transcript processing, and CRM synchronization. Runs in an internal Docker container — accessible only via Tailscale, never exposed to the internet.

**Caddy**
Reverse proxy with automatic Let's Encrypt certificate management. Single Caddyfile config. Zero manual certificate renewal.

**Tailscale**
Zero-config VPN for admin access. SSH and n8n dashboard accessible only through Tailscale — no public SSH port exposure.

---

## Security Measures

- Root SSH login disabled
- Password authentication disabled (keys only)
- Hetzner firewall: only ports 80, 443, 22 open
- PostgreSQL, Redis, n8n ports not exposed publicly
- Unattended security upgrades enabled
- fail2ban installed
- Anthropic DPA in place for Claude API usage

---

## MCP Integration

Twenty CRM exposes a native Streamable HTTP MCP endpoint. Claude can query, create, and update CRM records in natural language — no API wrappers needed.

```json
{
  "mcpServers": {
    "twenty": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://crm.[domain]/mcp",
        "--header",
        "Authorization: Bearer [token]"
      ]
    }
  }
}
```

---

## Stack

- **Hetzner Cloud** — VPS provider (Helsinki, Finland)
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
- CRM directly queryable by Claude via MCP
- All data in EU jurisdiction
- 10€/month total infrastructure cost
