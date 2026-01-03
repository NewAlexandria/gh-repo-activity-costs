# PR Metrics Analyzer

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

1. **Python 3**: Ensure Python 3.6+ is installed.
2. **GitHub CLI (`gh`)**: The script uses `gh` to communicate with the GitHub API.
   - [Install GitHub CLI](https://cli.github.com/)
   - Authenticate with: `gh auth login`

## Quick Start

### 1. Fetch Data
Fetch the last 100 PRs from a repository:
```bash
python3 pr_metrics_analyzer.py --repo owner/repo --limit 100
```
This generates `pr_metrics_report.json`.

### 2. Analyze Report
Generate a summary from the report:
```bash
python3 pr_metrics_analyzer.py --analyze pr_metrics_report.json --stream
```

## Detailed Usage

For advanced usage, including resumability and date filtering, see the [Usage Guide](usage-guide.md).
