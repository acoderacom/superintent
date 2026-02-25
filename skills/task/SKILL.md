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

**Don't explore codebase yet** — knowledge informs exploration in Step 2.

### Step 2: Implementation — The work

1. **Explore relevant codebase** — use `subagent_type=Explore` to understand current state. For complex codebases, run multiple in parallel.
   - Use citation file paths to guide where to explore. If no knowledge was found, explore broadly
   - **If a cited file doesn't exist** → validate those knowledge entries: `npx superintent knowledge validate <id1>,<id2>,<id3>` — mention the conflict to the user
   - When knowledge conflicts with current state, current state wins
2. Implement directly — no ticket, no task tracking
3. Run build and code checks (test, lint, typecheck)
4. Fix failures → re-run
5. Offer to extract knowledge only if the change produced something worth remembering (gotcha, pattern, architectural insight)

**Complexity check:** If unclear or risky → "This seems complex, switch to `/ticket`?"
