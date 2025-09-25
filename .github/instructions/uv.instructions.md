---
description: "Comprehensive guide for using uv as a Python project and package management tool"
applyTo: "**/*.py,**/pyproject.toml,**/uv.lock,**/requirements*.txt"
---

# uv Project and Package Management Instructions

## Overview

You MUST use uv as the primary Python package manager and project management tool. uv is an extremely fast Python package installer and resolver written in Rust that serves as a drop-in replacement for pip, pip-tools, and virtualenv.

## Core Principles

- You WILL ALWAYS use uv for Python dependency management instead of pip, poetry, or conda
- You MUST create and maintain pyproject.toml files for all Python projects
- You WILL use uv's built-in virtual environment management
- You MUST leverage uv's lockfile functionality for reproducible builds
- You WILL use uv for Python version management when needed

## Project Initialization

### New Python Project
```bash
# Create a new Python project with uv
uv init my-project
cd my-project

# Or initialize in existing directory
uv init
```

### New Python Package
```bash
# Create a new Python package (installable library)
uv init --package my-package
cd my-package

# Create package in existing directory
uv init --package

# Create package with specific Python version
uv init --package my-package --python 3.11
```

### New Python Library
```bash
# Create a new Python library (for distribution)
uv init --lib my-library
cd my-library

# Create library in existing directory
uv init --lib

# Create library with specific build backend
uv init --lib my-library --build-backend hatchling
```

### Project Template Options
```bash
# Create application project (default)
uv init my-app

# Create package project (installable via pip)
uv init --package my-package

# Create library project (for PyPI distribution)
uv init --lib my-library

# Create with custom package name
uv init --name custom-name my-project

# Create without Git initialization
uv init --no-git my-project

# Create with specific Python requirement
uv init --python ">=3.9" my-project
```

### Project Type Selection Guide

**Application Project (`uv init`)**
- Use for standalone applications, scripts, or web services
- Creates basic project structure with src/ layout
- Includes basic pyproject.toml configuration
- Best for projects that won't be distributed as packages

**Package Project (`uv init --package`)**
- Use for installable Python packages
- Creates package structure with proper __init__.py files
- Configured for local installation and development
- Best for internal libraries or tools within organizations

**Library Project (`uv init --lib`)**
- Use for libraries intended for PyPI distribution
- Creates complete package structure with build configuration
- Includes proper metadata for publishing
- Best for open-source libraries or packages for public distribution

### Existing Project Migration
```bash
# Convert existing requirements.txt to pyproject.toml
uv add --requirements requirements.txt

# Convert existing poetry project
# uv automatically detects and works with existing pyproject.toml
```

## Dependency Management

### Adding Dependencies

#### Standard Package Installation
```bash
# Add production dependencies
uv add requests fastapi pydantic

# Add development dependencies
uv add --dev pytest black mypy ruff

# Add optional dependencies with groups
uv add --group test pytest pytest-cov
uv add --group lint ruff black mypy
uv add --group docs sphinx mkdocs

# Add with version constraints
uv add "django>=4.0,<5.0"
uv add "numpy==1.24.0"
```

#### Adding from URLs and Git Repositories

**Git Repository URLs**
```bash
# Add from GitHub repository (latest commit)
uv add git+https://github.com/user/repo.git

# Add from GitHub with specific branch
uv add git+https://github.com/user/repo.git@main
uv add git+https://github.com/user/repo.git@develop

# Add from GitHub with specific tag/version
uv add git+https://github.com/user/repo.git@v1.2.3
uv add git+https://github.com/user/repo.git@1.0.0

# Add from GitHub with specific commit hash
uv add git+https://github.com/user/repo.git@abc123def456

# Add from private GitHub repository (requires authentication)
uv add git+https://github.com/private-org/private-repo.git

# Add from GitLab
uv add git+https://gitlab.com/user/repo.git

# Add from Bitbucket
uv add git+https://bitbucket.org/user/repo.git

# Add from custom Git server
uv add git+https://git.example.com/user/repo.git
```

**Git with SSH URLs**
```bash
# Add using SSH (requires SSH key setup)
uv add git+ssh://git@github.com/user/repo.git
uv add git+ssh://git@gitlab.com/user/repo.git

# Add SSH with specific branch
uv add git+ssh://git@github.com/user/repo.git@develop
```

