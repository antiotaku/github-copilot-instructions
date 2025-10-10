---
description: 'Python coding conventions and guidelines'
applyTo: '**/*.py'
---

# Python Coding Conventions & Style Guide

## 0. Overview

This document outlines the coding conventions and style guidelines for Python projects. It covers Python version policies, project structure, type hinting, coding practices, testing, and practical examples.

## 1. Python Version Policy

- Minimum Supported Version: Python 3.10
- Recommended Version: Python 3.11 or later
- Deprecated Versions: Python 3.9 and earlier
- Unsupported Versions: Python 2.x
- MUST **NOT** use deprecated or obsolete Python features.

---

## 2. Default Coding Style: Google Python Style Guide

- Follow the Google Python Style Guide as the baseline for coding style.
- If there are conflicts between Google Style and Other Guidelines, prefer Google Style unless there is a compelling reason not to.

---

## 3. Project Structure & Packaging

### Project Structure

- PEP 420: Use implicit namespace packages for multi-directory packages.

### Project Packaging

- PEP 517/518: Use `pyproject.toml` for build system configuration.
    - Use `uv` and `hatchling` for building and packaging.
- PEP 621: Use `pyproject.toml` for project metadata (name, version, description, authors, license, dependencies).
- PEP 561: For libraries, include type information by adding a `py.typed` file in the package directory.

### Single application Example

For a single application, the project name should be the same as the package name. For example, a project named `myapp` would have the following structure:


```plaintext
myapp/                       # Project root, `myapp` is the project name
├── src/                     # Source code directory
│   └── myapp/               # Package namespace from project name
│       ├── __init__.py      # Package initializer
│       ├── main.py          # Main application module
│       └── py.typed         # Marker file for type information
├── tests/                   # Tests directory
│   ├── __init__.py          # Test package initializer
│   └── test_main.py         # Example test module
└── pyproject.toml           # Build system and project metadata
```

If `project.scripts` is defined in `pyproject.toml` as below:

```toml
[project.scripts]
myapp = "myapp.main:main"
```

For running the application after installation, use:

```sh
# run the installed script
myapp
# or
python -m myapp.main
```

### Individual Package Example

For individual libraries, the project name should be the same as the package name. For example, a project named `mylib` would have the following structure:

```plaintext
mylib/                      # Project root, `mylib` is the project name
├── src/                    # Source code directory
│   └── mylib/              # Package namespace from project name
│       ├── __init__.py     # Package initializer
│       ├── module.py       # Example module
│       └── py.typed        # Marker file for type information
├── tests/                  # Tests directory
│   ├── __init__.py         # Test package initializer
│   └── test_module.py      # Example test module
├── scripts/                # Optional scripts directory
│   └── cli.py              # Example script
└── pyproject.toml          # Build system and project metadata
```

For importing the package in code, use:

```python
from mylib import module
```

If the project includes scripts, define them in `pyproject.toml` as below:

```toml
[project.scripts]
mylib-cli = "mylib.scripts.cli:main"
```

For running the script after installation, use:

```sh
# run the installed script
mylib-cli
# or
python -m mylib.scripts.cli
```

### Multi-Package Projects with Common Prefix Example

For multi-package projects with a common prefix, the project name should be the common prefix followed by a hyphen and a suffix for each package. For example, a project named `astron-foo` would have the following structure:

```plaintext
astron-foo/                  # Project root, `astron-foo` is the project name
├── src/                     # Source code directory
│   └── astron/              # Top-level namespace from project name prefix `astron`
│       └── foo/             # Package namespace from project name suffix `foo`
│           ├── __init__.py  # Package initializer
│           ├── module.py    # Example module
│           └── py.typed     # Marker file for type information
├── tests/                   # Tests directory
│   ├── __init__.py          # Test package initializer
│   └── test_module.py       # Example test module
└── pyproject.toml           # Build system and project metadata
```

- Top-level namespace is derived from the common prefix (e.g., `astron`).
- Package namespace is derived from the suffix (e.g., `foo`).

For importing the package in code, use the full namespace:

```python
from astron.foo import module
```

Multi-package projects often do not include scripts. 

---

## 4. Type Hints & Typing Standards

- PEP 484: Use type hints for all public functions and methods.
- PEP 526: Use variable annotations for module-level and class-level variables.
- PEP 563: Consider using `from __future__ import annotations` for forward compatibility and to enable postponed evaluation of annotations (optional but recommended for Python 3.10+).
- PEP 585: Use built-in generics (`list`, `dict`) instead of `List`, `Dict`.
- PEP 586: Use `Literal` for constant values.
- PEP 589: Use `TypedDict` for dict-like structured data.
- PEP 544: Use `Protocol` for structural subtyping (duck typing with types).
- PEP 560: Follow for internal improvements to typing — relevant if you're writing custom typing extensions.

**For Python 3.10+:**

- PEP 604: Use `int | str` for unions instead of `Union[int, str]`.
- PEP 612: Use `ParamSpec` for higher-order functions and decorators.
- PEP 647: Use `TypeGuard` for type narrowing.
- PEP 673: Use `Self` for methods returning the instance.
- PEP 655: Use `Required` / `NotRequired` for `TypedDict` fields.

**For Python 3.11+:**

- PEP 646: Use `TypeVarTuple` for variadic generics (e.g., `*Ts`).
- PEP 675: Use `LiteralString` for safe string formatting (e.g., SQL).
- PEP 681: Use `dataclass_transform` for libraries like Pydantic/SQLAlchemy to integrate with type checkers.

**For Python 3.12+:**

- PEP 695: Prefer the new type parameter syntax for generics in classes and functions when targeting 3.12+ (keep `typing.TypeVar`/`typing.Generic` for 3.10/3.11 compatibility).

---

