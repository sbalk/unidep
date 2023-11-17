# :rocket: `conda-join` - Unified Conda and Pip Requirements Management :rocket:

[![PyPI](https://img.shields.io/pypi/v/conda_join.svg)](https://pypi.python.org/pypi/conda_join)
[![Build Status](https://github.com/basnijholt/conda_join/actions/workflows/pytest.yml/badge.svg)](https://github.com/basnijholt/conda_join/actions/workflows/pytest.yml)
[![CodeCov](https://codecov.io/gh/basnijholt/conda_join/branch/main/graph/badge.svg)](https://codecov.io/gh/basnijholt/conda_join)

`conda_join` is a Python package designed to streamline the management and combination of multiple `requirements.yaml` files into a single Conda `environment.yaml`, whilest also being able to import the `requirements.yaml` file in `setup.py` where it will add the Python PyPI dependencies to `requires`.
This tool is ideal for projects with multiple subcomponents, each having its own dependencies, where some are only available on conda and some on PyPI (`pip`), simplifying the process of creating a unified Conda environment, while being pip installable with the Python only dependencies. 🖥️🔥

## :books: Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [:page_facing_up: Requirements File Structure](#page_facing_up-requirements-file-structure)
  - [Basic Structure](#basic-structure)
  - [Example](#example)
  - [Explanation](#explanation)
- [:package: Installation](#package-installation)
- [:memo: Usage](#memo-usage)
  - [:wrench: Advanced Configuration](#wrench-advanced-configuration)
  - [:scroll: Output Options](#scroll-output-options)
  - [:question: Help Menu](#question-help-menu)
- [:warning: Limitations](#warning-limitations)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## :page_facing_up: Requirements File Structure

The `requirements.yaml` files that `conda_join` processes should follow a specific structure for the tool to correctly interpret and combine them. Here's an overview of the expected format:

### Basic Structure
Each `requirements.yaml` file should contain the following key elements:

- **name**: (Optional) A name for the environment. This is not used in the combined output but can be helpful for documentation purposes.
- **channels**: A list of channels from which packages will be sourced. Commonly includes channels like `conda-forge`.
- **dependencies**: A list of package dependencies. This can include both Conda and Pip packages.

### Example
Here is an example of a typical `requirements.yaml` file:

```yaml
name: example_environment
channels:
  - conda-forge
dependencies:
  - numpy  # same name on conda and pip
  - pandas  # same name on conda and pip
  - conda: scipy  # different name on conda and pip
    pip: scipy-package
  - pip: package3  # only available on pip
  - conda: mumps  # only available on conda
```

### Explanation
- Dependencies listed as simple strings (e.g., `- numpy`) are assumed to be Conda packages.
- If a package is available through both Conda and Pip but with different names, you can specify both using the `conda: <conda_package>` and `pip: <pip_package>` format.
- Packages only available through Pip should be listed with the `pip:` prefix.

`conda_join` will combine these dependencies into a single `environment.yaml` file, structured as follows:

```yaml
name: some_name
channels:
  - conda-forge
dependencies:
  - numpy
  - pandas
  - scipy
  pip:
    - scipy-package
    - package3
```



## :package: Installation

To install `conda_join`, run the following command:

```bash
pip install -U conda_join
```

Or just copy the script to your computer:
```bash
wget https://raw.githubusercontent.com/basnijholt/requirements.yaml/main/conda_join.py
```

## :memo: Usage

After installation, you can use `conda_join` to scan directories for `requirements.yaml` files and combine them into an `environment.yaml` file. Basic usage is as follows:

```bash
conda_join -d [DIRECTORY] --depth [DEPTH] -o [OUTPUT_FILE]
```

- `-d` or `--directory`: Specify the base directory to scan (default is current directory).
- `--depth`: Specify the depth for scanning subdirectories (default is 1).
- `-o` or `--output`: Specify the output file for the combined environment (default is `environment.yaml`).

### :wrench: Advanced Configuration

`conda_join` allows advanced configurations such as verbose output and printing to `stdout` instead of a file.

- To enable verbose output, use the `-v` or `--verbose` flag.
- To print the combined environment to `stdout` instead of saving to a file, use the `--stdout` flag.

Example with advanced options:

```bash
conda_join -d src --depth 2 -o dev_environment.yaml --verbose
```

### :scroll: Output Options

- The output `environment.yaml` file will contain a unified list of dependencies from all scanned `requirements.yaml` files.
- If the `--stdout` flag is used, the combined environment will be printed to the console.

### :question: Help Menu

For more options, use:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- conda-join -h -->
<!-- echo '```' -->
<!-- CODE:END -->
<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
```

<!-- OUTPUT:END -->


## :warning: Limitations

- The current version of `conda_join` does not support conflict resolution between different versions of the same package in multiple `requirements.yaml` files.
- Designed primarily for use with Conda environments; may not fully support other package management systems.

* * *

Try `conda_join` today for a streamlined approach to managing your Conda environment dependencies across multiple projects! 🎉👏
