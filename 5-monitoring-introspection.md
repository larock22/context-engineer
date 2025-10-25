---
allowed-tools: Edit, View, Bash(git:*), Bash(docker:*), Bash(kubectl:*), Bash(curl:*), Bash(jq:*), Bash(grep:*), Bash(rg:*), Bash(gh:*)
description: Document incidents, investigations, and post-mortems. Turn production reality into actionable knowledge that feeds back into patterns and metadata—closing the learning loop.
writes-to: .claude/debug_history
---

# Monitoring & Introspection

You are tasked with documenting incidents, debugging sessions, production failures, and post-mortems—capturing the journey from symptom to root cause to resolution. These living records feed insights back into patterns, anchors, and specifications, closing the learning loop. All debug history documents must be stored in the `.claude/debug_history` directory.

### Purpose

**Where reality meets theory.** Production teaches us what specs and tests miss. Every investigation, failure, and fix writes its own post-mortem here. This creates a living record of root causes, false starts, and hard-won insights. Critically, findings **back-propagate** into the knowledge base—updating patterns with new failure modes, strengthening anchors with discovered edge cases, enriching specs with real-world context.

Acts as your **learning and feedback layer** — the mechanism that keeps the knowledge base grounded in operational reality.

### Initial Setup (auto-reply)

```
Provide the incident, bug, outage, or investigation you want to document. I'll guide you through capturing the timeline, root cause analysis, resolution steps, and insights—then help you back-propagate learnings into the broader knowledge base.
```

Wait for the user's incident/investigation details.

**Document types**: Incident post-mortems, bug investigations, debugging sessions, performance issues, near misses.

## Workflow (strict order)

1. **Gather evidence**: Logs, metrics, traces, screenshots, exact timestamps (ISO-8601)
2. **Document timeline**: Detection → Investigation → Mitigation → Resolution
3. **Establish root cause**: Immediate trigger, underlying causes, missing safeguards
4. **Document resolution**: Immediate fix (tactical), permanent fix (strategic)
5. **Assess impact**: User, business, technical, severity classification
6. **Identify back-propagation actions**: What patterns/anchors/specs/tests/monitoring to update
7. **Gather metadata**: Slugified title, UUID, ISO-8601 timestamps, quoted `[[slug]]` relations
8. **Generate document**: Follow template with concrete evidence
9. **Add GitHub permalinks**: If on main/pushed
10. **Execute back-propagation**: Update patterns/anchors/specs/tests/cheatsheets
11. **Validate & manifest**: `kb-claude validate --strict && kb-claude manifest`

---

## DEBUG HISTORY Documentation

### Agent Instructions

- **Fill all fields** - use actual values, not placeholders
- `title`: Descriptive incident/bug name
- `link`: Kebab-case slug = filename (NOT timestamp-based)
- `type`: `debug_history`
- `ontological_relations`: **Quote all `[[slug]]` values**
- `tags`: incident/bug/investigation, severity, affected components
- `created_at`/`updated_at`: ISO-8601 timestamps
- `uuid`: Generate valid UUID v4
- **Validate before saving**: `kb-claude validate --strict` must pass

### Frontmatter + Body Template

