# flowscope-examples

Example GitHub Actions workflows demonstrating the permission patterns that
[flowscope](https://github.com/mshade/flowscope) detects.

## Workflows

| File | Scenario | Flowscope result |
|------|----------|-----------------|
| `write-all.yml` | `permissions: write-all` at workflow level | `HARD_BLOCK` (Rule 1) |
| `implicit-full-access.yml` | `permissions: {}` — equivalent to write-all on GHA | `HARD_BLOCK` (Rule 2) |
| `workflow-level-write-bleed.yml` | Workflow-level write scope with an unscoped job | `HARD_BLOCK` (Rule 3) |
| `agentic-requires-review.yml` | Agentic action with write scope, no observed baseline | `REQUIRES_REVIEW` (Rule 4) |
| `clean-least-privilege.yml` | Every job scoped explicitly; no write scope bleed | Passes |

## Scan this repo

```bash
task scan -- https://github.com/mshade/flowscope-examples
```

Or scan a specific workflow:

```bash
task scan -- https://github.com/mshade/flowscope-examples .github/workflows/write-all.yml
```
