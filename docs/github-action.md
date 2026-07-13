# GitHub Action

The composite Action scans a checked-out MCP server repository, writes a report,
adds the result to the job summary, and exits nonzero when findings meet the
configured threshold. It does not start the MCP server.

Use immutable commit pins:

```yaml
name: mcp-risk-lint

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read
  pull-requests: write

jobs:
  mcp-risk-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - uses: auraoneai/mcp-risk-linter@a41c7c1ba00461c4b18d7ae3159697ba04eb7bb4 # v0.1.6
        with:
          path: .
          fail-on: high
          format: markdown
          output: mcp-risk-report.md
          comment: "true"
```

`comment: "true"` calls the GitHub API only on `pull_request` events and needs a
token with permission to write pull request comments. Tokens for pull requests
from forks may be read-only, so keep the job summary as the primary evidence.
The Action installs its Python package from the Action checkout; the Action tag
and PyPI package are released as `0.1.6`.

The report path's parent directory must already exist. Markdown reports are
included in the job summary and optional pull request comment. JSON and SARIF
are written to disk, while the summary records that the report was generated.

For GitHub code scanning, run the CLI with `--format sarif --out
mcp-risk.sarif`, then upload that file with the code-scanning workflow approved
for your repository.
