# AI Knowledge Management System

> **TL;DR** — Converted an organization's scattered data into structured Markdown, creating a second brain that continuously feeds context to Claude. The core idea: the richer and more structured the data available to an LLM, the sharper its reasoning. Every document, meeting, and client insight becomes fuel for AI cognition.

---

## Problem

LLMs are only as good as the context they receive. Most organizations have valuable knowledge — meeting notes, client insights, strategic decisions, process documentation — but it's buried in PDFs, Drive folders, and email threads. In that form, it's inaccessible to AI.

The challenge isn't storing information. It's making it *readable by machines in a way that augments reasoning*.

## Solution

Designed and built a structured Obsidian vault that serves as the organization's second brain. Every piece of knowledge is converted to Markdown with typed YAML frontmatter, wikilinks to related concepts, and validated tags. The vault connects directly to Claude via MCP — giving the AI read/write access to the full knowledge base in any session.

The result: Claude doesn't just answer questions. It reasons *with* the organization's accumulated knowledge as active context.

---

## Core Idea: Structured Markdown as LLM Fuel

Plain text documents give LLMs raw material. Structured Markdown with consistent frontmatter, typed relationships, and clean taxonomy gives LLMs *cognitive scaffolding* — the difference between handing someone a pile of papers and handing them an indexed library.

Key design decisions:
- Every document has a `type` field — Claude knows if it's reading a meeting memo, a client profile, a skill definition, or a process document
- Wikilinks create an explicit knowledge graph — Claude can traverse relationships, not just search keywords
- Tag ontology enforced at write time — no drift, no ambiguity, no orphaned concepts
- GDPR-clean by design — external contact data anonymized before entering the vault

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
    ├── /Asiakkaat/          ← Client profiles & CRM
    ├── /Palaverit/          ← Meeting memos
    ├── /Skills/             ← Claude skill definitions
    ├── /Prosessikuvaukset/  ← Process documentation
    └── /Tiimi/              ← Team profiles
```

---

## Key Features

**YAML Frontmatter Automation**
Every document entering the vault gets structured frontmatter automatically: type classification, date, author, related links, status, and validated tags. Claude generates this from content analysis — no manual metadata work.

**GDPR Anonymization Pipeline**
Obsidian Sync operates under Canadian law, outside EU GDPR jurisdiction. Built a mandatory anonymization pass that strips personally identifiable information from external contacts before any content is written to the vault. Internal team members are exempt.

**Tag Ontology & Validation**
Maintained a canonical tag vocabulary across types, themes, industries, people, and status. A dedicated `tag-guardian` skill validates all tags against the ontology before writing, preventing taxonomy drift over time.

**Meeting Memo Pipeline**
Fireflies.ai transcripts → Claude analysis → GDPR anonymization → structured Obsidian memo → auto-filed in correct folder. Full pipeline runs in one Claude session. Meeting knowledge enters the second brain within minutes of the call ending.

**Bidirectional Linking**
Documents are connected via Obsidian wikilinks. Claude maintains these links when creating new content, so the knowledge graph grows coherently rather than as a flat document dump.

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
- Claude's output quality measurably improves when operating with vault context vs. without
