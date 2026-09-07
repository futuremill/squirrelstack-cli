# SquirrelStack CLI

Command-line tool for [SquirrelStack](https://squirrelstack.app): errors, helpdesk tickets, and project epics and stories.

## Install

```bash
brew tap futuremill/squirrelstack
brew install squirrelstack
```

Or download the script directly:

```bash
curl -fsSL https://raw.githubusercontent.com/futuremill/squirrelstack-cli/main/squirrelstack -o /usr/local/bin/squirrelstack
chmod +x /usr/local/bin/squirrelstack
```

## Requirements

- macOS (uses Keychain for token storage)
- Ruby (pre-installed on macOS)
- curl and openssl (pre-installed on macOS)

## Authentication

On first use, you'll be prompted to log in via your browser or paste a PAT. Tokens are stored securely in the macOS Keychain.

```bash
squirrelstack login     # Authenticate via browser (OAuth)
squirrelstack logout    # Log out the active user
squirrelstack whoami    # Show the active user and account
```

## Users

You can be logged in as **multiple users** at once — each `login` stores a
separate token in the Keychain, keyed by the email reported by the server. The
CLI picks which user to act as from the following sources, in order:

1. `--user <email>` flag
2. `SQUIRRELSTACK_USER` env var
3. Nearest `.squirrelstack` file, walked up from the current directory (`user=<email>`)
4. `~/.squirrelstack` global default (`user=<email>`)
5. The only logged-in user, if there's exactly one

When more than one user is logged in and none is selected, commands stop and ask
you to pick one.

```bash
# Log in as additional users (repeat for each)
squirrelstack login

# See who's logged in (the active one is marked *)
squirrelstack users

# Pin the current directory tree to a user
squirrelstack users use bob@example.com

# Set a global default user
squirrelstack users use bob@example.com -g

# Remove a pinned user
squirrelstack users use --clear        # remove user= from ./.squirrelstack
squirrelstack users use --clear -g     # remove user= from ~/.squirrelstack

# Log out a specific user (default: the active one)
squirrelstack logout bob@example.com
```

A single `.squirrelstack` file can pin both a user and an account
(`user=<email>` and `account=<slug>` on separate lines). If you upgraded from an
earlier version, your existing login is migrated automatically on first use.

## Accounts

Your PAT identifies you as a user — you can belong to multiple SquirrelStack accounts.
The CLI picks one per request from the following sources, in order:

1. `--account <slug>` flag
2. `SQUIRRELSTACK_ACCOUNT` env var
3. Nearest `.squirrelstack` file, walked up from the current directory
4. `~/.squirrelstack` global default
5. Server default (only works when your user has exactly one account)

```bash
# See what accounts you can access (and which one is currently selected)
squirrelstack accounts

# Pin the current directory tree to an account
squirrelstack accounts use acme

# Set a global default
squirrelstack accounts use acme -g

# Remove a pinned account
squirrelstack accounts use --clear           # remove ./.squirrelstack
squirrelstack accounts use --clear -g        # remove ~/.squirrelstack

# Show what's currently resolved and from where
squirrelstack accounts current
```

The `.squirrelstack` file is plain text with one `key=value` per line
(`account=<slug>` and optionally `user=<email>`). Commit it if your team should
share the same default for a project, or gitignore it if it's personal.

## Usage

```bash
# List open errors
squirrelstack errors
squirrelstack errors --severity critical
squirrelstack errors --env production
squirrelstack errors --status all --json

# View error details
squirrelstack errors show <ID>
squirrelstack errors show <ID> --json

# List every occurrence (instance) of an error
squirrelstack errors occurrences <ID>
squirrelstack errors occurrences <ID> --limit 500
squirrelstack errors occurrences <ID> --json

# Update an error
squirrelstack errors update <ID> --status resolved
squirrelstack errors update <ID> --priority high
squirrelstack errors update <ID> --assign <user_id>
squirrelstack errors update <ID> --status resolved --priority low --assign none

# Add a note
squirrelstack errors comment <ID> "Root cause identified"

# List team members (for --assign)
squirrelstack errors members
```

## Helpdesk tickets

```bash
# List open tickets (state: new or assigned)
squirrelstack tickets
squirrelstack tickets --state closed
squirrelstack tickets --state all --priority urgent
squirrelstack tickets --json

# View a ticket with its full message thread
squirrelstack tickets show <ID>
squirrelstack tickets show <ID> --json
```

## Projects

Epics and stories on the account's board. Creating and updating needs the
Projects edit permission on your membership.

```bash
# List epics (optionally by bucket or owner)
squirrelstack epics
squirrelstack epics --bucket next
squirrelstack epics --json

# Show an epic with its stories and notes
squirrelstack epics show <ID>

# Create and update epics (new epics default to the future bucket)
squirrelstack epics create "Billing" --bucket next --theme Feature --owner <user_id>
squirrelstack epics update <ID> --bucket now
squirrelstack epics update <ID> --title "Billing v2" --start 2026-10-01 --end 2026-12-01

# List stories (optionally by status, epic or owner)
squirrelstack stories
squirrelstack stories --status doing
squirrelstack stories --epic <epic_id>

# Show a story with its tasks and notes
squirrelstack stories show <ID>

# Create a story in an epic, then move it across the board
squirrelstack stories create "Wire up search" --epic <epic_id> --points 2
squirrelstack stories update <ID> --status doing --owner <user_id>
squirrelstack stories update <ID> --status done
```

## Environment Variables

| Variable | Description |
|---|---|
| `SQUIRRELSTACK_TOKEN` | PAT token (overrides Keychain) |
| `SQUIRRELSTACK_ACCOUNT` | Account slug to send as `X-Account-Slug` |
| `SQUIRRELSTACK_URL` | API base URL (default: `https://squirrelstack.app`) |

## Releasing

1. Bump `VERSION` in `squirrelstack`
2. Commit and run `bin/release`

This tags the release, creates a GitHub release, and updates the Homebrew formula automatically.

## License

Proprietary. Copyright Future Mill Ltd.
