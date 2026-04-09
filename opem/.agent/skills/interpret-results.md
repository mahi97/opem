# Skill: Interpret OPEM Results

Understand the output from `Static_Analysis` and `Dynamic_Analysis` functions.

## Result Dictionary

When `TestMode=True`, analysis functions return a Python dict with these categories of keys:

### Per-Current-Step Arrays

These are lists indexed by current step:

| Key | Unit | Description |
|-----|------|-------------|
| `I` | A | Cell load current |
| `Vcell` | V | Single cell voltage |
| `VStack` | V | Stack voltage (= N × Vcell) |
| `P` | W | Single cell power |
| `PStack` | W | Stack power |
| `EFF` | — | Cell efficiency (Vcell / 1.482) |
| `R` | Ω | Cell resistance (V/I) |

**Dynamic-model-specific arrays:**

| Key | Unit | Description |
|-----|------|-------------|
| `PH2` | atm | Hydrogen partial pressure |
| `PO2` | atm | Oxygen partial pressure |
| `PH2O` | atm | Water partial pressure (Padulles II, Hauer, Amphlett) |
| `Eta_Act` | V | Activation loss (Padulles-Amphlett only) |
| `Eta_Ohmic` | V | Ohmic loss (Padulles-Amphlett only) |
| `Eta_Conc` | V | Concentration loss (Padulles-Amphlett only) |

**Static-model-specific arrays (Amphlett):**

| Key | Unit | Description |
|-----|------|-------------|
| `Eta_Act` | V | Activation overpotential |
| `Eta_Ohmic` | V | Ohmic overpotential |
| `Eta_Conc` | V | Concentration overpotential |
| `Loss` | V | Total loss |

### Overall (Scalar) Parameters

| Key | Unit | Description |
|-----|------|-------------|
| `P-Max` | W | Maximum power point |
| `V@Pmax` | V | Voltage at maximum power |
| `EFF@Pmax` | — | Efficiency at maximum power |
| `VStack@Pmax` | V | Stack voltage at maximum power |
| `P-Elec` | W | Total electrical energy (integral of V-I curve) |
| `P-Thermal` | W | Total thermal energy |
| `Wmax(Linear-Aprox)` | W | Maximum power from linear approximation |
| `V0(Linear-Aprox)` | V | Open-circuit voltage from linear approximation |

## Output Files

When `ReportMode=True`, three files are generated in the `Folder` directory:

### 1. `.opem` file (text report)
Human-readable log with all input parameters and output values at each current step, plus overall parameters and warnings.

### 2. `.csv` file (data table)
Comma-separated values with one row per current step. Column headers match the output parameter names. Import into pandas:

```python
import pandas as pd
df = pd.read_csv("MyTest.csv")
```

### 3. `.html` file (interactive report)
Self-contained HTML page with Chart.js-powered interactive plots of V-I and P-I curves plus summary tables.

## Working with Results Programmatically

### Extract V-I curve data

```python
from opem.Static.Amphlett import Static_Analysis
from opem.Params import Vectors

result = Static_Analysis(
    InputMethod=Vectors["Amphlett_Analysis (Static)"],
    TestMode=True,
    PrintMode=False,
    ReportMode=False
)

currents = result["I"]
voltages = result["Vcell"]
powers = result["P"]

# Filter out None values (invalid operating points)
valid = [(i, v, p) for i, v, p in zip(currents, voltages, powers) if v is not None]
```

### Find maximum power point

```python
pmax = result["P-Max"]
v_at_pmax = result["V@Pmax"]
eff_at_pmax = result["EFF@Pmax"]
print(f"Max power: {pmax} W at {v_at_pmax} V (efficiency: {eff_at_pmax})")
```

### Compare two models

```python
from opem.Static.Amphlett import Static_Analysis as amphlett
from opem.Static.Larminie_Dicks import Static_Analysis as larminie

r1 = amphlett(InputMethod=vec1, TestMode=True, PrintMode=False, ReportMode=False)
r2 = larminie(InputMethod=vec2, TestMode=True, PrintMode=False, ReportMode=False)

print(f"Amphlett Pmax: {r1['P-Max']} W")
print(f"Larminie Pmax: {r2['P-Max']} W")
```

## Understanding `None` Values

A `None` value in any output array means the calculation was invalid at that current step. Common causes:
- Current exceeds limiting current density → negative voltage
- Division by zero in an intermediate calculation
- Logarithm of a negative number

These `None` values are expected at extreme operating points and should be filtered out before plotting or further analysis.