**Subdirectory in Git Repository**
```bash
# Add package from subdirectory in Git repo
uv add git+https://github.com/user/monorepo.git#subdirectory=packages/mylib

# Add with branch and subdirectory
uv add git+https://github.com/user/monorepo.git@main#subdirectory=libs/core
```

**Direct Archive URLs**
```bash
# Add from zip archive URL
uv add https://github.com/user/repo/archive/main.zip

# Add from tarball URL
uv add https://github.com/user/repo/archive/v1.2.3.tar.gz

# Add from wheel URL
uv add https://files.pythonhosted.org/packages/.../package-1.0.0-py3-none-any.whl
```

**Local File URLs**
```bash
# Add from local Git repository
uv add git+file:///path/to/local/repo

# Add from local directory
uv add file:///path/to/local/package

# Add from local archive
uv add file:///path/to/package.tar.gz
```

#### URL Dependencies with Groups
```bash
# Add Git dependency to development group
uv add --dev git+https://github.com/user/test-utils.git

# Add Git dependency to specific group
uv add --group lint git+https://github.com/user/custom-linter.git

# Add Git dependency with extras
uv add "git+https://github.com/user/repo.git[extra1,extra2]"
```

#### Advanced URL Dependency Options
```bash
# Add with editable installation (for development)
uv add --editable git+https://github.com/user/repo.git

# Add with specific Python requirement
uv add --python ">=3.9" git+https://github.com/user/repo.git

# Add multiple URL dependencies at once
uv add \
  git+https://github.com/user/repo1.git \
  git+https://github.com/user/repo2.git@v1.0.0 \
  https://github.com/user/repo3/archive/main.zip
```

### Removing Dependencies
```bash
# Remove dependencies
uv remove requests fastapi

# Remove development dependencies
uv remove --dev pytest
```

### Installing Dependencies
```bash
# Install all dependencies (production + dev)
uv sync

# Install only production dependencies
uv sync --no-dev

# Install with specific groups
uv sync --group test --group lint

# Install from lockfile exactly
uv sync --locked
```

### Workspace Management
```bash
# Initialize workspace (monorepo)
uv init --workspace

# Add package to existing workspace
uv init --package packages/new-package

# Sync all packages in workspace
uv sync

# Sync specific package in workspace
uv sync --package core-package

# Add dependency to specific workspace package
uv add --package utils-package requests

# Run commands in specific workspace package
uv run --package api-service python main.py

# Build specific package in workspace
uv build --package core-package

# Build all packages in workspace
uv build --all
```

## Virtual Environment Management

### Automatic Virtual Environment
```bash
# uv automatically creates and manages virtual environments
# No need to manually create or activate

# Run commands in the project environment
uv run python script.py
uv run pytest
uv run black .
uv run mypy src/

# Run with specific Python version
uv run --python 3.11 python script.py
```

### Manual Virtual Environment Control
```bash
# Create virtual environment explicitly
uv venv

# Create with specific Python version
uv venv --python 3.11

# Activate virtual environment (if needed)
# On Windows
.venv\Scripts\activate
# On Unix
source .venv/bin/activate
```

## Python Version Management

```bash
# Install Python versions
uv python install 3.11 3.12

# List available Python versions
uv python list

# Use specific Python version for project
uv python pin 3.11
```

## Tool Management

### Installing and Running Tools
```bash
# Install a tool globally
uv tool install black
uv tool install ruff
uv tool install mypy

# Install tool with specific version
uv tool install "black==23.12.0"

# Install tool from Git repository
uv tool install git+https://github.com/user/tool.git

# Install tool with extras
uv tool install "httpie[socks]"

# Run a tool without installing (ephemeral)
uvx black .
uvx ruff check .
uvx mypy src/

# Run tool with specific version
uvx black==23.12.0 .

# Run tool from Git repository
uvx git+https://github.com/user/tool.git
```

### Managing Installed Tools
```bash
# List installed tools
uv tool list

# Show tool information
uv tool show black

# Upgrade a tool
uv tool upgrade black

# Upgrade all tools
uv tool upgrade --all

# Uninstall a tool
uv tool uninstall black

# Reinstall a tool (clean install)
uv tool install --force black
```

