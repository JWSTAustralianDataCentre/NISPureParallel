## Project context
- Python project using **Pixi** environments for `jwst` and `snakemake`.
- Primary tasks: run unit checks, dry-run DAGs, and small integration runs with sample JWST NIRISS data.
- Avoid internet where possible (Codex sandboxes restrict egress). Prefer cached/packaged assets. Exception would be JWST CRDS at https://jwst-crds.stsci.edu and preprocess step of the pipeline.

## Setup (idempotent)
1. Ensure Pixi is available; if missing, install:
   - macOS/Linux: `curl -fsSL https://pixi.sh/install.sh | bash`
   - Add `~/.pixi/bin` to PATH.
2. Resolve envs without mutating locks: `pixi install --no-lockfile-update`
3. Print envs: `pixi env list`

## Sanity checks
- `pixi run -e jwst python -c "import jwst, sys; print(jwst.__version__)"`  # import test
- `pixi run -e snakemake snakemake -n -s workflow/SingleField_leo-00.smk`    # dry-run

## CRDS
- Use a local cache if present: set `CRDS_SERVER_URL=https://jwst-crds.stsci.edu` and `CRDS_PATH=.crds`.
- If cache is missing and network is allowed, warm minimal NIRISS subset:
  `pixi run -e jwst crds sync --include-instruments NIRISS --contexts ${CRDS_CONTEXT:-}`

## Typical tasks
- “Explain the DAG & targets”: run `snakemake -n` and summarise rules/inputs/outputs.
- “Run a tiny test”: use bundled sample data under `tests/data/` and run `-j 4`.
- “Create PR”: format, lint (if configured), then branch + commit with a descriptive message.

## Guardrails
- Never rewrite large data files.
- Ask before changing `pixi.toml` or Snakemake profiles.
- Prefer `--no-lockfile-update` unless the user explicitly requests changes.
