---
title: Fix kb-claude Slugified Title Mismatch
link: fix-kb-claude-slugified-title-mismatch
type: cheatsheets
ontological_relations:
  - relates_to: "[[documentation-format-standardization-for-kb-claude]]"
  - relates_to: "[[context-engineer-instruction-file-system]]"
tags:
  - operations
  - runbook
  - kb-claude
  - validation
  - troubleshooting
created_at: 2025-10-25T20:16:35Z
updated_at: 2025-10-25T20:16:35Z
uuid: 6d126194-45d6-4fcc-ba34-31440adcee13
---

## Summary

This runbook addresses the recurring `kb-claude validate --strict` warning: "link should match slugified title". This happens when the `link` field in frontmatter doesn't exactly match the slugified version of the `title` field. The error requires renaming the file and updating the link field to match the slugified title.

## Constraints

- Filename MUST exactly match the `link` field
- `link` field MUST exactly match the slugified `title`
- Slugification rules: lowercase, replace spaces with hyphens, remove special characters
- Cannot use timestamps in filenames (e.g., `2025-10-25_doc.md` is invalid)

## Risks

- **Data loss**: Renaming files incorrectly can break git history
- **Broken references**: Other documents linking to old filename will break
- **Validation failure**: Document cannot be committed until fixed

## Prerequisites

- kb-claude CLI installed
- Document already created with frontmatter
- Access to terminal in project directory

## Steps

### 1. Run validation to identify the issue

```bash
kb-claude validate --strict
```

**Expected output showing the error:**
```
warning: ./.claude/metadata/my-doc.md — `link` `my-document` should match slugified title `my-great-document`
```

This tells you:
- Current filename: `my-doc.md`
- Current link field: `my-document`
- Required link (slugified title): `my-great-document`

### 2. Determine correct slugified title

**Slugification rules:**
- Convert to lowercase
- Replace spaces with hyphens `-`
- Remove special characters (keep only alphanumeric and hyphens)
- Remove leading/trailing hyphens

**Examples:**
```
"Authentication Flow Analysis" → "authentication-flow-analysis"
"Context Engineer System" → "context-engineer-system"
"Fix kb-claude Errors" → "fix-kb-claude-errors"
"API v2.0 Migration" → "api-v20-migration"
"User's Guide" → "users-guide"
```

### 3. Update the link field in frontmatter

Edit the document and change the `link` field to match the slugified title:

```yaml
---
title: My Great Document
link: my-great-document  # ← Update this to match slugified title
type: metadata
# ... rest of frontmatter
---
```

### 4. Rename the file to match the link field

```bash
# From the project root
cd .claude/metadata  # or cheatsheets/ or patterns/

# Rename: old-filename.md → slugified-title.md
mv my-doc.md my-great-document.md
```

**Important:** The new filename must be `<link-value>.md`

### 5. Verify the fix

```bash
kb-claude validate --strict
```

**Expected output (success):**
```
Validated .claude hierarchy at /path/to/project/.claude; no issues found.
```

### 6. Update manifest and commit

```bash
kb-claude manifest
git add .claude/
git commit -m "fix: correct slugified title for my-great-document"
```

## Verification

**Checks:**
- [ ] `kb-claude validate --strict` passes with no warnings
- [ ] Filename matches link field exactly
- [ ] Link field matches slugified title exactly
- [ ] Manifest updated successfully
- [ ] No broken references in other documents

**Acceptance signal:**
```bash
kb-claude validate --strict && echo "✅ Validation passed"
```

## Rollback

If something goes wrong:

```bash
# 1. Undo git changes
git restore .claude/

# 2. Or manually rename file back
cd .claude/<directory>
mv new-name.md old-name.md

# 3. Restore original frontmatter
# Edit file and change link field back

# 4. Re-validate
kb-claude validate --strict
```

## Common Failure Modes

### Symptom: Still getting validation warning after rename

**Likely cause:** Typo in link field or filename

**Quick check:**
```bash
# Check exact link value
grep "^link:" .claude/metadata/my-doc.md

# Check actual filename
ls -la .claude/metadata/my-doc.md
```

**Remediation:** Ensure link field and filename are EXACTLY the same (case-sensitive)

---

### Symptom: Validation says link doesn't match title

**Likely cause:** Title was slugified incorrectly

**Quick check:**
```bash
# What's the title?
grep "^title:" .claude/metadata/my-doc.md

# Manual slugification:
# 1. Convert to lowercase
# 2. Replace spaces with -
# 3. Remove special chars
```

**Remediation:** 
- Use online slugify tool or write a quick script
- Update both link field AND filename

---

### Symptom: Can't find the file after renaming

**Likely cause:** Renamed to wrong directory or wrong name

**Quick check:**
```bash
# Find all markdown files
find .claude -name "*.md" -type f

# Search for content
grep -r "title: My Document" .claude/
```

**Remediation:**
- Use git status to see what was renamed
- Move file to correct location
- Verify link field matches new location

---

### Symptom: Other documents now have broken links

**Likely cause:** Other docs referenced old filename

**Quick check:**
```bash
# Search for references to old filename
grep -r "old-filename" .claude/

# Search in ontological_relations
grep -r "relates_to.*old-filename" .claude/
```

**Remediation:**
```bash
# Update all references to new filename
# In each referencing document:
# Change: relates_to: "[[metadata/old-filename]]"
# To:     relates_to: "[[metadata/new-filename]]"

# Validate all docs
kb-claude validate --strict
```

## Prevention Checklist

Before creating any new document:

1. **Write title first**, then slugify it for link field
2. **Use slugified title as filename immediately**
3. **Never use timestamps in filenames** (use `created_at` field instead)
4. **Validate before first commit:**
   ```bash
   kb-claude validate --strict
   ```
5. **Use this helper to generate proper names:**
   ```bash
   # Generate slugified version of title
   echo "My Document Title" | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | sed 's/[^a-z0-9-]//g'
   ```

## Quick Reference

**Slugification command:**
```bash
# Convert title to slug
TITLE="Your Document Title Here"
SLUG=$(echo "$TITLE" | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | sed 's/[^a-z0-9-]//g' | sed 's/--*/-/g' | sed 's/^-//;s/-$//')
echo "link: $SLUG"
echo "filename: $SLUG.md"
```

**Common patterns:**
```
Title                                  → link + filename
"Authentication Flow"                  → authentication-flow.md
"Fix kb-claude Validation"            → fix-kb-claude-validation.md
"Context Engineer System v2"          → context-engineer-system-v2.md
"User's Guide to API"                  → users-guide-to-api.md
```

## References

- kb-claude validation: Run `kb-claude validate --help`
- Format specification: `.claude/metadata/documentation-format-standardization-for-kb-claude.md`
- Instruction system: `.claude/metadata/context-engineer-instruction-file-system.md`
- Claude Code docs: https://docs.claude.com/en/docs/claude-code/slash-commands

