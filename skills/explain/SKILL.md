---
name: explain
model: sonnet
description: Explain concepts, patterns, or decisions by searching knowledge FIRST. Triggers on "explain...", "why do we...", "how does X work", "what is our approach to...", "what does X do". Search knowledge before any codebase exploration. If knowledge found, answer from knowledge only.
---

# Explain

Answer questions about concepts, patterns, architecture, or decisions.

## Workflow

### Step 1: Search knowledge (BLOCKING)

```bash
npx superintent knowledge search "<intent>" --branch-auto --limit 3
```

Extract key terms from user's question for query.

**Semantic Search:** ≥0.45 relevant, ≥0.55 strong. Don't discard low scores.

### Step 2: Answer

**Knowledge found (score ≥0.45):**

Synthesize explanation from knowledge. Cite: "Based on [title]...". Include code locations from citations.

**No codebase exploration.** Knowledge is the authoritative answer. STOP here.

---

**No knowledge found (all scores <0.45):**

**Explore relevant codebase** — use `subagent_type=Explore` to understand current state. For complex codebases, run multiple in parallel.
   - Use citation file paths to guide where to explore. If no knowledge was found, explore broadly
   - **If a cited file doesn't exist** → validate those knowledge entries: `npx superintent knowledge validate <id1>,<id2>,<id3>` — warn user that referenced code no longer exists
   - After answering, suggest `/learn` if the topic is worth capturing
