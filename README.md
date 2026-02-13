# Task Scheduling

Public repository to host GitHub Actions scheduled workflows that support applications by running automated tasks on a schedule.

## Overview

This repository contains GitHub Actions workflows designed to run on scheduled intervals to perform automated tasks such as:
- Calling Azure Web APIs
- Running health checks
- Triggering batch jobs
- Data synchronization tasks
- And other scheduled operations

## Workflows

### Scheduled Azure API Call

Located at: `.github/workflows/scheduled-azure-api-call.yml`

This workflow makes scheduled HTTP requests to an Azure Web API endpoint.

**Schedule:** Runs daily at 8:00 AM UTC (configurable)

**Features:**
- Scheduled execution via cron syntax
- Manual trigger option via `workflow_dispatch`
- Secure credential management using GitHub Secrets
- HTTP response validation
- Error handling and logging

## Setup Instructions

### 1. Configure GitHub Secrets

To use the scheduled Azure API call workflow, you need to set up the following secrets in your repository:

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret** and add the following:

| Secret Name | Description | Example |
|------------|-------------|---------|
| `AZURE_API_URL` | The full URL of your Azure Web API endpoint | `https://your-app.azurewebsites.net/api/endpoint` |
| `AZURE_API_KEY` | Your API authentication token or key | `Bearer eyJ0eXAiOiJKV1QiLCJhbGc...` |

### 2. Customize the Schedule

The workflow uses cron syntax to define when it runs. Edit `.github/workflows/scheduled-azure-api-call.yml`:

```yaml
on:
  schedule:
    - cron: '0 8 * * *'  # Runs at 8:00 AM UTC daily
```

**Common cron schedule examples:**
- `'0 * * * *'` - Every hour
- `'*/15 * * * *'` - Every 15 minutes
- `'0 0 * * *'` - Daily at midnight UTC
- `'0 9 * * 1-5'` - Weekdays at 9:00 AM UTC
- `'0 0 1 * *'` - First day of every month

Use [crontab.guru](https://crontab.guru/) to help build your cron expressions.

### 3. Customize the API Request

Modify the API call in the workflow to match your specific requirements:

```yaml
- name: Call Azure Web API
  run: |
    response=$(curl -s -w "\n%{http_code}" \
      -X POST \  # Change HTTP method (GET, POST, PUT, DELETE)
      -H "Content-Type: application/json" \
      -H "Authorization: Bearer ${AZURE_API_KEY}" \
      -d '{"your": "payload"}' \  # Customize request body
      "${AZURE_API_URL}")
```

### 4. Manual Triggering

You can manually trigger the workflow from the GitHub Actions UI:

1. Go to **Actions** tab in your repository
2. Select the **Scheduled Azure API Call** workflow
3. Click **Run workflow** button
4. Choose the branch and click **Run workflow**

## Adding New Workflows

To add additional scheduled workflows:

1. Create a new `.yml` file in `.github/workflows/`
2. Define your schedule using the `on.schedule` trigger
3. Add necessary secrets in repository settings
4. Commit and push the workflow file

Example workflow structure:

```yaml
name: Your Workflow Name

on:
  schedule:
    - cron: '0 12 * * *'
  workflow_dispatch:

jobs:
  your-job:
    runs-on: ubuntu-latest
    steps:
      - name: Your Task
        run: |
          # Your automation script here
```

## Monitoring

- View workflow runs in the **Actions** tab
- Check logs for each workflow execution
- Set up notifications for workflow failures in repository settings

## Security Best Practices

- ✅ Always use GitHub Secrets for sensitive data (API keys, tokens, URLs)
- ✅ Never commit secrets directly in workflow files
- ✅ Use least-privilege access for API keys
- ✅ Regularly rotate API keys and tokens
- ✅ Review workflow run logs to ensure no sensitive data is exposed

## Troubleshooting

### Workflow doesn't run on schedule
- GitHub Actions may delay scheduled workflows during high load
- Scheduled workflows are disabled if there's no activity in the repository for 60 days
- Ensure the workflow file syntax is valid

### API call fails
- Verify secrets are set correctly in repository settings
- Check that the API endpoint is accessible and valid
- Review the workflow logs for error messages
- Test the API endpoint manually using curl or Postman

### Debugging
Enable debug logging by setting repository secrets:
- `ACTIONS_STEP_DEBUG` = `true` - Enable debug logging
- `ACTIONS_RUNNER_DEBUG` = `true` - Enable runner diagnostic logging

## Contributing

To contribute new scheduled workflows or improvements:

1. Fork the repository
2. Create a feature branch
3. Add or modify workflows
4. Test your changes
5. Submit a pull request

## License

This repository is public and available for use in your projects.
