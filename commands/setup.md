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

Ensure `.gitignore` covers `.superintent/.env` and `.superintent/*.db`. Read `.gitignore` (create if missing), then add only lines that aren't already present. Do not append duplicates.

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

This pattern auto-approves all `npx superintent` commands, including heredoc input via `npx superintent ticket create --stdin <<'EOF'`.

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
<!-- superintent:knowledge:start -->

<!-- superintent:knowledge:end -->

### Setup Commands

| Command                               | Description                                               |
| ------------------------------------- | --------------------------------------------------------- |
| `npx superintent init [--url <url>]`  | Create database tables                                    |
| `npx superintent status`              | Check Turso connection                                    |
| `npx superintent ui [-p <port>] [-o]` | Start web UI (default port 3456, -o to auto-open browser) |

### Ticket Operations

| Action  | Command                                                                                                                                                                                                         |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Create  | `npx superintent ticket create --stdin` (JSON: `{"title","intent","type","context","constraints","assumptions","changeClass","plan",[...]}`)                                                                    |
| Get     | `npx superintent ticket get <id>`                                                                                                                                                                               |
| Preview | `npx superintent ticket preview <id>`                                                                                                                                                                           |
| Update  | `npx superintent ticket update <id> [--stdin] [--status] [--complete-all] [--complete-task <indices>] [--complete-dod <indices>] [--comment <text>] [--author <name>] [--context <context>] [--spec <spec-id>]` |
| List    | `npx superintent ticket list [--status <status>] [--limit N]`                                                                                                                                                   |
| Delete  | `npx superintent ticket delete <id>`                                                                                                                                                                            |

### Spec Operations

| Action  | Command                                                                                     |
| ------- | ------------------------------------------------------------------------------------------- |
| Create  | `npx superintent spec create --stdin` (JSON: `{"title","content","author"}`)                |
| Get     | `npx superintent spec get <id>`                                                             |
| Preview | `npx superintent spec preview <id>`                                                         |
| List    | `npx superintent spec list [--limit N]`                                                     |
| Update  | `npx superintent spec update <id> [--stdin] [--title] [--comment <text>] [--author <name>]` |
| Delete  | `npx superintent spec delete <id>`                                                          |

### Knowledge Operations

| Action      | Command                                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Search      | `npx superintent search "<query>" [--limit N] [--namespace] [--category] [--ticket-type] [--tags] [--author] [--branch] [--branch-auto] [--min-score]`                   |
| Extract     | `npx superintent extract <ticket-id> [--namespace <namespace>]`                                                                                                          |
| Create      | `npx superintent knowledge create --stdin` (JSON: `{"title","namespace","content","category","source","confidence","scope","tags",[...]}`)                               |
| Get         | `npx superintent knowledge get <id>`                                                                                                                                     |
| Preview     | `npx superintent knowledge preview <id>`                                                                                                                                 |
| List        | `npx superintent knowledge list [--namespace] [--category] [--scope] [--source] [--author] [--branch] [--branch-auto] [--status active\|inactive\|all] [--limit N]`      |
| Update      | `npx superintent knowledge update <id> [--stdin] [--title] [--namespace] [--category] [--tags] [--scope] [--origin] [--confidence] [--comment <text>] [--author <name>]` |
| Activate    | `npx superintent knowledge activate <id>`                                                                                                                                |
| Deactivate  | `npx superintent knowledge deactivate <id>`                                                                                                                              |
| Promote     | `npx superintent knowledge promote <id>`                                                                                                                                 |
| Recalculate | `npx superintent knowledge recalculate [--dry-run]`                                                                                                                      |
```

## Step 8: Confirm

Say: "superintent configured. Use `/ticket` to create tickets, `/task` for quick fixes, `/spec` for big features."
