# MCP Integration Architecture

> **TL;DR** — Connected three independent systems (Obsidian, Twenty CRM, GitHub) to Claude Desktop via MCP, creating a unified AI workspace where Claude can read and write across knowledge base, CRM, and code — in a single session, without switching tools.

---

## Problem

AI assistants are powerful in isolation but fragmented across tools. Switching context between a knowledge base, a CRM, and a code repository breaks flow and loses context. The ideal: one AI interface with read/write access to everything.

## Solution

Built a multi-server MCP configuration for Claude Desktop that simultaneously connects to three systems via different transport methods. Each system exposes its own MCP server; Claude Desktop orchestrates them as a unified tool layer.

---

## Architecture

```
Claude Desktop
    │
    ├── Obsidian MCP (HTTP, local)
    │       └── vault_read / vault_write / vault_patch
    │           search_simple / search_query / tag_list
    │
    ├── Twenty CRM MCP (HTTPS, remote)
    │       └── query_records / create_record / update_record
    │
    └── GitHub MCP (HTTPS, API)
            └── push_files / create_repository
                get_file_contents / create_or_update_file
```

---

## Transport Methods

| System | Transport | Endpoint |
|--------|-----------|----------|
| Obsidian | Streamable HTTP (local) | `http://127.0.0.1:27123/mcp/` |
| Twenty CRM | Streamable HTTP (remote) | `https://crm.[domain]/mcp` |
| GitHub | REST API via MCP server | `@modelcontextprotocol/server-github` |

---

## Claude Desktop Configuration

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": [
        "mcp-remote@0.1.0",
        "http://127.0.0.1:27123/mcp/",
        "--header",
        "Authorization: Bearer [obsidian-key]"
      ]
    },
    "twenty": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://crm.[domain]/mcp",
        "--header",
        "Authorization: Bearer [twenty-key]"
      ]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "[github-token]"
      }
    }
  }
}
```

---

## Key Technical Decisions

**Obsidian: HTTP over HTTPS**
The Local REST API plugin generates a self-signed certificate. Node.js rejects untrusted certificates by default, causing MCP connection failures. Solution: use the HTTP server (port 27123) for local connections — same security posture on localhost, no certificate issues.

**mcp-remote version pinning**
Obsidian Local REST API v2.x switched to Streamable HTTP transport. Older `mcp-remote@latest` expected SSE streaming and returned `404 Not Found`. Pinned to `mcp-remote@0.1.0` for SSE compatibility, then migrated to native HTTP transport when plugin version was confirmed.

**Twenty CRM: native MCP discovery**
Initial assumption was that Twenty CRM's MCP endpoint was cloud-only. Discovered the self-hosted instance also exposes `/mcp` natively — no separate proxy needed.

---

## Capabilities Unlocked

With this setup, Claude can in a single session:
- Read meeting context from Obsidian, pull CRM data for those clients, write a follow-up plan back to the vault
- Query CRM for prospects, run company research, draft outreach emails
- Pull code context from GitHub, cross-reference with vault documentation
- Create CRM records from meeting transcripts without any manual data entry

---

## Stack

- **Claude Desktop** — MCP client host
- **Obsidian Local REST API** — vault MCP server
- **Twenty CRM** — CRM MCP server (self-hosted)
- **@modelcontextprotocol/server-github** — GitHub MCP server
- **mcp-remote** — SSE/HTTP bridge for remote MCP servers
- **Node.js / npx** — MCP server runtime
