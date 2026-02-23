# Superintent

**Imposing engineering discipline on AI-assisted development.**

AI coding tools keep getting faster. Teams ship more code with less understanding every week. Six months in, you have a codebase that works and nobody can explain — including the AI that built it. **Ship fast, forget faster.**

Superintent is a Claude Code plugin that makes compounding the default. Every ticket leaves behind structured knowledge. Every cycle makes the next one smarter. The AI and the human both benefit — the AI stops starting from zero, the human gets a project that explains itself.

## The Problem With Vibe Coding

Vibe coding works. That's what makes it dangerous.

You get a working feature, you ship it, you move on. But nothing was captured. Nobody knows why it was built that way. Nobody knows what was considered and rejected. The gotcha that took 45 minutes to debug? Gone. The code exists but the understanding that produced it evaporated when the session ended.

The faster the tool, the worse this gets.

## The Superintent Loop

```
Intent ──► Work ──► Review ──► Compound
  ▲                                │
  │         knowledge extracted    │
  └────────────────────────────────┘
```

**Intent** — Say what you want. Superintent matches the effort to the size:
- `/spec` — Big features. Think first, break into tickets later.
- `/ticket` — Standard work. Plan, build, review, extract.
- `/task` — Small stuff. Just do it.

**Work** — AI builds, informed by everything the project already knows. Not starting from zero.

**Review** — Human decides. Approve, adjust, or reject. This is what makes the knowledge trustworthy.

**Compound** — Knowledge is extracted: what was built, how, why, what failed, what was rejected. Stored with structure, confidence scores, citations, and semantic search.

## Who Benefits

**The AI** searches knowledge before writing code. It avoids past mistakes, follows established patterns, and makes informed decisions instead of generic ones. Every cycle makes it more useful.

**The human** gets a living project knowledge base that writes itself. Architecture decisions with rationale. Gotchas with symptoms. Patterns with context. Not a documentation effort — a byproduct of the work you were already doing.

**New team members** onboard from the knowledge base. "Why did we do it this way?" has an answer before they ask.

## Commands

| Command | Purpose |
| --- | --- |
| `/spec` | Design big features, break them into tickets |
| `/ticket` | Create and execute development tickets |
| `/task` | Quick, confident changes — skip the ceremony |
| `/learn` | Capture knowledge from codebase exploration |
| `/explain` | Answer questions from stored knowledge first |
| `/maintain` | Sync the knowledge summary in `CLAUDE.md` |

## Change Classes

Not all changes carry the same risk. Superintent classifies work to match the review effort to the blast radius:

| Class | Scope | Risk | Behavior |
| --- | --- | --- | --- |
| **A** | Single file, tests, docs | Low | Auto-execute |
| **B** | Cross-module, APIs, dependencies | Moderate | Propose, then execute |
| **C** | Schema, auth, payments, infra | High | Full plan, approval required |

## Knowledge System

### Categories

Every piece of extracted knowledge is typed:

| Category | What it captures |
| --- | --- |
| **architecture** | How the system is structured |
| **pattern** | Reusable approaches with why and when |
| **truth** | Validated facts about the project |
| **principle** | Rules the team follows and why |
| **gotcha** | Things that failed and how to avoid them |

### Confidence Scoring

Each knowledge entry carries a confidence score (0–1) that reflects how reliable it is. Scores start based on category — truths at 0.9, patterns at 0.8, principles at 0.75 — then grow with usage and decay with staleness.

### Citations as Provenance

Knowledge entries include **citations** — `file:line` references with file-level hashes that link knowledge to its source code. Line numbers are navigation hints; the hash covers the entire file.

Validation returns three statuses: **valid** (file unchanged), **changed** (file evolved — informational, no penalty), or **missing** (file deleted — signals knowledge may be orphaned). Only missing files affect confidence scoring.

Skills check citations before using knowledge. Missing source files trigger the **Knowledge Conflict Protocol**: the user is informed and chooses whether to update, deactivate, or keep the knowledge.

## Why the "Ceremony" Matters

When AI just executes without planning, you get code fast. You also get code with no documented rationale that will confuse everyone three weeks from now.

The ticket workflow forces what developers skip when left to their instincts: *stop and think before you build.* Intent. Constraints. Change class. Edge cases. Rollback plan. That's not overhead — that's engineering discipline. The fact that AI is doing the building makes it more important, not less.

The first ticket you create already delivers value — not because the knowledge base returned anything, but because you made a better plan than you would have without it. The compounding comes later. The discipline is immediate.

## Superintent CLI

The data layer lives in the **[CLI](https://github.com/acoderacom/superintent-cli)** — tickets, knowledge, specs, and semantic search. No global install needed — runs via `npx`:

```
npx superintent@latest
```

Supports both **local** (SQLite in `.superintent/local.db`) and **cloud** (Turso) storage. Local mode requires no account — just `npx superintent init` and go.

## Installation

Requires [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and Node.js 18+.

**1. Install the plugin**

```
/plugin marketplace add acoderacom/superintent
/plugin install superintent
```

**2. Initialize your project**

```
/superintent:setup
```

The setup command walks you through database configuration (local or cloud), writes your `CLAUDE.md`, and verifies the connection.

## Principles

**Compound over repeat.** Every cycle leaves behind knowledge. Nothing is wasted.

**Intent over instruction.** Say what you want, not how. The system figures out the how.

**Right-sized work.** Not everything needs a spec. Not everything is a quick task. Match the effort to the intent.

**Human at the gates.** AI executes, human decides. Review isn't a bottleneck — it's what makes the knowledge trustworthy.

## License

MIT — See [LICENSE](LICENSE) for details.

Inspired by [superpowers](https://github.com/obra/superpowers) by Jesse Vincent.
