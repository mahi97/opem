# Copilot Rules for OPEM

## Code Style
- Follow PEP 8 and existing code conventions.
- All public functions must have docstrings in Sphinx `:param:` / `:type:` / `:return:` format.
- Use `try/except (TypeError, ZeroDivisionError)` for calculation functions that may receive invalid inputs — return `None` on failure.
- Use `print("[Error] ... Calculation Failed (...)")` for error messages in calculation functions.

## Architecture
- Static models go in `opem/Static/`, dynamic models go in `opem/Dynamic/`.
- Each model module must export a top-level analysis function: `Static_Analysis` or `Dynamic_Analysis`.
- Shared calculation functions (power, efficiency, linear approximation) live in `opem/Static/Amphlett.py` and are imported by other models.
- All model parameters, descriptions, and standard test vectors are defined in `opem/Params.py`.
- Report generation (`.opem`, `.csv`, `.html`) is handled by `opem/Functions.py`.

## Testing
- Tests use pytest with `--doctest-modules`. Test files are in the `test/` directory.
- Each model has a corresponding `test/test_<ModelName>.py` file with doctests.
- Run tests: `python -m pytest test --cov=opem --cov-report=term`
- Run linters: `pydocstyle opem`, `bandit -r opem`, `vulture opem`

## Parameters
- Physical constants `R`, `F`, `Eth`, `HHV`, `uF` are defined in `opem/Params.py` — never redefine them in model modules.
- The `lambda` parameter (membrane humidity) must be clamped to 14–23 using `filter_lambda()`.
- The `alpha` parameter must be clamped to 0–1 using `filter_alpha()`.

## Output Conventions
- Analysis functions must support: `InputMethod`, `TestMode`, `PrintMode`, `ReportMode`, `Folder`.
- When `TestMode=True`, return a result dict. When `False`, run interactively.
- Output dict keys must match the parameter names in `*_OutputParams` from `Params.py`.
