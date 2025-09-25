---
description: "Comprehensive instructions for using pytest and pytest-cov for Python testing and coverage."
applyTo: "**/*.py"
---

# Pytest & Pytest-cov Instructions

## Purpose
You WILL use these instructions to write, run, and maintain Python tests using `pytest` and `pytest-cov`. These instructions ensure high-quality, maintainable, and well-covered code.

---

## 1. Setup & Installation

You MUST install `pytest` and `pytest-cov` in your Python environment:

For example, using pip:
```sh
pip install pytest pytest-cov
```

For example, using poetry:
```sh
poetry add --dev pytest pytest-cov
```

For example, using pipenv:
```sh
pipenv install --dev pytest pytest-cov
```

For example, using uv,
```sh
uv add pytest pytest-cov --dev
uv sync
```

You MUST add them to your `requirements.txt` or `pyproject.toml` for project reproducibility.

---

## 2. Test Discovery & Structure

- You MUST place all test files in a `tests/` directory or alongside modules as `test_*.py` or `*_test.py`.
- You MUST name test functions as `test_*`.
- You MUST use plain `assert` statements for test conditions.
- You MUST group related tests in classes prefixed with `Test` (no `__init__` method), and you SHOULD prefer class-based test cases for better organization, extensibility, and shared setup/teardown.
- You MUST keep tests isolated and independent.
- You MUST use fixtures for setup/teardown logic (see below).

---

## 3. Writing Tests


You WILL:
- Prefer class-based test cases for all but the simplest tests, as they:
    - Provide better organization and grouping of related tests.
    - Allow for shared setup/teardown using fixtures.
    - Make it easier to extend and maintain tests as your codebase grows.
- Write one assertion per test unless testing related behaviors.
- Use parameterization (`@pytest.mark.parametrize`) for repetitive test logic.
- Use fixtures (`@pytest.fixture`) for reusable setup/teardown.
- Use `pytest.raises` for exception testing.
- Avoid print statements; use pytest's reporting.
- When using class-based test cases:
    - Test classes MUST be named with a `Test` prefix (e.g., `TestCalculator`).
    - Test classes MUST NOT have an `__init__` method; use fixtures for setup.
    - You MAY use `@pytest.mark.usefixtures` or class-level fixtures for setup/teardown.
    - All test methods MUST be named `test_*`.
    - You MAY use `self` for shared state within the class, but prefer fixtures for isolation.

**Example:**
```python
import pytest

# Function-based tests
@pytest.fixture
def sample_data():
    return [1, 2, 3]

def test_sum(sample_data):
    assert sum(sample_data) == 6

@pytest.mark.parametrize("a,b,expected", [(1, 2, 3), (2, 2, 4)])
def test_add(a, b, expected):
    assert a + b == expected

def test_raises():
    with pytest.raises(ZeroDivisionError):
        1 / 0

# Class-based tests
class TestMath:
    @pytest.fixture(autouse=True)
    def setup_data(self):
        self.values = [1, 2, 3]

    def test_sum(self):
        assert sum(self.values) == 6

    @pytest.mark.parametrize("a,b,expected", [(1, 2, 3), (2, 2, 4)])
    def test_add(self, a, b, expected):
        assert a + b == expected

    def test_raises(self):
        with pytest.raises(ZeroDivisionError):
            _ = 1 / 0
```

---

## 4. Running Tests

You WILL run all tests with:
```python
import pytest

# Preferred: Class-based tests
class TestMath:
    @pytest.fixture(autouse=True)
    def setup_data(self):
        self.values = [1, 2, 3]

    def test_sum(self):
        assert sum(self.values) == 6

    @pytest.mark.parametrize("a,b,expected", [(1, 2, 3), (2, 2, 4)])
    def test_add(self, a, b, expected):
        assert a + b == expected

    def test_raises(self):
        with pytest.raises(ZeroDivisionError):
            _ = 1 / 0

# Function-based tests are still supported for simple cases:
@pytest.fixture
- You MUST document complex test logic with comments explaining WHY (not WHAT).
    return [1, 2, 3]

- You MUST run tests and coverage in CI/CD pipelines.
    assert sum(sample_data) == 6
```
- You MUST review coverage reports and add tests for uncovered code.

---

## 7. Continuous Integration (CI)

You WILL integrate pytest and coverage in your CI workflows (e.g., GitHub Actions):

```yaml
    - name: Install dependencies
      run: pip install -r requirements.txt
    - name: Run tests with coverage
      run: pytest --cov=src/ --cov-report=xml
```
You WILL configure coverage reporting tools (e.g., Codecov, Coveralls) as needed.

---

## 8. Troubleshooting

- Use `pytest -v -s` for verbose output and to see print/debug output.
- Use `pytest --maxfail=3` to stop after 3 failures.
- Use `pytest --pdb` to drop into debugger on failure.
- Use `pytest --tb=short|line|no` to control traceback style.

---


## 9. pytest.ini: Usage and Configuration

You WILL use a `pytest.ini` file at the project root to configure pytest's behavior and default options.

### How to Use
- Place `pytest.ini` in your project root (same level as your main code and tests folders).
- Use it to set global pytest options, markers, test paths, and plugin configuration.
- This file is read automatically by pytest when running tests.

### Common Configuration Options
- `addopts`: Default command-line options (e.g., `-v --cov=src/`)
- `testpaths`: Directories to search for tests (e.g., `tests`)
- `markers`: Register custom markers to avoid warnings
- `minversion`: Minimum pytest version required
- `filterwarnings`: Control warnings display

### Example pytest.ini
```ini
[pytest]
addopts = -v --cov=src/ --cov-report=term-missing
testpaths = tests
minversion = 7.0
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
filterwarnings =
    ignore::DeprecationWarning
```

### Best Practices
- You MUST document any custom markers in the `markers` section.
- You SHOULD use `addopts` for common options to simplify CI and local runs.
- You SHOULD use `testpaths` to avoid accidental test discovery outside your intended folders.
- You MAY use `filterwarnings` to manage noisy or irrelevant warnings.

---

## 10. Example: Minimal Project Structure

```
project/
├── src/
│   └── mymodule.py
├── tests/
│   └── test_mymodule.py
├── requirements.txt
└── pytest.ini
```

---

## 10. References

- [Pytest Documentation](https://docs.pytest.org/en/stable/)
- [pytest-cov Documentation](https://pytest-cov.readthedocs.io/en/latest/)
- [Python Testing Cookbook](https://realpython.com/pytest-python-testing/)

---

## Success Criteria

- All code is covered by tests (target: ≥90% coverage).
- Tests are isolated, repeatable, and easy to understand.
- CI runs all tests and enforces coverage thresholds.
- Coverage and test reports are available for review.