### Tool Environments
```bash
# Run tool with specific Python version
uv tool install --python 3.11 black

# Install tool in isolated environment
uv tool install --isolated black

# Show tool installation directory
uv tool dir black

# List tool binaries
uv tool list --show-paths
```

### Common Development Tools
```bash
# Code formatting
uv tool install black
uv tool install isort
uv tool install autopep8

# Linting
uv tool install ruff
uv tool install flake8
uv tool install pylint

# Type checking
uv tool install mypy
uv tool install pyright

# Testing
uv tool install pytest
uv tool install tox
uv tool install coverage

# Documentation
uv tool install sphinx
uv tool install mkdocs
uv tool install pdoc

# Package management
uv tool install twine
uv tool install build
uv tool install pip-audit

# Web development
uv tool install cookiecutter
uv tool install pre-commit
uv tool install invoke

# Data science
uv tool install jupyter
uv tool install ipython
uv tool install notebook
```

### Ephemeral Tool Usage (uvx)
```bash
# Run tools without permanent installation
uvx ruff check .
uvx black --check .
uvx mypy src/
uvx pytest tests/

# Run with specific requirements
uvx --from "black==23.12.0" black .
uvx --from "ruff>=0.1.0" ruff check .

# Run with multiple packages
uvx --with requests --with click python -c "import requests, click"

# Run script with dependencies
uvx --with rich python script.py

# Run Jupyter notebook temporarily
uvx jupyter notebook

# Run one-off commands
uvx cowsay "Hello from uv!"
uvx python -m http.server 8000
```

## pyproject.toml Configuration

### Application Project Structure (`uv init`)
```toml
[project]
name = "my-app"
version = "0.1.0"
description = "My application"
authors = [
    { name = "Your Name", email = "your.email@example.com" }
]
readme = "README.md"
requires-python = ">=3.9"

dependencies = [
    "fastapi>=0.100.0",
    "pydantic>=2.0.0",
    "uvicorn>=0.20.0",
]

[project.scripts]
my-app = "my_app.main:main"

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]
```

### Package Project Structure (`uv init --package`)
```toml
[project]
name = "my-package"
version = "0.1.0"
description = "My package"
authors = [
    { name = "Your Name", email = "your.email@example.com" }
]
readme = "README.md"
requires-python = ">=3.9"

dependencies = [
    "requests>=2.28.0",
    "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

# Configure build to include only specific folders
[tool.hatch.build.targets.wheel]
packages = ["src/my_package"]

[tool.hatch.build.targets.sdist]
include = [
    "src/",
    "tests/",
    "README.md",
    "LICENSE",
]
exclude = [
    "docs/",
    "examples/",
    "scripts/",
    "temp/",
]

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]
```

### Library Project Structure (`uv init --lib`)
```toml
[project]
name = "my-library"
version = "0.1.0"
description = "My library for PyPI distribution"
authors = [
    { name = "Your Name", email = "your.email@example.com" }
]
readme = "README.md"
license = { text = "MIT" }
requires-python = ">=3.9"
keywords = ["library", "python"]
classifiers = [
    "Development Status :: 4 - Beta",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.9",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
]

dependencies = [
    "requests>=2.28.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]
test = [
    "pytest>=7.0.0",
    "pytest-cov>=4.0.0",
    "pytest-asyncio>=0.21.0",
]
lint = [
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]

[project.urls]
"Homepage" = "https://github.com/username/my-library"
"Bug Reports" = "https://github.com/username/my-library/issues"
"Source" = "https://github.com/username/my-library"
"Documentation" = "https://my-library.readthedocs.io/"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "ruff>=0.1.0",
]
```

