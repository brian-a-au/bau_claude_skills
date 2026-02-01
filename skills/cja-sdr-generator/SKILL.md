---
name: cja-sdr-generator
description: Guide for using cja_auto_sdr to generate Solution Design Reference documents from Adobe CJA, compare Data Views, and track changes. Use when working with CJA documentation, audits, or migration validation.
---

# CJA SDR Generator Skill

This skill provides guidance on using the [cja_auto_sdr](https://github.com/brian-a-au/cja_auto_sdr) tool to automate Solution Design Reference (SDR) documentation from Adobe Customer Journey Analytics.

## When to Use This Skill

Invoke this skill when:
- Generating SDR documentation from CJA Data Views
- Comparing Data Views between environments (Production vs Staging)
- Tracking changes to Data View configurations over time
- Setting up automated CJA audits in CI/CD pipelines
- Managing multiple Adobe organizations with profiles
- Troubleshooting cja_auto_sdr errors

---

## Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/brian-a-au/cja_auto_sdr.git
cd cja_auto_sdr

# Install dependencies (macOS/Linux)
curl -LsSf https://astral.sh/uv/install.sh | sh
uv sync

# Windows alternative
python -m venv .venv
.venv\Scripts\activate
pip install -e .
```

### Configuration

Create `config.json` with your Adobe Developer Console credentials:

```json
{
  "org_id": "YOUR_ORG_ID@AdobeOrg",
  "client_id": "YOUR_CLIENT_ID",
  "secret": "YOUR_CLIENT_SECRET",
  "scopes": "your_scopes_from_developer_console"
}
```

> **Prerequisite:** You need OAuth Server-to-Server credentials from Adobe Developer Console with both **CJA API** and **Experience Platform API** enabled. See the `adobe-api-setup` skill for detailed setup instructions.

### Verify Setup

```bash
# Validate configuration
uv run cja_auto_sdr --validate-config

# List accessible Data Views
uv run cja_auto_sdr --list-dataviews
```

---

## Two Main Modes

| Mode | Purpose | Default Output |
|------|---------|----------------|
| **SDR Generation** | Document Data View components (metrics, dimensions) | Excel workbook |
| **Diff Comparison** | Compare two Data Views or track changes over time | Console output |

---

## SDR Generation Commands

### Basic Usage

```bash
# Generate SDR by Data View ID
cja_auto_sdr dv_12345

# Generate SDR by name (case-sensitive, exact match required)
cja_auto_sdr "Production Analytics"

# Generate and open file immediately
cja_auto_sdr dv_12345 --open

# Quick stats only (no full report)
cja_auto_sdr dv_12345 --stats
```

### Output Formats

```bash
# Excel (default) - includes Data Quality sheet
cja_auto_sdr dv_12345 --format excel

# Other formats
cja_auto_sdr dv_12345 --format csv
cja_auto_sdr dv_12345 --format json
cja_auto_sdr dv_12345 --format html
cja_auto_sdr dv_12345 --format markdown

# Generate all formats at once
cja_auto_sdr dv_12345 --format all

# Output to stdout (for piping)
cja_auto_sdr dv_12345 --format json --output -
```

### Batch Processing

```bash
# Process multiple Data Views in parallel
cja_auto_sdr dv_12345 dv_67890 dv_abcde

# Mix IDs and names
cja_auto_sdr dv_12345 "Production Analytics" "Staging"

# Continue processing if some fail
cja_auto_sdr dv_1 dv_2 dv_3 --continue-on-error

# Custom output directory
cja_auto_sdr dv_12345 --output-dir ./reports
```

### Performance Options

```bash
# Skip data quality validation (20-30% faster)
cja_auto_sdr dv_12345 --skip-validation

# Enable caching for repeated runs (50-90% faster on cache hits)
cja_auto_sdr dv_12345 --enable-cache

# Adjust parallel workers (default: auto)
cja_auto_sdr dv_1 dv_2 dv_3 --workers 4
```

---

## Diff Comparison Commands

### Compare Two Data Views

```bash
# Compare by ID
cja_auto_sdr --diff dv_12345 dv_67890

# Compare by name
cja_auto_sdr --diff "Production" "Staging"

# Show only changes (hide unchanged components)
cja_auto_sdr --diff dv_12345 dv_67890 --changes-only

# Custom labels in output
cja_auto_sdr --diff dv_12345 dv_67890 --diff-labels "Before" "After"
```

### Snapshot Management

```bash
# Save a snapshot for later comparison
cja_auto_sdr dv_12345 --snapshot ./snapshots/baseline.json

# Compare current state to a saved snapshot
cja_auto_sdr dv_12345 --diff-snapshot ./snapshots/baseline.json

# Compare against most recent snapshot (auto-finds it)
cja_auto_sdr dv_12345 --compare-with-prev

# Compare two snapshot files (no API calls needed)
cja_auto_sdr --compare-snapshots ./old.json ./new.json

# Auto-save snapshots during diff operations
cja_auto_sdr --diff dv_12345 dv_67890 --auto-snapshot

# Keep only last N snapshots per Data View
cja_auto_sdr --diff dv_12345 dv_67890 --auto-snapshot --keep-last 10
```

### Diff Output Formats

```bash
# Console output (default for diff)
cja_auto_sdr --diff dv_12345 dv_67890

# Markdown (great for documentation)
cja_auto_sdr --diff dv_12345 dv_67890 --format markdown

# JSON (for integrations)
cja_auto_sdr --diff dv_12345 dv_67890 --format json

# Excel workbook
cja_auto_sdr --diff dv_12345 dv_67890 --format excel

# GitHub/GitLab PR comment format
cja_auto_sdr --diff dv_12345 dv_67890 --format-pr-comment
```

### CI/CD Integration

Exit codes for pipeline automation:

| Code | Meaning |
|------|---------|
| 0 | Success (no changes found) |
| 1 | Error occurred |
| 2 | Changes detected |
| 3 | Changes exceeded threshold |

```bash
# Fail pipeline if changes exceed 10%
cja_auto_sdr --diff dv_12345 dv_67890 --warn-threshold 10

# Use in CI script
cja_auto_sdr --diff dv_prod dv_staging --quiet-diff
case $? in
  0) echo "No differences" ;;
  2) echo "Review needed" ;;
  3) echo "Too many changes" && exit 1 ;;
