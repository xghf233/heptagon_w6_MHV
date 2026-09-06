# Local AI Sandbox Rules

## Scope

This repository is `heptagon_w6_MHV`, a publication copy of the Symbol edition
originally developed in `HZQ-git`. It contains `nanoinfra-main_symbol/` only.
The original `HZQ-git` is separate; do not modify or synchronize it implicitly.
AI agents may
inspect and edit source code, tests, configuration, and documentation here. The
AutoDL copy is the execution environment for every smoke test, unit test, training
run, and GPU check.

## Local rules

- Treat this Git repository as the source of truth for code.
- Before editing, inspect `git status` and preserve unrelated user changes.
- Make code and documentation changes locally; review `git diff` after editing.
- Do not run Python tests, smoke tests, training, benchmarks, or model inference on
  the Mac unless the user explicitly changes this rule for a specific command.
- Do not create, rebuild, activate, or install packages into `.venv` unless the
  user explicitly requests local execution support. The existing local `.venv` is
  optional and is not part of the repository.
- Do not download datasets, model weights, checkpoints, or large dependencies on
  the Mac.
- Never place API keys, passwords, SSH details, tokens, datasets, checkpoints, or
  logs in Git.
- Do not push, force-push, reset, delete branches, or rewrite history without the
  user's explicit confirmation.

## Remote execution rules

- Remote: `https://github.com/xghf233/heptagon_w6_MHV.git`.
  The server checkout and environment paths have not yet been confirmed.
- Run project module commands from `nanoinfra-main_symbol/` beneath that checkout.
  Preserve this directory depth; do not mix checkouts in the same `PYTHONPATH`.
- Move code between local and remote through Git after the user reviews the diff.
  Do not use `rsync --delete` and do not copy `.venv`, data, checkpoints, outputs,
  or logs between machines as part of code synchronization.
- Before a remote update, verify that the remote worktree is clean. If it contains
  uncommitted changes or divergent commits, stop and report them instead of
  overwriting anything.
- Before any GPU command, dependency installation, data download, or paid/long
  operation, explain the command, reads/writes, expected cost, and expected output,
  then wait for explicit user confirmation.
- Run the smallest relevant remote verification first. A smoke test is still a
  remote compute action and is not automatic.

## Project discipline

- Keep experiment code under the selected edition's `projects/`; avoid changing
  reusable `core/` or `modalities/` unless the change is genuinely framework-level.
- The standard edition is not included. Keep heptagon changes scoped to its
  project; retain amplitude_symbol because heptagon imports its attention tools.
- Keep scientific claims separate from observations. Record split strategy,
  seeds, model size, data size, commands, and evaluation protocol.
- Do not treat random-split and orbit-grouped results as directly comparable.
- Generated datasets, checkpoints, and run artifacts stay outside Git; only small,
  reviewed reports, summary JSON, and figures may be committed. The sibling local
  `nanoinfra-artifacts/` store is not part of this repository.

See `LOCAL_REMOTE_WORKFLOW.md` for the operational workflow.
