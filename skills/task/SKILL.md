---
name: task
description: Quick execution for confident, low-risk changes. Triggers on "quick fix", "just do it", "simple change", or "/task". Skips ceremony, preserves knowledge.
---

# Task

Skip ceremony, preserve knowledge. The lightweight entry point in the superintent loop.

**When to use:** Simple fixes, obvious changes, Class A only.
**When NOT to use:** Complex features, unclear requirements, Class B/C → `/ticket`.

## Workflow

### Step 1: Recall — Search what we already know

Before asking questions, search the knowledge base — the source of truth.

```bash
npx superintent knowledge search "<user's intent keywords>" --branch-auto --limit 3
```

**Semantic Search:** ≥0.45 relevant, ≥0.55 strong. Don't discard low scores.

**Citation check:** If results include `citations`, validate them:

```bash
npx superintent knowledge validate <id>
```

- **valid** → file unchanged since knowledge was written, trust fully
- **changed** → source file has evolved — knowledge is likely still valid, use citations as navigation hints for Step 2
- **missing** → source file was deleted — knowledge may be about removed code, verify in Step 2

**Don't explore codebase yet** — knowledge informs exploration in Step 2.

### Step 2: Implementation - The work

1. **Explore relevant codebase** — use `subagent_type=Explore` understand current state **Parallel exploration:** For complex codebase, run multiple Explore agents in parallel, if knowledge found → start from patterns/files, else broad. If knowledge conflicts with current state, current state wins for the current task — then auto-lower confidence: `npx superintent knowledge update <id> --confidence <current - 0.15>` and mention the conflict to the user
2. Implement directly — no ticket, no task tracking
3. Run build and code checks (test, lint, typecheck)
4. Fix failures → re-run
5. Offer to extract knowledge only if the change produced something worth remembering (gotcha, pattern, architectural insight)

**Complexity check:** If unclear or risky → "This seems complex, switch to `/ticket`?"
