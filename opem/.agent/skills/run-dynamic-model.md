# Skill: Run a Dynamic PEMFC Simulation

Run time-varying fuel cell simulations using OPEM's dynamic models.

## Common Pattern

All dynamic models share the same `Dynamic_Analysis` interface:

```python
result = Dynamic_Analysis(
    InputMethod=input_dict,   # dict of parameters
    TestMode=True,            # True = return result dict
    PrintMode=False,          # True = print to console
    ReportMode=True,          # True = generate report files
    Folder="/path/to/output"  # Output directory
)
```

## Padulles I Model

Basic dynamic model with hydrogen/oxygen gas pressure dynamics.

```python
from opem.Dynamic.Padulles1 import Dynamic_Analysis

result = Dynamic_Analysis(
    InputMethod={
        "T": 343,            # Cell temperature [K]
        "E0": 0.6,           # No-load voltage [V]
        "N0": 88,            # Number of cells in stack
        "KO2": 2.52e-3,      # Oxygen valve constant [kmol/(s·atm)]
        "KH2": 4.22e-3,      # Hydrogen valve constant [kmol/(s·atm)]
        "tH2": 3.37,         # Hydrogen time constant [s]
        "tO2": 6.74,         # Oxygen time constant [s]
        "B": 0.04777,        # Activation voltage constant [V]
        "C": 0.0136,         # Activation voltage constant [A^-1]
        "Rint": 0.00303,     # Internal resistance [ohm]
        "rho": 1.168,        # Hydrogen-oxygen flow ratio
        "qH2": 0.0004,       # Hydrogen molar flow [kmol/s]
        "i-start": 0.1,      # Current sweep start [A]
        "i-stop": 100,       # Current sweep stop [A]
        "i-step": 0.1,       # Current step size [A]
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Padulles II Model

Extends Padulles I with water vapor partial pressure tracking.

```python
from opem.Dynamic.Padulles2 import Dynamic_Analysis

