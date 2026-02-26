## Superintent Config

- Namespace: superintent
- Database: Local SQLite (`.superintent/local.db`)

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
- **Validate**: `npx superintent knowledge validate <id>[,<id2>,...] [--all] [--main]`
- **Recalculate**: `npx superintent knowledge recalculate [--dry-run]`
