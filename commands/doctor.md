---
name: doctor
model: sonnet
description: Validate and heal the knowledge base against the current codebase. Triggers on "heal", "heal knowledge", "fix stale knowledge", "knowledge health", "knowledge doctor", "repair citations".
---

# Doctor

Validate every knowledge entry against the real codebase. Fix what drifted. Deactivate what's gone. The knowledge base immune system.

**When to use:**

- Nightly cron / scheduled maintenance
- After a large merge or refactor
- Before a sprint — ensure knowledge base is clean
- When recall returns many `changed` citations

**When NOT to use:**

- Adding new knowledge → `/learn`
- Distilling to CLAUDE.md → `/maintain`

## Workflow

### Step 1: Validate All — Single pass

Run one command to validate every active entry:

```bash
npx superintent knowledge validate --main
```

Parse the JSON result. Each entry has `valid`, `changed`, `missing` counts. Classify:

- **valid** — `changed == 0 && missing == 0`. No action needed.
- **changed** — `changed > 0 && missing == 0`. File exists but hash differs.
- **missing** — `missing > 0`. At least one cited file deleted.

Report summary:

```
Validation complete ({N} entries):
  {X} valid — no action needed
  {Y} changed — will inspect
  {Z} missing — will resolve
```

If all valid → "Knowledge base is healthy. No healing needed." → Done.

### Step 2: Group Changed Entries by File

Build a **file → entries map** from the validation results. For each changed entry, extract the citation paths with `status: "changed"` and group entries by their changed file paths.

Example: if entries A, B, C all cite `src/commands/knowledge.ts` as changed, they form one group.

This is the key optimization — instead of reading the same file N times for N entries, read it once and decide for all N entries.

### Step 3: Inspect Changed — Read each unique file once

Launch **one** `subagent_type=Explore` agent with all unique changed file paths. The prompt should:

1. List all unique changed files to read
2. For each file, list which knowledge entries cite it and what those entries describe (title + brief content summary)
3. Ask the agent to verify: does the file still support what the knowledge claims?

The explore agent reads each file once and returns a per-file verdict:
- **accurate** — file changed but knowledge claims still hold
- **drifted** — knowledge is partially outdated, note what changed
- **wrong** — knowledge fundamentally contradicts the current file

### Step 4: Apply Verdicts to Entries

Map file-level verdicts back to entries:

**A. All changed citations → accurate**: Content still describes reality.

→ Refresh citation hashes only (pass ALL citations, not just changed ones — CLI auto-generates fresh hashes):

```bash
npx superintent knowledge update <id> --json '{"citations": [{"path": "{file1:line}"}, {"path": "{file2:line}"}]}'
```

Run all hash-refresh updates **in parallel** (up to 10 at a time).

**B. Any changed citation → drifted**: Knowledge partially outdated.

→ Update content and refresh citations:

```bash
npx superintent knowledge update <id> --json '{"content": "{corrected content using knowledge content formats}", "citations": [{"path": "{file:line}"}]}'
```

**C. Any changed citation → wrong**: Knowledge fundamentally contradicts current code.

→ Flag for user decision (Step 6).

### Step 5: Resolve Missing — Handle deleted files

For each `missing` entry:

**A. All citations missing** — the code this knowledge described is gone.

→ Deactivate:

```bash
npx superintent knowledge deactivate <id>
```

**B. Some citations missing, some valid/changed** — partial deletion.

→ Flag for user decision (Step 6).

### Step 6: User Decisions — Present flagged entries

For each flagged entry from Steps 4C and 5B, present to the user one at a time:

1. Show title, content, category, confidence
2. Show what changed or went missing
3. `AskUserQuestion`: "What should we do with this entry?"
   - **Update** — agent rewrites content based on current codebase state
   - **Deactivate** — entry is no longer relevant
   - **Keep as-is** — knowledge is still conceptually valid despite code changes

### Step 7: Report — Summary of actions taken

Report all actions:

```
Knowledge Base Heal Complete:
  {A} entries valid — no changes
  {B} entries healed — citations refreshed
  {C} entries updated — content corrected
  {D} entries deactivated — source code removed
  {E} entries kept as-is — user decided

  Confidence adjustments:
  - {list of entries with adjusted confidence}
```

If any entries were updated or deactivated, suggest: "Run `/maintain` to refresh CLAUDE.md with these changes."

---

## Reference

### Call Budget Comparison

| Step | Before (batch-per-entry) | After (group-by-file) |
|------|--------------------------|----------------------|
| Validate | N bash calls (batched 10) | 1 call (`--main`) |
| File reads | up to N explore calls | O(unique files) |
| Heal updates | N bash calls | N bash calls (parallel) |

### Confidence Adjustments During Healing

- **All citations valid** — no change
- **Citations refreshed** (content still accurate) — +0.05 (capped at 0.95)
- **Content corrected** (partial drift) — reset to category default
- **Kept as-is** despite changes — -0.10

### Content Formats for Updates

When correcting knowledge content, use the same formats:

**Architecture:** `Component` / `Responsibility` / `Interfaces`

**Pattern:** `Why` / `When` / `Pattern`

**Truth:** `Fact` / `Verified`

**Principle:** `Rule` / `Why` / `Applies`

**Gotcha:** `Attempted` / `Failed Because` / `Instead` / `Symptoms`