result = Dynamic_Analysis(
    InputMethod={
        "T": 343,            # Cell temperature [K]
        "E0": 0.6,           # No-load voltage [V]
        "N0": 88,            # Number of cells
        "KO2": 2.52e-3,      # Oxygen valve constant [kmol/(s·atm)]
        "KH2": 4.22e-3,      # Hydrogen valve constant [kmol/(s·atm)]
        "KH2O": 7.716e-3,    # Water valve constant [kmol/(s·atm)]
        "tH2": 3.37,         # Hydrogen time constant [s]
        "tO2": 6.74,         # Oxygen time constant [s]
        "tH2O": 18.418,      # Water time constant [s]
        "B": 0.04777,        # Activation voltage constant [V]
        "C": 0.0136,         # Activation voltage constant [A^-1]
        "Rint": 0.00303,     # Internal resistance [ohm]
        "rho": 1.168,        # Hydrogen-oxygen flow ratio
        "qH2": 0.0004,       # Hydrogen molar flow [kmol/s]
        "i-start": 0.1,
        "i-stop": 100,
        "i-step": 0.1,
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Padulles-Hauer Model

Includes methanol reformer dynamics for hydrogen generation.

```python
from opem.Dynamic.Padulles_Hauer import Dynamic_Analysis

result = Dynamic_Analysis(
    InputMethod={
        "T": 343,            # Cell temperature [K]
        "E0": 0.6,           # No-load voltage [V]
        "N0": 88,            # Number of cells
        "KO2": 2.52e-3,      # Oxygen valve constant [kmol/(s·atm)]
        "KH2": 4.22e-3,      # Hydrogen valve constant [kmol/(s·atm)]
        "KH2O": 7.716e-3,    # Water valve constant [kmol/(s·atm)]
        "tH2": 3.37,         # Hydrogen time constant [s]
        "tO2": 6.74,         # Oxygen time constant [s]
        "tH2O": 18.418,      # Water time constant [s]
        "t1": 2,             # Reformer time constant 1 [s]
        "t2": 2,             # Reformer time constant 2 [s]
        "B": 0.04777,        # Activation voltage constant [V]
        "C": 0.0136,         # Activation voltage constant [A^-1]
        "Rint": 0.00303,     # Internal resistance [ohm]
        "rho": 1.168,        # Hydrogen-oxygen flow ratio
        "qMethanol": 0.0002, # Methanol molar flow [kmol/s]
        "CV": 2,             # Reformer conversion factor
        "i-start": 0.1,
        "i-stop": 100,
        "i-step": 0.1,
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Padulles-Amphlett Model

Highest-fidelity dynamic model combining gas dynamics with detailed electrochemistry.

```python
from opem.Dynamic.Padulles_Amphlett import Dynamic_Analysis

result = Dynamic_Analysis(
    InputMethod={
        "T": 343,            # Cell temperature [K]
        "E0": 1.229,         # Reversible voltage [V]
        "N0": 88,            # Number of cells
        "KO2": 2.52e-3,      # Oxygen valve constant [kmol/(s·atm)]
        "KH2": 4.22e-3,      # Hydrogen valve constant [kmol/(s·atm)]
        "KH2O": 7.716e-3,    # Water valve constant [kmol/(s·atm)]
        "tH2": 3.37,         # Hydrogen time constant [s]
        "tO2": 6.74,         # Oxygen time constant [s]
        "tH2O": 18.418,      # Water time constant [s]
        "t1": 2,             # Reformer time constant 1 [s]
        "t2": 2,             # Reformer time constant 2 [s]
        "A": 50.6,           # Active area [cm^2]
        "l": 0.0178,         # Membrane thickness [cm]
        "lambda": 23,        # Membrane humidity (14-23)
        "JMax": 1.5,         # Max current density [A/cm^2]
        "B": 0.016,          # Mass transfer constant [V]
        "R": 0,              # Electronic resistance [ohm]
        "Rint": 0.00303,     # Internal resistance [ohm]
        "rho": 1.168,        # Hydrogen-oxygen flow ratio
        "qMethanol": 0.0002, # Methanol molar flow [kmol/s]
        "CV": 2,             # Reformer conversion factor
        "i-start": 0.1,
        "i-stop": 100,
        "i-step": 0.1,
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Chakraborty Model

Simplified dynamic model with Nernst gain and ohmic loss.

```python
from opem.Dynamic.Chakraborty import Dynamic_Analysis

result = Dynamic_Analysis(
    InputMethod={
        "T": 1273,           # Cell temperature [K]
        "E0": 0.6,           # Open circuit voltage [V]
        "N0": 1,             # Number of cells
        "u": 0.8,            # Fuel utilization ratio
        "rHO": 1.145,        # Hydrogen-oxygen flow ratio
        "KH2": 0.0000422,    # Hydrogen valve constant [kmol/(s·atm)]
        "KO2": 0.0000252,    # Oxygen valve constant [kmol/(s·atm)]
        "KH2O": 0.0000077,   # Water valve constant [kmol/(s·atm)]
        "Rint": 0.00303,     # Internal resistance [ohm]
        "R": 0,              # Electronic resistance [ohm]
        "i-start": 1,
        "i-stop": 100,
        "i-step": 0.1,
        "Name": "MyTest"
    },
    TestMode=True,
    PrintMode=False,
    ReportMode=True
)
```

## Using Standard Test Vectors

```python
from opem.Params import Vectors

# Available keys:
# "Padulles_Analysis I (Dynamic)"
# "Padulles_Analysis II (Dynamic)"
# "Padulles_Hauer Analysis (Dynamic)"
# "Padulles_Amphlett Analysis (Dynamic)"
# "Chakraborty_Analysis (Dynamic)"

from opem.Dynamic.Padulles1 import Dynamic_Analysis
result = Dynamic_Analysis(
    InputMethod=Vectors["Padulles_Analysis I (Dynamic)"],
    TestMode=True,
    PrintMode=False,
    ReportMode=False
)
```

## Tips

- Dynamic models simulate the same current sweep as static models but include gas dynamics and time constants.
- The `rho` parameter controls the hydrogen-to-oxygen stoichiometric ratio.
- For reformer models (Padulles-Hauer, Padulles-Amphlett), `qMethanol` and `CV` control the fuel supply chain.
- Set `ReportMode=False` when running in loops or parameter sweeps to avoid generating thousands of files.