### URL Dependencies in pyproject.toml
```toml
[project]
dependencies = [
    # Standard PyPI packages
    "requests>=2.28.0",
    "fastapi>=0.100.0",
    
    # Git repository dependencies
    "my-package @ git+https://github.com/user/my-package.git",
    "dev-tools @ git+https://github.com/user/dev-tools.git@v1.2.3",
    "private-lib @ git+ssh://git@github.com/company/private-lib.git",
    
    # Git with subdirectory
    "subpackage @ git+https://github.com/user/monorepo.git#subdirectory=packages/sub",
    
    # Direct archive URLs
    "archive-pkg @ https://github.com/user/repo/archive/main.zip",
    "wheel-pkg @ https://files.pythonhosted.org/packages/.../pkg-1.0.0-py3-none-any.whl",
    
    # Local paths (for development)
    "local-pkg @ file:///absolute/path/to/package",
    "relative-pkg @ file://./relative/path/to/package",
]

[project.optional-dependencies]
dev = [
    "test-utils @ git+https://github.com/user/test-utils.git@develop",
    "dev-server @ git+https://github.com/user/dev-server.git[extras]",
]

test = [
    "mock-lib @ git+https://github.com/user/mock-lib.git@v2.0.0",
]
```

### Advanced Configuration
```toml
[tool.uv]
# Configure index URLs
index-url = "https://pypi.org/simple"
extra-index-url = ["https://download.pytorch.org/whl/cpu"]

# Configure resolution strategy
resolution = "highest"  # or "lowest-direct"

# Configure prerelease handling
prerelease = "allow"  # or "disallow", "explicit"

# Configure Python requirement
python-requirement = ">=3.9,<3.13"

# Configure package inclusion/exclusion
no-binary = ["numpy"]
only-binary = ["torch"]

# Configure Git authentication (alternative to environment variables)
git-credentials = [
    { url = "https://github.com/", username = "oauth2", password = "${GITHUB_TOKEN}" },
    { url = "https://gitlab.com/", username = "oauth2", password = "${GITLAB_TOKEN}" },
]
```

## Lockfile Management

### Working with uv.lock
```bash
# Generate/update lockfile
uv lock

# Upgrade all dependencies
uv lock --upgrade

# Upgrade specific packages
uv lock --upgrade-package requests

# Install from lockfile
uv sync --locked

# Check if lockfile is up to date
uv lock --check
```

## Folder-Specific Build Configuration

### Build System for Specific Folders

#### Hatchling Build Configuration
```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel]
# Include only specific packages/folders
packages = ["src/core", "src/utils"]

# Alternative: specify package directory mapping
[tool.hatch.build.targets.wheel.sources]
"src" = ""  # Map src/ to package root
"lib" = "mylib"  # Map lib/ to mylib/ in package

[tool.hatch.build.targets.sdist]
# Include specific folders and files
include = [
    "src/core/",
    "src/utils/",
    "tests/core/",
    "tests/utils/",
    "README.md",
    "LICENSE",
    "pyproject.toml",
]

# Exclude unwanted folders
exclude = [
    "docs/",
    "examples/",
    "scripts/",
    "temp/",
    "*.log",
    "**/__pycache__",
    "**/*.pyc",
]
```

#### Setuptools Build Configuration
```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[tool.setuptools.packages.find]
where = ["src"]  # Look for packages in src/ only
include = ["core*", "utils*"]  # Include packages matching patterns
exclude = ["tests*", "docs*"]  # Exclude test and doc packages

[tool.setuptools.package-data]
"core" = ["data/*.json", "templates/*.html"]
"utils" = ["config/*.yaml"]

[tool.setuptools.exclude-package-data]
"*" = ["*.pyc", "__pycache__", "*.log"]
```

#### PDM Build Configuration
```toml
[build-system]
requires = ["pdm-backend"]
build-backend = "pdm.backend"

[tool.pdm.build]
includes = ["src/core", "src/utils"]
excludes = ["tests/", "docs/", "examples/"]

# Custom build script for specific folders
[tool.pdm-backend]
custom-build-script = "build_script.py"
```

### Monorepo and Multi-Package Configuration

#### Workspace Structure
```toml
# Root pyproject.toml for monorepo
[tool.uv.workspace]
members = [
    "packages/core",
    "packages/utils", 
    "packages/cli",
    "services/api",
    "services/worker",
]

# Exclude specific folders from workspace
exclude = [
    "packages/experimental",
    "archived/*",
]

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "ruff>=0.1.0",
]
```

#### Individual Package Configuration
```toml
# packages/core/pyproject.toml
[project]
name = "myproject-core"
version = "1.0.0"
dependencies = ["requests>=2.28.0"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel]
# Only include this specific package
packages = ["src/core"]

[tool.hatch.build.targets.sdist]
include = [
    "src/core/",
    "tests/",
    "README.md",
]
```

