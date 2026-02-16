# Sidcar-Derived Discovery Data

This folder contains VIN-filtered command discovery artifacts extracted from Sidcar `db.sqlite` captures.

## Source VIN

- `KNAE55LC2L6081446`

## 2020 Artifacts

- `tests/test_cases_sidcar/2020/commands/*.yaml`
  - Generated command test fixtures from observed responses.
- `tests/test_cases_sidcar/2020/support_summary.yaml`
  - Summary of decoded command IDs, signal IDs, and case counts.
- `tests/test_cases_sidcar/2020/raw_command_counts.tsv`
  - Raw command frequencies observed in Sidcar sessions.
- `tests/test_cases_sidcar/2020/candidate_missing_commands.tsv`
  - Prioritized commands with non-`NO DATA` responses that are not currently decoded by the active registry.

## Regeneration

Run:

```bash
python3 tests/extract_sidcar_to_yaml.py \
  --db Data/db.sqlite \
  --vin KNAE55LC2L6081446 \
  --out-dir Data/generated_tests_vin/2020/commands \
  --max-rows 600000 \
  --max-cases-per-command 50
```

Then copy into this folder as needed.

## Unknown Command Clustering

To cluster VIN-observed commands that are not currently modeled:

```bash
python3 tests/cluster_sidcar_unknown_commands.py \
  --db Data/db.sqlite \
  --vin KNAE55LC2L6081446 \
  --out-dir tests/test_cases_sidcar/2020 \
  --max-rows 800000
```

This writes:

- `tests/test_cases_sidcar/2020/unknown_clusters.tsv`
- `tests/test_cases_sidcar/2020/known_seen.tsv`

## Negative-Response Tracking

Use `tests/test_cases_sidcar/2020/negative_response_only.tsv` to track commands that consistently return `7F` negative responses in captured sessions.

`0902` (Mode 09 PID 02 VIN) appears in captures but is not directly modelable in this repository schema, which currently supports services `01`, `21`, and `22`. For this VIN, equivalent VIN data is captured via `7E0.7E8.22F190`.

## Negative Context Analysis

To inspect setup-command context before negative responses:

```bash
python3 tests/analyze_sidcar_negative_context.py \
  --db Data/db.sqlite \
  --vin KNAE55LC2L6081446 \
  --out tests/test_cases_sidcar/2020/negative_context.tsv
```

This writes `tests/test_cases_sidcar/2020/negative_context.tsv`.

## Positive Byte Correlation

To correlate positive raw-byte groups (`22E001/22E002/22E004/22E00A`) against known anchors:

```bash
python3 tests/correlate_sidcar_positive_groups.py \
  --db Data/db.sqlite \
  --vin KNAE55LC2L6081446 \
  --model-year 2020 \
  --out tests/test_cases_sidcar/2020/raw_correlations_positive.tsv \
  --window-sec 2.0 \
  --min-n 500 \
  --min-abs-r 0.2
```

This writes:

- `tests/test_cases_sidcar/2020/raw_correlations_positive.tsv`
- `tests/test_cases_sidcar/2020/raw_correlation_notes.md`

## Formula Fitting (Confidence-Gated)

To fit raw-byte formulas with context and session-split confidence checks:

```bash
python3 tests/fit_sidcar_positive_formulas.py \
  --db Data/db.sqlite \
  --vin KNAE55LC2L6081446 \
  --model-year 2020 \
  --out tests/test_cases_sidcar/2020/raw_formula_candidates.tsv \
  --window-sec 2.0 \
  --context-window-sec 60 \
  --min-n-global 500 \
  --min-abs-r-global 0.2 \
  --min-n-context 200
```

This writes:

- `tests/test_cases_sidcar/2020/raw_formula_candidates.tsv`
- `tests/test_cases_sidcar/2020/raw_formula_shortlist.tsv`
