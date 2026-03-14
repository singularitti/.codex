---
name: uv-python-env
description: Manage uv-based Python environments in a workspace. Use when Python or packages are missing, when a uv venv is absent, or when you need to initialize or sync dependencies with uv and run Python via uv.
---

# UV Python Environment

## Core workflow

- Work from the workspace root.
- If no `pyproject.toml` exists, initialize the project with `uv init .`.
- If `pyproject.toml` exists and dependencies are defined, create or repair the env with `uv sync --no-cache --upgrade`.
- Use `uv add <pkg>` for runtime dependencies and `uv add --dev <pkg>` for dev tools (e.g., `ipython`, `rich`, `see`).

## Activate and run

- Activate the venv with `source /path/to/workspace/.venv/bin/activate`.
- Prefer `uv run python <file.py>` to run scripts.
- If a Python snippet is needed, write it to a real `.py` file first and then run it. Avoid `uv run python -c "<multiline>"`, stdin, or heredoc launches, especially for multiprocessing workloads.
- When writing a helper script, avoid `argparse` CLI scaffolding. Prefer small, local, low-coupling functions that take arguments, and call them explicitly under `if __name__ == "__main__":` without a `main()` function when possible.

## Long-running jobs

- Treat `uv run` tasks that may run longer than a quick validation as batch jobs, not interactive terminal sessions.
- Prefer non-PTY execution for long-running jobs. Do not use a TTY just to watch logs unless interactive input is actually required.
- Before starting a large run, estimate scale from the input size and expected output count. If the run may create a very large number of files or take substantial time, say so explicitly.
- If a long-running command must stay attached for monitoring, use bounded waits and periodic checks rather than leaving an indefinite live session open.
- After the useful work is done, explicitly terminate or close any leftover shell, PTY, or helper processes started for staging, monitoring, or progress checks.
- When possible, prefer progress checks that do not keep extra long-lived terminal sessions alive.

## Troubleshooting

- If Python is not found, activate the venv or run with `uv run`.
- If a package is missing, add it with `uv add` (or `uv add --dev` for dev-only tools), then retry.