#### Cross-Package Dependencies in Monorepo
```toml
# packages/utils/pyproject.toml
[project]
name = "myproject-utils"
dependencies = [
    "myproject-core",  # Depends on sibling package
    "pydantic>=2.0.0",
]

# Development dependencies for the entire workspace
[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
]
```

### Conditional Build Configuration

#### Environment-Based Builds
```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel.hooks.custom]
# Custom build script for conditional inclusion
path = "build_hooks.py"

# build_hooks.py example:
# import os
# def build_hook(build_data, config):
#     if os.environ.get('BUILD_TYPE') == 'minimal':
#         build_data['packages'] = ['src/core']
#     else:
#         build_data['packages'] = ['src/core', 'src/utils', 'src/extras']
```

#### Platform-Specific Builds
```toml
[tool.hatch.build.targets.wheel]
# Include platform-specific folders
packages = ["src/common"]

[tool.hatch.build.targets.wheel.shared-data]
"src/windows" = "data/windows"  # Windows-specific
"src/linux" = "data/linux"     # Linux-specific
"src/macos" = "data/macos"     # macOS-specific
```

## Building and Publishing

### Building Packages and Libraries
```bash
# Build package/library for distribution
uv build

# Build wheel only
uv build --wheel

# Build source distribution only
uv build --sdist

# Build with specific output directory
uv build --out-dir dist/

# Build and check package
uv build && twine check dist/*
```

### Publishing to PyPI
```bash
# Publish to PyPI (for libraries)
uv build
uvx twine upload dist/*

# Publish to Test PyPI first
uvx twine upload --repository testpypi dist/*

# Install and test from Test PyPI
uv add --index-url https://test.pypi.org/simple/ my-library
```

### URL Dependencies Authentication & Security

#### GitHub Authentication
```bash
# For private repositories, use personal access token
# Set environment variable (recommended)
export GITHUB_TOKEN=ghp_your_token_here
uv add git+https://github.com/private-org/private-repo.git

# Or embed token in URL (less secure)
uv add git+https://oauth2:ghp_your_token_here@github.com/private-org/private-repo.git

# Using SSH keys (most secure for frequent access)
uv add git+ssh://git@github.com/private-org/private-repo.git
```

#### GitLab Authentication
```bash
# Using GitLab personal access token
export GITLAB_TOKEN=glpat_your_token_here
uv add git+https://gitlab.com/private-group/private-repo.git

# Using deploy tokens
uv add git+https://gitlab+deploy-token-username:deploy_token@gitlab.com/group/repo.git
```

#### Security Considerations
```bash
# Pin to specific commits for security (recommended for production)
uv add git+https://github.com/user/repo.git@abc123def456789

# Verify repository authenticity before adding
git ls-remote https://github.com/user/repo.git

# Use HTTPS URLs instead of HTTP for security
uv add git+https://github.com/user/repo.git  # Good
# uv add git+http://github.com/user/repo.git   # Avoid

# For sensitive environments, mirror dependencies internally
uv add git+https://internal-git.company.com/mirrors/external-repo.git
```

### Local Package Development
```bash
# Install package in editable mode for development
uv pip install -e .

# Install with optional dependencies
uv pip install -e ".[dev,test]"

# Install from local path
uv add ./path/to/local/package
uv add ../relative/path/to/package
uv add /absolute/path/to/package

# Install from local Git repository
uv add git+file:///absolute/path/to/local/repo
uv add ./local-repo  # For local directories

# Install from Git repository (remote)
uv add git+https://github.com/user/repo.git
```

## Common Workflows

### Application Development Workflow
```bash
# 1. Initialize application
uv init my-app && cd my-app

# 2. Add dependencies
uv add fastapi uvicorn pydantic
uv add --dev pytest black mypy ruff

# 3. Install development tools (optional global install)
uv tool install black ruff mypy pytest

# 4. Run development server
uv run uvicorn main:app --reload

# 5. Run tests (using project dependencies or tools)
uv run pytest
# OR using installed tool
uvx pytest

# 6. Format and lint code
uv run black .
uv run ruff check .
uv run mypy src/
# OR using installed tools
uvx black .
uvx ruff check .
uvx mypy src/
```

