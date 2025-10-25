---
allowed-tools: Edit, View, Bash(git:*), Bash(python:*), Bash(pytest:*), Bash(jest:*), Bash(vitest:*), Bash(npm:*), Bash(cargo:*), Bash(go:*), Bash(coverage:*), Bash(mutmut:*), Bash(nyc:*), Bash(gh:*)
description: Define correctness up front through test specifications. Document acceptance criteria, edge cases, invariants, and observability plans before implementation.
writes-to: .claude/qa
---

# Test-Driven Development & QA Verification

You are tasked with defining correctness criteria before code is written—documenting test specifications, acceptance criteria, edge cases, invariants, and observability plans. All QA verification documents must be stored in the `.claude/qa` directory.

NOTE: At MOST a singular test file should be created for a single feature, fix, or component. If you need to test multiple things, take a step and reconsider the problem scope. No matter what one test file is created, it should be able to test the entire feature, fix, or component. Ask the user if the scope is correct, wait for the user's response before continuing. 

If a test file already exists, we need to analyze what went wrong  and adapt, document the new test file, and update the QA verification document.

If a test file does not exist, we need to create it, document the test file, to capture the character test or golden test.

### Purpose

**Define correctness first.** Tests aren't overhead—they're contracts that keep velocity from turning into chaos. Document what "working" means before touching implementation. Map edge cases to invariants, define observable success signals, and create verification plans that serve as living specifications.

Acts as your **correctness contract layer** — what must be true for the system to work, and how we'll know when it does.

### Initial Setup (auto-reply)

```
Provide the feature, fix, or component you want to define test specifications for. I'll analyze requirements, document acceptance criteria, identify edge cases, map invariants, and create a comprehensive QA verification plan.
```

Wait for the user's testing requirements.

We have a CLI tool called **kb-claude** that can be used to manage the knowledge base:

- `kb-claude new "Title"` – guided prompt for new entries; handles tags, relations, timestamps, UUIDs, and file placement.
- `kb-claude search keyword` – case-insensitive search across titles, tags, relations, and body text.
- `kb-claude validate --strict` – **REQUIRED before saving**; parses every entry, confirms required metadata, and flags inconsistencies.
- `kb-claude manifest` – rebuild `.claude/manifest.md`, a table summarizing every document. Run after every save.
- `kb-claude link source target` – insert reciprocal relations between two slugs.

## Workflow (strict order)

1. **Read mentioned files fully (no paging):**
   - If user mentions specific files (specs, tickets, existing tests), read them FULLY first
   - Use Read tool WITHOUT limit/offset parameters
   - Get complete context before planning tests

2. **Analyze requirements and scope:**
   - Extract acceptance criteria from specs (`metadata/`)
   - Identify the component under test (link to `code_index/`)
   - Understand the "what" and "why" before defining "correct"
   - Review relevant patterns for architectural constraints

3. **Define acceptance criteria:**
   - What does "working" mean for this feature/component?
   - What are the success signals (user-facing and system-facing)?
   - What behaviors are in-scope vs out-of-scope?
   - What constitutes a regression?

4. **Identify edge cases and invariants:**
   - Happy path scenarios
   - Boundary conditions (empty, null, max, min, negative)
   - Error conditions and failure modes
   - Concurrency and race conditions (if applicable)
   - Security edge cases (injection, overflow, unauthorized access)
   - Map each edge case to relevant `memory_anchors/` invariants

5. **Design observability strategy:**
   - What logs/metrics/traces confirm correct behavior?
   - What monitoring alerts should fire on failure?
   - How will we know in production if this is working?
   - What debugging information needs to be captured?

6. **Plan test coverage:**
   - Unit tests: pure logic, isolated components
   - Integration tests: component interactions, external dependencies
   - E2E tests: user flows, critical paths
   - Property-based tests: invariant checking (if applicable)
   - Performance tests: latency, throughput, resource usage (if applicable)
   - Security tests: penetration, fuzzing (if applicable)

7. **Document test implementation guidance:**
   - Test file locations and naming conventions
   - Setup/teardown requirements
   - Mock/stub strategies for dependencies
   - Test data generation approaches
   - CI/CD integration requirements

