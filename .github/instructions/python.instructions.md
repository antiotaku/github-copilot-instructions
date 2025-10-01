# Default Python Project Folder Structures

## 1. Single Application (Always Has CLI Entry)

```
myapp/
├── myapp/             # Application code
│   ├── __init__.py
│   └── ...
├── tests/
├── pyproject.toml
├── README.md
├── scripts/           # (Optional) CLI scripts or entry points
└── ...
```
**Notes:**
- The application must define a CLI entry point (e.g., via `scripts` or `entry_points.console_scripts` in `pyproject.toml`).

## 2. Library (Never Has CLI Entry)

```
mylib/
├── mylib/             # Library code
│   ├── __init__.py
│   └── ...
├── tests/
├── pyproject.toml
├── README.md
└── ...
```
**Notes:**
- A library must not define any CLI entry points. It is intended for import/use by other code.

## 3. Package (May Have CLI Entry)

```
mypkg/
├── mypkg/             # Package code
│   ├── __init__.py
│   └── ...
├── tests/
├── pyproject.toml
├── README.md
├── scripts/           # (Optional) CLI scripts or entry points
└── ...
```
**Notes:**
- A package may define CLI entry points, but it is not required. If present, use `scripts/` or `entry_points.console_scripts` in `pyproject.toml`.

## Multiple Distribution (PEP 420) with astron Namespace

For any astron-*** project, use the following layout:

```
astron-foo/
├── astron/            # Namespace folder (NO __init__.py)
│   └── foo/           # Subpackage for this distribution
│       ├── __init__.py
│       └── ...
├── tests/
├── pyproject.toml
├── README.md
├── scripts/           # (Optional) CLI scripts or entry points (if package or app)
└── ...
```
**Notes:**
- The astron namespace is shared by all astron-*** projects. Each distribution provides its own subpackage under `astron/`.
- Only packages or applications should define CLI entry points; libraries must not.

When multiple astron-*** projects are installed, they all share the astron namespace:

```
site-packages/
├── astron/
│   ├── foo/
│   └── bar/
```
# Python Coding Conventions & Style Guide

## Python Version Policy

- **Default Python version:** 3.11
- **Minimum supported version:** 3.10
- Do **not** support Python 3.8 or 3.9 unless absolutely required by a dependency. Do not support Python 2 or any Python 3 version older than 3.10.
---
description: 'Python coding conventions and guidelines'
applyTo: '**/*.py'
---


# Python Coding Conventions & Style Guide

## 1. Default Coding Style: Google Python Style Guide

- All Python code **must** follow the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html) as the default for naming, formatting, docstrings, and code organization.
- Use [Google-style docstrings](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings) for all public modules, classes, methods, and functions.
- If a style or convention is not covered by the Google guide, follow the supplemental checklists below.

---

## 2. Supplemental: Modern Python 3.10+ Coding Style & Typing Checklist

### 📖 Basic Style
- Follow PEP 8: 4-space indentation, line length ≤ 88/100, import order (stdlib → third-party → local).
- Follow PEP 257: Every public function, class, and module must have a docstring.
- Follow PEP 20 (The Zen of Python): Clarity, simplicity, and readability first.

### 📝 Type Hints (Python 3.10+)
- PEP 484: Use type hints consistently for all public APIs.
- PEP 561: If your package provides type hints, include a `py.typed` file at the package root.
- PEP 585: Prefer built-in generics (`list`, `dict`) instead of `List`, `Dict`.
- PEP 604: Use `int | str` for unions instead of `Union[int, str]`.
- PEP 586: Use `Literal` for constant values.
- PEP 589: Use `TypedDict` for dict-like structured data.
- PEP 544: Use `Protocol` for structural subtyping (duck typing with types).
- PEP 560: Internal improvements to typing — relevant if you’re writing custom typing extensions.