### Package Development Workflow
```bash
# 1. Initialize package
uv init --package my-package && cd my-package

# 2. Add dependencies
uv add requests pydantic
uv add --dev pytest black mypy ruff

# 3. Install development tools globally
uv tool install black mypy ruff pytest build

# 4. Install in editable mode
uv pip install -e .

# 5. Run tests
uv run pytest
# OR using tool
uvx pytest

# 6. Format and lint
uvx black .
uvx ruff check .
uvx mypy src/

# 7. Build package
uv build
# OR using tool
uvx build
```

### Library Development Workflow
```bash
# 1. Initialize library
uv init --lib my-library && cd my-library

# 2. Add dependencies
uv add requests
uv add --dev pytest black mypy ruff sphinx

# 3. Install development tools globally
uv tool install black mypy ruff pytest coverage twine build sphinx

# 4. Install in editable mode
uv pip install -e ".[dev]"

# 5. Run tests with coverage
uv run pytest --cov=my_library
# OR using tools
uvx coverage run -m pytest
uvx coverage report

# 6. Build documentation
uvx sphinx-build docs docs/_build

# 7. Build and publish
uv build
uvx twine upload dist/*
```

### CI/CD Integration
```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v3
        with:
          version: "latest"
      
      - name: Set up Python
        run: uv python install
      
      - name: Install dependencies
        run: uv sync --all-extras --dev
      
      - name: Run tests
        run: uv run pytest
      
      - name: Run linting (using uvx for faster execution)
        run: |
          uvx black --check .
          uvx ruff check .
          uvx mypy src/
      
      - name: Build package (if library/package)
        run: uvx build
      
      - name: Upload to PyPI (if library and on release)
        if: github.event_name == 'release'
        run: uvx twine upload dist/*
        env:
          TWINE_USERNAME: __token__
          TWINE_PASSWORD: ${{ secrets.PYPI_API_TOKEN }}
```

### Tool Best Practices

#### When to Use `uv tool install` vs `uvx`
```bash
# Use uv tool install for:
# - Tools you use frequently across multiple projects
uv tool install black ruff mypy pytest

# - Tools that need specific versions consistently
uv tool install "black==23.12.0"

# Use uvx for:
# - One-off commands or tools you rarely use
uvx cowsay "Hello World!"

# - Testing tools before permanent installation
uvx --from "ruff>=0.2.0" ruff check .

# - Running tools with specific dependencies
uvx --with rich python script.py

# - CI/CD pipelines (no permanent installation needed)
uvx pytest
uvx black --check .
```

#### Tool Environment Management
```bash
# Keep tools updated
uv tool upgrade --all

# Clean up unused tools
uv tool uninstall unused-tool

# Use isolated environments for conflicting tools
uv tool install --isolated tool-with-conflicts

# Check what tools are available
uv tool list --show-paths
```

### Docker Integration
```dockerfile
FROM python:3.11-slim

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /usr/local/bin/

# Copy project files
COPY . /app
WORKDIR /app

# Install dependencies
RUN uv sync --frozen --no-cache

# Run application
CMD ["uv", "run", "python", "-m", "my_project"]
```

## Best Practices

### Project Structure
- You MUST use pyproject.toml as the single source of truth for project configuration
- You WILL organize dependencies into logical groups (dev, test, lint, docs)
- You MUST commit uv.lock to version control for reproducible builds
- You WILL use semantic versioning for project versions

### Dependency Management
- You MUST specify minimum versions for critical dependencies
- You WILL use version ranges that allow compatible updates
- You MUST regularly update dependencies using `uv lock --upgrade`
- You WILL separate production and development dependencies clearly

### Performance Optimization
- You MUST use `uv sync --locked` in production/CI for fastest installs
- You WILL cache uv installations in CI/CD pipelines
- You MUST use `--no-dev` flag for production deployments
- You WILL leverage uv's parallel installation capabilities
- You MUST use `uvx` in CI/CD for ephemeral tool execution
- You WILL install frequently used tools globally with `uv tool install`

### Tool Management Best Practices
- You MUST install commonly used development tools globally: `black`, `ruff`, `mypy`, `pytest`
- You WILL use `uvx` for one-off commands and CI/CD pipelines
- You MUST keep tools updated with `uv tool upgrade --all`
- You WILL use `uvx --from` to test specific tool versions before installing
- You MUST use isolated environments for tools with conflicting dependencies

