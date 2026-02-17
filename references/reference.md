# Reference

Shared reference tables and definitions used across skills.

## Confidence Defaults

| Category              | Default |
| --------------------- | ------- |
| Truth                 | 0.9     |
| Architecture / Gotcha | 0.85    |
| Pattern               | 0.8     |
| Principle             | 0.75    |

## Decision Scopes

**new-only** — Apply only to new code

**backward-compatible** — Apply without breaking existing behavior

**global** — Apply everywhere

**legacy-frozen** — Document only, don't change legacy code

## Content Formats

**Architecture:** `Component` / `Responsibility` / `Interfaces`

**Pattern:** `Why` / `When` / `Pattern`

**Truth:** `Fact` / `Verified`

**Principle:** `Rule` / `Why` / `Applies`

**Gotcha:** `Attempted` / `Failed Because` / `Instead` / `Symptoms`

## Scoring Criteria

| Factor      | Weight | Rule                                                                      |
| ----------- | ------ | ------------------------------------------------------------------------- |
| Confidence  | 3x     | Multiply confidence by 3 (e.g., 0.95 → 2.85)                              |
| Usage count | 1x     | usage_count as-is (capped at 10)                                          |
| Category    | bonus  | truth: +2, architecture: +1.5, principle: +1, pattern: +0.5, gotcha: +0.5 |
| Recency     | bonus  | Updated in last 7 days: +1, last 30 days: +0.5                            |
