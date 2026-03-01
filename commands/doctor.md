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

### Step 1: Validate & Heal — Fast pass

Run one command to validate and auto-fix hash drift:

```bash
npx superintent knowledge validate --main --heal --hash
```

The `--heal` flag auto-fixes entries where files still exist but hashes drifted (`changed > 0 && missing === 0`). It updates citation hashes directly in the DB using hashes already computed during validation — no file re-reads.

Parse the JSON result. Key fields: `validated`, `healed`, `entries[]` (each with `valid`, `changed`, `missing`, `healed` fields).

Classify entries after healing:

- **valid** — `changed == 0 && missing == 0 && healed == false`. Untouched.
- **healed** — `healed == true`. Hash drift auto-fixed. Needs content verification (Step 2).
- **missing** — `missing > 0`. At least one cited file deleted. Needs resolution (Step 3).

Report summary:

```
Validation complete ({N} entries):
  {X} valid — no action needed
  {H} healed — hashes refreshed, will verify content
  {Z} missing — will resolve
```

If no healed and no missing → "Knowledge base is healthy. No healing needed." → Done.

### Step 2: Verify Healed — Content drift check

For healed entries, verify that the knowledge content still matches reality. The hash changed because the file changed — the content may have drifted too.

**Group healed entries by file.** Build a file → entries map from healed entries' citation paths. Then split into batches of ~5 files each.

Launch **parallel** `subagent_type=Explore` agents — one per batch. Each agent's prompt should:

1. List the files in its batch to read
2. For each file, list which knowledge entries cite it (title + brief content summary)
3. Verify: does the file still support what the knowledge claims?

Each agent returns a per-file verdict:
- **accurate** — file changed but knowledge claims still hold
- **drifted** — knowledge is partially outdated, note what changed
- **wrong** — knowledge fundamentally contradicts the current file

**Apply verdicts:**

**A. All citations → accurate**: Content verified. No further action.

**B. Any citation → drifted**: Knowledge partially outdated.

→ Update content and refresh citations:

```bash
npx superintent knowledge update <id> --json '{"content": "{corrected content using knowledge content formats}", "citations": [{"path": "{file:line}"}]}' --comment "Doctor heal: {brief description of what drifted and what was corrected}" --author "doctor"
```

**C. Any citation → wrong**: Knowledge contradicts current code.

→ Flag for user decision (Step 4).

### Step 3: Resolve Missing — Handle deleted files

For each entry with `missing > 0`:

**A. All citations missing** — the code this knowledge described is gone.

→ Deactivate with comment:

```bash
npx superintent knowledge update <id> --comment "Doctor heal: deactivated — all cited files removed" --author "doctor"
npx superintent knowledge deactivate <id>
```

**B. Some citations missing, some valid** — partial deletion.

→ Flag for user decision (Step 4).

### Step 4: User Decisions — Present flagged entries

For each flagged entry from Steps 2C and 3B, present to the user one at a time:

1. Show title, content, category, confidence
2. Show what changed, drifted, or went missing
3. `AskUserQuestion`: "What should we do with this entry?"
   - **Update** — agent rewrites content based on current codebase state, add `--comment "Doctor heal: {what changed}" --author "doctor"`
   - **Deactivate** — entry is no longer relevant, add comment before deactivating: `--comment "Doctor heal: deactivated — {reason}" --author "doctor"`
   - **Keep as-is** — knowledge is still conceptually valid despite code changes, add `--comment "Doctor heal: kept as-is — {reason}" --author "doctor"`

### Step 5: Report — Summary of actions taken

Report all actions:

```
Knowledge Base Heal Complete:
  {A} entries valid — no changes
  {B} entries healed — hashes refreshed, content verified accurate
  {C} entries updated — content corrected for drift
  {D} entries deactivated — source code removed
  {E} entries kept as-is — user decided

  Confidence adjustments:
  - {list of entries with adjusted confidence}
```

If any entries were updated or deactivated, suggest: "Run `/maintain` to refresh CLAUDE.md with these changes."

---

## Reference

### Call Budget

| Step | Calls |
|------|-------|
| Validate + heal | 1 CLI call (`--main --heal --hash`) |
| Verify healed | ceil(unique files / 5) parallel Explore agents |
| Deactivate missing | 1 per fully-missing entry |
| Content updates | 1 per drifted entry |
| User decisions | 1 per flagged entry |

### Confidence Adjustments During Healing

- **All citations valid** — no change
- **Healed + verified accurate** — +0.05 (capped at 0.95)
- **Content corrected** (partial drift) — reset to category default
- **Kept as-is** despite changes — -0.10

### Content Formats for Updates

When correcting knowledge content, use the same formats:

**Architecture:** `Component` / `Responsibility` / `Interfaces`

**Pattern:** `Why` / `When` / `Pattern`

**Truth:** `Fact` / `Verified`

**Principle:** `Rule` / `Why` / `Applies`

**Gotcha:** `Attempted` / `Failed Because` / `Instead` / `Symptoms`