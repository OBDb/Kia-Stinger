# Kia Stinger Signalset

Stinger-specific OBD signal definitions used to decode CAN/UDS data for Sidcar.

## About the Stinger

The Kia Stinger is a rear-wheel-drive-based sport sedan/liftback developed as Kia's performance flagship. It was launched for the 2018 model year and tuned under the direction of Albert Biermann, helping establish Kia's performance reputation.

Quick highlights:

- Platform/layout: front-engine, rear-wheel-drive architecture (with available AWD)
- Body style: fastback/liftback with sport sedan dynamics
- Character: grand-touring feel with daily-driver practicality
- Production era: 2018-2023 model years
- Legacy: one of Kia's most recognizable enthusiast-focused vehicles

Powertrain highlights:

- Launch-era options included a turbocharged 2.0L inline-4 and a twin-turbo 3.3L V6
- Later model years introduced a 2.5L turbo inline-4 in many markets
- 3.3T models are the flagship performance trim, paired with Kia's 8-speed automatic
- Rear-wheel drive is standard architecture, with all-wheel drive available

## Scope

- Vehicle: Kia Stinger (CK platform)
- Supported model years: 2018-2023
- This fork is intentionally Stinger-only, not a full Kia multi-model signalset.

## Main Files

- Active signalset: `signalsets/v3/default.json`
- Generation metadata: `generations.yaml`
- Test fixtures by year: `tests/test_cases/<year>/commands/*.yaml`
- Command support summaries: `tests/test_cases/<year>/command_support.yaml`

## Testing

Validate formatting:

```bash
python3 tests/schemas/cli.py --check signalsets/v3/default.json
```

Run repo tests:

```bash
pytest tests/ --ignore=tests/schemas
```

## Notes

- Some external tools may display "unsupported" hints for specific years based on scan evidence.  
  That does not override the intended 2018-2023 compatibility defined in `default.json` filters.
- Test discovery is scoped to commands currently defined in `signalsets/v3/default.json`.

## Contributing

1. Create a branch for your change.
2. Update `signalsets/v3/default.json` and related fixtures.
3. Run the formatting and test commands above.
4. Open a merge request with a clear summary of added/changed commands.
