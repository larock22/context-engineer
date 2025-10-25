---
allowed-tools: Edit, View, Bash(git:*), Bash(python:*), Bash(pytest:*), Bash(mypy:*), Bash(black:*), Bash(coverage:*), Bash(mutmut:*), Bash(docker:*), Bash(trivy:*), Bash(hadolint:*), Bash(dive:*), Bash(npm:*), Bash(kubectl:*), Bash(helm:*), Bash(lighthouse:*), Bash(jq:*), Bash(curl:*), Bash(gh:*)
description: Document the unexplored. Agent uses best judgment to create cheatsheets (operational runbooks) and/or patterns (architectural decisions) from tribal knowledge.
writes-to: .claude/cheatsheets, .claude/patterns 
---



### Purpose

**Document the unexplored.** Turn volatile insights, undocumented patterns, and tribal knowledge into durable, **one-screen** artifacts. This captures operational runbooks (cheatsheets) and architectural decisions (patterns)—grounded by `metadata/` and `memory_anchors/`.

### Initial Setup (auto-reply)

```
Provide the area you want documented (operational task, architectural pattern, or unexplored territory). I'll analyze what's needed and create the appropriate documentation—cheatsheet, pattern, or both—using best judgment based on the content.
```

### Decision Framework: Cheatsheet vs Pattern vs Both

**Create a CHEATSHEET when:**
- User needs operational/execution guidance (how to DO something)
- Task involves specific steps, commands, or procedures
- Focus is on verification, rollback, and failure modes
- Examples: "Deploy service X", "Debug auth issues", "Rotate API keys"

**Create a PATTERN when:**
- User needs architectural/design guidance (WHY something was decided)
- Content involves tradeoffs, alternatives, and consequences
- Focus is on decision rationale and invariants
- Examples: "Why we use event sourcing", "Auth strategy", "Error handling approach"

**Create BOTH when:**
- Complex area with both implementation details AND design decisions
- Pattern explains the "why", cheatsheet provides the "how"
- Example: "Caching strategy" (pattern) + "Cache invalidation procedure" (cheatsheet)

**Use best judgment:**
- If unclear, start with what's most immediately useful
- Can always add the companion document later
- Link them together via `related:` field

**Agent Autonomy:**
- YOU decide what documentation type(s) to create based on the content
- Explain your reasoning briefly (1-2 sentences) before creating docs
- Focus on capturing the unexplored—things not yet documented
- Prioritize tribal knowledge, implicit patterns, and undocumented procedures

### Workflow (strict order)

1. **Read mentioned files fully (no paging).**
2. **Analyze & decide**: Determine if cheatsheet, pattern, or both are needed (use Decision Framework above).
3. **Locate truth sources**: relevant `metadata/<spec>.md`, `memory_anchors/`, and any files referenced.
4. **Draft outline**: Follow appropriate template below based on decision.
5. **Cross-check**: ensure every statement maps to concrete files, decisions, or evidence.
6. **Assemble document(s)** using templates below.
7. **GitHub permalinks** (if pushed): replace local paths with `https://github.com/{owner}/{repo}/blob/{commit}/{file}#L{line}`.
8. **Validate & manifest**: `kb-claude validate --strict` → `kb-claude manifest`.
9. **Link graph**: `kb-claude link <doc-type>/<slug> metadata/<spec-slug>` (and any anchors/specs referenced).

---

## CHEATSHEET Documentation

### Agent Instructions
- **Fill in all fields with common sense** - use actual values, not placeholders
- `title`: Clear, descriptive title (6-10 words)
- `link`: Kebab-case slug derived from title (this MUST match the filename)
- `type`: Must be `cheatsheets` (PLURAL - matches directory name)
- `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
- `tags`: Relevant keywords for discoverability
- `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
- `uuid`: Generate a valid UUID v4
- **Filename requirement**: Save as `.claude/cheatsheets/<link-value>.md` where link matches slugified title
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <descriptive title in 6-10 words>
link: <kebab-case-slug-from-title>
type: cheatsheets
ontological_relations:
  - relates_to: "[[metadata/<spec-slug>]]"
  - relates_to: "[[memory_anchors/<anchor-slug>]]"
tags:
  - operations
  - runbook
  - <relevant-tag>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
---

## Summary
One-paragraph overview: goal, boundaries, and success signal.

## Constraints
- <hard constraint 1>
- <hard constraint 2>

## Risks
- <risk 1>
- <risk 2>
---

## Prerequisites
- Tooling / access / env vars required.
- Feature flags or config toggles (if any).

## Steps
1. Action with exact target (file/service), expected effect.
2. …
3. …

## Verification
- **Checks** (logs / metrics / CLI outputs)
- **Acceptance signal** (the one-liner truth)

## Rollback
- Minimal, safe, tested reversal path.

