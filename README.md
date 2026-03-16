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

## Environment Variables

| Variable | Description |
|---|---|
| `SQUIRRELSTACK_TOKEN` | PAT token (overrides Keychain) |
| `SQUIRRELSTACK_URL` | API base URL (default: `https://squirrelstack.app`) |

## License

Proprietary. Copyright Future Mill Ltd.
