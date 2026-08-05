# Installomator-Monitoring

Installomator-Monitoring is a wrapper script for running Installomator labels in sequence and recording the outcome of each run. It is intended for environments where you want a simple monitoring layer around Installomator, including persistent logging and optional Jira ticket creation when a label fails.

## What the script does

- Reads labels from `Installomator/Labels.txt`
- Ignores empty lines and comment lines that start with `#`
- Runs `Installomator/Installomator.sh` once for each actionable label
- Logs progress and results to `/Library/Logs/Management/InstallomatorMonitoring.log`
- Writes a separate failure log with full Installomator output when any label fails
- Optionally creates a Jira issue for each failed label

## Requirements

- macOS with `zsh`
- An `Installomator` directory next to `InstallomatorMonitoring.sh`
- `Installomator/Installomator.sh`
- `Installomator/Labels.txt`
- `jq` if Jira integration is enabled
- Network access to your Jira instance if Jira integration is enabled

## Repository layout

- `InstallomatorMonitoring.sh` – main monitoring script
- `README.md` – project documentation

The script expects this additional structure beside it at runtime:

- `Installomator/Installomator.sh`
- `Installomator/Labels.txt`

## Labels file

The labels file should contain one Installomator label per line. Blank lines and lines beginning with `#` are skipped.

Example:

```text
# Browsers
googlechrome
firefox

# Productivity
slack
zoom
```

## Logging

The script writes standard log output to:

`/Library/Logs/Management/InstallomatorMonitoring.log`

If any label fails, it also creates a timestamped failure log such as:

`/Library/Logs/Management/InstallomatorMonitoringLog_YYYY-MM-DD_HH-MM-SS.log`

That failure log includes:

- timestamp
- label
- Installomator exit code
- full Installomator output

## Jira integration

The script includes optional Jira issue creation for failed labels. Configure these variables in `InstallomatorMonitoring.sh`:

- `jiraEnabled`
- `jiraBaseUrl`
- `jiraProjectKey`
- `jiraIssueType`
- `jiraUserEmail`
- `jiraApiToken`
- `jiraStatusTransitionId`
- `jiraSummaryPrefix`

If `jiraEnabled` is set to `true` and the required settings are populated, the script creates a Jira issue for each failed label. If `jiraStatusTransitionId` is also set, the new issue is moved with that transition after creation.

## Usage

1. Place `InstallomatorMonitoring.sh` next to your `Installomator` folder.
2. Add the labels you want to process to `Installomator/Labels.txt`.
3. Adjust the Jira settings in the script if you want ticket creation.
4. Run the script:

```zsh
sudo zsh /path/to/InstallomatorMonitoring.sh
```

## Exit behavior

- Returns `0` when all actionable labels complete successfully
- Returns `1` when one or more labels fail
- Returns `1` if required files or log paths cannot be created

## Description

This script provides a simple monitoring layer for Installomator by processing a maintained list of labels, logging every run, capturing full failure output, and optionally raising Jira tasks for anything that does not install successfully.