---
title: Context Engineer Instruction File System
link: context-engineer-instruction-file-system
type: metadata
ontological_relations:
  - relates_to: "[[documentation-format-standardization-for-kb-claude]]"
  - relates_to: "[[0-meta-research]]"
  - relates_to: "[[1-documentation]]"
tags:
  - documentation
  - instruction-files
  - slash-commands
  - frontmatter
  - kb-claude
  - context-engineer
created_at: 2025-10-25T20:14:00Z
updated_at: 2025-10-25T20:14:00Z
uuid: 20ef9a99-7b15-4b41-bb3d-5463e2df11db
---

## Summary

The Context Engineer project uses **instruction files** that function as custom slash commands for AI agents. These Markdown files with frontmatter define specialized agent behaviors for research, documentation, code indexing, and Q&A. Each instruction file specifies which tools an agent can use, what directories it writes to, and detailed workflows for completing tasks.

This document serves as a **guide for team members** to understand and create new instruction files following the validated kb-claude format. All documentation must pass `kb-claude validate --strict` before being committed.

## Research Findings

### System Architecture

The Context Engineer system is built around specialized instruction files that agents invoke to perform specific tasks. These files are inspired by Claude Code's slash command system but adapted for knowledge management and codebase documentation.

**Core Instruction Files:**
- `0-meta-research.md` - Multi-agent research system that spawns sub-agents to explore codebases
- `1-documentation.md` - Documentation agent that creates cheatsheets and patterns from tribal knowledge
- `qa.md` - Q&A response system (planned)
- `code-index.md` - Code indexing and navigation (planned)

### Frontmatter Structure

Following Claude Code's slash command pattern, each instruction file uses YAML frontmatter to define agent constraints and capabilities:

```yaml
---
allowed-tools: Edit, View, Bash(git:*), Bash(python:*), ...
description: Brief description of what this agent does
writes-to: .claude/metadata, .claude/patterns
---
```

**Key Fields:**
- `allowed-tools` - Comma-separated list of permitted tools and bash commands
- `description` - One-sentence summary of agent purpose
- `writes-to` - Target directories for generated documentation

### Tool Permission Patterns

Based on Claude Code's permission system:

**Wildcard patterns:**
```yaml
allowed-tools: Bash(git:*)      # All git commands
allowed-tools: Bash(pytest:*)   # All pytest commands
allowed-tools: Edit             # All file edits
```

**Specific commands:**
```yaml
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
```

### Generated Document Requirements

Documents created by these agents MUST follow kb-claude validation rules:

#### CRITICAL Requirements

1. **Quote all `[[slug]]` references in ontological_relations:**
```yaml
ontological_relations:
  - relates_to: "[[metadata/some-doc]]"  # ✅ CORRECT
  - relates_to: [[metadata/some-doc]]    # ❌ FAILS VALIDATION
```

2. **Filename must equal link field (slugified title):**
```yaml
title: Authentication Flow Analysis
link: authentication-flow-analysis
# Save as: authentication-flow-analysis.md
# NOT: 2025-10-25_auth.md
```

3. **Required frontmatter fields:**
```yaml
title: <descriptive title>
link: <kebab-case-slug-from-title>
type: metadata|cheatsheet|pattern
ontological_relations:
  - relates_to: "[[type/slug]]"
tags:
  - tag1
  - tag2
created_at: 2025-10-25T20:14:00Z
updated_at: 2025-10-25T20:14:00Z
uuid: 20ef9a99-7b15-4b41-bb3d-5463e2df11db
```

### Validation Workflow

All documents must pass validation before being saved:

```bash
# 1. Create document with proper frontmatter
# 2. Validate
kb-claude validate --strict

# 3. Update manifest
kb-claude manifest

# 4. Commit only if validation passes
```

## Architectural Patterns

### Multi-Agent Research Pattern (0-meta-research.md)

**Purpose:** Conduct comprehensive codebase research by spawning parallel sub-agents.

