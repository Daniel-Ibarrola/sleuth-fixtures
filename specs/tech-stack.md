# Tech stack

The fewest moving parts that let us produce realistic, intentional CI failures.

## Language and runtime

- **Python 3.13** — single supported version. No multi-version matrix unless a failure mode demands it.

## Tooling

- **uv** — environment, dependency, and lockfile management. `pyproject.toml` + `uv.lock` are the source of truth.
- **pytest** — test runner. Tests are the substrate most failure modes are built on top of.
- **ruff** — lint and format. One tool, one config block, no Black/isort/flake8 stack.

## CI

- **GitHub Actions** — the only CI provider. This is what `sleuth` analyzes; no other CI matters here.
- One workflow per failure mode under `.github/workflows/`.
- A separate baseline workflow runs lint + tests on every PR and on pushes to `main`, so the repo's health is always observable independently of the deliberately broken workflows.

## Services

- **Postgres** (via GitHub Actions service container) — used only by the "service not ready" failure-mode workflow. Not a runtime dependency of the Python code.

## What we are not adding (until something needs it)

- No web framework, ORM, async runtime, or background worker.
- No Docker image of the project itself.
- No release process, tagging, or PyPI publishing.
- No multi-OS or multi-Python matrix.
- No Dependabot/Renovate. Dependency drift is fine — these workflows are meant to break.
