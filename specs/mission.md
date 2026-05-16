# Mission

**sleuth-fixtures** is a small Python repository whose GitHub Actions CI is deliberately broken in well-known, recognizable ways. Its sole purpose is to give the [`sleuth`](../specs/sleuth/mission.md) CLI tool reliable, on-demand CI failures to diagnose.

## What it is

A throwaway Python project — minimal application code, a handful of tests — that exists only to host realistic-looking failing GitHub Actions runs. Each failure mode lives in its own workflow file. Every workflow is initially green; a follow-up commit then breaks it in a specific way. Re-running a broken workflow yields a fresh failing run ID with the same failure shape, on demand.

## Who it's for

- **Primary user:** the `sleuth` tool, pointed at this repo's runs during development.
- **Secondary user:** the author of `sleuth`, who needs a known-failing run to test against without waiting for real CI to break somewhere else.

## Why it exists

`sleuth` analyzes GitHub Actions failures. Developing and demoing it requires a steady supply of failing runs that cover the failure patterns engineers actually see. Real-world failures are rare, unpredictable, and often private. This repo solves that by manufacturing them.

## Scope

Failure modes covered (one workflow per mode):

- Dependency install failure (missing/incompatible package).
- Service container not ready (Postgres unhealthy when tests start).
- Flaky test (intermittent failure).
- Test assertion failure.
- Timeout / hung step.
- Misconfigured environment variable.

The repo stays as simple as possible. If something turns out to be useful — more failure modes, more workflows, more tooling — we add it. Nothing is explicitly off-limits, but nothing is added speculatively.

## Success criteria

- Every failure mode listed above has its own workflow file.
- For each mode, re-running the workflow produces a fresh failing run on demand.
- Pointing `sleuth` at any of these runs gives it a realistic, diagnosable failure.
- A reader can understand what's in this repo, and why, in under five minutes.
