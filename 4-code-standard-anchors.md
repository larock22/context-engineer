---
allowed-tools: Edit, View, Bash(git:*), Bash(rg:*), Bash(grep:*), Bash(find:*), Bash(gh:*)
description: Define and enforce invariants. Create UUID-tracked memory anchors for non-negotiable truths—naming rules, timeout policies, architectural laws that preserve system integrity.
writes-to: .claude/memory_anchors
---

# Coding Standards & Memory Anchors

You are tasked with defining and enforcing system invariants—the non-negotiable truths that preserve integrity across the codebase. These are captured as memory anchors: UUID-tracked documents that define naming conventions, timeout policies, architectural laws, and quality standards. All memory anchor documents must be stored in the `.claude/memory_anchors` directory.

### Purpose

**Define what must never change.** Memory anchors are your system's constitutional law—the invariants that all code must respect. They're referenced by every other layer (specs, patterns, tests, code indexes) to ensure consistency. UUID-tracked so they can't drift silently. Enforced by CI, linting, runtime checks, and code review.

Acts as your **invariant enforcement layer** — the bedrock truths that keep the system coherent as it evolves.

### Initial Setup (auto-reply)

```
Provide the invariant, standard, or architectural law you want to establish as a memory anchor. I'll analyze current usage, document the standard, define enforcement mechanisms, and create a UUID-tracked anchor that can be referenced across the knowledge base.
```

Wait for the user's standard/invariant definition request.

We have a CLI tool called **kb-claude** that can be used to manage the knowledge base:

- `kb-claude new "Title"` – guided prompt for new entries; handles tags, relations, timestamps, UUIDs, and file placement.
- `kb-claude search keyword` – case-insensitive search across titles, tags, relations, and body text.
- `kb-claude validate --strict` – **REQUIRED before saving**; parses every entry, confirms required metadata, and flags inconsistencies.
- `kb-claude manifest` – rebuild `.claude/manifest.md`, a table summarizing every document. Run after every save.
- `kb-claude link source target` – insert reciprocal relations between two slugs.

## Types of Memory Anchors

Memory anchors fall into several categories:

### 1. Architectural Laws
- Layering rules (what can depend on what)
- Communication patterns (sync vs async, events vs calls)
- Data flow constraints (unidirectional, immutable, etc.)
- Deployment boundaries
- Separation of concerns rules

### 2. Resource Policies
- Timeout values (HTTP, database, queue)
- Rate limits (API, background jobs)
- Retry policies (backoff, max attempts)
- Circuit breaker thresholds
- Connection pool sizes

### 3. Security Standards
- Authentication requirements
- Authorization models
- Input validation rules
- Encryption standards
- Secret management policies

### 4. Quality Thresholds
- Code coverage minimums
- Performance budgets (latency, throughput)
- Error rate tolerances
- Log retention policies
- Monitoring requirements

## Workflow (strict order)

1. **Read mentioned files fully (no paging):**
   - If user mentions specific files, standards docs, or linting configs, read them FULLY first
   - Use Read tool WITHOUT limit/offset parameters
   - Get complete context before defining the anchor

2. **Analyze current state:**
   - Search codebase for existing usage patterns: `rg <pattern>`
   - Identify inconsistencies or violations
   - Check existing linting rules, CI checks, or runtime validations
   - Review related specs, patterns, and code indexes