**Key Workflow:**
1. Read mentioned files fully (no paging)
2. Analyze and decompose research question
3. Spawn max 3 parallel sub-agents (codebase-locator, codebase-analyzer, context-synthesis)
4. Wait for all sub-agents to complete
5. Synthesize findings with concrete file paths and evidence
6. Generate metadata document with proper frontmatter
7. Validate and update manifest

**Output:** `.claude/metadata/<slugified-title>.md`

**Critical Rules:**
- Maximum 3 sub-agents per query (hard limit)
- Deploy sub-agents IN PARALLEL (punishment for sequential)
- Never write placeholder values
- Always validate before saving

### Documentation Agent Pattern (1-documentation.md)

**Purpose:** Document unexplored territory using agent judgment to create cheatsheets or patterns.

**Decision Framework:**
- **Cheatsheet** - Operational "how-to" (steps, commands, procedures)
- **Pattern** - Architectural "why" (decisions, tradeoffs, alternatives)
- **Both** - Complex areas needing implementation + rationale

**Key Workflow:**
1. Read mentioned files fully
2. Analyze and decide: cheatsheet, pattern, or both
3. Locate truth sources (metadata, memory_anchors)
4. Draft outline following appropriate template
5. Cross-check all statements against concrete evidence
6. Assemble document(s)
7. Add GitHub permalinks if applicable
8. Validate and update manifest

**Output:** 
- `.claude/cheatsheets/<slugified-title>.md`
- `.claude/patterns/<slugified-title>.md`

**Agent Autonomy:**
- Agent decides what documentation type to create
- Must explain reasoning briefly before creating
- Focus on tribal knowledge and implicit patterns

### Knowledge Base Structure

Following kb-claude conventions:

```
.claude/
  metadata/           # Research findings and analysis
  debug_history/      # Debugging timelines
  qa/                 # Q&A and learning notes
  code_index/         # File or module references
  patterns/           # Architectural decisions
  cheatsheets/        # Operational runbooks
  memory_anchors/     # Core concepts tracked by UUID
  manifest.md         # Auto-generated summary
```

Each instruction file maps to specific output directories via `writes-to` frontmatter.

## Key Files

- `/home/tuna/context-engineer/0-meta-research.md` - Research agent instruction file
- `/home/tuna/context-engineer/1-documentation.md` - Documentation agent instruction file
- `.claude/metadata/documentation-format-standardization-for-kb-claude.md` - Format specification
- `.claude/manifest.md` - Auto-generated knowledge base index

## How to Create New Instruction Files

### Step 1: Define Agent Purpose

Decide what specific task this agent will perform:
- Research a specific domain?
- Generate specific documentation type?
- Perform code analysis?
- Answer questions?

### Step 2: Create Frontmatter

```yaml
---
allowed-tools: Edit, View, Bash(<command>:*)
description: One-sentence description of agent purpose
writes-to: .claude/<target-directory>
---
```

### Step 3: Document Workflows

Provide numbered steps with:
- Clear prerequisites
- Specific actions to take
- Validation requirements
- Output format specifications

### Step 4: Include Agent Instructions

For generated documents, include:
- Field-by-field frontmatter guidance
- Template with proper quoting
- Example with actual values (not placeholders)
- Validation command requirements

### Step 5: Add Critical Rules

Document:
- Hard limits (e.g., max sub-agents)
- Formatting requirements (quoting, timestamps)
- Validation workflow
- Punishment warnings for non-compliance

### Step 6: Validate Structure

Ensure the instruction file:
- Has complete frontmatter
- References correct kb-claude commands
- Includes validation requirements
- Specifies filename = link field requirement
- Shows proper `[[slug]]` quoting in examples

## Common Pitfalls

### ❌ Incorrect Ontological Relations

```yaml
# WRONG - Will fail YAML parsing
ontological_relations:
  - relates_to: [[metadata/doc]]
```