8. **Gather metadata for the QA document:**
   - **CRITICAL**: Filename must be the slugified title, NOT a timestamp
   - Example: `authentication-service-qa-verification.md` (NOT `2025-10-25_auth_tests.md`)
   - Generate valid UUID v4
   - Use ISO-8601 timestamps
   - Create kebab-case slug from title (this becomes the filename)
   - **IMPORTANT**: Quote all `[[slug]]` values in ontological_relations: `"[[slug]]"`

9. **Generate QA verification document:**
   - Use the analysis from steps 2-7
   - Follow the template below
   - Include concrete examples, not abstract descriptions
   - Link test cases to specific acceptance criteria

10. **Add GitHub permalinks (if applicable):**
    - Check if on main branch or if commit is pushed: `git branch --show-current` and `git status`
    - If on main/master or pushed, generate GitHub permalinks for test files:
      - Get repo info: `gh repo view --json owner,name`
      - Create permalinks: `https://github.com/{owner}/{repo}/blob/{commit}/{file}#L{line}`
    - Replace local file references with permalinks in the document

11. **Validate & manifest:**
    - Run `kb-claude validate --strict` - must pass before saving
    - Run `kb-claude manifest` to update the knowledge base index
    - Link in graph: `kb-claude link qa/<slug> metadata/<spec-slug>`

---

## QA VERIFICATION Documentation

### Agent Instructions

- **Fill in all fields with common sense** - use actual values, not placeholders
- `title`: Component/feature name + "QA Verification" (e.g., "Authentication Service QA Verification")
- `link`: Kebab-case slug derived from title (this MUST match the filename)
- `type`: Must be `qa` (matches directory name)
- `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
- `tags`: Include `qa`, `testing`, component name, and test types (unit, integration, e2e)
- `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
- `uuid`: Generate a valid UUID v4
- **Filename requirement**: Save as `.claude/qa/<link-value>.md` where link matches slugified title
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <Component/Feature Name> QA Verification
link: <kebab-case-slug-from-title>
type: qa
ontological_relations:
  - relates_to: "[[metadata/<spec-slug>]]"
  - relates_to: "[[code_index/<component-slug>]]"
  - relates_to: "[[memory_anchors/<invariant-slug>]]"
  - relates_to: "[[patterns/<relevant-pattern>]]"
tags:
  - qa
  - testing
  - <component-name>
  - <test-type>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
coverage_baseline:
  unit: <percentage>
  integration: <percentage>
  e2e: <percentage>
  updated: <ISO-8601-timestamp>
---

## Summary

High-level overview of what correctness means for this component (2-3 sentences). Describe the testing strategy and verification approach.

---

## Scope

**Feature/Component under test:**
- Component name and purpose
- Key behaviors being verified
- Related specs: `[[metadata/<spec-slug>]]`

**Testing boundaries:**
- What IS tested (in-scope)
- What is NOT tested (out-of-scope, tested elsewhere)
- Dependencies and how they're handled (mocked vs real)

---

## Acceptance Criteria

### Primary success criteria:

1. **[Criterion 1]** - Specific, measurable condition
   - Verification method: how to check
   - Success signal: what "pass" looks like

2. **[Criterion 2]** - Specific, measurable condition
   - Verification method: how to check
   - Success signal: what "pass" looks like

3. **[Criterion 3]** - Specific, measurable condition
   - Verification method: how to check
   - Success signal: what "pass" looks like

### Regression prevention:

- What previous bugs must not reoccur
- What behaviors must remain stable
- What performance characteristics must be maintained

---

## Edge Cases & Invariants

### Happy Path

| Scenario | Input | Expected Output | Invariants Enforced |
|----------|-------|-----------------|---------------------|
| [Scenario 1] | [Example input] | [Expected result] | `[[anchor-slug]]` |
| [Scenario 2] | [Example input] | [Expected result] | `[[anchor-slug]]` |

### Boundary Conditions

| Scenario | Input | Expected Behavior | Rationale |
|----------|-------|-------------------|-----------|
| Empty input | `""` / `null` / `[]` | [Error or default] | [Why this matters] |
| Maximum size | [Max value] | [Accept or reject] | [Why this matters] |
| Minimum size | [Min value] | [Accept or reject] | [Why this matters] |
| Invalid format | [Malformed input] | [Error handling] | [Why this matters] |

