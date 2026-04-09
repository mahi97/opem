# Skill: Select a PEMFC Model

Choose the right OPEM model based on your simulation requirements.

## Decision Guide

### Static Models (steady-state analysis)

Use static models when you need a voltage–current (V-I) polarization curve at fixed operating conditions.

| Model | Best For | Key Feature |
|-------|----------|-------------|
| **Amphlett** | General-purpose PEMFC analysis | Most detailed: activation, ohmic, and concentration losses. Requires membrane parameters (thickness `l`, lambda). |
| **Chamberline-Kim** | Empirical curve fitting | Simpler empirical model with parameters `E0`, `b`, `R`, `m`, `n`. Good when you have experimental data to fit. |
| **Larminie-Dicks** | Textbook-style analysis | Uses exchange current density `i_0`, limiting current `i_L`. Good for educational use. |

### Dynamic Models (time-varying analysis)

Use dynamic models when you need to simulate transient behavior or time-dependent current profiles.

| Model | Best For | Key Feature |
|-------|----------|-------------|
| **Padulles I** | Basic dynamic response | Gas pressure dynamics with hydrogen/oxygen time constants. |
| **Padulles II** | Water management effects | Extends Padulles I with water partial pressure tracking. |
| **Padulles-Hauer** | Methanol reformer systems | Adds reformer dynamics (methanol-to-hydrogen conversion). |
| **Padulles-Amphlett** | High-fidelity dynamic | Combines Padulles gas dynamics with Amphlett electrochemistry (activation + ohmic + concentration losses). |
| **Chakraborty** | Simplified dynamic | Nernst gain and ohmic loss model with fuel utilization factor. |

## Quick Selection Rules

1. **Starting out?** → Use **Amphlett** (static) for a comprehensive V-I curve.
2. **Have experimental data?** → Use **Chamberline-Kim** and fit `E0`, `b`, `R`, `m`, `n` to your measurements.
3. **Need transient response?** → Use **Padulles-Amphlett** for the most complete dynamic model.
4. **Modeling a reformer system?** → Use **Padulles-Hauer**.
5. **Teaching or learning?** → Use **Larminie-Dicks** (static) or **Chakraborty** (dynamic) for simplicity.

## Module Imports

```python
# Static models
from opem.Static.Amphlett import Static_Analysis
from opem.Static.Chamberline_Kim import Static_Analysis
from opem.Static.Larminie_Dicks import Static_Analysis

# Dynamic models
from opem.Dynamic.Padulles1 import Dynamic_Analysis
from opem.Dynamic.Padulles2 import Dynamic_Analysis
from opem.Dynamic.Padulles_Hauer import Dynamic_Analysis
from opem.Dynamic.Padulles_Amphlett import Dynamic_Analysis
from opem.Dynamic.Chakraborty import Dynamic_Analysis
```