3. **Define the invariant clearly:**
   - What is the rule/standard/law?
   - Why does it exist? (Rationale and consequences of violation)
   - What are the boundaries? (When does it apply, when doesn't it?)
   - What are acceptable exceptions? (If any, and how to justify them)

4. **Identify references and dependencies:**
   - Which `metadata/` specs depend on this anchor?
   - Which `patterns/` implement or extend this anchor?
   - Which `code_index/` components enforce this anchor?
   - Which `qa/` tests verify this anchor?
   - Which `cheatsheets/` explain how to follow this anchor?

5. **Document migration path (if changing existing standard):**
   - What's the current state vs desired state?
   - How to migrate existing code?
   - Timeline and milestones
   - Who's responsible for enforcement?

6. **Gather metadata for the memory anchor document:**
   - **CRITICAL**: Filename must be the slugified title, NOT a timestamp
   - Example: `http-timeout-policy.md` (NOT `2025-10-25_timeouts.md`)
   - Generate valid UUID v4 (this is CRITICAL for anchors—it's their permanent ID)
   - Use ISO-8601 timestamps
   - Create kebab-case slug from title (this becomes the filename)
   - **IMPORTANT**: Quote all `[[slug]]` values in ontological_relations: `"[[slug]]"`

7. **Generate memory anchor document:**
   - Use the analysis from steps 2-6
   - Follow the template below
   - Include concrete examples of compliance and violations
   - Specify exact enforcement tooling and configuration


8 **Validate & manifest:**
    - Run `kb-claude validate --strict` - must pass before saving
    - Run `kb-claude manifest` to update the knowledge base index
    - Link related documents: `kb-claude link memory_anchors/<slug> <related-slug>`

9. **Create enforcement backlinks:**
    - Update specs that rely on this anchor to reference it
    - Update patterns that implement this anchor
    - Update tests that verify this anchor
    - Update code indexes that enforce this anchor

---

## MEMORY ANCHOR Documentation

### Agent Instructions

- **Fill in all fields with common sense** - use actual values, not placeholders
- `title`: Descriptive anchor name (e.g., "HTTP Timeout Policy", "API Naming Convention")
- `link`: Kebab-case slug derived from title (this MUST match the filename)
- `type`: Must be `memory_anchors` (PLURAL - matches directory name)
- `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
- `tags`: Include `anchor`, `standard`, category (naming/architecture/security/etc.)
- `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
- `uuid`: Generate a valid UUID v4 - **THIS IS THE ANCHOR'S PERMANENT IDENTITY**
- **Filename requirement**: Save as `.claude/memory_anchors/<link-value>.md` where link matches slugified title
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <Descriptive Anchor Name>
link: <kebab-case-slug-from-title>
type: memory_anchors
ontological_relations:
  - relates_to: "[[metadata/<related-spec>]]"
  - relates_to: "[[patterns/<related-pattern>]]"
tags:
  - anchor
  - standard
  - <category>
  - <domain>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
enforcement:
  static_analysis: [<linter>, <formatter>, <type-checker>]
  ci_cd: [<check-name>, <pipeline-stage>]
  runtime: [<guard>, <middleware>, <assertion>]
  monitoring: [<alert-name>, <metric-name>]
audit:
  last_audit: <ISO-8601-timestamp>
  next_audit: <ISO-8601-timestamp>
  audit_frequency: <quarterly|monthly|per-release>
  auditor: <role-or-name>
---

## Summary

One-paragraph overview: what this anchor defines, why it exists, and what it protects against. (2-3 sentences)

**Anchor UUID**: `<uuid>` (permanent identifier for cross-references)

---

## The Invariant

### Statement

Clear, unambiguous declaration of the rule/standard/law:

> [The invariant in one sentence, written as an imperative or constraint]

### Scope

**Applies to:**
- Component/layer/file types where this is enforced
- Environments (dev, staging, prod)
- Specific situations or contexts

**Does NOT apply to:**
- Exceptions and why they exist
- Legacy code (with migration timeline)
- Specific edge cases (with justification)

### Rationale

**Why this invariant exists:**
- Problem it solves
- Risk it mitigates
- Quality attribute it preserves (performance, security, maintainability)

**Consequences of violation:**
- Technical: what breaks or degrades
- Business: user impact or operational impact
- Maintenance: long-term costs

---

## The Standard

### Specification

Detailed, actionable description of the standard:

**Rules:**
1. **Rule 1**: [Specific, measurable constraint]
   - Examples: [Good examples]
   - Anti-patterns: [Bad examples]
   - Rationale: [Why this specific rule]

2. **Rule 2**: [Specific, measurable constraint]
   - Examples: [Good examples]
   - Anti-patterns: [Bad examples]
   - Rationale: [Why this specific rule]

**Parameters/Values:**
- Timeout: X seconds (or: min Y, max Z)
- Naming pattern: `^[a-z][a-z0-9_]*$`
- Threshold: > 80% or < 100ms
- Format: ISO-8601, camelCase, kebab-case, etc.

---

## Enforcement

### Static Analysis

**Linting rules:**
- Tool: [eslint/pylint/clippy/etc.]
- Config file: `path/to/config`
- Rule ID: `rule-name` (link to documentation)
- Severity: error/warning
- Auto-fix: yes/no

**Formatting rules:**
- Tool: [prettier/black/gofmt/etc.]
- Config file: `path/to/config`
- Commands: `npm run format`, `cargo fmt`, etc.

**Type checking:**
- Tool: [typescript/mypy/flow/etc.]
- Config file: `path/to/config`
- Strict mode: enabled/disabled


**Pre-commit hooks:**
- Tool: [husky/pre-commit/lefthook]
- Hook file: `path/to/hook`
- Checks: [list of checks]

### Runtime Checks

**Guards/Assertions:**
- Location: `path/to/guard-code`
- Trigger: [when this executes]
- Behavior on violation: [error/warning/fallback]
- Example:
  ```
  <code-showing-runtime-check>
  ```

**Middleware/Interceptors:**
- Location: `path/to/middleware`
- Applied to: [routes/functions/layers]
- Enforcement logic: [how it validates]

### Monitoring & Alerting

**Alerts:**
- Alert name: `InvariantViolation-<anchor-name>`
- Condition: count > 0 in 5m
- Severity: [critical/warning]
- Notification: [PagerDuty/Slack/email]
- Runbook: `[[cheatsheets/<response-guide>]]`

---

## Exceptions

### Approved Exceptions

**Exception 1: [Scenario]**
- Location: `path/to/exceptional-code`
- Justification: [Why this is allowed]
- Approval: [Who approved, when]
- Sunset date: [When to revisit or remove]
- Tracking issue: [link to issue/ticket]

**Exception 2: [Scenario]**
- Location: `path/to/exceptional-code`
- Justification: [Why this is allowed]
- Approval: [Who approved, when]
- Sunset date: [When to revisit or remove]
- Tracking issue: [link to issue/ticket]



**Audit checklist:**
- [ ] Scan codebase for violations: `rg '<pattern>'`
- [ ] Review exceptions for expiry
- [ ] Verify monitoring alerts are working
- [ ] Update compliance metrics
- [ ] Review rationale (is it still valid?)
- [ ] Update documentation if needed

**Audit findings log:**

### Audit [ISO-8601-timestamp]
- Compliance: X%
- New violations: N (locations: [list])
- Expired exceptions: N (removed: [list])
- Findings: [summary]
- Actions: [what was done]

---

## Related Documentation

**Specifications:**
- `[[metadata/<spec-slug>]]` - Feature that depends on this anchor

**Patterns:**
- `[[patterns/<pattern-slug>]]` - Design pattern that implements this anchor

**Code indexes:**
- `[[code_index/<component-slug>]]` - Component that enforces this anchor

**QA verification:**
- `[[qa/<qa-slug>]]` - Tests that verify this anchor

**Operational guides:**
- `[[cheatsheets/<cheatsheet-slug>]]` - How to comply with this anchor

**Other anchors:**
- `[[memory_anchors/<related-anchor>]]` - Related or dependent anchor

---

## References

- Standards documents (IEEE, RFC, etc.)
- Tool documentation (linter, formatter)
- GitHub permalinks to enforcement code
- Industry best practices
- Internal RFCs or ADRs
```

---

## Rules

* **No placeholders.** Fill all frontmatter fields with actual values using common sense.
* **UUID is sacred**: The UUID is the anchor's permanent identity—never change it, always reference it.
* **Concrete examples required**: Show both compliant and non-compliant code.
* **Enforcement must be actionable**: Specify exact tools, configs, commands.
* **Audit schedule mandatory**: Anchors drift without regular verification.
* Must reference at least one downstream document (spec/pattern/qa/code_index) in `ontological_relations`.
* **Validate before saving**: Run `kb-claude validate --strict` - document must pass before committing.
* Define invariants that are **enforceable**, not aspirational.
* Document **why**, not just **what**—rationale prevents erosion.

---

## Follow-ups & Updates

When an anchor evolves:

1. **Update with extreme care** - anchors are foundational:
   ```
   ## Revisions [ISO-8601-timestamp]
   
   **Change summary:**
   - What changed: [old rule → new rule]
   - Rationale: [why the change was necessary]
   - Breaking: [yes/no - does it break existing code?]
   - Migration: [how to adapt existing code]
   
   **Impact analysis:**
   - Affected components: [list with file counts]
   - Affected tests: [list]
   - Affected documentation: [list]
   
   **Approval:**
   - Approved by: [role/name]
   - Approved date: <ISO-8601-timestamp>
   - RFC/ADR: [link if applicable]
   ```

2. **Update `updated_at` timestamp** to current time

3. **DO NOT change UUID** - it's the anchor's permanent identity

4. **Re-validate:**
   ```bash
   kb-claude validate --strict
   kb-claude manifest
   ```

5. **Update all references:**
   - Notify all documents that reference this anchor
   - Update specs, patterns, tests, code indexes
   - Update enforcement tooling configs
   - Update monitoring dashboards

6. **Broadcast change:**
   - Create migration guide: `[[cheatsheets/<migration-guide>]]`
   - Announce to team (Slack, email, standup)
   - Update onboarding documentation

---

## Special Considerations for Memory Anchors

### UUID Stability

**Memory anchors use UUIDs as their primary identity:**
- The UUID appears in frontmatter
- Other documents reference anchors by UUID in comments: `// ANCHOR: [[uuid]]`
- **NEVER change the UUID** - it's permanent
- If an anchor is deprecated, create a new one with a new UUID
- Document deprecation in the old anchor with reference to the new one

### Referencing Anchors

**In code comments:**
```
// ANCHOR: [[http-timeout-policy]] - All HTTP requests must timeout in 30s
const timeout = 30_000;
```

**In other knowledge base documents:**
```yaml
ontological_relations:
  - relates_to: "[[memory_anchors/http-timeout-policy]]"
```

**In code review:**
```
This PR complies with [[memory_anchors/naming-convention]] anchor.
```

### Anchor Deprecation

When an anchor needs to be retired:

1.  ```
   ## ⚠️ DEPRECATED
   
   This anchor is deprecated as of <date>.
   Use `[[memory_anchors/<new-anchor>]]` instead.
   
   Reason: [why deprecated]
   Migration guide: `[[cheatsheets/<migration-guide>]]`
   ```

2. **Do NOT delete** - keep for historical reference
3. **Update all references** to point to the new anchor


## Global Documentation Guardrails

* **Critical ordering**:
  * Always read mentioned files fully **before** analysis.
  * Search codebase for current usage patterns before defining standard.
  * Validate frontmatter **before** saving: `kb-claude validate --strict` must pass.
  * Update manifest after every write: `kb-claude manifest`.

* **Frontmatter consistency**:
  * Use exact format: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`
  * Snake_case keys (`created_at`, `updated_at`, `enforcement`, `audit`).
  * **CRITICAL**: Quote all ontological relations: `relates_to: "[[slug]]"` (prevents YAML parsing errors)
  * **CRITICAL**: Filename MUST equal link field (slugified title, NOT timestamps)
  * **CRITICAL**: UUID is permanent—never change it
  * Timestamps in ISO-8601 format: `2025-10-25T14:30:00Z`
  * **No placeholders.** Fill with actual values using common sense.
  * Example: Title "HTTP Timeout Policy" → `link: http-timeout-policy` → filename: `http-timeout-policy.md`

* **Graph hygiene**:
  * Every anchor should be referenced by at least one downstream document.
  * Link to specs, patterns, qa docs, and code indexes that depend on this anchor.
  * Use `[[slug]]` syntax for all ontological relations.
  * Reference anchor UUID in code comments for traceability.


* **Punitive rules**:
  * DO NOT create anchors without enforcement mechanisms.
  * DO NOT save with placeholders.
  * DO NOT change anchor UUIDs—create new anchors instead.
  * Documents failing `kb-claude validate --strict` **must not** be saved.
  * YOU WILL BE PUNISHED FOR SAVING INVALID DOCUMENTS.

---

## Important Notes

- **Anchors are constitutional law**: They should change rarely and deliberately
- **Enforcement is mandatory**: An unenforced anchor is worse than no anchor
- **UUIDs enable traceability**: Use them in code comments, other docs, and tooling
- **Audit prevents drift**: Regular verification keeps anchors alive
- **Examples speak louder**: Show compliant and non-compliant code
- **Link bidirectionally**: anchor ← spec ← pattern ← code_index ← qa ← cheatsheet
- **Document rationale**: Future readers need to understand the "why"
- **Migration over prescription**: If existing code violates, provide a migration path
- **Exceptions are temporary**: All exceptions need sunset dates
- **Monitoring catches violations**: Don't rely on code review alone
- **Anchors compose**: Complex standards reference simpler anchors
- **Version control**: Keep deprecated anchors for historical context
- **Team buy-in required**: Anchors imposed from above fail; anchors adopted by consensus succeed

