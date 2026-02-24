---
name: maintain
model: sonnet
description: Maintain CLAUDE.md from knowledge database. Triggers on "maintain", "update claude.md", "sync knowledge", "refresh claude.md".
---

# Maintain

Distill the most important knowledge into CLAUDE.md. Knowledge DB is the source of truth; CLAUDE.md is the concise, always-visible summary.

**When to use:**

- Knowledge has been added/updated and CLAUDE.md is stale

**When NOT to use:**

- You want to add content to CLAUDE.md that isn't in the knowledge DB — edit CLAUDE.md manually outside the markers

## Workflow

### Step 1: Inventory — Gather and validate in two calls

Read the project's `CLAUDE.md` file. Identify the `<!-- superintent:knowledge:start -->` and `<!-- superintent:knowledge:end -->` markers. If markers don't exist yet, note that — you'll add them in Step 4.

Fetch all entries and validate in parallel (2 calls total):

```bash
# Call 1: Full entry data for scoring (run in parallel)
npx superintent knowledge list --status active --branch-auto --limit 999

# Call 2: Citation health for all entries (run in parallel)
npx superintent knowledge validate --all
```

**Join by ID** — match each entry's scoring data (confidence, usage_count, category, created_at) with its citation health (valid/changed/missing counts).

**Score** each entry using the scoring criteria from Reference. Check citation status:
- **valid** → trust fully, score normally
- **changed** → likely still valid, score with lower weight
- **missing** → flag for deactivation, exclude from ranking

### Step 2: Rank — Sort accumulated scores

Score each active knowledge entry. **Higher score = more important.**

1. Apply scoring criteria (sum all weights)
2. Exclude: confidence < 0.5, entries too implementation-specific (prefer general abstractions)
3. **Sort** by total score descending. Take the top entries that provide useful coverage without bloating CLAUDE.md

### Step 3: Distill — Write one-liner summaries

For each selected knowledge entry, write a **one-liner summary** — the most important abstraction, not the full content. Link to the knowledge ID so Claude can fetch details when needed.

**Output format per entry:**

```
- **{Title}** — {one-line concise summary} (`{KNOWLEDGE-ID}`)
```

**Group by category:**

```markdown
### Architecture

- **Project Overview** — Turso-backed CLI plugin for tickets, knowledge, and specs (`KNOWLEDGE-YYYYMMDD-HHMMSSMMM`)
- **Source Code Architecture** — Modular TypeScript: commands/, db/, embed/, ui/, utils/ (`KNOWLEDGE-YYYYMMDD-HHMMSSMMM`)

### Key Facts

- **CLI Commands** — 8 commands: init, status, ticket, knowledge, search, extract, spec, ui (`KNOWLEDGE-YYYYMMDD-HHMMSSMMM`)

### Patterns

- ...

### Gotchas

- ...
```

**Rules:**

- One line per entry — no paragraphs
- Use the knowledge title as the bold label
- Summary should be the "so what" — what Claude needs to know at a glance
- Knowledge ID in backtick-parens at the end for lookup via `npx superintent knowledge get <id>`
- Omit empty category groups

### Step 4: Update — Write to CLAUDE.md

1. **If markers exist** in CLAUDE.md:
   - Replace everything between `<!-- superintent:knowledge:start -->` and `<!-- superintent:knowledge:end -->` with the distilled output from Step 3
   - Preserve everything outside the markers exactly as-is
2. **If markers don't exist** in CLAUDE.md:
   - Append the markers and distilled content at the end of the file:

```markdown
<!-- superintent:knowledge:start -->

### Architecture

- ...

### Key Facts

- ...
<!-- superintent:knowledge:end -->
```

3. **If no changes needed** (distilled content matches what's already between markers):
   - Report "CLAUDE.md is up to date — no changes needed."
   - Do not write the file. Done.
4. **Before writing**, show the user a summary of changes:
   - How many entries added/updated/removed
   - Which knowledge IDs are included
5. `AskUserQuestion`: "Apply these changes to CLAUDE.md?" → Apply | Revise | Cancel
6. **Report** after writing:
   - Number of knowledge entries distilled
   - Categories included
   - Reminder: "Content outside the markers was not modified."

---

## Reference

### Scoring Criteria

| Factor      | Weight | Rule                                                                      |
| ----------- | ------ | ------------------------------------------------------------------------- |
| Confidence  | 3x     | Multiply confidence by 3 (e.g., 0.95 → 2.85)                              |
| Usage count | 1x     | usage_count as-is (capped at 10)                                          |
| Category    | bonus  | truth: +2, architecture: +1.5, principle: +1, pattern: +0.5, gotcha: +0.5 |
| Recency     | bonus  | Updated in last 7 days: +1, last 30 days: +0.5                            |
