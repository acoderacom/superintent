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

### Step 1: Inventory — List all active entries

```bash
npx superintent knowledge list --status active --branch-auto --limit 100
```

Count total entries. Report: "Found {N} active knowledge entries. Starting validation."

### Step 2: Validate — Check every citation

Run validation for **all entries in parallel**:

```bash
npx superintent knowledge validate <id>
```

Group results into three buckets:

- **valid** — all citations match current files. No action needed.
- **changed** — file exists but hash differs. Needs inspection.
- **missing** — file deleted. Needs resolution.

Report summary:

```
Validation complete:
  {X} valid — no action needed
  {Y} changed — will inspect
  {Z} missing — will resolve
```

If all valid → "Knowledge base is healthy. No healing needed." → Done.

### Step 3: Inspect Changed — Read files, compare content

For each `changed` entry, use `subagent_type=Explore` to read the cited files.
   - Batch changed entries that cite the same files into one explore call

For each entry, determine:

**A. Content still accurate** — the file changed but the knowledge still describes reality correctly (e.g., new code was added but existing patterns unchanged).

→ Refresh citation hash only:

```bash
npx superintent knowledge update <id> --stdin <<'KNOWLEDGE'
{"citations": [{"path": "{file:line}"}]}
KNOWLEDGE
```

CLI auto-generates fresh hash. Entry healed.

**B. Content partially drifted** — the knowledge is mostly right but some details are outdated (e.g., a function was renamed, a new step was added to a process).

→ Update content and refresh citations:

```bash
npx superintent knowledge update <id> --stdin <<'KNOWLEDGE'
{"content": "{corrected content using knowledge content formats}", "citations": [{"path": "{file:line}"}]}
KNOWLEDGE
```

**C. Content fundamentally wrong** — the knowledge describes something that no longer exists or works completely differently.

→ Flag for user decision (Step 5).

### Step 4: Resolve Missing — Handle deleted files

For each `missing` entry (all cited files deleted):

**A. All citations missing** — the code this knowledge described is gone.

→ Deactivate:

```bash
npx superintent knowledge deactivate <id>
```

**B. Some citations missing, some valid/changed** — partial deletion. The knowledge may still have value.

→ Flag for user decision (Step 5).

### Step 5: User Decisions — Present flagged entries

For each flagged entry from Steps 3C and 4B, present to the user one at a time:

1. Show title, content, category, confidence
2. Show what changed or went missing
3. `AskUserQuestion`: "What should we do with this entry?"
   - **Update** — agent rewrites content based on current codebase state
   - **Deactivate** — entry is no longer relevant
   - **Keep as-is** — knowledge is still conceptually valid despite code changes

### Step 6: Report — Summary of actions taken

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