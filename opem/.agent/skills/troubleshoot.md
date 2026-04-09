# Skill: Troubleshoot OPEM Errors

Diagnose and resolve common issues when using OPEM.

## Error: `None` Values in Output

**Symptom:** Some entries in the result dict are `None`.

**Cause:** A calculation failed at that current step, usually because the current exceeds the cell's physical limits.

**Fix:**
- Reduce `i-stop` so the current range stays within the cell's operating region.
- For Amphlett model: check `JMax` (max current density) and `B` (mass transfer constant).
- For Chamberline-Kim: check `m` and `n` parameters — high `n` causes rapid voltage drop.
- `None` values at extreme currents are physically expected and not bugs.

## Warning: Negative Voltage

**Message:** `Warning : The value of I(>X) leads to minus amount of V, please check your inputs`

**Cause:** The current value makes the cell voltage go negative, which is non-physical.

**Fix:**
- Lower `i-stop` to stay below the limiting current.
- Increase `JMax` if it is too conservative.
- The warning includes the current threshold — use that as your `i-stop`.

## Warning: Errors in Some Current Values

**Message:** `Warning : There are errors in the simulations in some of I amounts...`

**Cause:** Some intermediate current steps had calculation errors, but the overall simulation ran.

**Fix:**
- Check the `.opem` output file for which current steps failed.
- Usually harmless — filter `None` values from results.

## Error: Lambda Out of Range

**Message:** `[Warning] Opem Automatically Set Lambda To Maximum Value (23)` or `Minimum Value (14)`

**Cause:** The `lambda` (membrane humidity) parameter must be between 14 and 23. OPEM auto-clamps it.

**Fix:**
- Set `lambda` to a value between 14 and 23.
- Lambda = 14 represents dry membrane; Lambda = 23 represents fully humidified.

## Error: Alpha Out of Range

**Message:** `[Warning] Opem Automatically Set Alpha To Maximum Value (1)` or `(0)`

**Cause:** The `alpha` parameter must be between 0 and 1.

**Fix:**
- Set `alpha` to a value between 0 and 1.

## Error: TypeError in Calculations

**Symptom:** Functions return `None` and print `[Error] ... Calculation Failed`.

**Cause:** A non-numeric value was passed as input.

**Fix:**
- Ensure all input dict values are `int` or `float`, not `str`.
- Common mistake: reading from CSV/input gives strings — convert with `float()`.

```python
# Wrong
params = {"T": "343.15", "PH2": "1"}

# Correct
params = {"T": 343.15, "PH2": 1}
```

## Error: FileNotFoundError for Reports

**Symptom:** Report generation fails with a file system error.

**Fix:**
- Ensure the `Folder` parameter points to an existing, writable directory.
- Create the output directory before calling the analysis function:

```python
import os
os.makedirs("output", exist_ok=True)
result = Static_Analysis(InputMethod=params, TestMode=True, Folder="output")
```

## Error: ModuleNotFoundError

**Symptom:** `ModuleNotFoundError: No module named 'art'`

**Fix:**
```bash
pip install opem
# or
pip install art requests
```

## Performance: Simulation Is Slow

**Cause:** Very small `i-step` creates millions of data points.

**Fix:**
- Use a larger `i-step` (e.g., 0.1 instead of 0.001).
- For high-resolution curves, first run with coarse steps to identify the interesting range, then refine.

## CLI: Interactive Mode Issues

**Symptom:** CLI hangs or prompts are confusing.

**Fix:**
- Run `opem test` to verify the installation without entering interactive mode.
- Run `opem --version` to check the version.
- Use the library API (`TestMode=True`) instead of the CLI for scripted workflows.
