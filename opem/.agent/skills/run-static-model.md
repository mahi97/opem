# Skill: Run a Static PEMFC Simulation

Run steady-state fuel cell simulations using OPEM's static models.

## Common Pattern

All static models share the same `Static_Analysis` interface:

```python
result = Static_Analysis(
    InputMethod=input_dict,   # dict of parameters (or Get_Input for interactive)
    TestMode=True,            # True = return result dict; False = interactive
    PrintMode=False,          # True = print output to console
    ReportMode=True,          # True = generate .opem, .csv, .html files
    Folder="/path/to/output"  # Output directory (default: current dir)
)
```

## Amphlett Model

The most detailed static model with activation, ohmic, and concentration losses.

```python
from opem.Static.Amphlett import Static_Analysis

result = Static_Analysis(
    InputMethod={
        "T": 343.15,        # Cell temperature [K]
        "PH2": 1,           # Hydrogen partial pressure [atm]
        "PO2": 1,           # Oxygen partial pressure [atm]
        "i-start": 0,       # Current sweep start [A]
        "i-stop": 100,      # Current sweep stop [A]
        "i-step": 0.1,      # Current step size [A]
        "A": 50.6,          # Active area [cm^2]
        "l": 0.0178,        # Membrane thickness [cm]
        "lambda": 23,       # Membrane humidity (14-23)
        "N": 1,             # Number of cells in stack
        "R": 0,             # Electronic resistance [ohm] (optional, default=0)
        "JMax": 1.5,        # Maximum current density [A/cm^2]
        "B": 0.016,         # Constant in mass transfer term [V]
        "Name": "MyTest"    # Simulation name (used in output files)
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Chamberline-Kim Model

Empirical model — good for fitting experimental V-I data.

```python
from opem.Static.Chamberline_Kim import Static_Analysis

result = Static_Analysis(
    InputMethod={
        "A": 50.0,          # Active area [cm^2]
        "E0": 0.982,        # Open circuit voltage [V]
        "b": 0.0689,        # Tafel slope [V]
        "R": 0.328,         # Ohmic resistance [ohm]
        "m": 0.000125,      # Mass transfer coefficient [V]
        "n": 9.45,          # Mass transfer exponent [cm^2/A]
        "N": 1,             # Number of cells in stack
        "i-start": 1,       # Current sweep start [A]
        "i-stop": 100,      # Current sweep stop [A]
        "i-step": 0.1,      # Current step size [A]
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Larminie-Dicks Model

Textbook electrochemical model using exchange and limiting current densities.

```python
from opem.Static.Larminie_Dicks import Static_Analysis

result = Static_Analysis(
    InputMethod={
        "A": 0.06,          # Tafel slope [V]
        "E0": 1.178,        # Open circuit voltage [V]
        "T": 328.15,        # Cell temperature [K]
        "i_0": 0.00654,     # Exchange current density [A]
        "i_n": 0.23,        # Internal current [A]
        "i_L": 100.0,       # Limiting current [A]
        "R_M": 0.0018,      # Membrane resistance [ohm]
        "N": 23,            # Number of cells in stack
        "i-start": 0.1,     # Current sweep start [A]
        "i-stop": 98,       # Current sweep stop [A]
        "i-step": 0.1,      # Current step size [A]
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Using Standard Test Vectors

OPEM provides validated test vectors for every model:

```python
from opem.Params import Vectors

# Available keys:
# "Amphlett_Analysis (Static)"
# "Chamberline_Kim_Analysis (Static)"
# "Larminiee_Analysis (Static)"

from opem.Static.Amphlett import Static_Analysis
result = Static_Analysis(
    InputMethod=Vectors["Amphlett_Analysis (Static)"],
    TestMode=True,
    PrintMode=False,
    ReportMode=False
)
```

## Tips

- Set `TestMode=True` to get the result as a Python dict (required for programmatic use).
- Set `PrintMode=False` to suppress console output in scripts.
- Set `ReportMode=False` if you only need the dict and don't want output files.
- The `lambda` parameter (membrane humidity) is auto-clamped to the range 14–23.
- Current range parameters `i-start`, `i-stop`, `i-step` are auto-corrected if start > stop or step is negative.
