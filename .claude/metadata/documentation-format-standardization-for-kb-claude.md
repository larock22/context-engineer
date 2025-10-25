---
title: Documentation Format Standardization for kb-claude
link: documentation-format-standardization-for-kb-claude
type: metadata
ontological_relations:
  - relates_to: "[[0-meta-research]]"
  - relates_to: "[[1-documentation]]"
tags:
  - documentation
  - standards
  - kb-claude
  - frontmatter
  - validation
created_at: 2025-10-25T20:05:05Z
updated_at: 2025-10-25T20:05:05Z
uuid: 40706dba-095e-4264-b9e6-e3e6658f99fc
---

## Summary

Updated documentation instruction files (`0-meta-research.md` and `1-documentation.md`) to use correct frontmatter format compatible with `kb-claude validate --strict`. The new format uses `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, and `uuid` fields with proper ISO-8601 timestamps and `[[slug]]` syntax for ontological relations.

This standardization enables proper validation, manifest generation, and knowledge graph navigation across all documentation types: metadata (research), cheatsheets (operational runbooks), and patterns (architectural decisions).

## Research Findings

### Original Format Issues

The original frontmatter format in both instruction files used an incorrect structure:
- Used `id` instead of `uuid`
- Used `created`/`updated` instead of `created_at`/`updated_at`
- Used `links`, `anchors`, `files`, `related` as separate arrays instead of unified `ontological_relations`
- Missing `link` field for kebab-case slug
- Had placeholders in examples without clear agent instructions

### New Standardized Format

**Core Fields:**
- `title`: Descriptive title (4-10 words depending on document type)
- `link`: Kebab-case slug derived from title
- `type`: Document type (`metadata`, `cheatsheet`, or `pattern`)
- `ontological_relations`: Unified relations using `relates_to: [[slug]]` syntax
- `tags`: Array of relevant keywords for discoverability
- `created_at`: ISO-8601 timestamp (e.g., `2025-10-25T20:05:05Z`)
- `updated_at`: ISO-8601 timestamp
- `uuid`: Valid UUID v4

**Example:**
```yaml
---
title: Example Documentation Title
link: example-documentation-title
type: metadata
ontological_relations:
  - relates_to: "[[metadata/other-doc]]"
  - relates_to: "[[memory_anchors/key-anchor]]"
tags:
  - component
  - concept
created_at: 2025-10-25T20:05:05Z
updated_at: 2025-10-25T20:05:05Z
uuid: 40706dba-095e-4264-b9e6-e3e6658f99fc
---
```

### Agent Instructions Added

Both instruction files now include explicit "Agent Instructions" sections that:
- Mandate filling all fields with common sense (no placeholders)
- Provide clear examples of proper formats
- Require validation before saving: `kb-claude validate --strict` must pass
- Emphasize ontological relations enable knowledge graph navigation

### Validation Requirements

**Mandatory validation workflow:**
1. Create document with proper frontmatter
2. Run `kb-claude validate --strict` before saving
3. Documents failing validation must not be saved
4. Run `kb-claude manifest` after every successful save
5. Punishment warnings added for non-compliance

## Architectural Patterns

### Knowledge Graph via Ontological Relations

The `ontological_relations` field unifies all document relationships:
- Links to related metadata research documents
- Links to operational cheatsheets
- Links to architectural patterns
- Links to memory anchors (invariants/key decisions)
- Uses `[[slug]]` syntax for wiki-style linking

This enables bidirectional navigation and creates a knowledge graph of all documentation.

### Document Type Specialization

Three document types serve distinct purposes:

1. **Metadata** (`.claude/metadata/`)
   - Research findings and analysis
   - Written by `0-meta-research.md` agent
   - Focuses on "what exists and why"

2. **Cheatsheets** (`.claude/cheatsheets/`)
   - Operational runbooks
   - Written by `1-documentation.md` agent
   - Focuses on "how to execute tasks"

3. **Patterns** (`.claude/patterns/`)
   - Architectural decisions
   - Written by `1-documentation.md` agent
   - Focuses on "design rationale and tradeoffs"

### Agent Autonomy for Documentation

The `1-documentation.md` agent now has a decision framework to choose between creating cheatsheets, patterns, or both based on content analysis. This captures "unexplored" territory intelligently.

## Key Files

- `0-meta-research.md` - Research agent instruction file
- `1-documentation.md` - Documentation agent instruction file  
- `.claude/metadata/` - Research documents storage
- `.claude/cheatsheets/` - Operational runbooks storage
- `.claude/patterns/` - Architectural decisions storage

## Implementation Changes

### 0-meta-research.md Updates

**Step 5 - Gather Metadata:**
- Added checklist for frontmatter preparation
- Generate UUID v4, ISO-8601 timestamps, kebab-case slug
- Identify relevant ontological relations

**Step 6 - Generate Research Document:**
- Complete frontmatter template with agent instructions
- Body structure: Summary → Research Findings → Architectural Patterns → Key Files → References
- Validation requirement emphasized

**kb-claude Commands:**
- Clarified validation is REQUIRED before saving
- Manifest must be run after every save

### 1-documentation.md Updates

**Decision Framework Added:**
- Criteria for creating cheatsheets vs patterns vs both
- Agent autonomy to choose based on content
- Focus on "documenting the unexplored"

**Template Updates:**
- Both cheatsheet and pattern templates updated with correct frontmatter
- Agent Instructions section added to each
- Validation requirements in Rules sections

**Global Guardrails:**
- Frontmatter consistency rules updated
- Validation workflow emphasized
- Punitive rules clarified

## References

- Source files: `/home/tuna/context-engineer/0-meta-research.md`
- Source files: `/home/tuna/context-engineer/1-documentation.md`
- Testing document: This file serves as first real-world validation test

