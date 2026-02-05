# pip – Python Package Installer

## Purpose of this page

This page explains what `pip` is, how it works, and how to use it correctly in this environment.

In this environment, where Python is installed matters.
Installing packages to the wrong location results in scripts and executables that cannot run due to application control policies.

## What is `pip`?

`pip` is Python’s package installer.

- It installs Python packages into a specific Python environment
- It does **not** install software system-wide
- It does **not** bypass application execution controls

Key idea:

> `pip` installs into Python, and Python’s location determines what can execute.

## What `pip` is not

`pip` is often misunderstood. It is **not**:

- A standalone application
- Global across the system
- Tied to an IDE (Spyder, VS Code, PyCharm)
- A way around application blocking

Because of this:

> Installing a package successfully does **not** mean it can be executed.

## Critical environment rule

!!! warning
    Users must **not** install Python or pip packages into their local profile.

In this environment:

- Scripts and executables under user profiles cannot run
- Python installed under `%LOCALAPPDATA%` will fail execution
- `pip install --user` creates non-functional installs

### Approved Python locations

Python must be installed in one of the following locations:

- Standalone Python: `C:\Apps\PythonXX\`
- Conda-managed environments: `C:\Apps\<env-name>\`

Only these locations allow Python scripts and executables to run.

## Where `pip` lives

`pip` lives inside a Python installation.

This means:

- Every Python installation has its own `pip`
- `pip` installs packages relative to that Python’s location

Examples:

- `C:\Apps\Python39\Scripts\pip.exe`
- `C:\Apps\data-env\Scripts\pip.exe`

There is no single system-wide `pip`.

## Why `pip` is often “not recognized”

If you see:

```text
pip is not recognized as an internal or external command
```

That usually means:

- `pip.exe` exists
- Its directory is not on `PATH`

This is normal in locked-down enterprise environments.

## Required way to run `pip`

!!! note
    Always invoke pip through Python in this environment.

```powershell
python -m pip install <package>
```

This guarantees:

- The correct Python interpreter
- The correct install location
- No reliance on `PATH`

This is the supported method in this repository.

## Supported Python install models

### Standalone Python (recommended)

Python installed to:

- `C:\Apps\Python39\`

Benefits:

- Packages installed with pip are executable
- No reliance on user profile paths

Example:

```powershell
C:\Apps\Python39\python.exe -m pip install requests
```

### Conda environments (if Conda is installed)

If Conda is available:

- Create environments under `C:\Apps`
- Activate the environment
- Use pip inside the environment

Example:

```powershell
conda create --prefix C:\Apps\data-env python=3.9
conda activate C:\Apps\data-env
python -m pip install numpy pandas
```

!!! note
    Do not create Conda environments under the user profile.

## IDEs vs Python (critical concept)

IDEs do not install Python packages.

Examples:

- Spyder
- VS Code
- PyCharm

They:

- Point to a Python interpreter
- Use whatever packages are installed in that interpreter

If a package is missing in an IDE, the Python environment is incorrect, not the IDE.

## Common pip commands

### Install a package

```powershell
python -m pip install requests
```

### Install a specific version

```powershell
python -m pip install requests==2.31.0
```

### Upgrade a package

```powershell
python -m pip install --upgrade requests
```

### Uninstall a package

```powershell
python -m pip uninstall requests
```

### List installed packages

```powershell
python -m pip list
```

### Show package details

```powershell
python -m pip show requests
```

## Installing from a wheel (`.whl`)

A `.whl` file (wheel) is a prebuilt Python package.

Rules:

- Wheels must match the Python version
- Wheels must match system architecture
- Wheels must be installed using pip

Example:

```powershell
python -m pip install "C:\Path\To\package-cp39-win_amd64.whl"
```

### Understanding wheel compatibility

Example wheel:

```text
num_dual-0.13.2-cp39-abi3-win_amd64.whl
```

Meaning:

- `cp39` → Python 3.9
- `win_amd64` → 64-bit Windows

!!! warning
    A wheel built for Python 3.9 will not install on Python 3.10 or newer.

## `--user` installs (not allowed)

```powershell
python -m pip install --user requests
```

!!! warning
    Do not use `--user` in this environment.

User-profile installs:

- May appear to install successfully
- Will fail execution
- Commonly cause runtime and PATH issues

## PATH warnings (informational)

You may see warnings such as:

```text
script is installed in '...\Scripts' which is not on PATH
```

This means:

- The package installed successfully
- Optional command-line helpers are not globally accessible
- Python imports and IDE usage still work

## Verify active Python and pip

```powershell
python --version
python -m pip --version
```

Confirm:

- Python path is under `C:\Apps`
- Versions match expectations

## Summary: rules to remember

- `pip` installs into Python, not Windows
- Python must be installed under `C:\Apps`
- User-profile installs are blocked
- Always use `python -m pip`
- Wheel versions must match Python
- IDEs follow the interpreter, not the other way around
