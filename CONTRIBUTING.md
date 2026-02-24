# Contributing to Fowlcon

Thank you for your interest in contributing to Fowlcon! This project is an agentic code review tool -- most of the "code" is markdown prompts and shell scripts, not a traditional application.

## Getting Started

### Prerequisites

- A CLI that supports agent commands (Claude Code, Amp, Cursor, or similar)
- `bash` (for shell scripts)
- `bats-core` (for running shell script tests): `brew install bats-core` or see [bats-core installation](https://bats-core.readthedocs.io/en/stable/installation.html)
- `gh` CLI (for testing against real PRs): `brew install gh`

### Installation

```bash
git clone https://github.com/block/fowlcon.git
cd fowlcon
./script/install
```

### Running Tests

```bash
# Run all shell script tests
bats tests/scripts/

# Run a specific test file
bats tests/scripts/test-update-node-status.bats
```

## How to Contribute

### Reporting Issues

Use the [bug report template](.github/ISSUE_TEMPLATE/bug-report.md) for bugs. For feature requests or design discussions, open a regular issue.

### Submitting Changes

1. Fork the repository
2. Create a feature branch from `main`
3. Make your changes following the conventions in [AGENTS.md](./AGENTS.md)
4. For shell scripts: write tests first (TDD with bats-core)
5. For agent prompts: test against a real PR and document results
6. Every change should: verify (tests pass), document (update relevant docs), commit
7. Open a PR with a clear description of what and why

### What Makes a Good PR

- **Small and focused.** One logical change per PR. Easy to review (we eat our own cooking here).
- **Tested.** Shell scripts have bats tests. Prompt changes are tested against real PRs with results documented.
- **Documented.** If you add a file, explain its purpose. If you change behavior, update AGENTS.md or README.md.
- **Principled.** Read the 10 core principles in README.md. Your change should align with them.

### Areas Where Help is Welcome

- Shell script improvements (reliability, edge cases, portability)
- Agent prompt refinement (better tree construction, clearer explanations)
- Testing against diverse PRs (different languages, sizes, patterns)
- Documentation and examples
- TUI exploration (V1.01 -- see README for context)

## Code of Conduct

This project follows the [Block Open Source Code of Conduct](https://block.xyz/code-of-conduct). Be kind, be constructive.

## Questions?

Open an issue or reach out to the maintainers listed in [CODEOWNERS](./CODEOWNERS).
