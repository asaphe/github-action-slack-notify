# github-action-slack-notify

A composite GitHub Action that sends Slack notifications to the workflow trigger user (via DM) or to a channel. Resolves GitHub users to Slack users via organization verified domain emails.

## Usage

### Direct Message to Trigger User

```yaml
- uses: asaphe/github-action-slack-notify@v1
  with:
    slack_bot_token: ${{ secrets.SLACK_BOT_TOKEN }}
    github_app_token: ${{ secrets.GH_APP_TOKEN }}
    workflow_status: ${{ job.status }}
    email_domain: example.com
```

### Send to Channel

```yaml
- uses: asaphe/github-action-slack-notify@v1
  with:
    slack_bot_token: ${{ secrets.SLACK_BOT_TOKEN }}
    workflow_status: ${{ job.status }}
    channel_id: C0123456789
    custom_message: "Deployment complete"
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `slack_bot_token` | Slack Bot Token | Yes | - |
| `github_app_token` | GitHub App Token with `read:org` scope (for email lookup) | No | `""` |
| `workflow_status` | Status: started, success, failure, cancelled | Yes | - |
| `custom_message` | Custom message to include | No | `""` |
| `email_domain` | Email domain for fallback user lookup (e.g., `example.com`) | No | `""` |
| `channel_id` | Slack channel ID (sends to channel instead of DM) | No | `""` |

## How It Works

### DM Mode (default)
1. Uses GitHub GraphQL API to find the user's organization-verified email
2. Prefers emails matching the configured `email_domain`
3. Falls back to `{username}@{email_domain}` if no verified email found
4. Looks up the Slack user ID by email
5. Sends a formatted DM with workflow status, repository, and run link

### Channel Mode
When `channel_id` is set, sends a plain text message directly to the channel (bot auto-joins if needed).

### Trigger Events
Notifications are sent for: `workflow_dispatch`, `pull_request`, `schedule`, `release`

## Required Slack Bot Scopes

- `chat:write` — Send messages
- `users:read.email` — Look up users by email
- `channels:join` — Auto-join channels (for channel mode)

## License

Apache 2.0 - See [LICENSE](LICENSE) for details.
