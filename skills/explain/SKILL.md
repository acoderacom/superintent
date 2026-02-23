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

**MANDATORY — Validate every result that has citations.** Do NOT skip this. Run validate for each knowledge entry returned:

```bash
npx superintent knowledge validate <id>
```

Run all validate calls in parallel (one per result). Then check each status:

- **valid** → trust the knowledge, use citations to point user to code locations
- **changed** → source file has evolved — note this in your answer so the user knows code may have shifted
- **missing** → source file was deleted — warn the user that referenced code no longer exists

### Step 2: Answer

**Knowledge found (score ≥0.45):**

Synthesize explanation from knowledge. Cite: "Based on [title]...". Include code locations if mentioned. If citations were missing, warn: "Note: some referenced source files no longer exist."

**No codebase exploration.** Knowledge is the authoritative answer. STOP here.

---

**No knowledge found (all scores <0.45):**

 **Explore relevant codebase** — use `subagent_type=Explore` understand current state, **Parallel exploration:** For complex codebase, run multiple Explore agents in parallel. After answering, suggest `/learn` if the topic is worth capturing.
