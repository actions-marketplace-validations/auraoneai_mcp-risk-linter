# mcp-risk-linter

`mcp-risk-linter` statically scans a Model Context Protocol server repository
for capability, permission, secret-handling, and disclosure risks before human
review or installation.

## At a Glance

| | |
| --- | --- |
| Job | Find review-worthy MCP tool and repository risks with deterministic local rules. |
| Built for | MCP maintainers, agent platform teams, security reviewers, and CI owners. |
| Differentiator | Scans source, JSON metadata, and docs without starting the MCP server. |
| Produces | Discovered tool inventory plus Markdown, JSON, or SARIF findings with remediation. |

## Install

```bash
python -m pip install "mcp-risk-linter==0.1.6"
```

## Verified Quickstart

Run from a source checkout:

```bash
mcp-risk-linter scan examples/safe_server \
  --format json \
  --out /tmp/mcp-risk-report.json \
  --fail-on high
```

The bundled safe fixture discovers two tools and returns no findings. The
`risky_stdio_server` and `broad_filesystem_server` fixtures intentionally
exercise findings and can return exit code `1` at the default `high` threshold.

## What It Checks

- Python, JavaScript, and TypeScript source patterns for shell execution, broad
  filesystem access, outbound network calls, and secret-like environment use;
- JSON and source patterns that expose tool names and descriptions;
- vague tool descriptions and mutating tools without side-effect language;
- missing security documentation and missing authentication or permission
  boundary language;
- justified inline suppressions using
  `mcp-risk-linter: ignore RULE -- justification`.

The rules are regex and metadata heuristics. A finding is a review prompt, not
proof of a vulnerability or exploit.

## GitHub Action

The published Action should be pinned to its immutable release commit:

```yaml
- uses: auraoneai/mcp-risk-linter@42a7ef839a7b2bd111fd83f70c6657e831ae0ee6 # v0.1.6
  with:
    path: .
    fail-on: high
    format: markdown
    output: mcp-risk-report.md
```

See [`docs/github-action.md`](docs/github-action.md) for the complete workflow,
permissions, job-summary behavior, and optional pull request comments.

## Runtime, Data, and Network Boundary

- The CLI recursively reads local source, JSON, and documentation files while
  skipping common VCS, virtual environment, dependency, build, and cache
  directories.
- The scanner does not execute server code or make network requests.
- Reports include repository paths, source line numbers, snippets, tool names,
  and descriptions. Review reports before posting them outside the repository.
- The composite Action may contact the configured Python package index while
  installing build requirements. With `comment: "true"`, it also calls the
  GitHub API to create or update a pull request comment.

## Limitations

- Findings come from static rules and text heuristics. They are review prompts,
  not proof of exploitability, vulnerability, or compliance failure.
- The scanner cannot see runtime-only permissions, dynamic dispatch, or network
  behavior that is not inspectable in checked-in source and docs.

## Compatibility

The published `auraone-agent-studio-open` CLI declares
`mcp-risk-linter>=0.1.1` as a runtime dependency and exposes the same scanner
through `agentstudio risk-scan`.

This project is not affiliated with Anthropic, the Model Context Protocol
project, OpenAI, or a registry operator. It is not a penetration test, CVE
scanner, exploit detector, or official compliance program.

## Publication Status

Verified on 2026-07-13:

- PyPI: [`mcp-risk-linter==0.1.6`](https://pypi.org/project/mcp-risk-linter/0.1.6/)
- GitHub Action release: [`v0.1.6`](https://github.com/auraoneai/mcp-risk-linter/releases/tag/v0.1.6)
- The Action and PyPI package share release `0.1.6`; the Action installs the
  package source from its checked-out immutable commit.
- No external adoption or security-assurance claim is made.

## Next Action

Scan one MCP server before installation, review every high-severity finding with
the maintainer, and add only narrowly justified suppressions.
