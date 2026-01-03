# PR Metrics Analyzer Usage Guide

This guide provides detailed instructions for using the `pr_metrics_analyzer.py` script.

## Command Line Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `--repo` | Target repository in `owner/repo` format. | Required (for fetching) |
| `--limit` | Maximum number of PRs to fetch. The script fetches in batches of 50. | `100` |
| `--output` | File path to save the full JSON report. | `pr_metrics_report.json` |
| `--start-date` | Filter PRs created *after* this date (YYYY-MM-DD). | None |
| `--end-date` | Filter PRs created *before* this date (YYYY-MM-DD). | None |
| `--cursor` | Resume fetching from a specific GraphQL cursor (used after a crash). | None |
| `--analyze` | Analyze an existing JSON report file instead of fetching new data. | None |
| `--summary-output` | File path to save the generated statistics summary JSON. | None |
| `--stream` | Print the summary JSON to standard output (stdout). | `False` |

## Reliability Features

### Rate Limit Handling
The GitHub API has strict rate limits. This script is designed to:
- Detect "secondary rate limit" errors (HTTP 403).
- Automatically pause execution using an exponential backoff strategy (waiting longer after each retry).
- Retry the request up to 5 times before failing.
- **Action**: You will see messages like `[!] Rate limit hit. Waiting 60 seconds...` in the console. Do not interrupt the script; it will resume automatically.

### Safety Saves
To prevent data loss during long-running jobs (e.g., fetching 4000+ PRs), the script:
- Saves all currently fetched PRs to a "safety file" (e.g., `safety_repo-name.json`) after every batch of 50 PRs.
- If the script crashes or is killed, your data up to the last batch is safe in this file.

### Resumability
If the script is interrupted (e.g., you kill it or a persistent network error occurs), it will output a **resume command** before exiting.

**Example Crash Output:**
```
[!] Script interrupted or crashed.
[!] Safety file saved at: /absolute/path/to/safety_suvie-cloud.json
[!] To resume, run the command with:
    --cursor "Y3Vyc29yOn..."
```

**To Resume:**
Simply copy and run the provided command. The script will continue fetching from where it left off. You can later merge the JSON files manually if needed (or just analyze the safety file if it has enough data).

## Advanced Statistics

When analyzing a report using `--analyze`, the script generates:

1. **Median Runtime**: The middle value of all PR runtimes (less sensitive to outliers than average).
2. **Runtime Histogram**: A distribution of PR runtimes into bins:
   - `0-2`, `2-5`, `5-10`, `10-20`, `20-30` minutes
   - `30-35`, `35-40`, `40-45`, `45-50` minutes
   - `50+` minutes
3. **Monthly Breakdown**: Statistics aggregated by month (YYYY-MM), including:
   - Total PR count per month.
   - Average and Median runtime per month.
   - Per-month histograms to track performance trends over time.

4. **Cost Savings Projection**:
   - If you provide `--prediction-reduction` (minutes saved per PR) and `--prediction-cost` (cost per minute), the report will include projected savings.
   - Shows total time/cost saved across all PRs.
   - Shows monthly savings breakdowns.
   - Example use case: "If we reduce CI time by 5 minutes per PR, how much $ do we save?"

## Workflows

### 1. Daily/Weekly Monitoring
Fetch the last 50 PRs to keep an eye on recent performance.
```bash
python3 pr_metrics_analyzer.py --repo suvie-eng/suvie-cloud --limit 50 --stream
```

### 2. Historical Analysis (Full Year)
Fetch all PRs for a specific year.
```bash
python3 pr_metrics_analyzer.py \
  --repo suvie-eng/suvie-cloud \
  --start-date 2025-01-01 \
  --end-date 2025-12-31 \
  --limit 5000 \
  --output 2025_report.json
```

### 3. Analyze Existing Data
If you have a report from a previous run (or a safety file), you can generate new stats without re-fetching.
```bash
python3 pr_metrics_analyzer.py \
  --analyze safety_suvie-cloud.json \
  --summary-output summary_2025.json
```
