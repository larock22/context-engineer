---
allowed-tools: View, Bash(kb-claude:*), Bash(rg:*), Bash(find:*), Bash(wc:*)
description: Check knowledge base health and coherence. Review the manifest to identify gaps, broken links, coverage issues, and overall system vitality.
writes-to: none
---

# Manifest Health & System Reflection

You are tasked with inspecting the knowledge base health through the manifest—the automatically generated heartbeat that shows what's been defined, tested, anchored, and learned. This is about **reflection and diagnosis**, not creation.

### Purpose

**The heartbeat.** The manifest is your system-level reflection—showing what's been defined, tested, anchored, and learned. When updated automatically, it proves that the loop is alive and the knowledge base is coherent. This instruction helps you read that heartbeat, identify gaps, spot inconsistencies, and recommend improvements.

Acts as your **health monitoring layer** — verifying the knowledge base is complete, consistent, and actively maintained.

### Initial Setup (auto-reply)

```
I'll analyze the knowledge base health by reviewing the manifest and checking for gaps, stale content, broken links, and coverage issues across all six phases of the loop.
```

## Workflow (strict order)

1. **Regenerate manifest:**
   ```bash
   kb-claude manifest
   ```
   This ensures you're working with the latest state.

2. **Read the manifest:**
   ```bash
   cat .claude/manifest.md
   ```
   Understand the current knowledge base structure.

3. **Check phase coverage:**
   - **Metadata** (specs): Do we have clear requirements?
   - **Cheatsheets** (operational): Can someone run things?
   - **Patterns** (architectural): Are decisions documented?
   - **Code Index** (structural): Are components mapped?
   - **QA** (correctness): Are tests specified?
   - **Memory Anchors** (invariants): Are standards defined?
   - **Debug History** (learning): Are incidents captured?

4. **Identify gaps:**
   - Specs without code indexes
   - Code indexes without QA docs
   - QA docs without tests
   - Patterns without anchors
   - Anchors without enforcement
   - Components without cheatsheets
   - Incidents without back-propagation

5. **Check link integrity:**
   ```bash
   # Find all ontological_relations
   rg -A 10 'ontological_relations:' .claude/
   
   # Check for broken links
   # Look for [[slug]] references that don't exist
   ```

6. **Review freshness:**
   - When was each document last updated?
   - Are there stale documents (>6 months)?
   - Are recent incidents reflected in patterns/anchors?
   - Are new features documented?

7. **Report findings:**
   - Overall health score
   - Critical gaps
   - Recommended actions
   - Priority order

---

## Health Check Categories

### 1. Completeness

**Questions:**
- Does every major component have a code index?
- Does every feature have a spec in metadata?
- Does every critical flow have QA verification?
- Are key invariants captured as anchors?
- Are recurring incidents documented?

**Indicators:**
- Code directories without corresponding code_index docs
- Features in code without metadata specs
- Components without QA coverage
- Implicit standards not captured as anchors

### 2. Consistency

**Questions:**
- Do all documents follow the validation schema?
- Are ontological_relations bidirectional?
- Do all anchors have UUIDs?
- Are timestamps in ISO-8601 format?
- Do filenames match link fields?

**Check:**
```bash
kb-claude validate --strict
```

### 3. Connectivity

**Questions:**
- Are documents isolated or well-linked?
- Do specs link to patterns and code indexes?
- Do QA docs link to specs and code indexes?
- Do patterns reference anchors?
- Do incidents back-propagate to patterns/anchors?

**Indicators:**
- Documents with zero ontological_relations
- Specs without code_index links
- QA docs without anchor references
- Debug histories without follow-up actions

### 4. Currency

**Questions:**
- When was the last update to each phase?
- Are there stale documents (>6 months)?
- Do recent incidents have post-mortems?
- Are anchors being audited per schedule?

**Check:**
```bash
# Find documents older than 6 months
find .claude -name "*.md" -mtime +180

# Check last update across phases
rg "updated_at:" .claude/ | sort
```

### 5. Coverage

**Questions:**
- What percentage of components have code indexes?
- What percentage have QA docs?
- What percentage of patterns have anchors?
- Are all critical systems documented?

**Metrics:**
- Components mapped / Total components
- Tests specified / Components
- Anchors defined / Patterns
- Incidents documented / Production issues

### 6. Loop Vitality

**Questions:**
- Are incidents feeding back into patterns?
- Are patterns referencing anchors?
- Are QA docs verifying anchors?
- Is new learning captured?

**Indicators:**
- Recent debug_history entries
- Patterns updated after incidents
- Anchors created from lessons learned
- Cheatsheets for new procedures

---

## Health Report Template

