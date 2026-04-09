# Skill: Release Process

How to prepare and publish a new OPEM release.

## Pre-Release Checklist

1. **All tests pass** on the target branch:
   ```bash
   python -m pytest test --cov=opem --cov-report=term
   ```

2. **Linters pass:**
   ```bash
   pydocstyle opem
   bandit -r opem
   vulture opem
   ```

3. **Notebooks run without errors:**
   ```bash
   python otherfile/notebook_run.py
   ```

4. **Version check passes:**
   ```bash
   python otherfile/version_check.py
   ```

## Release Steps

### 1. Update version number

Edit `opem/Params.py`:
```python
Version = X.Y  # e.g., 1.5
```

Edit `setup.py`:
```python
version='X.Y',
download_url='https://github.com/ecsim/opem/tarball/vX.Y',
```

### 2. Update CHANGELOG.md

Move items from `[Unreleased]` to a new version section:

```markdown
## [Unreleased]
## [X.Y] - YYYY-MM-DD
### Added
- ...
### Changed
- ...
```

### 3. Update AUTHORS.md if needed

Add new contributors.

### 4. Commit and tag

```bash
git add -A
git commit -m "Version X.Y"
git tag vX.Y
git push origin master --tags
```

### 5. PyPI Release

The `.github/workflows/publish_pypi.yml` workflow handles PyPI publishing automatically on tagged releases.

Manual alternative:
```bash
python setup.py sdist bdist_wheel
twine upload dist/*
```

### 6. Conda Release

The `.github/workflows/publish_conda.yaml` workflow handles Conda publishing.

The Conda recipe is at `otherfile/meta.yaml`.

### 7. Post-Release

- Verify the package on PyPI: `pip install opem==X.Y`
- Verify on Conda: `conda install -c ecsim opem=X.Y`
- Create a GitHub Release from the tag with changelog notes.

## Version Locations

All places where the version number must be updated:

| File | Location |
|------|----------|
| `opem/Params.py` | `Version = X.Y` (line 3) |
| `setup.py` | `version='X.Y'` (line 41) |
| `setup.py` | `download_url` (line 48) |
| `otherfile/meta.yaml` | Conda recipe version |
| `CHANGELOG.md` | New version section header |