## 5. General Coding & Documentation Practices

- Always prioritize readability, clarity, and maintainability.
- Break down complex functions into smaller, testable units.
- Use descriptive names for functions, variables, and classes.
- For algorithmic code, include a brief explanation of the approach in the docstring.
- Handle edge cases and provide clear, custom exception messages.
- Document the use and purpose of any external libraries.
- Use consistent naming conventions and idiomatic Python.

---

## 6. Testing & Edge Cases

- Always include test cases for critical paths and edge cases (empty input, invalid types, large datasets, etc.).
- Write unit tests for all public functions and document them with Google-style docstrings.
- Use type checkers (`mypy`, `pyright`), linters (`ruff`), and formatters (`black` or `ruff`).

---

## 7. Practical Examples: Most Common Styles & Conventions

### 7.1 Imports

```python
# Standard library imports first, then third-party, then local
import os
import sys

import numpy as np
import requests

from myproject.utils import helper_function
```

### 7.2 Function Definitions & Type Hints

```python
def add(x: int, y: int) -> int:
    """Adds two integers.

    Args:
        x (int): First integer.
        y (int): Second integer.

    Returns:
        int: The sum of x and y.
    """
    return x + y
```

### 7.3 Class Definition & Docstring

```python
class Person:
    """Represents a person with a name and age."""

    def __init__(self, name: str, age: int) -> None:
        self.name = name
        self.age = age

    def greet(self) -> str:
        """Returns a greeting message."""
        return f"Hello, my name is {self.name}."
```

### 7.4 Constants

```python
PI: float = 3.14159  # Module-level constant
MAX_CONNECTIONS: int = 100  # Use UPPER_CASE for module-level constants
```

### 7.5 Google-Style Docstring for Module

```python
"""This module provides math utility functions."""
```

### 7.6 Exception Handling

```python
class CustomError(Exception):
    """Custom exception with a clear message."""

def divide(a: float, b: float) -> float:
    """Divides a by b, raises CustomError on division by zero."""
    if b == 0:
        raise CustomError("Division by zero is not allowed.")
    return a / b
```

### 7.7 Property Decorators

```python
class Rectangle:
    def __init__(self, width: float, height: float) -> None:
        self._width = width
        self._height = height

    @property
    def area(self) -> float:
        """Area of the rectangle."""
        return self._width * self._height
```

### 7.8 Type Aliases, Literals, TypedDict, Protocol

```python
from typing import Literal, TypedDict, Protocol

Vector = list[float]  # Type alias (PEP 585)

class Point(TypedDict):
    x: float
    y: float

class Drawable(Protocol):
    def draw(self) -> None: ...

def move_point(p: Point, dx: float, dy: float) -> Point:
    """Moves a point by dx and dy."""
    return {"x": p["x"] + dx, "y": p["y"] + dy}

def set_mode(mode: Literal["r", "w"]) -> None:
    """Accepts only 'r' or 'w' as mode."""
    pass
```

### 7.9 Data Classes

```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    is_active: bool = True
```

### 7.10 Unions, Optionals, and Modern Typing

```python
def find_user(user_id: int) -> User | None:
    """Returns a User if found, else None."""
    # ...
    return None

def process(value: int | str) -> str:
    """Accepts int or str (Python 3.10+ union syntax)."""
    return str(value)
```

### 7.11 Context Managers

```python
from contextlib import contextmanager

@contextmanager
def managed_resource():
    print("Resource acquired")
    try:
        yield
    finally:
        print("Resource released")
```

### 7.12 Testing Example (pytest)

```python
import pytest

def test_add():
    assert add(2, 3) == 5

def test_divide_zero():
    with pytest.raises(CustomError):
        divide(1, 0)
```

### 7.13 Formatting, Linting, and Imports

```sh
# Format code
black .
# Lint code
ruff .
# Sort imports
isort .
```

### 7.14 Edge Case Handling

```python
def safe_get(d: dict, key: str, default: str = "") -> str:
    """Safely get a value from a dict, with a default."""
    return d.get(key, default)
```


### 7.15 Forward References

Use quoted forward references for Python 3.10–3.12 compatibility. This avoids reliance on evaluation semantics at runtime and works consistently with type checkers.

```python
class TreeNode:
    def __init__(self, left: 'TreeNode' | None = None, right: 'TreeNode' | None = None) -> None:
        self.left = left
        self.right = right
```

---

## 8. References

<!-- Links below are external URLs, not local file paths -->

- Google Python Style Guide: https://google.github.io/styleguide/pyguide.html
- PEP 420: https://www.python.org/dev/peps/pep-0420/
- PEP 484: https://www.python.org/dev/peps/pep-0484/
- PEP 526: https://www.python.org/dev/peps/pep-0526/
- PEP 563: https://www.python.org/dev/peps/pep-0563/
- PEP 585: https://www.python.org/dev/peps/pep-0585/
- PEP 586: https://www.python.org/dev/peps/pep-0586/
- PEP 589: https://www.python.org/dev/peps/pep-0589/
- PEP 544: https://www.python.org/dev/peps/pep-0544/
- PEP 560: https://www.python.org/dev/peps/pep-0560/
- PEP 604: https://www.python.org/dev/peps/pep-0604/
- PEP 612: https://www.python.org/dev/peps/pep-0612/
- PEP 646: https://www.python.org/dev/peps/pep-0646/
- PEP 647: https://www.python.org/dev/peps/pep-0647/
- PEP 655: https://www.python.org/dev/peps/pep-0655/
- PEP 673: https://www.python.org/dev/peps/pep-0673/
- PEP 675: https://www.python.org/dev/peps/pep-0675/
- PEP 681: https://www.python.org/dev/peps/pep-0681/
- PEP 695: https://www.python.org/dev/peps/pep-0695/
