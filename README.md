# SquirrelStack CLI

Command-line tool for [SquirrelStack](https://squirrelstack.app) error management.

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
squirrelstack logout    # Remove token from Keychain
```

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

The `.squirrelstack` file is plain text and contains a single line: `account=<slug>`.
Commit it if your team should share the same default for a project, or gitignore it
if it's personal.

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
