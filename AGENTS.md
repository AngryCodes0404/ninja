# AGENTS.md

## Cursor Cloud specific instructions

### Project overview

This is `ninja`, the miner-facing starter harness for Bittensor Subnet 66 (SN66). It is a single-file Python agent (`agent.py`) with no third-party runtime dependencies. The validator imports `agent.py` and calls `solve()`.

### Development commands

All commands should be run from the repo root (`/workspace`).

| Task | Command |
|---|---|
| Syntax check | `python3 -m py_compile agent.py` |
| Lint (pyflakes) | `python3 -m pyflakes agent.py` |
| Import check | `python3 -c "from agent import solve; print('OK')"` |
| Test suite | `python3 -m pytest -q .github/tests/test_agent_pr_smoke.py` |
| Preflight (no judge) | `./scripts/precommit_ninja_pr.py --hotkey <SS58>` |
| Preflight (with judge) | `./scripts/precommit_ninja_pr.py --hotkey <SS58> --judge` (needs `OPENROUTER_API_KEY`) |

### Key caveats

- **Test dependencies**: `pytest` and `pyflakes` are installed via `pip install pytest pyflakes`. They are not listed in any requirements file — the update script handles this.
- **Known test baseline drift**: The pyflakes smoke test (`test_agent_py_has_no_pyflakes_findings`) compares findings against `KNOWN_BASELINE_FINDINGS` which includes a hardcoded line number (line 2833). If `agent.py` changes shift the `_wall_start` variable to a different line, this test will fail with a regression even though it is the same known warning. The baseline in `.github/tests/test_agent_pr_smoke.py` may need updating when `agent.py` is edited.
- **No dev server**: There is no application server to start. The agent is a library imported and invoked by an external validator.
- **No third-party deps in agent.py**: The agent must only use Python stdlib. Test deps (`pytest`, `pyflakes`) are dev-only.
- **Miner PRs may only edit `agent.py`**: The CI scope guard rejects PRs that touch other files.
- **Preflight with `--judge`** requires `OPENROUTER_API_KEY` env var; without it, skip the `--judge` flag.
