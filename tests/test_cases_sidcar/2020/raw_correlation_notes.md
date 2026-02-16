# Positive Group Correlation Notes (VIN `KNAE55LC2L6081446`)

These are statistical correlations only, not final decoded formulas.

## Highest-Confidence Candidates

- `STINGER_22E001_RAW_02`
  - Strongly tracks `VPWR` (`r=0.899`, `n=72940`)
  - Likely a voltage-related channel (possibly filtered/scaled battery/system voltage)
- `STINGER_22E001_RAW_03`
  - Tracks `VPWR` (`r=0.686`, `n=72940`)
  - Likely another voltage-related channel
- `STINGER_22E001_RAW_06`
  - Tracks `VSS` (`r=0.826`, `n=2068`) and `TP` (`r=0.714`, `n=910`)
  - Also tracks `STINGER_TURBO_BOOST` (`r=0.505`, `n=463053`)
  - Likely a load/speed/boost-linked channel

## Turbo/Wastegate-Linked Candidates

- `STINGER_22E004_RAW_02` tracks `STINGER_WG_SETPOINT` (`r=0.569`) and `STINGER_WG_DUTY` (`r=0.533`)
- `STINGER_22E004_RAW_05` tracks `STINGER_WG_SETPOINT` (`r=0.353`) and `STINGER_WG_DUTY` (`r=0.332`)
- `STINGER_22E00A_RAW_03` inversely tracks `STINGER_WG_SETPOINT` (`r=-0.488`) and `STINGER_WG_DUTY` (`r=-0.462`)
- `STINGER_22E00A_RAW_05` tracks `STINGER_WG_SETPOINT` (`r=0.481`) and `STINGER_WG_DUTY` (`r=0.448`)

## Lower-Confidence / Possibly Confounded

- `STINGER_22E00A_RAW_06` vs `FLI` (`r=-0.258`, `n=4222`) may be drive-cycle confounding, not direct meaning
- `STINGER_22E004_RAW_03` (`r=-0.354` with `VSS`) may be an inverse or derived term

## Validation Path Before Promotion

1. Capture targeted logs with controlled sweeps:
   - idle to steady cruise (speed sweep)
   - fixed-speed throttle blips
   - battery-voltage transitions (engine off/on, accessory loads)
2. Fit candidate linear transforms (`value = a*raw + b`) per hypothesis.
3. Promote only channels that remain stable across multiple sessions/conditions.

## Tight-Confidence Formula Pass

Formula candidates were generated with:

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

Key outputs:

- `tests/test_cases_sidcar/2020/raw_formula_candidates.tsv`
- `tests/test_cases_sidcar/2020/raw_formula_shortlist.tsv`

Top shortlist (`anchor ~= intercept + slope * raw`):

- **High** `STINGER_22E001_RAW_02 -> VPWR`
  - `VPWR ~= 2.941113 + 0.054399 * RAW_02`
  - `r=0.899252`, `n=72940`, `r_h1=0.810895`, `r_h2=0.913071`
- **High** `STINGER_22E001_RAW_06 -> STINGER_TURBO_BOOST`
  - `BOOST ~= 0.216761 + 0.050804 * RAW_06`
  - `r=0.504968`, `n=463053`, `r_h1=0.510857`, `r_h2=0.518345`
- **Medium** `STINGER_22E001_RAW_03 -> VPWR`
  - `VPWR ~= 8.368173 + 0.028976 * RAW_03`
  - `r=0.685644`, `n=72940`, `r_h1=0.548262`, `r_h2=0.731101`
