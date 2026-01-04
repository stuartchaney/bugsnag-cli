# bugsnag-cli

A command-line interface for interacting with the [Bugsnag API](https://bugsnagapiv2.docs.apiary.io/). List errors, view stacktraces, link issues, and watch for new errors with desktop notifications.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/bugsnag-cli.git
   cd bugsnag-cli
   ```

2. Make the script executable:
   ```bash
   chmod +x bin/bugsnag-cli
   ```

3. Add to your PATH (optional):
   ```bash
   ln -s $(pwd)/bin/bugsnag-cli /usr/local/bin/bugsnag-cli
   ```

## Requirements

- Ruby 2.6+
- A Bugsnag account with API access

## Configuration

Set your Bugsnag personal auth token as an environment variable:

```bash
export BUGSNAG_API_TOKEN="your-token-here"
```

You can generate a token at: https://app.bugsnag.com/settings/my-account

### Project Aliases (Optional)

Edit `bin/bugsnag-cli` to add shortcuts for your projects:

```ruby
PROJECTS = {
  "web" => "your-project-id-here",
  "api" => "another-project-id",
}.freeze
```

This lets you use `--project=web` instead of the full project ID.

## Usage

```
bugsnag-cli <command> <subcommand> [options]
```

### Commands

#### List Errors

```bash
# List all errors for a project
bugsnag-cli errors list --project=PROJECT_ID

# View a specific error with full stacktrace
bugsnag-cli errors list --project=PROJECT_ID --id=ERROR_ID
```

#### Link/Unlink Issues

Connect Bugsnag errors to your issue tracker (Linear, Jira, etc.):

```bash
# Link an issue
bugsnag-cli errors link --project=PROJECT_ID --id=ERROR_ID --url=https://linear.app/team/issue/ISSUE-123

# Unlink an issue
bugsnag-cli errors unlink --project=PROJECT_ID --id=ERROR_ID --url=https://linear.app/team/issue/ISSUE-123
```

#### Create Issue

Create an issue using your configured Bugsnag integration:

```bash
bugsnag-cli errors create-issue --project=PROJECT_ID --id=ERROR_ID
```

This uses whichever issue tracker integration you've configured in Bugsnag (Linear, Jira, GitHub, etc.).

#### Watch for New Errors

Monitor a project for new errors in real-time with desktop notifications (macOS):

```bash
# Watch with defaults (60s interval, 10+ events threshold)
bugsnag-cli errors watch --project=PROJECT_ID

# Custom settings
bugsnag-cli errors watch --project=PROJECT_ID --interval=30 --min-events=50
```

Options:
- `--interval=SECONDS` - Polling interval (default: 60)
- `--min-events=COUNT` - Only alert for errors with this many events (default: 10)

The watcher filters for errors first seen today and sends macOS notifications for each new error detected.

#### List Projects

Find your project IDs:

```bash
# List all projects across all organizations
bugsnag-cli projects list

# List projects for a specific organization
bugsnag-cli projects list --org=ORG_ID
```

## Examples

```bash
# List recent errors
bugsnag-cli errors list --project=5ff9e7bb1aecbb0012a6f8d9

# Get full details on a specific error
bugsnag-cli errors list --project=5ff9e7bb1aecbb0012a6f8d9 --id=64a2b3c4d5e6f7

# Link a Linear issue to an error
bugsnag-cli errors link \
  --project=5ff9e7bb1aecbb0012a6f8d9 \
  --id=64a2b3c4d5e6f7 \
  --url=https://linear.app/myteam/issue/BUG-123

# Watch for high-impact errors
bugsnag-cli errors watch --project=5ff9e7bb1aecbb0012a6f8d9 --min-events=100
```

## Output Examples

### Error List

```
ID                         SEVERITY  STATUS      EVENTS  MESSAGE
----------------------------------------------------------------------------------------------------
64a2b3c4d5e6f7890123456789 error     open          1523  Connection timeout to database
64a2b3c4d5e6f7890123456790 warning   open           342  Deprecated API endpoint called
----------------------------------------------------------------------------------------------------
2 errors
```

### Error Detail

```
====================================================================================================
ERROR: ConnectionError
====================================================================================================

Message:       Connection timeout to database
Status:        open
Severity:      error
Events:        1523
Users:         892
First seen:    2024-01-15T10:23:45Z
Last seen:     2024-01-15T14:56:12Z

----------------------------------------------------------------------------------------------------
LATEST EVENT
----------------------------------------------------------------------------------------------------

Context:       POST /api/users
URL:           https://api.example.com/api/users
Method:        POST
User:          12345 | user@example.com

----------------------------------------------------------------------------------------------------
EXCEPTION 1: ConnectionError
Message: Connection timeout to database
----------------------------------------------------------------------------------------------------

*   1. app/services/database.rb:45 in `connect`
*   2. app/services/user_service.rb:23 in `create`
*   3. app/controllers/users_controller.rb:15 in `create`
    4. gems/actionpack-7.0.0/lib/action_controller/metal.rb:234 in `dispatch`
```

## License

MIT
