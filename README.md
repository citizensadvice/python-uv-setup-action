# Python & UV Setup Action

A Github Action that will:

- Enable the magic cache for `runs-on` runners
- Setup Python using the version specified in the `pyproject.toml` file
- Setup uv package manager with caching enabled. The version of uv is [automatically detected](https://docs.astral.sh/uv/reference/settings/#required-version) from the `pyproject.toml` file
- Optionally set up Node.js and install the AWS CDK
- Install project dependencies using `uv sync --locked`
- Print the installed packages version tree

> [!NOTE]  
> - This action will **not** perform a fetch of your project. This must be performed before this action is run
> - The Python version is automatically detected from your `pyproject.toml` file
> - Dependencies are installed using `uv sync --locked` which ensures reproducible builds

## Cache

This action will cache uv dependencies for use with later workflow runs and use the `runs-on` magic cache if the workflow is executed on a `runs-on` runner. The cache is automatically enabled and uses uv's built-in caching mechanism.

## Inputs

| Input                      | Description                                                                         | Required | Default            |
| -------------------------- | ----------------------------------------------------------------------------------- | -------- | ------------------ |
| `project_root`             | The location of the python project root                                             | No       | `github.workspace` |
| `install_cdk`              | Install the AWS CDK                                                                 | No       | `false`            |
| `node_version`             | The version of Node.js to install                                                   | No       | `20`               |
| `cdk_version`              | The version of the AWS CDK to install                                               | No       | `latest`           |
| `python_version`           | The version of Python to install. Defaults to latest compatable with pyproject.toml | No       | ``                 |
| `report_outdated_packages` | Print outdated python packages to the step summary                                  | No       | `false`            |

## Example

```yaml
jobs:
  test:
    name: tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: citizensadvice/python-uv-setup-action@v1

      - name: Run tests
        run: uv run pytest
```

## Example with CDK

```yaml
jobs:
  deploy:
    name: deploy
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: citizensadvice/python-uv-setup-action@v1
        with:
          install_cdk: true

      - name: Deploy with CDK
        run: cdk deploy
```

## Action Versioning

The `v1` tag will be maintained and updated until there is an interface change.
