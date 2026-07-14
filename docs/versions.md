# Versions

What we expect to ship in each release. Living document — ideas progress from Future Ideas into versions as they're scoped.

## V1.0 — Attempt (shipped 2026-03-15, never validated)

Aspirational V1: all artifacts shipped, but the end-to-end integration
test never ran, so it never earned the release label. Kept in history
as an attempt. Original intent: full pipeline working end-to-end,
functional, not polished.

- Worker agent prompts: codebase-locator, codebase-analyzer, codebase-pattern-finder
- Concept-researcher agent prompt
- Coverage-checker agent prompt
- Orchestrator command (analysis phase + interactive walkthrough)
- Distribution (follow superpowers pattern)
- Tested against cashapp/backfila#546 *(never happened — carried into
  V1.003)*

## V1.001 — Agent Optimization (absorbed into the 2026-07 rework)

Never executed standalone. Absorbed 2026-07-14: items execute via the
reactivation sessions and loops (S/L/RQ labels resolve in
`docs/v1/2026-07-13-reactivation-playbook.md` and
`docs/v1/2026-07-13-reactivation-findings.md`); V1.003 is the release
that lands the results. Dispositions:

- Eval framework (skill-creator pattern, ~20 test cases) → S7 designs
  and seeds it (3–5 PRs); L2 grows the corpus toward ~20
- A/B prompt iteration for all agents and orchestrator → L1
- Revisit Q7 (tone) and Q8 (change boundary split) with eval data →
  RQ-7/RQ-8, L1 hypotheses
- Reconcile documentarian mandate item count (5 vs 6) → RQ-12, settled
  in S3
- Concept-researcher Bash access: decide with evidence → RQ-9, S3
  proposal + eval check
- Concept-researcher bottleneck: investigate and address → RQ-10, S2
  transcripts + S3
- "Explain the why" anti-rationalization experiment → L1 hypothesis
- Semi-formal reasoning evaluation for analyzer → L1 hypothesis
- Prompt size reduction experiment (target: half of V1) → superseded:
  the rework starts prompts minimal and grows them on evidence, rather
  than shrinking V1 text
- Few-shot examples updated from real PR output → S3/S5, sourced from
  the shakedown trees
- Expand test PR corpus beyond backfila#546 → L2

## V1.002 — Fixes and Improvements (absorbed into the 2026-07 rework)

Never executed standalone. Absorbed 2026-07-14, same treatment as
V1.001. Dispositions:

- Integration test: run full pipeline against cashapp/backfila#546,
  document findings → S1 (in progress; journal SF1/SF2 already logged)
- Priority fixes from V1 real usage → S2 triage → S5
- Script improvements: structured output (JSON), --help → L3
- Distribution: plugin packaging polish, install docs, marketplace
  readiness → S5 units (F4, F17)
- Review plugin manifest attribution (author may need to be Block Inc.)
  → S5 re-homing unit, per the S0 write-path decision
- README: installation instructions reflecting actual install mechanism
  → S5 (F4)
- `analysis.md` output file (referenced in README but not yet
  implemented) → S3 decides: implement it or retire the README claim
  (F6)
- Format adjustments discovered during real use → S2 → S5
- Walkthrough UX rough edges → S2 → S3(b)
- Reconcile coverage-checker agent checks vs `check-tree-quality.sh`
  scope → S5 (F5)

## V1.003 — Validated V1

The first release that earns the V1 label: the pipeline proven
end-to-end against real PRs. Lands when the V1.001 + V1.002 workstreams
(executed via the 2026-07 rework) validate:

- Integration gaps closed; pipeline runs end-to-end
- Prompts rebuilt for current models — starting minimal, growing only
  on evidence
- Eval framework with calibrated graders and a measured baseline
- Validation runs pass against backfila#546 plus at least one fresh PR

Later versions below (V1.01, V1.1, Future Ideas) predate the rework and
may be redefined as part of it.

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
