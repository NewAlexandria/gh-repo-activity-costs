# gh repo-activity-costs

A robust Python script to collect and analyze GitHub Pull Request metrics, specifically focusing on **Continuous Integration (CI) Action Runtimes**.

This tool leverages the GitHub CLI (`gh`) to fetch data via GraphQL, ensuring efficient data retrieval with built-in pagination, rate limit handling, and resumability.

## Features

- **Metric Collection**: Fetches PR details (author, state, merged at) and calculates total CI runtime minutes per PR.
- **Reliability**:
  - Automatically handles "secondary rate limits" with intelligent backoff and retries.
  - Periodic "safety saves" to `safety_<repo>.json` ensure data is not lost if the script crashes.
  - **Resumability**: Can resume fetching from the exact cursor position if interrupted.
- **Analysis & Summarization**:
  - Generates a detailed JSON report of all PRs and workflow runs.
  - Produces aggregated statistics: Total/Average/Median Runtime, Monthly Breakdowns, and Runtime Histograms.
- **Filtering**: Supports filtering by `start-date` and `end-date`.

## Prerequisites

1. **Python 3**: Ensure Python 3.6+ is installed.  Script uses it for scraping resilience.
2. **GitHub CLI (`gh`)**: The script uses `gh` to communicate with the GitHub API.
   - [Install GitHub CLI](https://cli.github.com/)
   - Authenticate with: `gh auth login`

## Installation

### GitHub CLI Extension (Recommended)
You can find and install this tool as a `gh` extension:

```bash
# Find the extension
gh extension search metrics

# Install it
gh extension install NewAlexandria/gh-repo-activity-costs

# Or install from a specific release
gh extension install NewAlexandria/gh-repo-activity-costs@v1.2.0

# List installed extensions
gh extension list

# Upgrade all extensions
gh extension upgrade --all
```

### Local Development

If you're interested in running this script locally or contributing to its development, please see the [Local Development](CONTRIBUTING.md#your-first-code-contribution) section in our contributing guide.

## Quick Start

### 1. Fetch Data

Fetch the last 100 PRs from a repository:
```bash
gh repo-activity-costs --repo owner/repo --limit 100
```
This generates `pr_metrics_report.json`.

### 2. Analyze Report

Generate a summary from the report:
```bash
gh repo-activity-costs --analyze pr_metrics_report.json --stream
```

### 3. Analyze Report

For a quick look at recent PR performance, fetch the last 50 PRs to keep an eye on recent performance.
```bash
gh repo-activity-costs --repo owner/repo --limit 50 --stream
```

## Detailed Usage

For advanced usage, including resumability and date filtering, see the [Usage Guide](usage-guide.md).
