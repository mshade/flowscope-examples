# flowscope-examples

Example GitHub Actions workflows demonstrating the permission patterns that
[flowscope](https://github.com/mshade/flowscope) detects.

The `flowscope-check` workflow runs flowscope against every example on each push.
Bad examples are flagged with violations; the clean example must pass.

## Examples

Files live in `examples/` (not in `.github/workflows/`) so they are not
executed by GitHub Actions — they are illustrative YAML for the scanner to
inspect.

| File | Scenario | Flowscope result |
|------|----------|-----------------|
| `examples/write-all.yml` | `permissions: write-all` at workflow level | `HARD_BLOCK` (Rule 1) |
| `examples/implicit-full-access.yml` | `permissions: {}` — equivalent to write-all on GHA | `HARD_BLOCK` (Rule 2) |
| `examples/workflow-level-write-bleed.yml` | Workflow-level write scope with an unscoped job | `HARD_BLOCK` (Rule 3) |
| `examples/agentic-requires-review.yml` | Agentic action with write scope, no observed baseline | `REQUIRES_REVIEW` (Rule 4) |
| `examples/pull-request-target-poisoning.yml` | `pull_request_target` trigger with write scope (fork-PR-poisoning vector) | `HARD_BLOCK` (Rule 5) |
| `examples/workflow-run-chain.yml` | `workflow_run` trigger with write scope (chained privilege) | `REQUIRES_REVIEW` (Rule 6) |
| `.github/workflows/clean-least-privilege.yml` | Every job scoped explicitly; no write scope bleed | Passes |

## Scan locally

### Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/getting-started/installation/) (recommended) or pip
- [go-task](https://taskfile.dev/installation/) for the `task scan` shortcut

### Setup

Clone both repos:

```bash
git clone https://github.com/mshade/flowscope.git
git clone https://github.com/mshade/flowscope-examples.git
```

Set up the Python environment in the flowscope repo:

```bash
cd flowscope
uv sync --extra dev
```

### Scan via task (recommended)

Run from inside the `flowscope` directory. The `task scan` command fetches
workflow files from GitHub and runs the analyser against them.

```bash
# Scan all examples in this repo
task scan -- https://github.com/mshade/flowscope-examples

# Scan a specific example
task scan -- https://github.com/mshade/flowscope-examples examples/write-all.yml
```

### Scan local files directly

Run flowscope against files you have checked out:

```bash
# From the flowscope directory
uv run flowscope ../flowscope-examples/examples/write-all.yml
uv run flowscope ../flowscope-examples/examples/clean-least-privilege.yml

# Or scan all examples at once
for f in ../flowscope-examples/examples/*.yml; do
  echo "=== $f ==="
  uv run flowscope "$f"
done
```
