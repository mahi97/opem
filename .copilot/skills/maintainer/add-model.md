# Skill: Add a New Fuel Cell Model

Step-by-step guide to add a new PEMFC model to OPEM.

## Prerequisites

Decide if your model is **static** (steady-state V-I curve) or **dynamic** (time-varying behavior).

## Step 1: Create the Model Module

### For a static model: `opem/Static/MyModel.py`

```python
# -*- coding: utf-8 -*-
"""MyModel model functions."""
import math
from opem.Params import MyModel_InputParams as InputParams
from opem.Params import MyModel_OutputParams as OutputParams
from opem.Params import R, F, HHV, uF, Eth, Report_Message
from opem.Params import MyModel_Description, Overall_Params_Max_Description, Overall_Params_Linear_Description
from opem.Static.Amphlett import (
    Power_Calc, Efficiency_Calc, VStack_Calc, PowerStack_Calc,
    Power_Thermal_Calc, Power_Total_Calc, Linear_Aprox_Params_Calc, Max_Params_Calc
)
import opem.Functions
import os


def Vcell_Calc(param1, param2, i):
    """
    Calculate cell voltage.

    :param param1: description [unit]
    :type param1: float
    :param param2: description [unit]
    :type param2: float
    :param i: cell load current [A]
    :type i: float
    :return: Vcell [V] as float
    """
    try:
        # Your voltage equation here
        return result
    except (TypeError, ZeroDivisionError):
        return None


def Static_Analysis(
    InputMethod=opem.Functions.Get_Input,
    TestMode=False,
    PrintMode=True,
    ReportMode=True,
    Folder=os.getcwd()
):
    """
    Run MyModel static analysis.

    :param InputMethod: input method or dict
    :param TestMode: test mode flag
    :type TestMode: bool
    :param PrintMode: print mode flag
    :type PrintMode: bool
    :param ReportMode: report mode flag
    :type ReportMode: bool
    :param Folder: output folder
    :type Folder: str
    :return: result as dict
    """
    # Follow the pattern in Amphlett.py Static_Analysis
    pass
```

### For a dynamic model: `opem/Dynamic/MyModel.py`

Same pattern but name the entry function `Dynamic_Analysis`.

## Step 2: Add Parameters to `opem/Params.py`

Add these entries at the end of `Params.py`:

```python
# --- MyModel Parameters ---

MyModel_Description = "Description of the model for the menu and reports."

MyModel_InputParams = {
    "param1": "Parameter 1 Description [unit]",
    "param2": "Parameter 2 Description [unit]",
    # ... all input parameters
    "i-start": "Start point of I(Cell load current) [A]",
    "i-stop": "Stop point of I(Cell load current) [A]",
    "i-step": "Step of I(Cell load current) [A]",
}

MyModel_OutputParams = {
    "Vcell": "V",
    "P": "W",
    "EFF": "",
    "VStack": "V",
    "PStack": "W",
    "R": "ohm",
    # ... all output parameters
}

MyModel_Standard_Vector = {
    "param1": 1.0,
    "param2": 2.0,
    "i-start": 0.1,
    "i-stop": 100,
    "i-step": 0.1,
    "Name": "MyModel_Standard_Test"
}
```

Then add your model to the `Vectors` dict:

```python
Vectors = {
    # ... existing entries ...
    "MyModel_Analysis (Static)": MyModel_Standard_Vector,
}
```

## Step 3: Register the Model

### In `opem/__init__.py`

Add:
```python
import opem.Static.MyModel  # or opem.Dynamic.MyModel
```

### In `opem/__main__.py`

Add import:
```python
from opem.Static.MyModel import Static_Analysis as MyModel_Analysis
```

Add to `ANALYSISDICT`:
```python
"MyModel_Analysis (Static)": MyModel_Analysis,
```

Add to `MENU`:
```python
"(Static)  MyModel Analysis": "MyModel_Analysis (Static)",
```

## Step 4: Write Tests

Create `test/test_MyModel.py`:

```python
# -*- coding: utf-8 -*-
'''
>>> from opem.Static.MyModel import *
>>> from math import isclose
>>> ABS_TOL = 1e-12
>>> REL_TOL = 0
>>> assert isclose(Vcell_Calc(1.0, 2.0, 0.5), expected_value, abs_tol=ABS_TOL)
>>> Vcell_Calc(None, 2.0, 0.5)  # should return None
>>> # Test full analysis with standard vector
>>> from opem.Params import Vectors
>>> result = Static_Analysis(InputMethod=Vectors["MyModel_Analysis (Static)"], TestMode=True, PrintMode=False, ReportMode=False)
>>> assert result is not None
'''
```

## Step 5: Add Documentation

Create a Jupyter notebook `Documents/MyModel.ipynb` following the pattern of existing notebooks. Include:
- Model description and equations
- Parameter table
- Code example running the model
- V-I and P-I plots

## Step 6: Update Package Metadata

### In `setup.py`

Add your subpackage if it's a new directory:
```python
packages=['opem', 'opem.Static', 'opem.Dynamic'],
```

### In `CHANGELOG.md`

Under `[Unreleased]`, add:
```
### Added
- MyModel static/dynamic model
```

## Step 7: Validate

```bash
python -m pytest test --cov=opem --cov-report=term
pydocstyle opem
bandit -r opem
```