```
---
title: <Descriptive Incident/Bug Name>
link: <kebab-case-slug-from-title>
type: debug_history
ontological_relations:
  - relates_to: "[[code_index/<affected-component>]]"
  - relates_to: "[[metadata/<related-spec>]]"
tags:
  - incident|bug|investigation
  - <severity>
  - <affected-component>
created_at: <ISO-8601-timestamp>
updated_at: <ISO-8601-timestamp>
uuid: <generate-uuid-v4>
incident:
  severity: P0|P1|P2|critical|major|minor
  detected_at: <ISO-8601-timestamp>
  resolved_at: <ISO-8601-timestamp>
  duration_minutes: <number>
  status: resolved|investigating|mitigated
---

## Summary
What broke, impact, root cause, resolution (2-3 sentences). Readable by non-technical stakeholders.

## Impact
- Users affected: <number>
- Functionality: <what stopped working>
- Duration: <how long>
- SLA breach: <yes/no>
- Severity: <why P0/P1/P2>

## Timeline (UTC, ISO-8601)
- `YYYY-MM-DDTHH:MM:SSZ` - First symptom observed
- `YYYY-MM-DDTHH:MM:SSZ` - Alert fired
- `YYYY-MM-DDTHH:MM:SSZ` - Root cause identified
- `YYYY-MM-DDTHH:MM:SSZ` - Incident mitigated
- `YYYY-MM-DDTHH:MM:SSZ` - Permanent fix deployed

## Symptoms & Evidence
**Observed:**
- Symptom with evidence (logs, metrics, traces)
- Alerts fired / missed

**Evidence excerpts:**
```
[2025-10-25T14:23:45Z] ERROR: <actual log line>
metric_name: value (normal: baseline)
```

## Investigation Journey
**Hypotheses tested:**
1. Hypothesis → Investigation → ❌ Ruled out / ✅ Confirmed

**Dead ends:** What didn't work and why

**Breakthrough:** What led to root cause

## Root Cause Analysis
**Immediate trigger:** What directly caused it
- Code: `path/to/file.ext:line`
- Config/external event

**Underlying causes:** Why system was vulnerable
- Systemic weakness, when introduced, why not caught

**Missing safeguards:**
- Test gap, monitoring gap, architecture gap
- Anchors violated: `[[memory_anchors/<slug>]]`

## Resolution
**Immediate fix (tactical):**
- Action, command, when, who, verification

**Permanent fix (strategic):**
- Code/config/architecture/process changes
- PR: <link>
- Why this prevents recurrence

## Back-Propagation (Close the Loop)
**These actions feed learnings back:**
- Tests added: `path/to/test` → `[[qa/<slug>]]`
- Monitoring improved: new alerts/metrics
- Patterns updated: `[[patterns/<slug>]]` - new failure mode
- Anchors created: `[[memory_anchors/<slug>]]` - new invariant
- Specs enriched: `[[metadata/<slug>]]` - new constraint
- Cheatsheet created: `[[cheatsheets/<slug>]]` - response runbook

## Action Items
**Immediate:** (before close)
- [x] Deploy fix, verify, update monitoring

**Short-term:** (1 week)
- [ ] Add test coverage - Owner, Due date
- [ ] Update docs - Owner, Due date

**Long-term:** (1 month)
- [ ] Architecture refactor - Owner, Due date

## Lessons Learned
**Went well:** Detection/resolution positives
**Went poorly:** Delays/complications
**Key insights:** What we learned, where captured
**Unanswered:** Follow-up needed

## Related
- Similar incidents: `[[debug_history/<slug>]]`
- Pattern recognition: recurring themes?
- Affected components: `[[code_index/<slug>]]`
```

---

## Rules

* **No placeholders.** Fill all frontmatter fields with actual values.
* **Evidence over speculation**: Include actual logs, metrics, traces.
* **Timeline precision**: Use exact ISO-8601 timestamps.
* **Blameless post-mortems**: Focus on systems, not people.
* **Back-propagation is mandatory**: Every incident must update the knowledge base.
* **Document false starts**: What didn't work is as valuable as what did.
* **Validate before saving**: `kb-claude validate --strict` must pass.
* Close the loop—incidents without learning are wasted pain.

---

## Back-Propagation Workflow

**The learning loop:**
```
Incident → Debug History → Lessons Learned
  ↓
  ├─→ Update Patterns (new failure modes)
  ├─→ Update Anchors (new invariants)
  ├─→ Update Specs (new constraints)
  ├─→ Update Tests (coverage gaps)
  ├─→ Create Cheatsheets (response procedures)
  └─→ Update Code Indexes (vulnerability notes)
```

**Track back-propagation:**
- Document ALL updates made
- Link debug history ↔ updated documents
- Close the loop in both directions

---

## Special Considerations

### Blameless Culture
- "The deployment process lacked verification" ✅
- "Engineer X deployed without testing" ❌
- Encourage honest reporting
- Fix the system, not the person

### Evidence Preservation
- Logs/metrics expire—capture early
- Screenshots of dashboards
- Save trace IDs and links
- Document exact commands

### Near Misses
- Document issues caught before production
- Same rigor, focus on "what could have happened"
- Verify safeguards that worked

### Pattern Recognition
- Similar root causes → systemic fix needed
- Multiple incidents with same root → architectural refactoring
- Multiple detection delays → monitoring overhaul

---

## Global Documentation Guardrails

* **Critical ordering**: Gather evidence first → timeline → root cause → resolution → back-propagation
* **Frontmatter**: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`, `incident`
* **Quote relations**: `relates_to: "[[slug]]"` prevents YAML errors
* **Filename = link field**: NOT timestamp-based
* **ISO-8601 timestamps**: `2025-10-25T14:30:00Z`
* **No placeholders**
* **Graph hygiene**: Link to `code_index/`, `memory_anchors/`, `patterns/`, `metadata/`
* **Validation**: `kb-claude validate --strict && kb-claude manifest` after every write

**Punitive rules:**
- DO NOT skip back-propagation
- DO NOT save with placeholders
- DO NOT blame individuals
- Documents failing validation **must not** be saved

---

## Important Notes

- **Close the loop**: Every incident feeds back into knowledge base
- **Evidence expires**: Capture immediately
- **Blameless culture**: Systems, not people
- **False starts matter**: Document what didn't work
- **Timeline precision**: Exact timestamps reveal patterns
- **Back-propagation mandatory**: Update patterns/anchors/specs/tests
- **Near misses count**: Don't wait for production incidents
- **Pattern recognition**: Multiple similar → systemic fix
- **Psychological safety**: Honest reporting requires trust
- **Knowledge compounds**: Each incident makes system smarter
- **Production teaches most**: What survives production is truth