### 📦 Project Structure & Distribution
- For any project named with the `astron-***` prefix, you MUST use [PEP 420](https://peps.python.org/pep-0420/) namespace packages to enable multiple distributions to share the same namespace.
- The top-level namespace folder MUST be named `astron` and MUST NOT contain an `__init__.py` file.
- This allows all `astron-***` projects to be installed together and share the `astron` namespace.
- Docstrings: Use Google-style or NumPy-style docstrings for compatibility with Sphinx/mkdocstrings.
- Packaging: Use `pyproject.toml` (PEP 517/518) as the modern build configuration baseline.

### 🔍 Practical Guidelines
- Function signatures: All public APIs must have complete type hints.
- Error handling: Custom exceptions should inherit from `Exception` and provide clear messages.
- Testing: Combine type checking (`mypy`/`pyright`), linting (`flake8`, `ruff`), and unit tests.
- Consistency: Enforce a formatter (`black` or `ruff`) and import sorter (`isort` or `ruff`).
- Source code MUST be free of type checking/linting errors.

---

## 3. Modern Python 3.10+ / 3.11+ Typing & Style Additions



### Python 3.10+
- PEP 604: Use `int | str` for unions instead of `Union[int, str]`.
- PEP 612: Use `ParamSpec` for higher-order functions and decorators.
- PEP 647: Use `TypeGuard` for type narrowing.
- PEP 673: Use `Self` for methods returning the instance.
- PEP 655: Use `Required` / `NotRequired` for `TypedDict` fields.

### Python 3.11+
- PEP 646: Use `TypeVarTuple` for variadic generics (e.g., `*Ts`).
- PEP 675: Use `LiteralString` for safe string formatting (e.g., SQL).
- PEP 681: Use `dataclass_transform` for libraries like Pydantic/SQLAlchemy to integrate with type checkers.

---

## 4. General Coding & Documentation Practices

- Always prioritize readability, clarity, and maintainability.
- Break down complex functions into smaller, testable units.
- Use descriptive names for functions, variables, and classes.
- For algorithmic code, include a brief explanation of the approach in the docstring.
- Handle edge cases and provide clear, custom exception messages.
- Document the use and purpose of any external libraries.
- Use consistent naming conventions and idiomatic Python.

---

## 5. Testing & Edge Cases

- Always include test cases for critical paths and edge cases (empty input, invalid types, large datasets, etc.).
- Write unit tests for all public functions and document them with Google-style docstrings.
- Use type checkers (`mypy`, `pyright`), linters (`flake8`, `ruff`), and formatters (`black`, `ruff`).

---


## 6. Practical Examples: Most Common Styles & Conventions

### 6.1 Imports
```python
# Standard library imports first, then third-party, then local
import os
import sys

import numpy as np
import requests

from myproject.utils import helper_function
```

### 6.2 Function Definitions & Type Hints
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

### 6.3 Class Definition & Docstring
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

### 6.4 Constants
```python
PI: float = 3.14159  # Module-level constant, all uppercase
```

### 6.5 Google-Style Docstring for Module
"""
This module provides math utility functions.
"""

### 6.6 Exception Handling
```python
class CustomError(Exception):
    """Custom exception with a clear message."""

def divide(a: float, b: float) -> float:
    """Divides a by b, raises CustomError on division by zero."""
    if b == 0:
        raise CustomError("Division by zero is not allowed.")
    return a / b
```

### 6.7 Property Decorators
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

### 6.8 Type Aliases, Literals, TypedDict, Protocol
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

### 6.9 Data Classes
```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    is_active: bool = True
```

### 6.10 Unions, Optionals, and Modern Typing
```python
from typing import Optional

def find_user(user_id: int) -> Optional[User]:
    """Returns a User if found, else None."""
    # ...
    return None

def process(value: int | str) -> str:
    """Accepts int or str (Python 3.10+ union syntax)."""
    return str(value)
```

### 6.11 Context Managers
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

### 6.12 Testing Example (pytest)
```python
import pytest

def test_add():
    assert add(2, 3) == 5

def test_divide_zero():
    with pytest.raises(CustomError):
        divide(1, 0)
```

### 6.13 Formatting, Linting, and Imports
```sh
# Format code
black .
# Lint code
ruff .
# Sort imports
isort .
```

### 6.14 Edge Case Handling
```python
def safe_get(d: dict, key: str, default: str = "") -> str:
    """Safely get a value from a dict, with a default."""
    return d.get(key, default)
```


### 6.15 Forward References
Use standard Python 3.10+ syntax for forward references. String annotations or `from __future__ import annotations` are not required for 3.10+.
```python
class TreeNode:
    def __init__(self, left: 'TreeNode' | None = None, right: 'TreeNode' | None = None) -> None:
        self.left = left
        self.right = right
```

---

---

## 7. References

- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)
- [PEP 257 – Docstring Conventions](https://peps.python.org/pep-0257/)
- [PEP 484 – Type Hints](https://peps.python.org/pep-0484/)
- [PEP 585 – Type Hinting Generics In Standard Collections](https://peps.python.org/pep-0585/)
- [PEP 604 – Allow writing union types as X | Y](https://peps.python.org/pep-0604/)
- [PEP 673 – Self Type](https://peps.python.org/pep-0673/)
- [PEP 681 – Data Class Transforms](https://peps.python.org/pep-0681/)
