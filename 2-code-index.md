---
allowed-tools: Edit, View, Bash(git:*), Bash(tree:*), Bash(find:*), Bash(gh:*), Bash(jq:*), Bash(cloc:*)
description: Map architectural boundaries and component dependencies. Create navigable code indexes that clarify system structure and composition.
writes-to: .claude/code_index
---

# Code Index & Architecture Mapping

You are tasked with mapping the architectural structure of the codebase—identifying component boundaries, documenting dependencies (inputs/outputs), and creating navigable indexes that make the system easy to reason about. All code index documents must be stored in the `.claude/code_index` directory.

### Purpose

**Map the territory.** Transform implicit architecture into explicit component maps. Document what talks to what, where boundaries live, and how pieces compose. This enables both humans and agents to navigate the codebase with confidence and understand modular design decisions.

Acts as your **structural navigation layer** — where components live, what they depend on, and how they fit together.

### Initial Setup (auto-reply)

```
Provide the component, module, or architectural area you want mapped. I'll analyze the structure, document boundaries, trace dependencies, and create a navigable code index.
```

Wait for the user's mapping request.

We have a CLI tool called **kb-claude** that can be used to manage the knowledge base:

- `kb-claude new "Title"` – guided prompt for new entries; handles tags, relations, timestamps, UUIDs, and file placement.
- `kb-claude search keyword` – case-insensitive search across titles, tags, relations, and body text.
- `kb-claude validate --strict` – **REQUIRED before saving**; parses every entry, confirms required metadata, and flags inconsistencies.
- `kb-claude manifest` – rebuild `.claude/manifest.md`, a table summarizing every document. Run after every save.
- `kb-claude link source target` – insert reciprocal relations between two slugs.

## Workflow (strict order)

1. **Read mentioned files fully (no paging):**
   - If user mentions specific files, read them FULLY first
   - Use Read tool WITHOUT limit/offset parameters
   - Get complete context before analysis

