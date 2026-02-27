# Agentic Guidelines for QuantStats JWT

This document outlines the operational guidelines, code style, and build commands for AI coding agents operating within the `quantstats_jwt` (fork of `quantstats_lumi`) repository. Strict adherence to these rules ensures that code quality is maintained, existing patterns are respected, and tasks are executed safely.

## 1. Build, Lint, and Test Commands

### 1.1 Running Tests
Currently, the repository lacks a dedicated `tests/` directory or an automated test suite. However, `pytest` is included in `requirements.txt`. Agents adding new features MUST create tests using `pytest` and place them in a `tests/` directory at the root level.

**To run the entire test suite:**
```bash
python -m pytest tests/
```

**To run a single test file:**
```bash
python -m pytest tests/test_stats.py
```

**To run a specific test case within a file:**
```bash
python -m pytest tests/test_stats.py::test_specific_function_name -v
```

*Note: If `pytest` throws import errors, ensure you are running it from the project root so the `quantstats_lumi` package is in the Python path.*

### 1.2 Linting & Formatting
The project does not enforce explicit linters (like `flake8`, `black`, or `isort`) via GitHub Actions. However, agents should strictly adhere to PEP-8 standards.
- Keep line lengths reasonable (typically under 100 characters).
- Maintain existing indentation (4 spaces).
- No trailing whitespace.

### 1.3 Building the Package
To build the distribution for PyPI (as defined in the GitHub Actions workflow):
```bash
python setup.py sdist bdist_wheel
```

## 2. Code Style & Architecture Guidelines

### 2.1 Imports and Namespacing (CRITICAL)
This project heavily uses the **"hidden import"** pattern to keep namespaces clean and avoid polluting `__all__` for end users. This is the most strictly enforced convention in the codebase.
- **Always prefix imported external or built-in modules with an underscore (`_`).**
  - **Correct:** `import numpy as _np`, `import pandas as _pd`, `from math import sqrt as _sqrt`
  - **Incorrect:** `import numpy as np`, `import pandas as pd`, `from math import sqrt`
- **Internal module imports must also use underscores:**
  - **Correct:** `from . import utils as _utils`, `from . import stats as _stats`
  - **Incorrect:** `from . import utils`, `import quantstats_lumi.stats`
- **Why?** This ensures that end-users importing `quantstats_lumi` only see the explicitly defined functions and not internal dependencies in their IDE auto-completion.

### 2.2 Formatting & Naming Conventions
- **Functions:** Use `snake_case` for all function names and variables (e.g., `profit_factor`, `pct_rank`).
- **Docstrings:** All public functions must have Python docstrings. Follow the existing style: a brief description followed by an `Args:` block if parameters are complex.
  ```python
  def distribution(returns, compounded=True, prepare_returns=True):
      """Returns the distribution of returns
      
      Args:
          * returns (Series, DataFrame): Input return series
          * compounded (bool): Calculate compounded returns?
      """
  ```
- **Type Hints:** The codebase currently does not use standard Python type hints (e.g., `def func(prices: pd.Series) -> float:`). Agents should prioritize mimicking the existing style (no type hints, relying on docstrings for parameter types) unless specifically requested by the user.

### 2.3 Error Handling
- The codebase prefers returning safe defaults (like `0.0`, `_np.nan`, or empty DataFrames) rather than crashing when calculating financial metrics, especially if a math operation fails due to missing data or divide-by-zero errors.
- Use `try/except Exception:` blocks where appropriate when dealing with volatile pandas operations in stats calculations:
  ```python
  try:
      # Complex calculation with pandas/numpy
      result = (returns.mean() / returns.std()) * _np.sqrt(252)
      return result
  except Exception:
      return 0.0
  ```

### 2.4 Code Organization
- `quantstats_lumi/stats.py`: Core mathematical and financial calculations. All new metrics belong here.
- `quantstats_lumi/reports.py`: Generation of HTML and text-based tearsheets/reports.
- `quantstats_lumi/plots.py`: Data visualization using matplotlib/seaborn.
- `quantstats_lumi/utils.py`: Data preparation, date conversions, and internal helpers.
- **Separation of Concerns:** Never place statistical calculations inside plotting functions; separate the logic. Calculate in `stats.py` and pass the prepared data to `plots.py`.

## 3. General Agent Protocol

### 3.1 Proactiveness & Verification
- When asked to add a new metric, define it in `stats.py` and ensure you add the corresponding wrapper/helper in `utils.py` if needed.
- Write unit tests for all new calculations using `pytest` to proactively verify your own work.
- Use print statements or logging locally during development to debug your code before finalizing changes.
- **Pathing:** ALWAYS construct absolute paths when performing file operations (e.g., `/home/wade/dev/quantstats_jwt/quantstats_lumi/stats.py`).
- **Context:** Use the `read` or `grep` tools to verify existing implementations before attempting edits. DO NOT guess the arguments of internal functions like `_utils._prepare_returns()`.

### 3.2 Modifying Code
- When modifying files, use exact string replacements (`edit`) where possible to avoid massive file rewrites. 
- Ensure that you read the surrounding functions to match the exact spacing and docstring styles of neighboring code.
- Do not modify or remove the Apache 2.0 license headers at the top of the files.
- Ensure that the `.gitignore` correctly ignores any temporary directories you might create during testing or development.

## 4. AI Rules
- No existing `.cursorrules` or `.github/copilot-instructions.md` files were found in this repository. This `AGENTS.md` file serves as the primary source of truth for all AI interactions.