# reusable-workflow-invoke-lint-deploy

[![Dependabot](https://flat.badgen.net/github/dependabot/yukihiko-shinoda/reusable-workflow-invoke-lint-deploy?icon=dependabot)](https://github.com/yukihiko-shinoda/reusable-workflow-invoke-lint-deploy/security/dependabot)

The deployment workflow that can be reused powered by [`Invoke Lint`].

## Advantage

Deploying a Python package to PyPI requires the same boilerplate in every project: checkout, Python setup, build, publish. This workflow centralises that sequence so callers inherit action-version bumps (via Dependabot on this repo) without touching their own workflow files.

It is designed for projects that use [pyinvoke](https://www.pyinvoke.org/) for build tasks and [uv](https://github.com/astral-sh/uv) for dependency management — the same toolchain used by [`Invoke Lint`].

## Quickstart

In a caller repo, create a workflow that triggers on a version tag and delegates to this one:

**Semantic versioning** (`v1.2.3`):

```yaml
on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+'

permissions:
  contents: read

jobs:
  deploy:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-deploy/.github/workflows/workflow.yml@v1
    secrets:
      pypi_password: ${{ secrets.PYPI_PASSWORD }}
```

**Timestamp versioning** (`v20260613120000`):

```yaml
on:
  push:
    tags:
      - 'v[0-9][0-9][0-9][0-9][01][0-9][0-3][0-9][0-2][0-9][0-5][0-9][0-5][0-9]'

permissions:
  contents: read

jobs:
  deploy:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-deploy/.github/workflows/workflow.yml@v1
    secrets:
      pypi_password: ${{ secrets.PYPI_PASSWORD }}
```

The caller repo must define an `invoke` task named `dist` that produces a `dist/` directory ready for upload.

## API

### Inputs

| Name              | Type   | Default    | Description                                    |
| ----------------- | ------ | ---------- | ---------------------------------------------- |
| `python-version`  | string | `'3.14'`   | Python version passed to `astral-sh/setup-uv`  |

### Secrets

| Name              | Required | Description                                              |
| ----------------- | -------- | -------------------------------------------------------- |
| `pypi_password`   | yes      | PyPI API token passed to `pypa/gh-action-pypi-publish`   |

[`Invoke Lint`]: https://pypi.org/project/invokelint/