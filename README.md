# :rocket: UniDep - Unified Conda and Pip Dependency Management :rocket:

![](https://private-user-images.githubusercontent.com/6897215/285626739-a5a9bd12-0a93-40d4-9a86-71d854811fce.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTEiLCJleHAiOjE3MDA5Njg3NDgsIm5iZiI6MTcwMDk2ODQ0OCwicGF0aCI6Ii82ODk3MjE1LzI4NTYyNjczOS1hNWE5YmQxMi0wYTkzLTQwZDQtOWE4Ni03MWQ4NTQ4MTFmY2UucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQUlXTkpZQVg0Q1NWRUg1M0ElMkYyMDIzMTEyNiUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyMzExMjZUMDMxNDA4WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9NzA3OWYwYWJhZGMwYzg2NWFlM2ZkZWY1MzQ2MGI2MGQ4MTZkMmM0NTY2OThhOWExNjRkOTQ1OGM4YTAwZWQ3ZiZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QmYWN0b3JfaWQ9MCZrZXlfaWQ9MCZyZXBvX2lkPTAifQ.kQgFzvVYXIrMnyiFUufFGalLgNp4THUf1WemFW3-EMI)

[![PyPI](https://img.shields.io/pypi/v/unidep.svg)](https://pypi.python.org/pypi/unidep)
[![Build Status](https://github.com/basnijholt/unidep/actions/workflows/pytest.yml/badge.svg)](https://github.com/basnijholt/unidep/actions/workflows/pytest.yml)
[![CodeCov](https://codecov.io/gh/basnijholt/unidep/branch/main/graph/badge.svg)](https://codecov.io/gh/basnijholt/unidep)

`unidep` simplifies Python project dependency management by enabling a single `requirements.yaml` file to handle both Conda and Pip dependencies.
This approach allows for creating a unified Conda `environment.yaml`, while also integrating with `setup.py` or `pyproject.toml`.
In addition, it can be used as a CLI to combine multiple `requirements.yaml` files into a single `environment.yaml` file.
Simplify your setup and maintain all your dependencies in one place with `unidep`.

## :rocket: Features

- **🔗 Unified Management**: Single-file handling of Conda and Pip dependencies.
- **⚙️ Project Tool Integration**: Easily works with `pyproject.toml` and `setup.py`, so `requirements.yaml` is used during `pip install`.
- **🏢 Monorepo Support**: Merge multiple `requirements.yaml` into one Conda environment `environment.yaml` using the CLI tool.
- **🌍 Platform-Specific Support**: Tailors dependencies for different operating systems or architectures.
- **🛠️ Conflict Resolution**: Simplifies complex dependency management by resolving version conflicts.

## :books: Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [:package: Installation](#package-installation)
- [:page_facing_up: `requirements.yaml` structure](#page_facing_up-requirementsyaml-structure)
  - [Example](#example)
  - [Key Points](#key-points)
  - [Platform Selectors](#platform-selectors)
    - [Supported Selectors](#supported-selectors)
    - [Usage](#usage)
    - [Implementation](#implementation)
  - [Conflict Resolution](#conflict-resolution)
    - [How It Works](#how-it-works)
- [:memo: Usage](#memo-usage)
  - [With `pyproject.toml` or `setup.py`](#with-pyprojecttoml-or-setuppy)
  - [:memo: As a CLI](#memo-as-a-cli)
    - [`unidep merge`](#unidep-merge)
    - [`unidep install`](#unidep-install)
    - [`unidep pip`](#unidep-pip)
    - [`unidep conda`](#unidep-conda)
- [Limitations](#limitations)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## :package: Installation

To install `unidep`, run the following command:

```bash
pip install -U unidep
```

Or just copy the script to your project:
```bash
wget https://raw.githubusercontent.com/basnijholt/unidep/main/unidep.py
```

## :page_facing_up: `requirements.yaml` structure

`unidep` processes `requirements.yaml` files with a specific format (similar but _**not**_ the same as a Conda `environment.yaml` file):

- **name** (Optional): For documentation, not used in the output.
- **channels**: List of sources for packages, such as `conda-forge`.
- **dependencies**: Mix of Conda and Pip packages.

### Example

Example of a `requirements.yaml` file:

```yaml
name: example_environment
channels:
  - conda-forge
dependencies:
  - numpy  # same name on conda and pip
  - conda: python-graphviz  # When names differ between Conda and Pip
    pip: graphviz
  - pip: slurm-usage  # pip-only
  - conda: mumps  # conda-only
  # Use platform selectors; below only on linux64
  - conda: cuda-toolkit  # [linux64]
```

**⚠️ `unidep` can process this file in `pyproject.toml` or `setup.py` and create a `environment.yaml` file.**

### Key Points

- Standard names (e.g., `- numpy`) are assumed to be the same for Conda and Pip.
- Use `conda: <package>` and `pip: <package>` to specify different names across platforms.
- Use `pip:` to specify packages that are only available through Pip.
- Use `conda:` to specify packages that are only available through Conda.

Using the CLI `unidep` will combine these dependencies into a single conda installable `environment.yaml` file.

### Platform Selectors

This tool supports a range of platform selectors that allow for specific handling of dependencies based on the user's operating system and architecture. This feature is particularly useful for managing conditional dependencies in diverse environments.

#### Supported Selectors

The following selectors are supported:

- `linux`: For all Linux-based systems.
- `linux64`: Specifically for 64-bit Linux systems.
- `aarch64`: For Linux systems on ARM64 architectures.
- `ppc64le`: For Linux on PowerPC 64-bit Little Endian architectures.
- `osx`: For all macOS systems.
- `osx64`: Specifically for 64-bit macOS systems.
- `arm64`: For macOS systems on ARM64 architectures (Apple Silicon).
- `macos`: An alternative to `osx` for macOS systems.
- `unix`: A general selector for all UNIX-like systems (includes Linux and macOS).
- `win`: For all Windows systems.
- `win64`: Specifically for 64-bit Windows systems.

#### Usage

Selectors are used in `requirements.yaml` files to conditionally include dependencies based on the platform:

```yaml
dependencies:
  - some-package  # [unix]
  - another-package  # [win]
  - special-package  # [osx64]
  - pip: cirq  # [macos]
    conda: cirq  # [linux]
```

In this example:
- `some-package` is included only in UNIX-like environments (Linux and macOS).
- `another-package` is specific to Windows.
- `special-package` is included only for 64-bit macOS systems.
- `cirq` is managed by `pip` on macOS and by `conda` on Linux. This demonstrates how you can specify different package managers for the same package based on the platform.

#### Implementation

The tool parses these selectors and filters dependencies according to the platform where it's being run.
This is particularly useful for creating environment files that are portable across different platforms, ensuring that each environment has the appropriate dependencies installed.

### Conflict Resolution

`unidep` features a conflict resolution mechanism to manage version conflicts and platform-specific dependencies in `requirements.yaml` files. This functionality ensures optimal package version selection based on specified requirements.

#### How It Works

- **Version Pinning Priority**: `unidep` gives priority to version-pinned packages when multiple versions of the same package are specified. For instance, if both `foo` and `foo <1` are listed, `foo <1` is selected due to its specific version pin.

- **Minimal Scope Selection**: `unidep` resolves platform-specific dependency conflicts by preferring the version with the most limited platform scope. For instance, given `foo <1 # [linux64]` and `foo >1`, it installs `foo <1` exclusively on Linux-64 and `foo >1` on all other platforms. This approach ensures platform-specific requirements are precisely met.

- **Resolving Intractable Conflicts**: When conflicts are irreconcilable (e.g., `foo >1` vs. `foo <1`), `unidep` issues a warning and defaults to the first encountered specification.

## :memo: Usage

### With `pyproject.toml` or `setup.py`

To use `unidep` in your project, you can configure it in `pyproject.toml`. This setup works alongside a `requirements.yaml` file located in the same directory. The behavior depends on your project's setup:

- **When using only `pyproject.toml`**: The `dependencies` field in `pyproject.toml` will be automatically populated based on the contents of `requirements.yaml`.
- **When using `setup.py`**: The `install_requires` field in `setup.py` will be automatically populated, reflecting the dependencies defined in `requirements.yaml`.

Here's an example `pyproject.toml` configuration:

```toml
[build-system]
build-backend = "setuptools.build_meta"
requires = ["setuptools", "wheel", "unidep"]

[project]
dynamic = ["dependencies"]
```

In this configuration, `unidep` is included as a build requirement, allowing it to process the Python dependencies in the `requirements.yaml` file and update the project's dependencies accordingly.

### :memo: As a CLI

See [example](example/) for more information or check the output of `unidep -h` for the available sub commands:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- unidep -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: unidep [-h] {merge,pip,conda,install} ...

Unified Conda and Pip requirements management.

positional arguments:
  {merge,pip,conda,install}
                        Subcommands
    merge               Merge requirements to conda installable
                        environment.yaml
    pip                 Get the pip requirements for the current platform
                        only.
    conda               Get the conda requirements for the current platform
                        only.
    install             Install the dependencies of a single
                        `requirements.yaml` file in the currently activated
                        conda environment with conda, then install the
                        remaining dependencies with pip, and finally install
                        the current package with `pip install [-e] .`.

options:
  -h, --help            show this help message and exit
```

<!-- OUTPUT:END -->

#### `unidep merge`

Use `unidep merge` to scan directories for `requirements.yaml` file(s) and combine them into an `environment.yaml` file.
See `unidep merge -h` for more information:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- unidep merge -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: unidep merge [-h] [-d DIRECTORY] [-o OUTPUT] [-n NAME] [--depth DEPTH]
                    [--stdout] [-v]

options:
  -h, --help            show this help message and exit
  -d DIRECTORY, --directory DIRECTORY
                        Base directory to scan for requirements.yaml files, by
                        default `.`
  -o OUTPUT, --output OUTPUT
                        Output file for the conda environment, by default
                        `environment.yaml`
  -n NAME, --name NAME  Name of the conda environment, by default `myenv`
  --depth DEPTH         Depth to scan for requirements.yaml files, by default
                        1
  --stdout              Output to stdout instead of a file
  -v, --verbose         Print verbose output
```

<!-- OUTPUT:END -->

#### `unidep install`

Use `unidep install` on a `requirements.yaml` file and install the dependencies on the current platform using conda, then install the remaining dependencies with pip, and finally install the current package with `pip install [-e] .`.
See `unidep install -h` for more information:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- unidep install -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: unidep install [-h] [-f FILE] [-v] [-e]
                      [--conda_executable {conda,mamba,micromamba}]
                      [--dry-run]

options:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  The requirements.yaml file to parse, by default
                        `requirements.yaml`
  -v, --verbose         Print verbose output
  -e, --editable        Install the project in editable mode
  --conda_executable {conda,mamba,micromamba}
                        The conda executable to use
  --dry-run             Only print the commands that would be run
```

<!-- OUTPUT:END -->

#### `unidep pip`

Use `unidep pip` on a `requirements.yaml` file and output the pip installable dependencies on the current platform (default).
See `unidep pip -h` for more information:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- unidep pip -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: unidep pip [-h] [-f FILE] [-v]
                  [--platform {linux-64,linux-aarch64,linux-ppc64le,osx-64,osx-arm64,win-64}]
                  [--separator SEPARATOR]

options:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  The requirements.yaml file to parse, by default
                        `requirements.yaml`
  -v, --verbose         Print verbose output
  --platform {linux-64,linux-aarch64,linux-ppc64le,osx-64,osx-arm64,win-64}
                        The platform to get the requirements for, by default
                        the current platform (`linux-64`)
  --separator SEPARATOR
                        The separator between the dependencies, by default ` `
```

<!-- OUTPUT:END -->

#### `unidep conda`

Use `unidep conda` on a `requirements.yaml` file and output the conda installable dependencies on the current platform (default).
See `unidep conda -h` for more information:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- unidep conda -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: unidep conda [-h] [-f FILE] [-v]
                    [--platform {linux-64,linux-aarch64,linux-ppc64le,osx-64,osx-arm64,win-64}]
                    [--separator SEPARATOR]

options:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  The requirements.yaml file to parse, by default
                        `requirements.yaml`
  -v, --verbose         Print verbose output
  --platform {linux-64,linux-aarch64,linux-ppc64le,osx-64,osx-arm64,win-64}
                        The platform to get the requirements for, by default
                        the current platform (`linux-64`)
  --separator SEPARATOR
                        The separator between the dependencies, by default ` `
```

<!-- OUTPUT:END -->

## Limitations
- **Conda-Focused**: Best suited for Conda environments.

* * *

Try `unidep` today for a streamlined approach to managing your Conda environment dependencies across multiple projects! 🎉👏
