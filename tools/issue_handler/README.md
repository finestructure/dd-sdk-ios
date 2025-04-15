# GitHub Issue Handler

A tool that automatically analyzes new GitHub issues for the Datadog iOS SDK using OpenAI and posts summaries to Slack.

## Features

- 🔍 Fetches GitHub issue details
- 🤖 Analyzes issues using OpenAI
- 💬 Posts analysis to Slack
- 🔄 Runs automatically on new issues via GitHub Actions
- 🛠️ Can be run manually for specific issues

## Setup

### 1. Create Virtual Environment

```bash
# Create a virtual environment
python3 -m venv venv

# Activate the virtual environment
source venv/bin/activate
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure Environment

First, create your local environment file:
```bash
# Create .env file from template
./setup_env.sh
```

This creates a `.env` file that you'll need to fill with the required tokens. The file looks like this:
```bash
# GitHub token with repo access
GITHUB_TOKEN=

# OpenAI API token
OPENAI_TOKEN=

# Slack webhook URL (for posting notifications)
SLACK_WEBHOOK_URL=

# Slack channel ID (where to post notifications)
SLACK_CHANNEL_ID=
```

### 3. Required Tokens

You'll need these tokens to use the tool. Here's how to get each one:

#### GitHub Token
1. Go to [GitHub Settings > Developer Settings > Personal Access Tokens](https://github.com/settings/tokens)
2. Generate a new token with `repo` scope
3. Copy the token (starts with `ghp_`)

#### OpenAI Token
1. Go to [OpenAI API Keys](https://platform.openai.com/api-keys)
2. Create a new API key
3. Copy the key (starts with `sk-`)

#### OpenAI System Prompt
1. Create your AI prompt for analyzing GitHub issues
2. This prompt will be used to guide the AI's analysis and response generation

#### Slack Access
The iOS SDK team already has a Slack app configured for this tool. You'll need:

1. **Slack Webhook URL**:
   - Create a webhook in your Slack workspace: https://api.slack.com/apps
   - Go to "Incoming Webhooks" and create a new webhook
   - Copy the webhook URL (starts with `https://hooks.slack.com/services/`)
   - Store it securely and never commit it

2. **Slack Channel Setup**:
   - Get the channel ID by right-clicking the channel in Slack and selecting "Copy Link"
   - The ID starts with "C" (e.g., "C12345678")
   - Invite the bot to your channel: `/invite @bot-name`

Note: If you need to post to a new channel, make sure to:
1. Invite the bot to that channel first
2. Use that channel's ID in your `.env` file

## Usage

### Run Manually

Make sure your virtual environment is activated, then analyze a specific issue:
```bash
python src/analyze_issue.py ISSUE_NUMBER
```

For example:
```bash
python src/analyze_issue.py 1234
```

### GitHub Action

The tool runs:
1. Automatically when a new issue is opened in the repository
2. Manually when triggered through GitHub Actions with an issue number

The GitHub Action uses repository secrets for authentication. These are already configured in the repository settings:
- `GITHUB_TOKEN` (automatically provided)
- `OPENAI_TOKEN`
- `OPENAI_SYSTEM_PROMPT` (contains the AI prompt for analyzing issues)
- `SLACK_WEBHOOK_URL`
- `SLACK_CHANNEL_ID`

## Output

For each issue, the tool will:
1. Analyze the issue using OpenAI
2. Post a message to Slack containing:
   - GitHub issue notification
   - Analysis summary
   - Suggested response
   - Follow-up questions
   - Confidence level

## Development

- Source code is in `src/`
- Tests are in `tests/`
- Environment variables are managed via `.env`

### Current Architecture

Main Components:
- analyze_issue.py - Main entry point that orchestrates the workflow
- github_handler.py - Fetches GitHub issue details via API
- openai_handler.py - Analyzes issues using OpenAI GPT-3.5-turbo
- slack_handler.py - Posts notifications and analysis to Slack
- GitHub Action - Automatically triggers on new issues

Workflow:
- GitHub issue is opened → triggers GitHub Action
- Fetches issue details from GitHub API
- Posts initial notification to Slack
- Analyzes issue with OpenAI using a custom prompt
- Posts Github issue and analysis on Slack

## Security Notes

- Never commit your `.env` file
- Keep all tokens secure and private
- Use GitHub Secrets for CI/CD
- The `.env` file is git-ignored by default
- Don't share tokens in public channels or repositories

## Running Tests

Make sure your virtual environment is activated, then run all tests:
```bash
pytest tests/
```

## Development Notes

- Always activate your virtual environment before running the tool: `source venv/bin/activate`
- The virtual environment is already git-ignored by default
- If you need to recreate the virtual environment, delete the `venv/` folder and run the setup steps again