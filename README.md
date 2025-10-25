# Agentic Coding Loop For Software Engineering

The rise of coding agents has led to a new way of working in software engineering. It is a new way of working that can be more efficient and effective than traditional software engineering.

But the same time the sheer number of agents and the complexity of the codebase has made it difficult to reason about the codebase. This can lead to a lot of confusion and frustration, and "slop" in the codebase.

The key to success is to be able to reason about the codebase in a way that is easy to understand and easy to navigate. This is where the knowledge base comes in. The ontological knowledge base and system below is a way to reason about the codebase in a way that is easy to understand and easy to navigate for both humans and agents.

The system does not need to be followed in a linear way. It is a way to reason about the codebase in a way that is easy to understand and easy to navigate for both humans and agents.

A worksheet is provided to help you reason about the codebase as you work, use common sense and your best judgment to decide what to do next. This system ties the onotlogical knowledge base to the codebase and modern software engineering practices to create a coherent and understandable codebase.

When in doubt, follow the flow, steps can be skipped if they are not relevant to the current task. Idealy, the system should be able to reason about the codebase and make decisions based on the context and the codebase. But say you run the speccifaction flow to map a bug, and you already have documents in the knowledge base that describe the bug, you can rreview and skip to the TDD flow.

Inspired by https://chrisloy.dev/post/2025/09/28/the-ai-coding-trap




1. **Specification** — Define the *why* and *what* before touching code. Clarify scope, intent, and success criteria.
2. **Documentation** — Write as you think. Capture reasoning, assumptions, and trade-offs early so the team (and future you) know *why* things exist.
3. **Modular Design** — Break problems into composable parts. Keep boundaries tight so both humans and AIs can reason about them clearly.
4. **Test-Driven Development** — Define correctness up front. Tests aren’t overhead; they’re contracts that keep velocity from turning into chaos.
5. **Coding Standards** — Enforce your house rules. AI can generate code fast, but it’s your standards that make it *maintainable*.
6. **Monitoring & Introspection** — Instrument everything. Logs, metrics, and feedback loops turn every fix into reusable insight.


## 3. Folder Layout

```
.claude/
  metadata/        component summaries
  debug_history/   debugging timelines
  qa/              Q&A and learning notes
  code_index/      file or module references
  patterns/        reusable fixes or design motifs
  cheatsheets/     quick references or how-tos
  memory_anchors/  core concepts tracked by UUID
  manifest.md      automatically generated summary
```

Each subdirectory is a distinct knowledge type. When creating entries, `type:` must match one of these folder names.

## 4. Document Structure

Every `.md` file starts with YAML front matter followed by narrative Markdown content.

```yaml
---
title: auth module broken after drizzle kit upgrade
link: auth-module-broken
type: debug_history
ontological_relations:
  - relates_to: [[drizzle-docs]]
  - relates_to: [[dependency-docs]]
tags:
  - dependencies
  - auth
created_at: 2025-10-23T14:00:00Z
updated_at: 2025-10-23T14:00:00Z
uuid: 123e4567-e89b-12d3-a456-426614174000
---
```

- **title** – human-readable summary.
- **link** – slug that doubles as filename.
- **type** – must align with a `.claude/` subfolder.
- **ontological_relations** – wiki-style cross-links (`[[slug]]`).
- **tags** – keywords for search.
- **uuid** – auto-generated for traceability.

The body is free-form Markdown: logs, analysis, diagrams, etc.

## 5. Core Commands

- `kb-claude init` – create the `.claude/` layout in a repo.
- `kb-claude new "Title"` – guided prompt for new entries; handles tags, relations, timestamps, UUIDs, and file placement.
- `kb-claude search keyword` – case-insensitive search across titles, tags, relations, and body text.
- `kb-claude validate [--strict]` – parse every entry, confirm required metadata, and flag inconsistencies (e.g., slug mismatch).
- `kb-claude manifest` – rebuild `.claude/manifest.md`, a table summarizing every document.
- `kb-claude link source target` – insert reciprocal relations between two slugs.

## 6. Daily Workflow Tips

- Treat `.claude/` as the project’s institutional memory. Capture debugging sessions, architecture decisions, and recurring Q&A.
- Before adding content, run `kb-claude search` to avoid duplication.
- Run `kb-claude validate --strict` before committing to keep the KB clean.
- The manifest acts like a changelog of insights—commit it alongside entries.

## 7. Design Principles

1. **Text over tools** – Markdown and YAML are the only storage formats.
2. **Structure without rigidity** – required metadata, free body text.
3. **Traceable knowledge** – everything has UUIDs and timestamps.
4. **Search-first** – think of `.claude/` as a local wiki you can grep.
5. **Small knowledge commits** – frequent, incremental captures beat perfect essays.
6. **Longevity** – the folder should remain useful in plain text for years.

## 8. Extending the Tool

Future directions: semantic search (Tantivy/SQLite), ontology graphs, automated summarizers, git-aware manifest diffs, or HTML/Notion exporters. The current MVP focuses on correctness, structure, and speed.

## 9. Getting Started

Install from [crates.io](https://crates.io/crates/claude-kb-cli):

```bash
cargo install claude-kb-cli
```

Then run:

```bash
kb-claude init
kb-claude new "First Entry" -t metadata
kb-claude manifest
```

That’s enough to see the typed knowledge base take shape. Everything else—searching, validating, linking—builds on that foundation.
