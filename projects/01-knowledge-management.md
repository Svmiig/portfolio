# AI Knowledge Management System

> **TL;DR** — Built an Obsidian vault as an organizational second brain, wired directly to Claude via MCP. Claude can read, write, search, and maintain the entire knowledge base autonomously. Includes GDPR-compliant data pipelines and a custom tag ontology.

---

## Problem

Consulting teams generate enormous amounts of tacit knowledge — meeting notes, client insights, process documentation — that never gets captured in a usable, searchable form. Static wikis get stale. CRMs miss context. Documents pile up in Drive.

## Solution

Designed and built a structured Obsidian vault with full Claude MCP integration. The vault functions as a living knowledge graph: every document has typed YAML frontmatter, wikilinks to related nodes, and tag-validated metadata. Claude can operate the vault as an autonomous agent — reading context before acting, writing structured notes, maintaining relationships between documents.

---

## Architecture

```
Claude Desktop
    │
    ▼
Obsidian Local REST API (MCP server)
    │
    ▼
Obsidian Vault (Markdown + YAML)
    │
    ├── /Asiakkaat/          ← Client CRM
    ├── /Palaverit/          ← Meeting memos
    ├── /Skills/             ← Claude skill definitions
    ├── /Prosessikuvaukset/  ← Process documentation
    └── /Tiimi/              ← Team profiles
```

---

## Key Features

**YAML Frontmatter Automation**
Every document entering the vault gets structured frontmatter automatically: type classification, date, author, related links, status, and validated tags. Claude generates this from content analysis.

**GDPR Anonymization Pipeline**
Obsidian Sync operates under Canadian law, outside EU GDPR jurisdiction. Built a mandatory anonymization pass that strips personally identifiable information from external contacts before any content is written to the vault. Internal team members are exempt.

**Tag Ontology & Validation**
Maintained a canonical tag vocabulary (types, themes, industries, people, status). A dedicated `tag-guardian` skill validates all tags against the ontology before writing, preventing taxonomy drift.

**Meeting Memo Pipeline**
Fireflies.ai transcripts → Claude analysis → GDPR anonymization → structured Obsidian memo → auto-filed in correct folder. Full pipeline runs in one Claude session, no manual formatting.

**Bidirectional Linking**
Documents are connected via Obsidian wikilinks. Claude maintains these links when creating new content, keeping the knowledge graph connected.

---

## Stack

- **Obsidian** — knowledge base host
- **Obsidian Local REST API** (Adam Coddington) — MCP server providing vault access
- **Claude Desktop** — MCP client
- **Anthropic Claude API** — reasoning and generation
- **Fireflies.ai** — meeting transcription source
- **Obsidian Sync** — cross-device sync

---

## Results

- 50+ structured documents created and maintained autonomously
- Meeting-to-memo time reduced from ~45 min manual to ~5 min automated
- Zero tag drift after implementing tag-guardian validation
- Full vault searchable and writable by Claude in any session
