---
allowed-tools: Edit, View, Bash(git:*), Bash(python:*), Bash(pytest:*), Bash(mypy:*), Bash(black:*), Bash(coverage:*), Bash(mutmut:*), Bash(docker:*), Bash(trivy:*), Bash(hadolint:*), Bash(dive:*), Bash(npm:*), Bash(kubectl:*), Bash(helm:*), Bash(lighthouse:*), Bash(jq:*), Bash(curl:*), Bash(gh:*)
description: Multi Agent reserach and metadata documenter sysem.
writes-to: .claude/metadata
---

# Research Codebase

You are tasked with conducting comprehensive research across the codebase to answer user questions by spawning parallel sub-agents and synthesizing their findings. All research documents must be stored in the `.claude/metadata` directory, following the YAML frontmatter and content structure below.

Where intent lives.
Each feature or fix starts here: the what and why before the how.
Store concise context synthesis of the codebase.

Acts as your source of truth for context — every other folder ties back to it.

## Initial Setup:

When this command is invoked, respond with:
```
Provide your research question or area of interest, and I'll analyze it thoroughly by exploring relevant components and connections.
```

Wait for the user's research query.

We have a CLI tool called **kb-claude** that can be used to manage the knowledge base:

- `kb-claude new "Title"` – guided prompt for new entries; handles tags, relations, timestamps, UUIDs, and file placement.
- `kb-claude search keyword` – case-insensitive search across titles, tags, relations, and body text.
- `kb-claude validate --strict` – **REQUIRED before saving**; parses every entry, confirms required metadata, and flags inconsistencies.
- `kb-claude manifest` – rebuild `.claude/manifest.md`, a table summarizing every document. Run after every save.
- `kb-claude link source target` – insert reciprocal relations between two slugs.

## Steps to follow after receiving the research query:

1. **Read any directly mentioned files first:**
   - If the user mentions specific files (tickets, docs, JSON), read them FULLY first
   - **IMPORTANT**: Use the Read tool WITHOUT limit/offset parameters to read entire files
   - **CRITICAL**: Read these files yourself in the main context before spawning any sub-tasks
   - This ensures you have full context before decomposing the research

2. **Analyze and decompose the research question:**
   - Break down the user's query into composable research areas
   - Take time to ultrathink about the underlying patterns, connections, and architectural implications the user might be seeking
   - Identify specific components, patterns, or concepts to investigate
   - Create a research plan using TodoWrite to track all subtasks
   - Consider which directories, files, or architectural patterns are relevant

3. **Spawn parallel sub-agent tasks for comprehensive research:**
   - Strictly limit to a maximum of 3 Task agents per research query. You may not spawn more than 3 sub-agents for any research query, regardless of the number of aspects or subtasks. This is a hard limit.
   - Only reference files and resources that are part of the current codebase or workspace. Do not reference or include any files, tickets, or resources that are external or not present in this workspace.


   YOU MUST DEPLOY THESE 3 IN PARELLEL
   YOU WILL BE PUNISHED FOR NOT DEPLOYING SUBAGENTS IN PARELLEL

   **For codebase research:**
   - Use the **codebase-locator** agent to find WHERE files and components live
   - Use the **codebase-analyzer** agent to understand HOW specific code works
   - Use the **context-synthesis** agent to find context as needed

   You MUST use these agents intelligently:
   - Start with locator agents to find what exists
   - Then use analyzer and synthesis agents on the most promising findings
   - Run agents in parallel only if they are searching for different things, and never exceed 3 concurrent sub-agents.
   - Each agent knows its job - just tell it what you're looking for
   - Don't write detailed prompts about HOW to search the agents already know
   You WILL be punished for not deploying the agents intelligently

4. **Wait for all sub-agents to complete and synthesize findings:**
   - IMPORTANT: Wait for ALL sub-agent tasks to complete before proceeding
   - Compile all sub-agent results (both codebase and thoughts findings)
   - Prioritize live codebase findings as primary source of truth
   - Use .claude as supplementary historical context
   - Connect findings across different components
   - Include specific file paths and line numbers for reference
   - Verify all thoughts/ paths are correct
   - Highlight patterns, connections, and architectural decisions
   - Answer the user's specific questions with concrete evidence

5. **Gather metadata for the research document:**
   - **CRITICAL**: Filename must be the slugified title, NOT a timestamp
   - Example: `authentication-flow-analysis.md` (NOT `2025-10-25_20-05-05_auth.md`)
   - **Fill all frontmatter fields with common sense** - use actual values, not placeholders
   - Generate valid UUID v4
   - Use ISO-8601 timestamps
   - Create kebab-case slug from title (this becomes the filename)
   - Identify relevant ontological relations
   - **IMPORTANT**: Quote all `[[slug]]` values in ontological_relations: `"[[slug]]"`

