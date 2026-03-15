# Versions

What we expect to ship in each release. Living document — ideas progress from Future Ideas into versions as they're scoped.

## V1.0 — Rough Beta

Full pipeline working end-to-end. Functional, not polished.

- Worker agent prompts: codebase-locator, codebase-analyzer, codebase-pattern-finder
- Concept-researcher agent prompt
- Coverage-checker agent prompt
- Orchestrator command (analysis phase + interactive walkthrough)
- Distribution (follow superpowers pattern)
- Tested against cashapp/backfila#546

## V1.001 — Agent Optimization

Parallel with V1.002. Agent prompts and the eval loop.

- Eval framework (skill-creator pattern, ~20 test cases)
- A/B prompt iteration for all agents and orchestrator
- Revisit Q7 (tone) and Q8 (change boundary split) with eval data
- Reconcile documentarian mandate item count (5 vs 6)
- Concept-researcher Bash access: decide with evidence
- Concept-researcher bottleneck: investigate and address
- "Explain the why" anti-rationalization experiment
- Semi-formal reasoning evaluation for analyzer
- Prompt size reduction experiment (target: half of V1)
- Few-shot examples updated from real PR output
- Expand test PR corpus beyond backfila#546

## V1.002 — Fixes and Improvements

Parallel with V1.001. Everything that isn't agent prompts.

- Priority fixes from V1 real usage
- Script improvements: structured output (JSON), --help
- Distribution: plugin packaging polish, install docs, marketplace readiness
- Review plugin manifest attribution (author may need to be Block Inc.)
- README: installation instructions reflecting actual install mechanism
- `analysis.md` output file (referenced in README but not yet implemented)
- Format adjustments discovered during real use
- Walkthrough UX rough edges
- Reconcile coverage-checker agent checks vs `check-tree-quality.sh` scope

## V1.01 — TUI

Responsive TUI. This is when we publicize. Mike doesn't believe the tool is useful without a snappy TUI.

- TUI framework decision (Ink / Bubble Tea / Ratatui / Textual)
- Interactive tree navigation with keyboard shortcuts
- Diff viewing per concept
- Status indicators and progress display
- Improved resumability (visual pickup where you left off)
- Evaluate Beads or alternative for state management

## V1.1 — GitHub Posting

Post review comments to GitHub PRs.

- GraphQL API integration
- Pending reviews (invisible until submitted, explicit affirmative to post)
- Graceful degradation (inline → top-level fallback)
- SHA re-indexing when PR HEAD moves

## Future Ideas

Rough implementation order. Will be pulled into versions as they're scoped.

1. Depth sentiment — review depth controls (quick scan / thorough / deep dive)
2. Cross-platform support — Amp, then other AI CLIs
3. Instrumentation / augment layer — observability into agent behavior
4. Compound engineering loop — capture patterns from past reviews to seed future ones
5. Multi-reviewer support — team reviews, not just single reviewer
6. Auto-accept patterns — if reviewer consistently accepts a pattern type, offer to auto-accept
7. CI integration — run Fowlcon as a CI step, generate tree as PR comment
8. GitHub App / bot deployment model
9. Cross-platform agent dispatch — using Claude to invoke non-Claude agents
