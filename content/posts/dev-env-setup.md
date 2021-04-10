---
title: "My Python Dev Env setup"
date: 2020-11-09T11:35:41-07:00
draft: false
toc: true
tags:
- notes
- python
---
Making a note to jot down the tools I preferred to use when setting up Python development environment -- **there should be one-- and preferably only one --obvious way to do things**

## Terminal/REPL/IDE

- zsh/oh-my-zsh [https://ohmyz.sh/](https://ohmyz.sh/)
  - plugins=(git history-substring-search zsh-autosuggestions)
  - theme: [power10k](https://github.com/romkatv/powerlevel10k)
  - [Cheatsheet](https://github.com/ohmyzsh/ohmyzsh/wiki/Cheatsheet)
- iTerm2
- iPython/bpython
- VsCode

## Python environment management

- Managing virtualenv — virtualenv/virtualenvwrapper or venv manages virtual environments for a specific Python version.
- Managing multiple Python versions With pyenv
    - Installation

        ```bash
        curl https://pyenv.run | bash
        or brew install pyenv

        export PATH="$HOME/.pyenv/bin:$PATH"
        eval "$(pyenv init -)"
        eval "$(pyenv virtualenv-init -)"
        ```

    1. Install multiple versions of Python
    2. Install the latest development version of Python
    3. Switch between the installed versions
    4. Use virtual environments with `pyenv`

        ```bash
        pyenv virtualenv <python_version> <environment_name>
        ```

    5. Activate different Python versions and virtual environments automatically
    6. some useful plugins
        - `pyenv-which-ext`: Plugin to automatically lookup system commands
        - `pyenv-virtualenv`: Plugin for pyenv and virtual environments
        - `pyenv-update`: Plugin for updating pyenv

## Dependency management tools
- Preferred: [Poetry](https://python-poetry.org/)
    - pyproject.toml and poetry.lock
- pip install -r requirements.txt
- [Pipenv](https://pipenv.pypa.io/en/latest/)
    - Pipfile and Pipfile.lock

## Linting tools
- Codes are read much longer time than being written       
- [pre-commit](https://pre-commit.com/)

```jsx
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v3.4.0
    hooks:
    - id: check-yaml
    - id: check-byte-order-marker
    - id: end-of-file-fixer
    - id: trailing-whitespace
    - id: check-merge-conflict
    - id: debug-statements
    - id: name-tests-test
    - id: check-builtin-literals
    - id: no-commit-to-branch
      args: [--branch, staging, --branch, master]
-   repo: https://github.com/pycqa/isort
    rev: 5.8.0
    hooks:
      - id: isort
        args: ["--profile", "black"]
-   repo: https://github.com/psf/black
    rev: 20.8b1
    hooks:
    - id: black
-   repo: https://gitlab.com/pycqa/flake8
    rev: 3.9.0
    hooks:
    - id: flake8
```
## Other interesting tools
### icdiff and pytest-icdiff
- icdiff: “Improved colored diff”
  ```
  pip install git+https://github.com/jeffkaufman/icdiff.git
  git icdiff
  git config --global icdiff.options '--highlight --line-numbers'
  ```
