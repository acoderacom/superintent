---
name: explain
model: sonnet
description: Explain concepts, patterns, or decisions by searching knowledge FIRST. Triggers on "explain...", "why do we...", "how does X work", "what is our approach to...", "what does X do". Search knowledge before any codebase exploration. If knowledge found, answer from knowledge only.
---

# Explain

Answer questions about concepts, patterns, architecture, or decisions.

## Workflow

### Step 1: Search Knowledge (BLOCKING)

```bash
npx superintent knowledge search "<intent>" --branch-auto --limit 3
```

Extract key terms from user's question for query.

**Semantic Search:** ≥0.45 relevant, ≥0.55 strong. Don't discard low scores.

**Citation validation:** If results include `citations`, validate them before answering:

```bash
npx superintent knowledge validate <id>
```

- **All valid** → trust the knowledge, use citations to point user to exact code locations
- **Some/all stale** → note which citations are stale in your answer so the user knows code has changed

### Step 2: Answer

**Knowledge found (score ≥0.45):**

Synthesize explanation from knowledge. Cite: "Based on [title]...". Include code locations if mentioned. If citations were stale, warn: "Note: some referenced code has changed since this knowledge was captured."

**No codebase exploration.** Knowledge is the authoritative answer. STOP here.

---

**No knowledge found (all scores <0.45):**

 **Explore relevant codebase** — use `subagent_type=Explore` understand current state, **Parallel exploration:** For complex codebase, run multiple Explore agents in parallel. After answering, suggest `/learn` if the topic is worth capturing.
