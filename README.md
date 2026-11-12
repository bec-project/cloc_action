# PR line count action

This composite action updates a sticky pull request comment with three rows for source LOC, test LOC, and docs, data, and assets. It compares the PR head with the merge base. cloc reports added, removed, and modified code lines for recognized source and test files. Git reports added and removed text lines for other files. Binary files contribute zero lines.

Add this workflow to the consuming repository's base branch.

```yaml
name: PR line count

on:
  pull_request_target:
    types: [opened, reopened, synchronize, edited]

permissions:
  contents: read
  pull-requests: write

concurrency:
  group: pr-line-count-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  line-count:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: bec-project/cloc_action@v1
        with:
          test-directories: tests,test
```

The action runs on Ubuntu and installs cloc. It checks out the trusted base and reads Git blobs from the PR commit as data. The `synchronize` trigger refreshes the comment after a push to an open PR.

Set `test-directories` to a comma-separated list of paths relative to the repository root. The default is `tests,test`.

Semantic release runs on pushes to `main`. It publishes versioned GitHub releases from Conventional Commits and advances the corresponding major tag, such as `v1`.