### Error Conditions

| Failure Mode | Trigger | Expected Behavior | Recovery |
|--------------|---------|-------------------|----------|
| [Error 1] | [What causes it] | [How system responds] | [How to recover] |
| [Error 2] | [What causes it] | [How system responds] | [How to recover] |

### Concurrency & Race Conditions (if applicable)

| Scenario | Setup | Expected Outcome | Invariant Protected |
|----------|-------|------------------|---------------------|
| [Race condition 1] | [Concurrent operations] | [Safe behavior] | `[[anchor-slug]]` |

### Security Edge Cases (if applicable)

| Attack Vector | Test Input | Expected Defense | Anchor Reference |
|---------------|------------|------------------|------------------|
| SQL Injection | `'; DROP TABLE--` | [Sanitization] | `[[security-anchor]]` |
| XSS | `<script>...</script>` | [Escaping] | `[[security-anchor]]` |
| Overflow | [Large payload] | [Rejection/limit] | `[[resource-anchor]]` |

---

## Test Coverage Plan

### Unit Tests

**Location:** `path/to/unit/tests/`

**Coverage goals:**
- Pure functions: 100%
- Business logic: 95%
- Edge cases: all documented above

**Key test files:**
- `path/to/test-file.test.ext` - Tests for [component]
- `path/to/test-file.test.ext` - Tests for [component]

**Mocking strategy:**
- External APIs: mock with fixed responses
- Database: mock/in-memory/test fixtures
- Time: freeze/control for determinism
- Randomness: seed for reproducibility

### Integration Tests

**Location:** `path/to/integration/tests/`

**Coverage goals:**
- Component interactions: all critical paths
- External dependencies: real connections (where feasible)
- Data flow: end-to-end through layers

**Key scenarios:**
1. [Integration scenario 1] - What it verifies
2. [Integration scenario 2] - What it verifies

**Test environment:**
- Database: test instance with migrations
- External services: test/staging endpoints or docker containers
- Configuration: test-specific env vars

### E2E Tests

**Location:** `path/to/e2e/tests/`

**Coverage goals:**
- Critical user flows: 100%
- Error paths: major failure modes
- Performance: baseline benchmarks

**Key user flows:**
1. [User flow 1] - Step-by-step verification
2. [User flow 2] - Step-by-step verification

**Test environment:**
- Full stack: all services running
- Test data: seeded fixtures or factories
- Cleanup: reset state between tests

### Property-Based Tests (if applicable)

**Location:** `path/to/property/tests/`

**Invariants to verify:**
1. **Invariant 1**: [Property that must always hold]
   - Generator strategy: [How to create test inputs]
   - Anchor: `[[memory_anchors/<anchor-slug>]]`

2. **Invariant 2**: [Property that must always hold]
   - Generator strategy: [How to create test inputs]
   - Anchor: `[[memory_anchors/<anchor-slug>]]`

### Performance Tests (if applicable)

**Location:** `path/to/performance/tests/`

**Benchmarks:**
- Latency: p50 < X ms, p99 < Y ms
- Throughput: > X req/sec
- Resource usage: memory < X MB, CPU < Y%

**Load scenarios:**
1. Normal load: [Description and targets]
2. Peak load: [Description and targets]
3. Stress test: [Description and breaking point]

### Security Tests (if applicable)

**Location:** `path/to/security/tests/`

**Test types:**
- Input validation: all injection vectors
- Authentication: bypass attempts
- Authorization: privilege escalation
- Rate limiting: abuse prevention

---

## Observability Plan

### Logging Strategy

**Success logs:**
- Event: [What to log on success]
- Level: INFO
- Fields: [Structured data to include]
- Purpose: [Why we need this]

**Error logs:**
- Event: [What to log on failure]
- Level: ERROR/WARN
- Fields: [Structured data to include]
- Purpose: [Debugging information needed]

**Trace logs:**
- Event: [What to log for debugging]
- Level: DEBUG
- Fields: [Structured data to include]
- Purpose: [When this is useful]

