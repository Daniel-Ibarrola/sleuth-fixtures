# sleuth-fixtures

A small Python repo whose GitHub Actions CI is deliberately broken in well-known ways. It exists to give the [`sleuth`](https://github.com/) CLI — a tool for diagnosing CI failures — a reliable supply of failing runs to analyze.

Each failure mode is its own workflow under `.github/workflows/`. Every workflow is initially green on `main`, then broken in a follow-up commit. Re-running a broken workflow produces a fresh failing run ID on demand.

## What's here

- A trivial Python package (`src/sleuth_fixtures/`) and a couple of tests — just enough substrate for the failing workflows to operate on.
- `specs/` — the constitution for this repo: [mission](specs/mission.md), [tech stack](specs/tech-stack.md), [roadmap](specs/roadmap.md).
- `.github/workflows/` — one workflow per failure mode (added in later phases).

## Local development

Requires [`uv`](https://docs.astral.sh/uv/) and Python 3.13.

```sh
uv sync                # create .venv and install dev deps
uv run pytest          # run tests
uv run ruff check      # lint
```
