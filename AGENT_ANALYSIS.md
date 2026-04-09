# OPEM Agent-Support Package Analysis

## Package Summary

**OPEM** (Open-Source PEMFC Simulation Tool) is a Python package for modeling and simulating proton-exchange membrane fuel cells (PEMFC). It provides both static and dynamic electrochemical models that predict cell voltage, power output, efficiency, and thermal behavior across a range of operating conditions.

- **Version:** 1.4
- **License:** MIT
- **Python:** ≥ 3.5
- **Dependencies:** `art`, `requests`
- **Entry point:** `opem` CLI and `python -m opem`

## User Types

| User Type | Description |
|-----------|-------------|
| **Researcher / Engineer** | Uses OPEM as a library to run fuel cell simulations in scripts, notebooks, or larger modeling pipelines. Primary consumer. |
| **Student / Learner** | Uses the CLI or Jupyter notebooks from the `Documents/` folder to explore PEMFC models interactively. |
| **Integrator** | Embeds OPEM models into larger energy-systems or control-systems simulations. |
| **Maintainer / Contributor** | Clones the repo, adds new models, fixes bugs, and releases new versions. |

## Models (Stable Public API)

### Static Models (`opem.Static`)

| Model | Module | Entry Function |
|-------|--------|----------------|
| Amphlett | `opem.Static.Amphlett` | `Static_Analysis(InputMethod, TestMode, PrintMode, ReportMode, Folder)` |
| Chamberline-Kim | `opem.Static.Chamberline_Kim` | `Static_Analysis(...)` |
| Larminie-Dicks | `opem.Static.Larminie_Dicks` | `Static_Analysis(...)` |

### Dynamic Models (`opem.Dynamic`)

| Model | Module | Entry Function |
|-------|--------|----------------|
| Padulles I | `opem.Dynamic.Padulles1` | `Dynamic_Analysis(InputMethod, TestMode, PrintMode, ReportMode, Folder)` |
| Padulles II | `opem.Dynamic.Padulles2` | `Dynamic_Analysis(...)` |
| Padulles-Hauer | `opem.Dynamic.Padulles_Hauer` | `Dynamic_Analysis(...)` |
| Padulles-Amphlett | `opem.Dynamic.Padulles_Amphlett` | `Dynamic_Analysis(...)` |
| Chakraborty | `opem.Dynamic.Chakraborty` | `Dynamic_Analysis(...)` |

## Top Usage Workflows

1. **Run a static simulation** — pass a parameter dict to `Static_Analysis()`, get voltage/power/efficiency curves.
2. **Run a dynamic simulation** — pass a parameter dict to `Dynamic_Analysis()`, get time-varying behavior.
3. **Compare models** — run multiple models with equivalent parameters and compare output dicts.
4. **Generate reports** — enable `ReportMode=True` to get `.opem`, `.csv`, and `.html` output files.
5. **Use standard test vectors** — use `Vectors` from `opem.Params` for quick validation runs.
6. **CLI interactive mode** — run `opem` to select and configure models interactively.

## Top Failure / Debug Workflows

1. **`None` in output** — a calculation returned `None` due to invalid input (division by zero, negative log argument). Check input parameter ranges.
2. **Warning: negative voltage** — current exceeds the physical limit for the cell. Reduce `i-stop` or check `JMax`/`B` parameters.
3. **Type errors** — string passed where float expected. Ensure all input dict values are numeric.
4. **Lambda out of range** — lambda must be 14–23; OPEM auto-clamps with a warning.
5. **Alpha out of range** — alpha must be 0–1; OPEM auto-clamps with a warning.
6. **Missing output files** — `Folder` parameter must be a writable directory path.

## Top Maintainer Workflows

1. **Add a new fuel cell model** — create module in `Static/` or `Dynamic/`, add params to `Params.py`, register in `__init__.py` and `__main__.py`, add test file, add notebook.
2. **Run tests** — `python -m pytest test --cov=opem --cov-report=term`
3. **Lint** — `pydocstyle opem`, `bandit -r opem`, `vulture opem`
4. **Run notebooks** — `python otherfile/notebook_run.py`
5. **Release** — update `Version` in `Params.py`, update `CHANGELOG.md`, tag and push.

## API Stability

| Component | Stability |
|-----------|-----------|
| `Static_Analysis` / `Dynamic_Analysis` | **Stable public API** |
| `opem.Params.Vectors` (standard test vectors) | **Stable public API** |
| Individual calc functions (e.g. `Enernst_Calc`) | **Stable** — used by analysis functions |
| `opem.Functions` utilities | **Internal** — may change between versions |
| `opem.Script` (Chart.js bundle) | **Internal** |
| `opem.Profile` | **Internal** — profiling script |
| HTML/CSV report generation | **Stable output format** |

## Consumer Skill Set

| Skill | Purpose |
|-------|---------|
| `select-model` | Choose the right PEMFC model for a use case |
| `run-static-model` | Run any static model programmatically |
| `run-dynamic-model` | Run any dynamic model programmatically |
| `interpret-results` | Understand output dict keys, units, and report files |
| `troubleshoot` | Diagnose and fix common errors |

## Maintainer Skill Set

| Skill | Purpose |
|-------|---------|
| `dev-setup` | Set up a development environment |
| `testing` | Run and write tests |
| `add-model` | Add a new fuel cell model end-to-end |
| `release` | Prepare and publish a release |
