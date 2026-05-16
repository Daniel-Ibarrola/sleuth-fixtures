# Roadmap

Each phase is small, lands in one or two sittings, and ends with something observable on GitHub. Phases describe outcomes, not implementation choices.

Status values: `todo`, `in progress`, `completed`. Update them as work proceeds — a phase is only `completed` when its exit criteria are met.

## Conventions for failure-mode phases

Every failure-mode phase (Phases 2 onward) follows the same pattern:

1. **Add the workflow in a green state** on `main`. It runs and passes.
2. **Break it in a follow-up commit** in a specific, recognizable way.

This gives `sleuth` both a last-known-green run and a current-broken run to compare against, which Phase 5 of `sleuth`'s own roadmap relies on. After the breaking commit lands, re-running the workflow on demand produces a fresh failing run ID with the same failure shape.

## Phase 0 — Bootstrap the Python project

**Status:** completed

- `pyproject.toml` initialized with `uv`, targeting Python 3.13.
- Dev dependencies: `pytest`, `ruff`.
- A trivial module and a passing test so there is something for CI to run.
- `uv run pytest` passes locally; `uv run ruff check` passes locally.

Exit criteria: a fresh clone can run lint and tests locally with two commands.

## Phase 1 — Baseline CI

**Status:** in progress

A `.github/workflows/ci.yml` workflow runs `ruff check` and `pytest` on every PR and push to `main`, using `uv` to install dependencies. This workflow stays green at all times — it is the canary for the repo's own health, separate from the deliberately broken fixture workflows.

Exit criteria: opening a PR runs the workflow and it passes against the Phase 0 project.

## Phase 2 — Fixture: dependency install failure

**Status:** todo

A workflow that installs dependencies and runs tests. Green initially. Broken by pinning a non-existent or incompatible package version, so `uv sync` (or equivalent) fails before tests run.

Exit criteria: re-running the broken workflow produces a fresh failing run whose log clearly shows the dependency install step failing.

## Phase 3 — Fixture: service container not ready

**Status:** todo

A workflow with a Postgres service container and a test that connects to it. Green initially (proper healthcheck/wait). Broken by removing the readiness wait so the test runs before Postgres is accepting connections.

Exit criteria: re-running the broken workflow produces a fresh failing run with a connection-refused–style error from the test step.

## Phase 4 — Fixture: flaky test

**Status:** todo

A workflow runs a test that fails intermittently. Green initially (deterministic). Broken by introducing nondeterminism (e.g. a random or time-based check) so it fails on most re-runs but not all.

Exit criteria: re-running the broken workflow several times produces a mix of pass and fail outcomes, with at least one failing run on demand.

## Phase 5 — Fixture: test assertion failure

**Status:** todo

A workflow runs a test that asserts something obviously false after the breaking commit. The plainest failure mode — pytest reports an `AssertionError` with a clear diff.

Exit criteria: re-running the broken workflow produces a fresh failing run whose log shows a single failed assertion.

## Phase 6 — Fixture: timeout / hung step

**Status:** todo

A workflow with a job- or step-level timeout. Green initially (step completes quickly). Broken by introducing a hang (`sleep` longer than the timeout, or a process that blocks indefinitely) so GitHub Actions kills the step.

Exit criteria: re-running the broken workflow produces a fresh failing run terminated by the timeout, visible in the log.

## Phase 7 — Fixture: misconfigured environment variable

**Status:** todo

A workflow step reads a required environment variable and fails when it's missing or wrong. Green initially (variable correctly set via `env:` or a secret). Broken by removing it or typoing the name.

Exit criteria: re-running the broken workflow produces a fresh failing run whose log clearly points at the missing/empty env var.

## Later (post-v1, do not start before all fixtures land)

- Additional failure modes if useful: cache corruption, OOM, network flake, permissions error on artifact upload.
- A README that lists every fixture workflow, its failure mode, and how to re-run it.
- A multi-Python or cross-OS matrix on selected fixtures, if `sleuth` grows the ability to reason about matrix axes.
