# GitHub Action

Use the action to fail pull requests when MCP server risk findings reach a configured severity threshold.

```yaml
name: mcp-risk-lint
on:
  pull_request:
  push:
    branches: [main]

jobs:
  mcp-risk-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: auraoneai/mcp-risk-linter@v0.1.0
        with:
          path: .
          fail-on: high
          format: markdown
```

For GitHub code scanning, run the CLI with `--format sarif --out mcp-risk.sarif`, then upload the SARIF file with `github/codeql-action/upload-sarif`.
