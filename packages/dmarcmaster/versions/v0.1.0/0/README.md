# dmarcmaster

Extract and display DMARC aggregate reports from Maildir for AI analysis.

Reads DMARC XML reports (gzipped or zipped) directly from Maildir messages and outputs structured, readable summaries.

## Install

```
nimble install dmarcmaster
```

## Usage

```
# Table output (default) from ~/Maildir/.Postmaster
dmarcmaster

# New messages only
dmarcmaster -n

# JSON output for programmatic use
dmarcmaster -j

# Summary: group by source IP, flag anomalies
dmarcmaster -s

# Filter by date
dmarcmaster --since=2026-03-20

# Custom maildir path
dmarcmaster /path/to/maildir
```

## Output Modes

**Table** (default): One line per record with date, submitter, source IP, DKIM/SPF results, and envelope info. Anomalous rows are flagged with `!`.

**JSON** (`-j`): Array of flat record objects — pipe to `jq` or feed to AI tools.

**Summary** (`-s`): Grouped by source IP with pass/fail counts and anomaly highlights (unknown IPs, DKIM/SPF failures, non-none dispositions).

## How It Works

1. Walks Maildir `new/` and `cur/` directories
2. Parses MIME structure to find gzip/zip attachments
3. Decompresses and parses DMARC XML
4. Deduplicates by report ID
5. Outputs in chosen format