```markdown
# Knowledge Base Health Report

**Generated**: <ISO-8601-timestamp>
**Manifest entries**: <count>

## Overall Health: 🟢 Healthy | 🟡 Needs Attention | 🔴 Critical Gaps

---

## Phase Coverage

| Phase | Count | Last Updated | Status |
|-------|-------|--------------|--------|
| Metadata (specs) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| Cheatsheets (ops) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| Patterns (arch) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| Code Index (structure) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| QA (tests) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| Memory Anchors (standards) | X | YYYY-MM-DD | 🟢/🟡/🔴 |
| Debug History (learning) | X | YYYY-MM-DD | 🟢/🟡/🔴 |

---

## Critical Gaps

1. **[Gap Type]**: [Description]
   - Impact: [What's affected]
   - Recommendation: [Action to take]
   - Priority: High/Medium/Low

---

## Link Integrity

- Total ontological_relations: X
- Potential broken links: Y
- Isolated documents (0 links): Z

**Broken links found:**
- `[[slug]]` referenced in `doc.md` but doesn't exist

---

## Freshness Issues

**Stale documents (>6 months):**
- `path/to/doc.md` - Last updated: YYYY-MM-DD

**Missing updates:**
- Recent incident without post-mortem
- New feature without spec
- New component without code index

---

## Loop Health

**Back-propagation check:**
- Recent incidents: X
- Incidents with follow-up actions: Y
- Patterns updated from incidents: Z
- Anchors created from learnings: W

**Status**: 🟢 Loop is active | 🟡 Some gaps | 🔴 Loop broken

---

## Recommendations

### High Priority
1. [Action] - [Reason]
2. [Action] - [Reason]

### Medium Priority
1. [Action] - [Reason]
2. [Action] - [Reason]

### Low Priority
1. [Action] - [Reason]

---

## Next Audit

- Recommended: <date>
- Frequency: Monthly/Quarterly
```

---

## Quick Health Checks

### One-Liner Checks

```bash
# Total documents per phase
find .claude/metadata -name "*.md" | wc -l
find .claude/cheatsheets -name "*.md" | wc -l
find .claude/patterns -name "*.md" | wc -l
find .claude/code_index -name "*.md" | wc -l
find .claude/qa -name "*.md" | wc -l
find .claude/memory_anchors -name "*.md" | wc -l
find .claude/debug_history -name "*.md" | wc -l

# Validation status
kb-claude validate --strict

# Last 5 updates
find .claude -name "*.md" -type f -printf '%T@ %p\n' | sort -rn | head -5

# Documents with no ontological_relations
rg -L "ontological_relations:" .claude/

# Potential broken links
rg '\[\[([^\]]+)\]\]' .claude/ -o | sort -u > /tmp/links.txt
# Compare against actual files

# Stale anchors needing audit
rg "next_audit:" .claude/memory_anchors/ | grep -v "2025"
```

---

## When to Run Health Checks

### Regular Schedule
- **Weekly**: Quick validation check
- **Monthly**: Full health report
- **Quarterly**: Deep audit with recommendations
- **After major incidents**: Verify back-propagation
- **Before releases**: Ensure documentation current

### Trigger Events
- After significant codebase changes
- When onboarding new team members
- Before architecture reviews
- When finding outdated documentation
- If loop feels "broken" or stale

---

## Rules

* **Read-only operation**: This instruction doesn't create documents, only inspects
* **Run validation first**: Always start with `kb-claude validate --strict`
* **Evidence-based**: Report actual findings, not assumptions
* **Actionable recommendations**: Specific tasks, not vague suggestions
* **Regular cadence**: Health checks should be routine, not reactive

---

## Important Notes

- **Manifest is auto-generated**: Never manually edit `.claude/manifest.md`
- **Health checks reveal gaps**: Use findings to guide new documentation
- **Loop vitality matters**: Knowledge base without learning is dead
- **Freshness indicates activity**: Stale docs suggest abandoned systems
- **Links create value**: Isolated docs are less useful
- **Back-propagation is critical**: Incidents must update knowledge base
- **Validation is foundational**: Invalid docs break tooling
- **Coverage over volume**: Better to have complete docs for critical systems than incomplete docs for everything
- **Report trends**: Improving/declining health over time matters
- **Use findings to prioritize**: Guide which instruction files to invoke next

---

## Integration with Other Instructions

After a health check, you might invoke:

- **0-meta-research**: To fill spec gaps
- **1-documentation**: To create missing cheatsheets/patterns
- **2-code-index**: To map undocumented components
- **3-tdd**: To specify tests for uncovered areas
- **4-code-standard-anchors**: To formalize implicit standards
- **5-monitoring-introspection**: To document undocumented incidents

**Health checks guide work prioritization.**

---

## The Heartbeat Metaphor

A healthy knowledge base has:
- **Regular rhythm**: Consistent updates across phases
- **Strong pulse**: Active back-propagation from incidents
- **Good circulation**: Links connecting all parts
- **No blockages**: No isolated or stale documents
- **Responsive**: Adapts to codebase changes
- **Sustainable**: Can be maintained long-term

When the manifest shows activity across all phases, bidirectional links, recent updates, and incident learnings feeding back into standards—**the loop is alive**. 