```yaml
# CORRECT - Quoted strings
ontological_relations:
  - relates_to: "[[metadata/doc]]"
```

### ❌ Timestamp-Based Filenames

```bash
# WRONG
.claude/metadata/2025-10-25_20-05-05_auth-analysis.md
```

```bash
# CORRECT - Filename matches link field
.claude/metadata/auth-analysis.md
```

```yaml
title: Auth Analysis
link: auth-analysis  # Must match filename
```

### ❌ Placeholder Values

```yaml
# WRONG
title: <your title here>
uuid: <generate-uuid>
```

```yaml
# CORRECT - Actual values
title: Authentication Flow Analysis
uuid: 20ef9a99-7b15-4b41-bb3d-5463e2df11db
```

### ❌ Skipping Validation

```bash
# WRONG - Saving without validation
git add .claude/metadata/new-doc.md
git commit
```

```bash
# CORRECT - Always validate first
kb-claude validate --strict
kb-claude manifest
git add .claude/metadata/new-doc.md .claude/manifest.md
git commit
```

## Best Practices

1. **Always validate before committing:**
   ```bash
   kb-claude validate --strict && kb-claude manifest
   ```

2. **Use actual timestamps:**
   ```bash
   date -u +"%Y-%m-%dT%H:%M:%SZ"
   # 2025-10-25T20:14:00Z
   ```

3. **Generate proper UUIDs:**
   ```bash
   uuidgen | tr '[:upper:]' '[:lower:]'
   # 20ef9a99-7b15-4b41-bb3d-5463e2df11db
   ```

4. **Slugify titles for filenames:**
   ```
   Title: "My Great Feature Analysis"
   Link: my-great-feature-analysis
   Filename: my-great-feature-analysis.md
   ```

5. **Quote all wiki-style links:**
   ```yaml
   ontological_relations:
     - relates_to: "[[metadata/related-doc]]"
     - relates_to: "[[patterns/design-pattern]]"
   ```

6. **Include concrete examples:**
   - Show actual file paths, not `<path/to/file>`
   - Use real UUIDs, not `<uuid>`
   - Provide actual timestamps, not `<timestamp>`

## Team Collaboration

### Adding New Instruction Files

1. Create new `.md` file in project root (e.g., `3-qa-system.md`)
2. Follow frontmatter format from existing files
3. Document clear workflows with validation requirements
4. Test by having agent execute the instructions
5. Update this document with the new file pattern

### Reviewing Instruction Files

When reviewing PRs with instruction file changes:
- ✅ Check frontmatter completeness
- ✅ Verify allowed-tools make sense
- ✅ Ensure validation requirements documented
- ✅ Confirm examples use proper quoting
- ✅ Check that filename requirements are clear

### Extending the System

New instruction files should:
- Map to a specific `.claude/` subdirectory
- Define clear `allowed-tools` constraints
- Include validation workflow
- Document output format requirements
- Provide agent autonomy guidelines

## References

- Claude Code Slash Commands: https://docs.claude.com/en/docs/claude-code/slash-commands
- kb-claude Documentation: `.claude/kb-claude.md`
- Format Standardization: `.claude/metadata/documentation-format-standardization-for-kb-claude.md`
- Existing Instruction Files: `0-meta-research.md`, `1-documentation.md`

## Validation Checklist

Before committing any instruction file or generated document:

- [ ] Frontmatter has all required fields
- [ ] `ontological_relations` uses quoted `"[[slug]]"` syntax
- [ ] `link` field matches filename (no timestamps)
- [ ] `created_at`/`updated_at` in ISO-8601 format
- [ ] `uuid` is valid UUID v4
- [ ] No placeholder values (all fields filled)
- [ ] `kb-claude validate --strict` passes
- [ ] `kb-claude manifest` updated
- [ ] Agent instructions include validation requirements
- [ ] Examples show proper formatting

This checklist ensures consistency across all documentation and prevents validation failures.