2. **Analyze architectural scope:**
   - Identify the component/module/subsystem to map
   - Determine boundaries (what's IN vs OUT of scope)
   - Identify entry points and public interfaces
   - Consider what makes this component distinct and composable

3. **Trace dependencies:**
   - **Inbound**: What depends on this component? (consumers, callers)
   - **Outbound**: What does this component depend on? (imports, services, databases)
   - Document both code-level (imports/exports) and runtime (APIs, events, data stores)
   - Note any circular dependencies or coupling concerns

4. **Map component structure:**
   - Document file/directory organization
   - Identify public vs internal interfaces
   - Note configuration files and environment dependencies
   - Map data models and schemas (if applicable)

5. **Verify against anchors:**
   - Check `memory_anchors/` for relevant invariants (naming conventions, timeout policies, etc.)
   - Document compliance or exceptions
   - Note any architectural standards being enforced

6. **Link to specifications and patterns:**
   - Find relevant `metadata/` specs that describe WHY this component exists
   - Link to `patterns/` that explain design decisions
   - Connect to `cheatsheets/` for operational procedures

7. **Gather metadata for the code index document:**
   - **CRITICAL**: Filename must be the slugified title, NOT a timestamp
   - Example: `authentication-service-index.md` (NOT `2025-10-25_auth_index.md`)
   - Generate valid UUID v4
   - Use ISO-8601 timestamps
   - Create kebab-case slug from title (this becomes the filename)
   - **IMPORTANT**: Quote all `[[slug]]` values in ontological_relations: `"[[slug]]"`

8. **Generate code index document:**
   - Use the structure gathered in steps 2-7
   - Follow the template below
   - Include concrete file paths and line numbers
   - Document dependencies with specifics (not just "uses database")

9. **Add GitHub permalinks (if applicable):**
   - Check if on main branch or if commit is pushed: `git branch --show-current` and `git status`
   - If on main/master or pushed, generate GitHub permalinks:
     - Get repo info: `gh repo view --json owner,name`
     - Create permalinks: `https://github.com/{owner}/{repo}/blob/{commit}/{file}#L{line}`
   - Replace local file references with permalinks in the document

10. **Validate & manifest:**
    - Run `kb-claude validate --strict` - must pass before saving
    - Run `kb-claude manifest` to update the knowledge base index
    - Link in graph: `kb-claude link code_index/<slug> metadata/<spec-slug>`

---

## CODE INDEX Documentation

### Agent Instructions

- **Fill in all fields with common sense** - use actual values, not placeholders
- `title`: Component/module name + "Code Index" (e.g., "Authentication Service Code Index")
- `link`: Kebab-case slug derived from title (this MUST match the filename)
- `type`: Must be `code_index` (matches directory name)
- `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
- `tags`: Include component name, architectural layer, and relevant technologies
- `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
- `uuid`: Generate a valid UUID v4
- **Filename requirement**: Save as `.claude/code_index/<link-value>.md` where link matches slugified title
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <Component Name> Code Index
link: <kebab-case-slug-from-title>
type: code_index
ontological_relations:
  - relates_to: "[[metadata/<spec-slug>]]"
  - relates_to: "[[patterns/<architectural-pattern>]]"
  - relates_to: "[[memory_anchors/<naming-or-structure-anchor>]]"
  - relates_to: "[[cheatsheets/<related-operational-guide>]]"
tags:
  - code-index
  - <component-name>
  - <architectural-layer>
  - <technology>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
---

## Summary

High-level overview of this component: purpose, scope, and role in the larger system (2-3 sentences).

## Component Boundary

**What's IN scope:**
- Feature/responsibility 1
- Feature/responsibility 2
- Data owned by this component

**What's OUT of scope:**
- Responsibilities delegated elsewhere
- Boundaries with adjacent components

**Public Interface:**
- API endpoints / exported functions / events emitted
- Configuration contracts
- Data schemas exposed

---

## File Structure

```
<component-root>/
├── <directory>/          # Description of purpose
│   ├── <key-file>.ext   # Specific role
│   └── ...
├── <directory>/          # Description
└── <config-file>        # Purpose
```

**Key files:**
- `path/to/file.ext` (lines X-Y) - Role and responsibilities
- `path/to/file.ext` (lines X-Y) - Role and responsibilities

---

## Dependencies

### Inbound Dependencies (Who depends on this)

**Consumers:**
- `path/to/consumer1.ext` - How it uses this component
- `path/to/consumer2.ext` - How it uses this component

**Integration points:**
- API calls from service X
- Events consumed by service Y
- Data read by component Z

### Outbound Dependencies (What this depends on)

**Code-level:**
- `import X from 'path'` - Purpose and usage
- `import Y from 'path'` - Purpose and usage

**Runtime:**
- Database: <name> (tables: A, B, C)
- External API: <service-name> (endpoints: /x, /y)
- Message queue: <topic/channel>
- Cache: <redis/memcached> (keys: pattern)
- Environment variables: VAR_NAME (purpose)

**Configuration:**
- Config file: `path/to/config`
- Feature flags: FLAG_NAME (purpose)

---

## Architecture Compliance

**Anchors enforced:**
- `[[memory_anchors/<anchor-slug>]]` - How compliance is achieved
- `[[memory_anchors/<anchor-slug>]]` - How compliance is achieved

**Exceptions/Deviations:**
- <deviation> - Justification and tracking issue (if applicable)

**Coupling concerns:**
- Note any tight coupling or circular dependencies
- Planned decoupling work (if applicable)

---

## Data Models

**Primary entities:**
- `EntityName` - Fields, validation rules, persistence layer
- `EntityName` - Fields, validation rules, persistence layer

**Schemas:**
- Database schema: `path/to/migration.sql`
- API schema: `path/to/openapi.yaml`
- Event schema: `path/to/events.proto`

---

## Entry Points

**How to invoke this component:**
- HTTP: `POST /api/endpoint` - Purpose
- CLI: `command subcommand` - Purpose  
- Function call: `functionName(params)` - Purpose
- Event trigger: `event.name` - Purpose

**Initialization:**
- Startup sequence: describe order of operations
- Required setup: environment, migrations, seed data

---

## Testing Strategy

**Test locations:**
- Unit tests: `path/to/tests/`
- Integration tests: `path/to/integration/`
- E2E tests: `path/to/e2e/`

**Coverage baseline:**
- Current coverage: X% (as of <date>)
- Critical paths covered: list key scenarios

---

## Observability

**Logging:**
- Log locations: `path/to/logs` or stdout
- Key log events: startup, errors, business events

**Metrics:**
- Metric names: `component.metric_name`
- Dashboards: link to Grafana/Datadog/etc.

**Tracing:**
- Trace context propagation: how spans are created
- Key trace points: entry/exit, external calls

---

## Related Documentation

**Specifications:**
- `[[metadata/<spec-slug>]]` - Why this component exists

**Patterns:**
- `[[patterns/<pattern-slug>]]` - Design decisions

**Operational guides:**
- `[[cheatsheets/<cheatsheet-slug>]]` - How to operate/debug

**Anchors:**
- `[[memory_anchors/<anchor-slug>]]` - Invariants enforced

---

## References

- GitHub permalinks (if applicable)
- Architecture diagrams (if external)
- Related RFCs or ADRs
```

---

## Rules

* **No placeholders.** Fill all frontmatter fields with actual values using common sense.
* **Concrete over abstract**: Prefer file paths and line numbers over vague descriptions.
* **Dependencies must be specific**: Not "uses database" but "PostgreSQL, tables: users, sessions, tokens".
* Must include at least one `metadata/` relation in `ontological_relations`.
* Link to relevant `memory_anchors/` for architectural standards.
* **Validate before saving**: Run `kb-claude validate --strict` - document must pass before committing.
* One component per document - prefer many focused indexes over one mega-document.
* Update code index when component boundaries change significantly.

---

## Follow-ups & Updates

When the component evolves:

1. **Append update section:**
   ```
   ## Updates [ISO-8601-timestamp]
   
   **Changes:**
   - Boundary change: describe what moved in/out
   - New dependency: what was added and why
   - Removed dependency: what was removed and why
   - Architecture shift: describe structural change
   ```

2. **Update `updated_at` timestamp** to current time

3. **Re-validate:**
   ```bash
   kb-claude validate --strict
   kb-claude manifest
   ```

4. **Update graph links** if relationships changed:
   ```bash
   kb-claude link code_index/<slug> <new-related-slug>
   ```

---

## Global Documentation Guardrails

* **Critical ordering**:
  * Always read mentioned files fully **before** analysis.
  * Validate frontmatter **before** saving: `kb-claude validate --strict` must pass.
  * Update manifest after every write: `kb-claude manifest`.

* **Frontmatter consistency**:
  * Use exact format: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`
  * Snake_case keys (`created_at`, `updated_at`).
  * **CRITICAL**: Quote all ontological relations: `relates_to: "[[slug]]"` (prevents YAML parsing errors)
  * **CRITICAL**: Filename MUST equal link field (slugified title, NOT timestamps)
  * Timestamps in ISO-8601 format: `2025-10-25T14:30:00Z`
  * **No placeholders.** Fill with actual values using common sense.
  * Example: Title "Auth Service Code Index" → `link: auth-service-code-index` → filename: `auth-service-code-index.md`

* **Graph hygiene**:
  * Every code index must link to at least one `metadata/` spec.
  * Link to `patterns/` that explain architectural decisions.
  * Link to `memory_anchors/` for enforced invariants.
  * Link to `cheatsheets/` for operational procedures.
  * Use `[[slug]]` syntax for all ontological relations.

* **kb-claude ops**:
  * `kb-claude new "<Title>" --type code_index`
  * `kb-claude validate --strict` (REQUIRED before saving)
  * `kb-claude manifest` (update after every write)
  * `kb-claude link <source-slug> <target-slug>`
  * `kb-claude search "<keyword>"`

* **Punitive rules**:
  * DO NOT CODE - focus on mapping, not implementing.
  * DO NOT save with placeholders.
  * Documents failing `kb-claude validate --strict` **must not** be saved.
  * YOU WILL BE PUNISHED FOR SAVING INVALID DOCUMENTS.

---

## Important Notes

- Focus on **structural clarity** over implementation details
- Document **composition** - how components fit together
- Trace **data flow** through the component
- Identify **coupling** and **cohesion** patterns
- Keep indexes **navigable** - think "map" not "encyclopedia"
- Update when boundaries shift or major refactoring occurs
- Link bidirectionally: code index ↔ spec ↔ pattern ↔ cheatsheet
- **Component boundaries** should align with team boundaries (Conway's Law)
- Document **why** boundaries exist (tied to patterns), not just where they are

