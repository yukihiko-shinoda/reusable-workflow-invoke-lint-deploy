# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains a single GitHub Actions reusable workflow ([.github/workflows/workflow.yml](.github/workflows/workflow.yml)) that deploys a Python package to PyPI. It is designed to be called from other repositories via `workflow_call`.

## Workflow: `workflow.yml`

**Trigger:** `workflow_call` only — this workflow is never run directly.

**Inputs:**
- `python-version` (string, default `'3.14'`) — the Python version passed to `astral-sh/setup-uv`.

**Secrets:**
- `pypi_password` (required) — PyPI API token passed to `pypa/gh-action-pypi-publish`.

**Steps:**
1. Checkout the caller's repo
2. Set up `uv` with the specified Python version
3. `uv sync` — install dependencies
4. `uv run invoke dist` — build the distribution artifact using [pyinvoke](https://www.pyinvoke.org/) (the caller's repo must define an `invoke` task named `dist`)
5. Publish to PyPI using the official `pypa/gh-action-pypi-publish` action

## Caller-side usage pattern

Caller repos trigger this workflow on a version tag:

```yaml
on:
  push:
    tags:
      - 'v*'

jobs:
  deploy:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-deploy/.github/workflows/workflow.yml@main
    secrets:
      pypi_password: ${{ secrets.PYPI_PASSWORD }}
```

The caller must define an `invoke` task named `dist` that produces a `dist/` directory ready for upload.

## Key design constraints

- The `dist` task is owned by the **caller** repo, not this one. This workflow only orchestrates; it does not define what "building" means.
- Action versions (`actions/checkout@v6`, `astral-sh/setup-uv@v7`, `pypa/gh-action-pypi-publish@release/v1`) are updated by Dependabot (weekly, GitHub Actions ecosystem).
- This repo has no local build/test commands — there is no Python source here, only the workflow YAML.
