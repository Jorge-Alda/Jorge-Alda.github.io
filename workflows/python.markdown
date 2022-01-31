---
layout: page
title: "Workflow: Python"
permalink: /workflows/python
---

1. TOC
{:toc}

I personally like to split my Python projects in two parts: one for the logic of the
program, in `.py` modules, and other for the inupt and output, data processing and
plotting, in Jupyter notebooks, each on its own repo. Here I cover the first part.

## Work environement

I use VisualStudio Code with the Python extension. At the moment I'm using Python
3.9.5.

## Repo structure

The parent directory only contains configuration files:

* **README.md:** Description of the repo, in markdown format. It can be styled
using [shields](shields.io).
* **.gitignore:** List of files and folders that aren't tracked by git. For python
projects, it typically includes `*.pyc`, `/dist/`, `*.egg-info`, `.env/`,...
* **LICENSE:** Can be created by GitHub. Recommended licenses are GPL3 and MIT.
* **CHANGELOG.md:** Keeps track of the major changes to the project. Instructions
[here](https://keepachangelog.com/en/1.0.0/).
* **pyproject.toml, setup.py and setup.cfg:** Needed to install the project with pip.
* **requirements.txt:** List of all the Python packages required by the project.
* **Dockerfile:** To create a docker container.

The repo contains a folder `test/` for the python tests, a folder `docs/` for the
Sphynx documentation, and finally a folder `myproject/` (i.e. the same as the repo)
for the python code. The folder `myproject/` and each of its subfolders contains a
`__init__.py` file that imports everything else.

## Setup

Python is currently changing the way to prepare packages, so this part is a bit of a
mess. The files [`setup.py`](https://github.com/Jorge-Alda/consoleffects/blob/master/setup.py) and [`pyproject.toml`](https://github.com/Jorge-Alda/consoleffects/blob/master/pyproject.toml) can be as simple as the ones linked.

The meat of the setup is in [`setup.cfg`](https://github.com/Jorge-Alda/consoleffects/blob/master/setup.cfg). Tha data about the package, version,
authors and urls are in the `[metadata]` section. The `packages` in `[options]`
defines in which folders to look for modules. `install_requires` lists the
required packages, with the minimum version.
A more complete example [here](https://gist.github.com/althonos/6914b896789d3f2078d1e6237642c35c)

To generate the installation files (remember to add them to `.gitignore`), run the command

```bash
python -m build
```

This creates two new folders, `dev/` and `project.egg-info`. To check that 
everything works fine, try to install in a virtual environment

```bash
python3 -m venv .env/fresh-install-test
. .env/fresh-install-test/bin/activate
pip install --force dist/myproject-versionnum-py3-none-any.whl
```

If the package is installed correctly, try to run it. Once that everything works,
exit the virtual environment with

```bash
deactivate
```

## Tests and coverage

Tests are a way to check that your code does what is supposed to do. Tests are useful,
for example, to detect an accidental change that breaks something or that is not
backwards-compatible.

I use `pytest`, which is probably the simplest test suite. In the `tests\` folder,
add an empty `__init__.py` file, and one or several `.py` files, with filenames
starting by `test_`, to contain the tests. In each file, write some functions, also
with names starting with `test_`, one for each individual test. The results of the
tests are determined by the `assert` statements: if the condition of the assert
evaluates to `True`, the test passes, and else it fails. One simple example:

```python
def test_passing():
    assert 2+2 == 4

def test_failing():
    assert 2+2 == 5
```

To test if the code raises the error that you expect, the syntax is a bit different:

```python
import pytest

def test_division():
    with pytest.raise(ZeroDivisionError):
        1/0
```

Obviously, you'll need tests that check your code, and not just some mathematical
expressions. Don't forget to `import` the modules containing the code.

To run the tests, in the root directory execute the command

```bash
python -m pytest
```

Alternatively VSCode can detect the tests and run them just by pressing a button.

Coverage is a measure of how many lines of your code are being watched by the tests.
Install the package

```bash
pip install coverage
```

and run the command

```bash
coverage run -m pytest
coverage report -m
```

to see what lines of your code are not covered by the tests.

On top of running the tests locally, you can use GitHub actions in order
to execute them in every push or pull request, with this
[simple example](https://github.com/Jorge-Alda/consoleffects/blob/master/.github/workflows/pytest.yml). For the coverage, you can generate a [Codecov](https://codecov.io/gh/Jorge-Alda/consoleffects) report
(needs a linked account) with [this action](https://github.com/Jorge-Alda/consoleffects/blob/master/.github/workflows/codecov.yml).