### Metrics & Monitoring

**Key metrics:**
1. **Metric name**: `component.operation.duration`
   - Type: histogram
   - Labels: [status, operation_type]
   - Alert threshold: p99 > X ms
   - Dashboard: [Link to Grafana/Datadog]

2. **Metric name**: `component.operation.errors`
   - Type: counter
   - Labels: [error_type, source]
   - Alert threshold: rate > X/min
   - Dashboard: [Link to Grafana/Datadog]

3. **Metric name**: `component.operation.count`
   - Type: counter
   - Labels: [operation_type, outcome]
   - Purpose: Traffic and success rate tracking

### Distributed Tracing

**Trace points:**
- Entry: [Where span starts]
- External calls: [What services/APIs to trace]
- Exit: [Where span ends]
- Context propagation: [How trace context flows]

**Trace attributes:**
- Operation: [What was attempted]
- Input summary: [Key parameters, sanitized]
- Outcome: [Success/failure indicator]
- Timing: [Critical sub-operations]

### Alerting Rules

| Alert Name | Condition | Severity | Response |
|------------|-----------|----------|----------|
| [Alert 1] | [Metric threshold] | Critical | [Action to take] |
| [Alert 2] | [Metric threshold] | Warning | [Action to take] |

---

## Test Implementation Guide

### Naming Conventions

**Test files:**
- Unit: `<module-name>.test.<ext>` or `<module-name>.spec.<ext>`
- Integration: `<feature>.integration.test.<ext>`
- E2E: `<user-flow>.e2e.test.<ext>`

**Test cases:**
- Pattern: `test/it/describe "should [expected behavior] when [condition]"`
- Example: `test "should return 401 when auth token is missing"`

### Setup & Teardown

**Before all tests:**
```
- Initialize test database/fixtures
- Start mock servers
- Set environment variables
- Clear caches
```

**Before each test:**
```
- Reset database state
- Clear mock call history
- Initialize test data
- Start fresh transaction (if applicable)
```

**After each test:**
```
- Rollback transaction (if applicable)
- Clean up temp files
- Reset mocks
- Verify no resource leaks
```

**After all tests:**
```
- Tear down database
- Stop mock servers
- Cleanup test artifacts
```

### Test Data Strategy

**Fixtures:**
- Location: `path/to/fixtures/`
- Format: JSON/YAML/SQL
- Usage: Load for deterministic tests

**Factories:**
- Location: `path/to/factories/`
- Purpose: Generate valid test data on-demand
- Examples: user factory, product factory

**Builders:**
- Pattern: Fluent API for constructing test objects
- Purpose: Readable test setup with defaults

### CI/CD Integration

**Test execution:**
- Run on: every commit, every PR, before deploy
- Parallelization: [Yes/No, strategy]
- Timeout: [Maximum execution time]
- Retry logic: [Flaky test handling]

**Coverage requirements:**
- Minimum overall: [percentage]%
- Minimum per-file: [percentage]%
- Block merge if below threshold: [Yes/No]

**Failure handling:**
- Fail fast: [Yes/No]
- Generate reports: [Format, location]
- Notify: [Where, how]

---

## Coverage Baseline

**Current coverage** (as of <ISO-8601-timestamp>):
- Unit tests: X%
- Integration tests: Y%
- E2E tests: Z%
- Overall: W%

**Target coverage:**
- Unit tests: [target]%
- Integration tests: [target]%
- E2E tests: [target]%
- Timeline: [When to achieve]

**Coverage gaps:**
1. [Untested area 1] - Reason and plan
2. [Untested area 2] - Reason and plan

---

## Related Documentation

**Specifications:**
- `[[metadata/<spec-slug>]]` - Why this feature exists

**Component index:**
- `[[code_index/<component-slug>]]` - Component structure and dependencies

**Patterns:**
- `[[patterns/<pattern-slug>]]` - Design decisions affecting tests

**Anchors:**
- `[[memory_anchors/<anchor-slug>]]` - Invariants enforced by tests

**Operational guides:**
- `[[cheatsheets/<cheatsheet-slug>]]` - How to run/debug tests

---

## References

