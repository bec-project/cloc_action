# PR line count

This reusable GitHub Actions workflow updates a sticky PR comment with source LOC, test LOC, and changes to docs, data, and assets. It compares the PR head with the merge base. For recognized source and test files, cloc counts added, removed, and modified code lines. For other files, Git counts added and removed text lines. Binary assets contribute zero lines.

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
    uses: bec-project/cloc_action/.github/workflows/pr-line-count.yml@main
    with:
      test-directories: tests,test
```

Pin the workflow reference to a commit SHA when adopting it. The counting job has read-only repository access and fetches the PR commit as input data. A separate job updates the comment. The `synchronize` event refreshes the count when someone pushes to the PR branch.

Set `test-directories` to a comma-separated list of paths relative to the consuming repository root. The default is `tests,test`.