## Common Failure Modes
- Symptom → likely cause → quick check → remediation.

## References
- Spec(s), anchors, permalinks (commit-pinned).
```

### Rules

* One screen max; link deeper context instead of bloating.
* **No placeholders.** Fill all frontmatter fields with actual values using common sense.
* Must include at least one `metadata/` relation and one `memory_anchors/` relation in `ontological_relations`.
* Prefer **imperatives**; avoid narrative.
* **Validate before saving**: Run `kb-claude validate --strict` - document must pass before committing.

### Follow-ups

* Append `## Updates [timestamp]` with deltas.
* Update `updated_at` timestamp.
* Re-run `kb-claude validate --strict` and `kb-claude manifest`.

```

---

## PATTERN Documentation

### Agent Instructions
- **Fill in all fields with common sense** - use actual values, not placeholders
- `title`: Clear pattern name (4-8 words)
- `link`: Kebab-case slug derived from title (this MUST match the filename)
- `type`: Must be `patterns` (PLURAL - matches directory name)
- `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
- `tags`: Include `pattern`, `decision`, and quality attributes (e.g., performance, security, reliability)
- `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
- `uuid`: Generate a valid UUID v4
- `decision_record`: Optional but recommended for architectural decisions
- **Filename requirement**: Save as `.claude/patterns/<link-value>.md` where link matches slugified title
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <named pattern in 4-8 words>
link: <kebab-case-slug-from-title>
type: patterns
ontological_relations:
  - relates_to: "[[metadata/<spec-slug>]]"
  - relates_to: "[[memory_anchors/<anchor-slug>]]"
  - relates_to: "[[cheatsheets/<related-cheatsheet>]]"
tags:
  - pattern
  - decision
  - <quality-attribute>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
decision_record:
  date: <ISO-8601-timestamp>
  approvers: [<names-or-roles>]
  git_commit: <sha-if-applicable>
---

## Summary
Problem framing and the solution idea in 2-3 sentences.
---

## Problem
- Short, concrete statement. Scope boundaries.

## Context & Forces
- Constraints (time, infra, data, risk).
- Quality attributes (latency, reliability, cost, safety, DX).

## Decision
- The chosen approach in one paragraph.
- Key invariants (tie back to anchors).

## Consequences
- Positive outcomes.
- Known liabilities / debt accepted.

## Alternatives Considered
- Option A — why rejected.
- Option B — why rejected.

## Implementation Notes (non-code)
- Files, modules, interfaces impacted.
- Observability hooks to confirm behavior.

## Verification Heuristics
- Signals that indicate the pattern is working (or regressing).

## References
- Specs, anchors, permalinks (commit-pinned).
```

### Rules

* **No placeholders.** Fill all frontmatter fields with actual values using common sense.
* Must include at least one `metadata/` relation and one `memory_anchors/` relation in `ontological_relations`.
* Record **alternatives** even if briefly; future readers need the landscape.
* No code. Point to files/permalinks only.
* **Validate before saving**: Run `kb-claude validate --strict` - document must pass before committing.

### Follow-ups

* Append `## Revisions [timestamp]` with changed forces/decision.
* Update `updated_at` timestamp.
* Re-run `kb-claude validate --strict` and `kb-claude manifest`.

---

## Global Documentation Guardrails

* **Critical ordering**
  * Always read mentioned files fully **before** sub-tasks.
  * Validate frontmatter **before** saving: `kb-claude validate --strict` must pass.
  * Update manifest after every write: `kb-claude manifest`.

* **Frontmatter consistency**:
  * Use exact format: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`
  * Snake_case keys (`created_at`, `updated_at`, `git_commit`).
  * **CRITICAL**: Quote all ontological relations: `relates_to: "[[slug]]"` (prevents YAML parsing errors)
  * **CRITICAL**: Filename MUST equal link field (slugified title)
  * Timestamps in ISO-8601 format: `2025-10-25T14:30:00Z`
  * **No placeholders.** Fill with actual values using common sense.
  * Example: Title "Deploy Service X" → `link: deploy-service-x` → filename: `deploy-service-x.md`

* **Graph hygiene**:
  * Prefer many short docs over one bloated doc.
  * Every doc must have at least: one `metadata/` relation, one `memory_anchors/` relation.
  * Use `[[slug]]` syntax for all ontological relations.

* **kb-claude ops**:
  * `kb-claude new "<Title>" --type cheatsheet|pattern`
  * `kb-claude validate --strict` (REQUIRED before saving)
  * `kb-claude manifest` (update after every write)
  * `kb-claude link <source-slug> <target-slug>`
  * `kb-claude search "<keyword>"`

* **Punitive rules (as requested)**:
  * DO NOT CODE.
  * DO NOT save with placeholders.
  * Documents failing `kb-claude validate --strict` **must not** be saved.


