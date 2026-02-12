# Superintent Development

## What is superintent?

superintent is a development framework where your intent drives the entire cycle — from idea to code to knowledge. Unlike typical AI coding tools that start from zero every session, superintent **compounds**. Every cycle makes the next one smarter.

## The Loop

```
  ┌──────────────────────────────────┐
  │                                  │
  ▼                                  │
Intent ──► Work ──► Test ──► Compound
                                │
                        knowledge extracted
```

**Intent** — Say what you want. superintent figures out the right size:

- `/spec` — big features, breaks down into tickets
- `/ticket` — standard work with planning and review
- `/task` — small stuff, just do it

**Work** — AI builds it. Informed by knowledge from past cycles — patterns, architecture, gotchas, decisions. Not starting from zero.

**Test** — Check if it's right. Automated checks (tests, lint, typecheck) + human review. The quality gate that makes compounding possible.

**Compound** — Knowledge is extracted from completed work and stored. What was built, how, why, what went wrong. This feeds back into the next Intent, making it smarter.

## Why superintent?

Most AI coding tools are **flat**. They execute, but they don't learn. Every session is a blank slate. Same mistakes, same questions, no memory.

superintent is **compound**. Each cycle adds knowledge. After 10 cycles, superintent knows your patterns. After 50, it knows your architecture. After 100, it knows your project better than a new hire.

**The difference isn't the AI. It's the loop.**

## How It Works

1. You express an intent: _"Build a user authentication system"_
2. superintent searches existing knowledge — has this project dealt with auth before?
3. superintent specs the work at the right level — this is big, so `/spec` breaks it into tickets
4. AI executes each ticket, informed by past knowledge
5. You review — approve, adjust, or reject
6. Knowledge is extracted: architecture decisions, patterns used, gotchas discovered
7. Next time auth comes up, superintent already knows your approach

## The Stack

- **Skills** — Claude skills that drive each stage (spec, ticket, task, explain, learn)
- **CLI Bridge** — `superintent` connects to the knowledge and ticket database
- **Vector DB** — Searchable knowledge that compounds over time
- **Human-in-the-loop** — You stay in control at the decision points

## Core Principles

**Intent over instruction.** Say what you want, not how to do it. superintent figures out the how.

**Compound over repeat.** Every cycle leaves behind knowledge. Nothing is wasted.

**Right-sized work.** Not everything needs a plan. Not everything is a quick task. superintent matches the level to the intent.

**Human at the gates.** AI executes, human decides. The review step isn't a bottleneck — it's what makes the knowledge trustworthy.
