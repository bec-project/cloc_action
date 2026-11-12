# PR line count action

This composite action updates a sticky pull request comment. It compares the PR head with the merge base and reports four rows.

- **Source LOC** and **Test LOC** show added, removed, and modified code lines as classified by cloc.
- **Comments and docstrings** shows changes to the lines cloc classifies as comments in source and test files, including Python docstrings.
- **Docs, data, and assets** shows Git's added and removed text lines for other files. Binary files contribute zero lines.

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
          test-file-patterns: 'test_*,*_test.go'
```

The action runs on Ubuntu and installs cloc. It checks out the trusted base and reads Git blobs from the PR commit as data. The `synchronize` trigger refreshes the comment after a push to an open PR.

Set `test-directories` to a comma-separated list of paths relative to the repository root. The default is `tests,test`.

Set `test-file-patterns` to a comma-separated list of filename globs. The default matches `test_*` and `*_test.go` in any directory. For example, add `*.spec.ts` to count TypeScript spec files as test LOC. Set the input to an empty string to use only the directory rules.

Semantic release runs on pushes to `main`. It publishes versioned GitHub releases from Conventional Commits and advances the corresponding major tag, such as `v1`.
