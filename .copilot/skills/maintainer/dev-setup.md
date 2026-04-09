# Skill: Development Environment Setup

## Prerequisites

- Python 3.5 or higher
- pip
- git

## Steps

### 1. Clone and install in editable mode

```bash
git clone https://github.com/ECSIM/opem.git
cd opem
pip install -e .
```

### 2. Install development dependencies

```bash
pip install -r dev-requirements.txt
```

This installs: `pytest`, `pytest-cov`, `bandit`, `vulture`, `pydocstyle`, pinned versions of `art` and `requests`.

### 3. Verify the installation

```bash
opem test        # Run CLI smoke test
opem --version   # Should print 1.4
```

### 4. Run the full test suite

```bash
python -m pytest test --cov=opem --cov-report=term
```

### 5. Run linters

```bash
pydocstyle opem          # Docstring style
bandit -r opem           # Security checks
vulture opem             # Dead code detection
```

### 6. Run notebooks (optional)

```bash
pip install notebook nbconvert
python otherfile/notebook_run.py
```

## Project Layout

```
opem/
├── opem/                    # Package source
│   ├── __init__.py          # Module imports, __version__
│   ├── __main__.py          # CLI entry point
│   ├── Functions.py         # Shared utilities (I/O, math, reports)
│   ├── Params.py            # All parameters, constants, descriptions
│   ├── Script.py            # Chart.js bundle for HTML reports
│   ├── Profile.py           # cProfile script
│   ├── Static/              # Static model modules
│   │   ├── Amphlett.py
│   │   ├── Chamberline_Kim.py
│   │   └── Larminie_Dicks.py
│   └── Dynamic/             # Dynamic model modules
│       ├── Padulles1.py
│       ├── Padulles2.py
│       ├── Padulles_Hauer.py
│       ├── Padulles_Amphlett.py
│       └── Chakraborty.py
├── test/                    # Doctest-based test files
├── Documents/               # Jupyter notebooks (model documentation)
├── MATLAB/                  # MATLAB wrappers
├── docker/                  # Docker support
├── otherfile/               # Build/release utilities
├── setup.py                 # Package metadata
├── requirements.txt         # Runtime dependencies
└── dev-requirements.txt     # Dev dependencies
```

## IDE Setup

- Configure your IDE to use the project Python interpreter where OPEM is installed in editable mode.
- The test runner is `pytest` with `--doctest-modules`.
- Docstring format is Sphinx-style (`:param:`, `:type:`, `:return:`).
