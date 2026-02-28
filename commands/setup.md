---
description: Configure superintent for this project
disable-model-invocation: true
---

# superintent Setup

## Step 1: Check Prerequisites

```bash
npx superintent@latest --version
```

If fails → guide user to [install.md](../references/install.md), then return here.

## Step 2: Choose Database Mode

`AskUserQuestion`: "Where to store superintent data?"

- **Local (Recommended)** → Turso database in `.superintent/local.db`, no account needed
- **Cloud** → Turso Cloud, requires account and credentials

## Step 3: Create Config

Ensure `.gitignore` covers `.superintent/.env`, `.superintent/*.db`, `.superintent/*.db-shm`, and `.superintent/*.db-wal`. Read `.gitignore` (create if missing), then add only lines that aren't already present. Do not append duplicates.

### If Local:

Create `.superintent/.env` automatically:

```bash
npx superintent init --url "file:.superintent/local.db"
```

### If Cloud:

Guide user to get Turso credentials via https://turso.tech (free tier) or Turso CLI.

Then tell user to create `.superintent/.env` with:

```env
TURSO_URL="libsql://your-db.turso.io"
TURSO_AUTH_TOKEN="your-token"
```

**IMPORTANT:** Do NOT ask for credentials in chat. User must create file manually.

After user confirms `.env` created:

```bash
npx superintent init
```

## Step 4: Verify

```bash
npx superintent status
```

## Step 5: Add Permissions

Check if `.claude/settings.local.json` exists, then add permission:

```json
{
  "permissions": {
    "allow": ["Bash(npx superintent *)"]
  }
}
```

This pattern auto-approves all `npx superintent` commands.

## Step 6: Detect Namespace

Use the current directory name as the namespace. Use the output as `{namespace}` in the CLAUDE.md template below.

## Step 7: Write CLAUDE.md

Append to project's `CLAUDE.md`:

### If Local:

```markdown
## Superintent Config

- Namespace: {namespace}
- Database: Local SQLite (`.superintent/local.db`)
```

### If Cloud:

```markdown
## Superintent Config

- Namespace: {namespace}
- Database: Turso Cloud (configured via `.superintent/.env`)
```

### Both modes - add commands:

```markdown
Always search knowledge before exploring the codebase — it is the primary source of truth. All `--json` flags accept inline JSON input.

<!-- superintent:knowledge:start -->

<!-- superintent:knowledge:end -->

### Setup Commands

- **Init**: `npx superintent init` (--url)
- **Status**: `npx superintent status`
- **Dashboard**: `npx superintent dashboard` (-p port, -o auto-open)

### Ticket Operations

- **Create**: `npx superintent ticket create --json` (JSON: title, intent, type, context, constraints, assumptions, changeClass, plan)
- **Get**: `npx superintent ticket get <id>`
- **Preview**: `npx superintent ticket preview <id>`
- **Update**: `npx superintent ticket update <id>` (--json, --status, --complete-all, --complete-task, --complete-dod, --comment, --author, --context, --spec)
- **List**: `npx superintent ticket list [--status <status>] [--limit N] [--offset N]`
- **Delete**: `npx superintent ticket delete <id>`

### Spec Operations

- **Create**: `npx superintent spec create --json` (JSON: title, content, author)
- **Get**: `npx superintent spec get <id>`
- **Preview**: `npx superintent spec preview <id>`
- **List**: `npx superintent spec list [--limit N] [--offset N]`
- **Update**: `npx superintent spec update <id>` (--json, --title, --comment, --author)
- **Delete**: `npx superintent spec delete <id>`

### Knowledge Operations

- **Search**: `npx superintent knowledge search "<query>"` (--limit, --namespace, --category, --ticket-type, --tags, --author, --branch, --branch-auto, --min-score)
- **Extract**: `npx superintent knowledge extract <ticket-id>` (--namespace)
- **Create**: `npx superintent knowledge create --json` (JSON: title, namespace, content, category, source, confidence, scope, tags)
- **Get**: `npx superintent knowledge get <id>`
- **Preview**: `npx superintent knowledge preview <id>`
- **List**: `npx superintent knowledge list` (--namespace, --category, --scope, --source, --author, --branch, --branch-auto, --status, --limit, --offset)
- **Update**: `npx superintent knowledge update <id>` (--json, --title, --namespace, --category, --tags, --scope, --origin, --confidence, --comment, --author)
- **Activate**: `npx superintent knowledge activate <id>`
- **Deactivate**: `npx superintent knowledge deactivate <id>`
- **Promote**: `npx superintent knowledge promote <id>`
- **Validate**: `npx superintent knowledge validate <id>[,<id2>,...] [--all] [--main] [--heal]`
- **Recalculate**: `npx superintent knowledge recalculate [--dry-run]`
```

## Step 8: Confirm

Say: "superintent configured. Use `/ticket` to create tickets, `/task` for quick fixes, `/spec` for big features."
