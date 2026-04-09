# Skill: Testing Guide

## Running Tests

### Full test suite

```bash
python -m pytest test --cov=opem --cov-report=term
```

### Run a specific model's tests

```bash
python -m pytest test/test_Amphlett.py
python -m pytest test/test_Functions.py
```

### Run with verbose output

```bash
python -m pytest test -v --cov=opem --cov-report=term
```

## Test Architecture

OPEM uses **doctests** embedded in test files (not unittest/pytest test classes).

Each test file in `test/` is a Python file with doctests in a module-level docstring:

```python
# test/test_Amphlett.py
'''
>>> from opem.Static.Amphlett import *
>>> assert isclose(Enernst_Calc(343.15, 1, 1), 1.19075, abs_tol=1e-12)
'''
```

The `pytest.ini` enables doctest collection:

```ini
[pytest]
addopts = --doctest-modules
doctest_optionflags = NORMALIZE_WHITESPACE IGNORE_EXCEPTION_DETAIL NUMBER ELLIPSIS
```

## Writing Tests for a New Function

### 1. Add doctests to the test file for the model

Open `test/test_<ModelName>.py` and add assertions:

```python
'''
>>> from opem.Static.MyModel import My_Calc
>>> from math import isclose
>>> ABS_TOL = 1e-12
>>> REL_TOL = 0
>>> assert isclose(My_Calc(1.0, 2.0), 3.0, abs_tol=ABS_TOL, rel_tol=REL_TOL)
>>> My_Calc(None, 2.0)  # Should handle gracefully
>>> My_Calc("bad", 2.0)  # Should handle gracefully
'''
```

### 2. Test error cases

Every calculation function should handle `TypeError` and `ZeroDivisionError` gracefully:

```python
'''
>>> My_Calc(None, 2.0)
[Error] My Calculation Failed (a:None, b:2.0)
>>> My_Calc(1.0, 0)
[Error] My Calculation Failed (a:1.0, b:0)
'''
```

### 3. Test the full analysis function

```python
'''
>>> result = Static_Analysis(InputMethod=Test_Vector, TestMode=True, PrintMode=False, ReportMode=False)
>>> assert result is not None
>>> assert "Vcell" in result
>>> assert "P" in result
'''
```

## Linting

```bash
# Docstring style (all public functions need Sphinx docstrings)
pydocstyle opem

# Security analysis
bandit -r opem

# Dead code detection
vulture opem
```

## CI Pipeline

The GitHub Actions workflow (`.github/workflows/test.yml`) runs:
1. Install package + test dependencies
2. `opem test` and `opem --version` (smoke test)
3. `python -m pytest test --cov=opem --cov-report=term`
4. Linters (pydocstyle, bandit, vulture) on Python 3.9 / Ubuntu only
5. Notebook execution and conversion on Python 3.9 / Ubuntu only
6. cProfile on `opem/Profile.py`

Matrix: Python 3.5–3.12 × Ubuntu/Windows/macOS.
