# gh2fizzy

Migrate GitHub Issues to Fizzy Cards - a command-line tool that transfers issues from GitHub to [Fizzy](https://fizzy.do) with full comment history preservation.

## Features

- Migrate GitHub issues to Fizzy cards with title and description
- Convert GitHub labels to Fizzy tags (auto-created)
- Preserve all comments with author attribution and timestamps
- Filter issues by state, labels, assignee, or milestone
- Migrate specific issues by number
- Dry-run mode to preview migrations
- Post-migration actions: close issues and/or add labels

## Prerequisites

### Required Tools

1. **GitHub CLI (`gh`)** - [Installation](https://cli.github.com/)
   ```bash
   gh --version
   gh auth login
   ```

2. **Fizzy CLI (`fizzy`)** - [Installation](https://github.com/robzolkos/fizzy-cli/releases)
   ```bash
   fizzy --version
   fizzy auth login YOUR_TOKEN
   ```

3. **jq** - JSON processor
   ```bash
   # macOS
   brew install jq

   # Linux
   apt-get install jq  # Debian/Ubuntu
   yum install jq      # RHEL/CentOS
   ```

### Optional Tools

4. **cmark** or **pandoc** - Markdown to HTML conversion

   GitHub issues use Markdown formatting. For proper rendering in Fizzy, install one of these converters:

   ```bash
   # cmark (recommended - lightweight)
   # macOS
   brew install cmark

   # Linux
   apt-get install cmark  # Debian/Ubuntu

   # OR pandoc (full-featured)
   # macOS
   brew install pandoc

   # Linux
   apt-get install pandoc
   ```

   If neither is installed, content will be migrated as plain text (Markdown syntax visible but not rendered).

### Environment Variables (Optional)

```bash
export FIZZY_ACCOUNT="YOUR_ACCOUNT_ID"
```

## Installation

```bash
# Clone or download the script
curl -O https://raw.githubusercontent.com/robzolkos/gh2fizzy/main/gh2fizzy
chmod +x gh2fizzy

# Or move to a directory in your PATH
mv gh2fizzy /usr/local/bin/
```

## Quick Start

```bash
# Migrate all open issues to a Fizzy board
gh2fizzy --board 12345

# Preview what would be migrated (dry run)
gh2fizzy --board 12345 --dry-run

# Migrate from a specific repository
gh2fizzy --board 12345 -R owner/repo
```

## Usage

```
gh2fizzy --board BOARD_ID [OPTIONS]
```

### Required Arguments

| Argument | Description |
|----------|-------------|
| `--board BOARD_ID` | Fizzy board ID to create cards in |

### GitHub Filters

| Argument | Short | Description | Default |
|----------|-------|-------------|---------|
| `--repo` | `-R` | GitHub repository (owner/repo) | Current repo |
| `--state` | | Issue state: `open`, `closed`, `all` | `open` |
| `--label` | `-l` | Filter by label (repeatable) | None |
| `--assignee` | | Filter by assignee username | None |
| `--milestone` | | Filter by milestone title | None |
| `--limit` | | Maximum issues to migrate | 100 |
| `--issue` | `-i` | Specific issue number (repeatable) | None |

### Fizzy Options

| Argument | Description | Default |
|----------|-------------|---------|
| `--account` | Fizzy account ID | `$FIZZY_ACCOUNT` |
| `--column` | Place cards in specific column | Triage |

### Behavior Options

| Argument | Short | Description |
|----------|-------|-------------|
| `--dry-run` | | Preview without making changes |
| `--verbose` | `-v` | Show detailed output |
| `--quiet` | `-q` | Suppress non-error output |
| `--close-issues` | | Close GitHub issues after migration |
| `--add-migrated-label` | | Add "migrated-to-fizzy" label |

## Examples

### Basic Migration

```bash
# Migrate all open issues
gh2fizzy --board 12345

# Migrate from a specific repo
gh2fizzy --board 12345 -R myorg/myrepo
```

### Filtered Migration

```bash
# Migrate only bugs
gh2fizzy --board 12345 -l bug

# Migrate issues with multiple labels
gh2fizzy --board 12345 -l bug -l urgent

# Migrate issues assigned to a user
gh2fizzy --board 12345 --assignee octocat

# Migrate closed issues
gh2fizzy --board 12345 --state closed

# Migrate all issues (open and closed)
gh2fizzy --board 12345 --state all
```

### Specific Issues

```bash
# Migrate specific issues by number
gh2fizzy --board 12345 -i 42 -i 43 -i 44
```

### With Post-Migration Actions

```bash
# Migrate and close issues in GitHub
gh2fizzy --board 12345 --close-issues

# Migrate and label issues as migrated
gh2fizzy --board 12345 --add-migrated-label

# Both: close and label
gh2fizzy --board 12345 --close-issues --add-migrated-label
```

### Targeting a Specific Column

```bash
# Place migrated cards in a specific column
gh2fizzy --board 12345 --column 67890
```

### Dry Run (Preview)

```bash
# See what would be migrated without making changes
gh2fizzy --board 12345 --dry-run --verbose
```

## Data Mapping

| GitHub | Fizzy |
|--------|-------|
| Issue title | Card title |
| Issue body | Card description |
| Issue labels | Card tags (auto-created) |
| Issue comments | Single card comment with attribution |

### Comment Format

GitHub comments are combined into a single Fizzy comment:

```markdown
**Migrated Comments from GitHub Issues**

---
**@octocat** commented on 2024-01-15 10:30:

Original comment text here...

---
**@developer** commented on 2024-01-16 14:22:

Another comment with full attribution...
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Invalid arguments |
| 3 | Missing dependencies |
| 4 | Authentication error |
| 5 | Partial failure (some issues failed) |

## Troubleshooting

### "GitHub CLI not authenticated"

```bash
gh auth login
gh auth status  # Verify
```

### "Fizzy CLI not authenticated"

```bash
# Get token from https://app.fizzy.do/my/profile
fizzy auth login YOUR_TOKEN
fizzy identity show  # Verify
```

### "Missing required tools"

Install the missing tools listed in the error message. See [Prerequisites](#prerequisites).

### "No issues found matching the criteria"

- Check if you're in a git repository (or use `-R owner/repo`)
- Verify the state filter matches your issues
- Try `gh issue list` to see available issues

### Finding Board and Column IDs

```bash
# List all boards
fizzy board list

# List columns for a board
fizzy column list --board BOARD_ID
```

## License

MIT