### Folder-Specific Build Best Practices
- You MUST explicitly configure `packages` in build system for specific folder inclusion
- You WILL use `include` and `exclude` patterns to control what gets packaged
- You MUST exclude development artifacts: `__pycache__`, `*.pyc`, `*.log`, `temp/`
- You WILL use workspace configuration for monorepos with multiple packages
- You MUST test builds with `uv build --check` before publishing
- You WILL use environment variables for conditional builds when needed
- You MUST document folder structure and build configuration in README

### Security Best Practices
- You MUST regularly audit dependencies with `uv tree`
- You WILL use dependency groups to minimize attack surface
- You MUST pin exact versions in lockfile for security-critical applications
- You WILL use trusted indexes and verify package integrity

## Troubleshooting

### Common Issues
```bash
# Clear uv cache if having resolution issues
uv cache clean

# Verbose output for debugging
uv sync --verbose

# Force reinstall of all packages
uv sync --reinstall

# Check for conflicts
uv tree --show-version-specifiers

# Resolve specific Python version issues
uv python list
uv python pin <version>
```

### URL Dependencies Troubleshooting

#### Common Issues and Solutions
```bash
# Authentication failed for private repository
# Solution: Set up proper authentication
export GITHUB_TOKEN=your_token
# Or use SSH keys
ssh-add ~/.ssh/id_rsa

# Git repository not found or access denied
# Solution: Verify URL and permissions
git ls-remote https://github.com/user/repo.git

# Subdirectory not found in repository
# Solution: Verify subdirectory path exists
uv add git+https://github.com/user/monorepo.git#subdirectory=correct/path

# Branch or tag not found
# Solution: Check available branches/tags
git ls-remote --heads --tags https://github.com/user/repo.git

# SSL certificate verification failed
# Solution: Update certificates or use SSH
uv add git+ssh://git@github.com/user/repo.git

# Large repository causing timeout
# Solution: Use shallow clone or specific commit
uv add git+https://github.com/user/large-repo.git@specific-commit

# Dependency resolution conflicts with URL packages
# Solution: Use explicit version constraints
uv add "package>=1.0.0" git+https://github.com/user/repo.git@v2.0.0
```

#### Debugging URL Dependencies
```bash
# Verbose output to see what uv is doing
uv add --verbose git+https://github.com/user/repo.git

# Check resolved dependencies
uv tree

# Update URL dependency to latest
uv lock --upgrade-package package-name

# Force reinstall URL dependency
uv sync --reinstall-package package-name
```

### Migration from Other Tools
```bash
# From pip + requirements.txt (including Git dependencies)
uv add --requirements requirements.txt
uv add --requirements requirements-dev.txt --dev

# From poetry (preserves Git dependencies)
# uv automatically works with existing pyproject.toml
uv sync

# From conda
# Export conda environment first, then add to uv
conda env export > environment.yml
# Manually add packages to pyproject.toml

# Convert pip Git dependencies to uv format
# pip: git+https://github.com/user/repo.git@v1.0.0
# uv:  uv add git+https://github.com/user/repo.git@v1.0.0
```

## Integration with IDEs

### VS Code Configuration
```json
{
    "python.defaultInterpreterPath": "./.venv/bin/python",
    "python.terminal.activateEnvironment": true,
    "python.formatting.provider": "black",
    "python.linting.enabled": true,
    "python.linting.ruffEnabled": true,
    "python.testing.pytestEnabled": true
}
```

## Error Handling

### Resolution Conflicts
- You MUST investigate resolution conflicts using `uv tree`
- You WILL use version constraints to resolve conflicts
- You MUST document any pinned versions with reasoning
- You WILL prefer upgrading dependencies over downgrading

### Performance Issues
- You MUST use `--no-build` when appropriate for faster installs
- You WILL configure HTTP timeouts for slow networks
- You MUST use local caches in CI/CD environments
- You WILL parallelize operations when possible

## Conclusion

uv provides a modern, fast, and reliable Python project management experience. By following these instructions, you ensure consistent, reproducible, and efficient Python development workflows that leverage uv's full capabilities while maintaining best practices for dependency management and project structure.