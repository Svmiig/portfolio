# Claude Skills Ecosystem

> **TL;DR** — Designed and built 25+ specialized Claude "skills" — structured prompt definitions that turn Claude into named AI agents with defined personas, tools, and workflows. Covers the full consulting lifecycle: sales, client analysis, proposal generation, training production, and quality assurance.

---

## Problem

AI tools used inconsistently across a team produce inconsistent output. Without shared standards, every team member prompts differently, quality varies, and institutional knowledge about "what works" never gets captured.

## Solution

Built a `Skills` ecosystem: a structured library of SKILL.md files, each defining a specialized Claude agent with a clear trigger condition, persona, workflow, and output format. Skills are loaded into Claude Desktop as system context. Any team member invoking a skill gets the same high-quality, consistent behavior.

---

## Skill Categories

### Sales & Client Acquisition
| Skill | Role |
|-------|------|
| `anna-liisa` | Strategic intelligence — company research, ICP analysis, pain points |
| `matti-sales-agent` | Hyperpersonalized first-contact email generation |
| `liidilinko` | Lead scoring pipeline — classifies contacts cold/warm/hot |
| `prospektilinko` | Lead-to-prospect conversion, B2B and B2C branches |
| `tarja` | Proposal generator — outputs branded DOCX with pricing tracks |
| `delivery-transkriptointi` | Deep transcript analysis — decisions, open questions, interpretation |

### Client Consulting
| Skill | Role |
|-------|------|
| `ai-transformation-scan` | Current state analysis — data, processes, capabilities, quick wins |
| `target-architecture` | Now → Next → Scale roadmap (30/90/180 days) |
| `ai-governance` | EU AI Act compliance framework, risk classification, AI literacy |
| `ai-strategy-simulation` | Live 60-min AI maturity workshop facilitation |
| `mira` | Organizational psychology — change resistance, SCARF, intervention design |

### Training Production
| Skill | Role |
|-------|------|
| `pinkbelt-basic-planner` | Generic training module with brand styling |
| `pinkbelt-custom-planner` | Client-customized training module with client branding |

### Brand & Communication
| Skill | Role |
|-------|------|
| `brand-aalto` | Brand voice, color system, tone-of-voice enforcement |
| `deck-writing-standard` | Slide writing rules — 1 idea, 8 words, 5 bullets, CTA |
| `humanizer` | Removes AI writing markers, corrects Finnish grammar |
| `thought-visualizer` | Converts text/ideas into HTML diagrams and infographics |

### Obsidian Integration
| Skill | Role |
|-------|------|
| `obsidian-frontmatter` | Auto-generates YAML frontmatter and files documents correctly |
| `gdpr-anonymisointi` | Mandatory GDPR anonymization before any vault write |
| `tag-guardian` | Validates tags against the canonical ontology |
| `muistio` | Meeting memo generation from Fireflies transcripts |
| `paiva-koonti` | End-of-day session analysis — decides what's worth saving |

### Orchestration
| Skill | Role |
|-------|------|
| `pera-orchestrator` | Coordinates multi-skill workflows, task decomposition |
| `sisu` | Critical quality assurance — fact-checking, hallucination detection |

---

## Skill Architecture

Each skill is a `SKILL.md` file following a canonical structure:

```markdown
---
name: skill-name
description: >
  Trigger conditions and summary for the Claude skill picker.
  Written in plain language. Used for auto-activation.
tags: [skill, category, ...]
status: active
---

# Skill Name

## Persona
...

## Workflow
Step 1 → Step 2 → Step 3

## Output format
...
```

Skills load as system context in Claude Desktop. The `description` field doubles as the auto-activation trigger — Claude recognizes when a user message matches and activates the skill without explicit invocation.

---

## Stack

- **Anthropic Claude** — reasoning engine
- **Claude Desktop** — MCP host and skill runner
- **Obsidian** — skill storage and versioning
- **YAML + Markdown** — skill definition format
- **python-docx / ReportLab** — for skills that generate DOCX/PDF output

---

## Results

- 25+ skills in production across 6 categories
- Full sales pipeline automatable end-to-end via skill chain
- Consistent output quality regardless of which team member runs a skill
- New skills deployable in under 30 minutes from concept to vault
