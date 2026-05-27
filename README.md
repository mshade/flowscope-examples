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
| `.github/workflows/clean-least-privilege.yml` | Every job scoped explicitly; no write scope bleed | Passes |

## Scan locally

```bash
# Scan all examples in this repo
task scan -- https://github.com/mshade/flowscope-examples

# Scan a specific example
task scan -- https://github.com/mshade/flowscope-examples examples/write-all.yml