- Test framework documentation
- Coverage report links
- CI/CD pipeline configuration
- GitHub permalinks (if applicable)
```

---

## Rules

* **No placeholders.** Fill all frontmatter fields with actual values using common sense.
* **Concrete over abstract**: Specific test scenarios, not "test the happy path."
* **Map to invariants**: Every edge case should reference a `memory_anchors/` invariant.
* Must include at least one `metadata/` relation and one `code_index/` relation in `ontological_relations`.
* **Validate before saving**: Run `kb-claude validate --strict` - document must pass before committing.
* Define correctness **before** implementation - this is TDD.
* Link acceptance criteria directly to test cases.
* Make observability plans actionable, not aspirational.
* Update coverage baseline after test runs.

---

## Follow-ups & Updates

When tests evolve or coverage improves:

1. **Append update section:**
   ```
   ## Updates [ISO-8601-timestamp]
   
   **Coverage changes:**
   - Unit: X% → Y% (+Z%)
   - Integration: X% → Y% (+Z%)
   - E2E: X% → Y% (+Z%)
   
   **New test scenarios:**
   - [Scenario]: [Why added]
   
   **Fixed gaps:**
   - [Gap]: [How addressed]
   
   **New edge cases discovered:**
   - [Edge case]: [How handling]
   ```

2. **Update `updated_at` timestamp** to current time

3. **Update `coverage_baseline` in frontmatter** with latest numbers

4. **Re-validate:**
   ```bash
   kb-claude validate --strict
   kb-claude manifest
   ```

5. **Link new discoveries:**
   ```bash
   kb-claude link qa/<slug> memory_anchors/<new-invariant>
   ```

---

## Global Documentation Guardrails

* **Critical ordering**:
  * Always read mentioned files fully **before** analysis.
  * Validate frontmatter **before** saving: `kb-claude validate --strict` must pass.
  * Update manifest after every write: `kb-claude manifest`.

* **Frontmatter consistency**:
  * Use exact format: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`
  * Snake_case keys (`created_at`, `updated_at`, `coverage_baseline`).
  * **CRITICAL**: Quote all ontological relations: `relates_to: "[[slug]]"` (prevents YAML parsing errors)
  * **CRITICAL**: Filename MUST equal link field (slugified title, NOT timestamps)
  * Timestamps in ISO-8601 format: `2025-10-25T14:30:00Z`
  * **No placeholders.** Fill with actual values using common sense.
  * Example: Title "Auth Service QA Verification" → `link: auth-service-qa-verification` → filename: `auth-service-qa-verification.md`

* **Graph hygiene**:
  * Every QA doc must link to its `metadata/` spec.
  * Link to `code_index/` for component under test.
  * Link to `memory_anchors/` for invariants being verified.
  * Link to `patterns/` for design constraints affecting tests.
  * Use `[[slug]]` syntax for all ontological relations.

* **kb-claude ops**:
  * `kb-claude new "<Title>" --type qa`
  * `kb-claude validate --strict` (REQUIRED before saving)
  * `kb-claude manifest` (update after every write)
  * `kb-claude link <source-slug> <target-slug>`
  * `kb-claude search "<keyword>"`

* **Punitive rules**:
  * DO NOT write implementation code - focus on defining correctness.
  * DO NOT save with placeholders.
  * Documents failing `kb-claude validate --strict` **must not** be saved.
  * YOU WILL BE PUNISHED FOR SAVING INVALID DOCUMENTS.

---

## Important Notes

- **TDD means tests first**: Define what "correct" means before implementation
- **Tests as documentation**: QA docs should be readable specifications
- **Edge cases map to anchors**: Connect test scenarios to system invariants
- **Observability is part of correctness**: If you can't measure it, you can't verify it
- **Coverage is a baseline, not a goal**: Focus on meaningful tests, not percentages
- **Update as you learn**: New edge cases discovered in production → add to QA doc
- **Link bidirectionally**: qa ↔ spec ↔ code_index ↔ anchors ↔ patterns
- **Make tests maintainable**: Clear naming, good setup/teardown, minimal mocking
- **Property-based testing**: When invariants are well-defined, generate test cases automatically
- **Security is correctness**: Include security test scenarios for all external inputs