esac
```

---

## Multi-Organization Profile Management

Manage credentials for multiple Adobe organizations:

```bash
# Create a profile interactively
cja_auto_sdr --profile-add client-a

# List all profiles
cja_auto_sdr --profile-list

# Use a specific profile
cja_auto_sdr --profile client-a --list-dataviews
cja_auto_sdr -p client-b "Main Data View"

# Test profile connectivity
cja_auto_sdr --profile-test client-a

# Set default profile via environment
export CJA_PROFILE=client-a
cja_auto_sdr --list-dataviews  # Uses client-a automatically
```

Profiles are stored in `~/.cja/orgs/<profile-name>/config.json`.

---

## Common Troubleshooting

### Configuration Errors

| Error | Solution |
|-------|----------|
| `Configuration file not found` | Run `cja_auto_sdr --sample-config` to generate template |
| `Missing required field: 'org_id'` | Add all required fields to config.json |
| `Configuration file is not valid JSON` | Check for missing commas, use double quotes |

### Authentication Errors

| Error | Solution |
|-------|----------|
| `CJA INITIALIZATION FAILED` | Verify credentials match Adobe Developer Console |
| `HTTP 401 Unauthorized` | Check Client Secret is current (not regenerated) |
| `HTTP 403 Forbidden` | Ensure both CJA API and AEP API are added to project |

### Data View Errors

| Error | Solution |
|-------|----------|
| `Data view not found` | Run `--list-dataviews` to see accessible views |
| `Name not found (case-sensitive)` | Copy exact name from `--list-dataviews` including case |
| `No data views found` | Check product profile permissions in Admin Console |

### Debug Mode

```bash
# Enable verbose logging
cja_auto_sdr dv_12345 --log-level DEBUG

# JSON logging for automated analysis
cja_auto_sdr dv_12345 --log-format json

# Dry run (validate without generating output)
cja_auto_sdr dv_12345 --dry-run
```

Log files are saved to `logs/` directory.

---

## Quick Reference

### Discovery Commands

```bash
cja_auto_sdr --list-dataviews              # List all Data Views
cja_auto_sdr --list-dataviews --format json # JSON output for scripting
cja_auto_sdr --interactive                  # Interactive Data View selection
cja_auto_sdr dv_12345 --stats              # Quick component count
cja_auto_sdr --validate-config             # Test configuration
```

### Common Options

| Option | Purpose |
|--------|---------|
| `--profile NAME`, `-p` | Use named profile |
| `--output-dir PATH` | Save output to directory |
| `--format FORMAT` | Output format (excel, csv, json, html, markdown, all) |
| `--open` | Open generated file immediately |
| `--skip-validation` | Skip data quality checks (faster) |
| `--continue-on-error` | Don't stop batch on failures |
| `--log-level DEBUG` | Verbose logging |
| `--dry-run` | Validate without generating output |

### Environment Variables

```bash
# Credentials (override config.json)
export ORG_ID="your_org_id@AdobeOrg"
export CLIENT_ID="your_client_id"
export SECRET="your_client_secret"
export SCOPES="your_scopes"

# Optional settings
export OUTPUT_DIR="./reports"
export LOG_LEVEL="INFO"
export CJA_PROFILE="default-profile"
```

---

## Resources

| Resource | URL |
|----------|-----|
| GitHub Repository | https://github.com/brian-a-au/cja_auto_sdr |
| CJA API Documentation | https://developer.adobe.com/cja-apis/docs/ |
| Adobe Developer Console | https://developer.adobe.com/console/ |
| cjapy Library | https://github.com/pitchmuc/cjapy |
