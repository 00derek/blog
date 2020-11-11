---
title: "Dev Env Setup"
date: 2020-09-09T11:35:41-07:00
draft: false
---

# Unix Shell/Terminal/REPL/IDE

- zsh/oh-my-zsh [https://ohmyz.sh/](https://ohmyz.sh/)
- iterm2
- ipython/bpython
    - jupyter notebook?
- PyCharm/VSCODE

# Python environment management

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
        - pyenv-which-ext: Plugin to automatically lookup system commands
        - pyenv-virtualenv: Plugin for pyenv and virtual environments
        - pyenv-update: Plugin for updating pyenv

# Dependency management tools

- pip install -r requirements.txt
- Pipenv [https://pipenv.pypa.io/en/latest/](https://pipenv.pypa.io/en/latest/)
    - Pipfile and Pipfile.lock
- Poetry [https://python-poetry.org/](https://python-poetry.org/)
    - pyproject.toml and poetry.lock

# Working on data management repo

**There should be one-- and preferably only one --obvious way to do things**

- [https://github.com/Exabeam/docker-exabeam-dl-data-management](https://github.com/Exabeam/docker-exabeam-dl-data-management)
    - linters: flake8 and black
        - Codes are read much longer time than being written
        - This style guide exists for consistency
    - pytest
- pre-commit — [https://pre-commit.com/](https://pre-commit.com/)

```jsx
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
    -   id: check-merge-conflict
    -   id: debug-statements
- repo: https://github.com/miki725/importanize/
    rev: 'master'
    hooks:
    - id: importanize
      args: [--verbose]
-   repo: https://github.com/psf/black
    rev: 19.3b0
    hooks:
    -   id: black
-   repo: https://gitlab.com/pycqa/flake8
    rev: master
    hooks:
    -   id: flake8
```

- post-commit — adding versionbump

```jsx
#!/bin/sh
if [ ! -e versionbump ]
then
    echo "patch" > versionbump
    git add versionbump
    git commit --amend --no-edit --no-verify
```