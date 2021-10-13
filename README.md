# Setup poetry-bin action

A Github action for installing and configuring [Poetry](https://python-poetry.org/) Python dependency tool in [binary format](https://github.co/gi0baro/poetry-bin).

## Usage

If all you need is default Poetry, simply add to your workflow:

```yaml
- name: Install Poetry
  uses: gi0baro/setup-poetry-bin@v1
```

In case you want to customise Poetry's config:

```yaml
- name: Install and configure Poetry
  uses: gi0baro/setup-poetry-bin@v1
  with:
    virtualenvs-create: true
    virtualenvs-in-project: false
    virtualenvs-path: ~/my-custom-path
```

### Defaults

The current default settings are:

```yaml
virtualenvs-create: true
virtualenvs-in-project: false
virtualenvs-path: {cache-dir}/virtualenvs
```

## Workflow examples and tips

### Testing

A basic example workflow for running your test-suite can be structured like this.

```yaml
name: test

on: pull_request

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v2
      - name: Set up python
        uses: actions/setup-python@v2
        with:
          python-version: 3.9
      - name: Setup Poetry
        uses: gi0baro/setup-poetry-bin@v1
        with:
          virtualenvs-in-project: true
      - name: Load cached venv
        id: cached-poetry-dependencies
        uses: actions/cache@v2
        with:
          path: .venv
          key: venv-${{ runner.os }}-${{ hashFiles('**/poetry.lock') }}
      - name: Install dependencies
        if: steps.cached-poetry-dependencies.outputs.cache-hit != 'true'
        run: poetry install --no-interaction --no-root
      - name: Install library
        run: poetry install --no-interaction
      - name: Run tests
        run: |
          poetry run pytest tests
          poetry run coverage report
```
