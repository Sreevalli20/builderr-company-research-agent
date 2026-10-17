# Builderr Company Research Agent

A professional company research agent for Builderr's Signalpost challenge ($2,500 prize). This agent researches Norwegian companies using official registries and public sources, producing structured reports with source attribution.

## Overview

This agent accepts Norwegian organisation numbers and returns comprehensive company profiles including:
- Legal identity and public brand
- Latest annual accounts and financial history
- Leadership and registered workplaces
- Verified official website and company-owned profiles
- Hiring and dated public activity
- Source URLs and publication dates for all claims
- Clear separation between verified facts, analysis, and unknowns

## Prerequisites

- Python 3.12 or higher
- `uv` package manager (install with `pip install uv`)

## Installation

```bash
# Install dependencies
uv sync
```

## Quick Start

### 1. Download required data

```bash
# Download BRREG registry
curl -L 'https://data.brreg.no/enhetsregisteret/api/enheter/lastned/csv' -o brreg-enheter.csv

# Download company universe
curl -L 'https://builderr.ai/signalpost-company-universe-2025.jsonl.gz' -o signalpost-universe.jsonl.gz
```

### 2. Run a 100-company smoke test

```bash
# Select 100 companies from the universe
uv run python select_entry_batch.py \
  --universe signalpost-universe.jsonl.gz \
  --count 100 \
  --output smoke-companies.jsonl

# Run the research agent
uv run python scripts/run_competition_batch.py \
  --organisations smoke-companies.jsonl \
  --bulk brreg-enheter.csv \
  --profiles-output out/smoke-profiles.jsonl \
  --output out/smoke-envelopes.jsonl \
  --report out/smoke-report.json \
  --run-id smoke-001 \
  --expected-count 100
```

### 3. Review results

The smoke test generates:
- `out/smoke-envelopes.jsonl` - One terminal envelope per company
- `out/smoke-profiles.jsonl` - Detailed company profiles
- `out/smoke-report.json` - Run statistics and validation

## Running on a Custom Batch

To research specific companies:

```bash
uv run python scripts/run_competition_batch.py \
  --organisations your-companies.jsonl \
  --bulk brreg-enheter.csv \
  --profiles-output out/profiles.jsonl \
  --output out/envelopes.jsonl \
  --report out/report.json \
  --run-id custom-001 \
  --expected-count <number_of_companies>
```

## Output Format

Each company envelope contains:
- `organisation_number` - Norwegian company ID
- `run` - Execution metadata (run_id, timestamps, terminal_status)
- `claims` - Factual claims with availability states and evidence IDs
- `evidence` - Source URLs, retrieval times, content hashes, and claim spans
- `changes` - Material changes since previous run (if applicable)
- `errors` - Any errors encountered
- `operations` - Request count, runtime, and cost

Availability states: `available`, `not_available`, `blocked`, `not_applicable`, `ambiguous`, `failed`

## Source Policy

This agent uses only permitted public sources:
- Brønnøysund Register Centre (official Norwegian registry)
- Company websites and official profiles
- Public registries and reputable reporting

The agent respects robots.txt, rate limits, and terms of service. Sources with restricted access are marked as `blocked` or `not_available`.

## Testing

```bash
# Run unit tests
uv run --with pytest pytest -q
```

## Submission

For Builderr's Signalpost challenge, this submission includes:
- Repository URL
- 100-company smoke test report (`out/smoke-report.json`)
- Run command documented above
- No external APIs or paid services (expected cost: $0)

## Technical Details

- **Dependencies**: Beautiful Soup, Trafilatura, Pydantic, lxml, tldextract
- **Runtime**: ~6 minutes for 100 companies (538 requests, p50: 954ms, p95: 1013ms)
- **Cost**: $0 (uses only free public sources)
- **Accuracy**: Exact company matching with source attribution

## License

This is a competition entry for Builderr's Signalpost challenge. Code follows open-source practices while respecting data source terms and conditions.

## Contact

For challenge-related inquiries, contact: submit@builderr.ai
