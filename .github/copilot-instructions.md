# OPEM — Copilot Instructions

## About This Project

OPEM (Open-Source PEMFC Simulation Tool) is a Python package for modeling and simulating proton-exchange membrane fuel cells. It provides 3 static models and 5 dynamic models.

## Code Conventions

- **Docstrings:** Sphinx format with `:param name:`, `:type name:`, `:return:` on every public function.
- **Error handling:** Calculation functions wrap logic in `try/except (TypeError, ZeroDivisionError)` and return `None` on failure. Dynamic model functions print `[Error] ... Calculation Failed (...)` before returning `None`.
- **Naming:** PascalCase for function names (e.g., `Vcell_Calc`, `Static_Analysis`). PascalCase for parameter dict keys.
- **Physical constants:** Defined once in `opem/Params.py` — never duplicate `R`, `F`, `Eth`, `HHV`, `uF` in model files.

## Architecture

- `opem/Static/` — Static (steady-state) model modules.
- `opem/Dynamic/` — Dynamic (time-varying) model modules.
- `opem/Params.py` — All parameters, constants, descriptions, standard test vectors, and HTML templates.
- `opem/Functions.py` — Shared utilities: math, I/O, report generation, input handling.
- `opem/__main__.py` — CLI entry point with interactive model selection.
- Each model exports one analysis function: `Static_Analysis(...)` or `Dynamic_Analysis(...)`.

## Testing

- Run: `python -m pytest test --cov=opem --cov-report=term`
- Tests are doctests in `test/test_*.py` files.
- Linters: `pydocstyle opem`, `bandit -r opem`, `vulture opem`

## Agent Skills

- **Consumer skills** (for users who `pip install opem`): see `opem/.agent/skills.json`
- **Maintainer skills** (for contributors): see `.copilot/manifest.json`

## Common Tasks

- **Add a model:** See `.copilot/skills/maintainer/add-model.md`
- **Release:** See `.copilot/skills/maintainer/release.md`
- **Run tests:** `python -m pytest test --cov=opem --cov-report=term`