6. **Generate research document:**
   - Use the metadata gathered in step 4 and 5
   
   **Agent Instructions:**
   - **Fill in all fields with common sense** - use actual values, not placeholders
   - `title`: Clear, descriptive research title describing what was investigated
   - `link`: Kebab-case slug derived from title (this MUST match the filename)
   - `type`: Always `metadata` for research documents
   - `ontological_relations`: **CRITICAL - Quote all `[[slug]]` values**: `"[[slug]]"` (prevents YAML parsing errors)
   - `tags`: Relevant keywords for discoverability (components, concepts, technologies)
   - `created_at`/`updated_at`: ISO-8601 format timestamps (e.g., `2025-10-25T14:30:00Z`)
   - `uuid`: Generate a valid UUID v4
   - **Filename requirement**: Save as `.claude/metadata/<link-value>.md` (NOT timestamp-based)
   - **Validate before saving**: `kb-claude validate --strict` must pass
   
   **Frontmatter Template:**
   ```
   ---
   title: <descriptive research title>
   link: <kebab-case-slug-from-title>
   type: metadata
   ontological_relations:
     - relates_to: "[[cheatsheets/<relevant-cheatsheet>]]"
     - relates_to: "[[patterns/<relevant-pattern>]]"
     - relates_to: "[[memory_anchors/<anchor-slug>]]"
   tags:
     - <component>
     - <concept>
     - <technology>
   created_at: <ISO-8601-timestamp>
   updated_at: <ISO-8601-timestamp>
   uuid: <generate-uuid-v4>
   ---
   
   ## Summary
   High-level overview of the research findings in 2-3 paragraphs.
   
   ## Research Findings
   
   ### [Component/Area 1]
   Detailed findings with file paths, line numbers, and concrete evidence.
   
   ### [Component/Area 2]
   Detailed findings with file paths, line numbers, and concrete evidence.
   
   ## Architectural Patterns
   Key patterns, connections, and design decisions discovered.
   
   ## Key Files
   - `path/to/file.ext` - Description and relevance
   
   ## References
   - GitHub permalinks (if applicable)
   - Related metadata documents
   - Related patterns/cheatsheets
   ```

7. **Add GitHub permalinks (if applicable):**
    - Check if on main branch or if commit is pushed: `git branch --show-current` and `git status`
    - If on main/master or pushed, generate GitHub permalinks:
       - Get repo info: `gh repo view --json owner,name`
       - Create permalinks: `https://github.com/{owner}/{repo}/blob/{commit}/{file}#L{line}`
    - Replace local file references with permalinks in the document


8. **Handle follow-up questions:**
   - If the user has follow-up questions, append to the same research document
   - Update `updated_at` timestamp to current time
   - Add a new section: `## Follow-up Research [ISO-8601-timestamp]`
   - Spawn new sub-agents as needed for additional investigation
   - Re-run `kb-claude validate --strict` and `kb-claude manifest` after updates

## Important notes:
- Always use parallel Task agents to maximize efficiency and minimize context usage
- Always run fresh codebase research - never rely solely on existing research documents
- Focus on finding concrete file paths and line numbers for developer reference
- Research documents should be self-contained with all necessary context
- Each sub-agent prompt should be specific and focused on read-only operations
- Consider cross-component connections and architectural patterns
- Include temporal context (when the research was conducted)
- Link to GitHub when possible for permanent references
- Keep the main agent focused on synthesis, not deep file reading
- Encourage sub-agents to find examples and usage patterns, not just definitions
- Explore all of .claude/ directory, be smart about pulling information from the directory using ripgrep or other tools.
- **File reading**: Always read mentioned files FULLY (no limit/offset) before spawning sub-tasks
- **Critical ordering**: Follow the numbered steps exactly
  - ALWAYS read mentioned files first before spawning sub-tasks (step 1)
  - ALWAYS wait for all sub-agents to complete before synthesizing (step 4)
  - ALWAYS gather metadata before writing the document (step 5 before step 6)
  - NEVER write the research document with placeholder values
  - ALWAYS validate before saving: `kb-claude validate --strict` must pass
  
- **Frontmatter consistency**:
  - Use exact format: `title`, `link`, `type`, `ontological_relations`, `tags`, `created_at`, `updated_at`, `uuid`
  - Snake_case keys (`created_at`, `updated_at`, `git_commit`)
  - **CRITICAL**: Quote all ontological relations: `relates_to: "[[slug]]"` (prevents YAML parsing errors)
  - **CRITICAL**: Filename MUST equal link field (slugified title, NOT timestamps)
  - Timestamps in ISO-8601 format: `2025-10-25T14:30:00Z`
  - **No placeholders.** Fill with actual values using common sense
  - Tags should be relevant to the research topic and components studied
  - Everything hinges on ontological relations and tags - they enable knowledge graph navigation
  - Example filename: `authentication-flow-analysis.md` with `link: authentication-flow-analysis`
  
- **Validation is MANDATORY**:
  - Run `kb-claude validate --strict` before saving any document
  - Documents failing validation **must not** be saved
  - Run `kb-claude manifest` after every successful save
  - You WILL be punished if documents are not valid

- **Punitive rules**:
  - DO NOT CODE - YOU WILL BE PUNISHED FOR CODING
  - DO NOT save with placeholders - YOU WILL BE PUNISHED
  - SAVE THE DOCUMENT IN THE CORRECT FORMAT FOR THE NEXT DEV
  - ALWAYS FOLLOW BEST PRACTICES