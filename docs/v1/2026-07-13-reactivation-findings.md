# V1 Reactivation Findings — 2026-07-13

Fowlcon is being reactivated after ~4 months idle (V1 shipped 2026-03-15).
This document is the condensed state assessment produced during the
reactivation planning session: what V1 actually is, what's broken, what's
stale, what held up, and the open questions the rework must answer.
Findings are numbered (F1…) and open questions (RQ-1…) for stable reference.

## Where V1 actually stands

V1 shipped all planned artifacts — five agent prompts, the orchestrator
command, plugin manifest, format templates, four tested shell scripts
(189 bats tests across scripts + format parsing). But the final task of
the V1 plan, the end-to-end integration test against cashapp/backfila#546,
never ran. It was deferred as the first V1.002 task and the project went
idle the same day. **The pipeline has never been exercised against a real
PR.** Every claim about prompt quality, format fitness, and walkthrough UX
is untested.

In honest terms, V1.0 was an *attempt* — aspirational, never validated —
and is relabeled as such rather than rewriting history. The first release
that earns the V1 label is a new, very minor version: **V1.003 —
Validated V1** (see `docs/versions.md`), reached when the rework lands
and the pipeline is proven end-to-end.

## Integration gaps (found by inspection, pre-run)

- **F1 — Comments file is never initialized.** `add-comment.sh` hard-fails
  if `review-comments.md` is absent, and no step in
  `commands/review_pr.md` creates it (Step 9 writes only the tree). The
  first `comment` action in any walkthrough errors out.
- **F2 — Template paths won't resolve at runtime.** The orchestrator
  references `templates/review-tree.md` relatively. Installed as a plugin,
  nothing anchors that path — no `${CLAUDE_PLUGIN_ROOT}` usage anywhere.
- **F3 — `check-tree-quality.sh` input is undefined.** The script requires
  a diff file-list argument; orchestrator Step 7 never says how to
  produce it.
- **F4 — No install script exists.** README's Installation section says
  `./scripts/install`; `scripts/` contains only the four state scripts.
  The plugin manifest is the only real install mechanism, and its
  discovery behavior was never verified end-to-end.
  *Confirmed and sharpened 2026-07-13 during S1 setup (journal SF1,
  SF2):* the repo has no marketplace manifest, so the plugin
  marketplace-add path fails outright; and the command actually
  resolves as `/fowlcon:review_pr` (filename-derived, namespaced — the
  frontmatter `name:` is ignored for `commands/*.md` files), so the
  documented `/review-pr` cannot be invoked by a customer at all.
- **F17 — No packaging boundary.** Installing the plugin from a local
  path copies the entire working tree into the plugin cache, including
  untracked and gitignored files (observed during S1 setup). Harmless
  for installs from the public repo, but the plugin needs an explicit
  packaging manifest/boundary before distribution polish.
- **F5 — Three overlapping coverage mechanisms, never reconciled**: the
  coverage-checker agent, `check-tree-quality.sh` check 5, and
  `coverage-report.sh` (which trusts the tree's self-reported counts).
  Already flagged in versions.md; still open.
- **F6 — `analysis.md` advertised but not produced.** README's Data
  section lists it; deferred in the V1 plan; no prompt writes it.
- **F7 — Test fixtures are synthetic.** All format tests run against
  hand-written sample trees, not output from a real run. The formats have
  never round-tripped through actual agent output.

## Staleness assessment (March 2026 → July 2026)

- **F8 — Model assumptions.** Frontmatter hardcodes bare aliases
  (`opus`/`sonnet`/`haiku`) sized to early-2026 capability tiers. The
  tiering *philosophy* (judgment at the top, mechanical checks at the
  bottom) is sound; the specific assignments, cost figures ($2–3/review),
  and benchmark numbers in the research docs all predate current models
  and need re-validation.
- **F9 — Anti-rationalization scaffolding sized for older models.** The
  orchestrator's Red Flags table, the repeated DO-NOT blocks in every
  worker, and the rigid step budgets ("maximum 3 passes", "one chance to
  fix") encode distrust of early-2026 model planning. Current models
  cut corners less; some of this is now redundant token weight. This was
  anticipated — V1.001 already planned a prompt-halving experiment.
  Counterpoint: workers still run on mid-tier models, where guardrails
  may still earn their keep. Decide with eval evidence, not vibes.
- **F10 — Platform assumptions.** Claude Code has moved since March:
  plugin conventions (`${CLAUDE_PLUGIN_ROOT}`), structured output schemas
  for agents, `AskUserQuestion` (option chips — directly relevant to
  walkthrough decisions), artifact rendering (relevant to tree display),
  richer skill/plugin ecosystem. None of this existed in the V1 design
  space.
- **F11 — Competitive data is four months old.** See Positioning below.
- **F12 — Known staged staleness.** The ~50-line V1.1 posting section in
  `templates/review-comments.md` (tracked in AGENTS.md for removal) and
  stale Beads references in reconstruction doc 04.

## What held up

- **F13 — The architecture.** Orchestrator → concept-researcher → three
  workers + coverage-checker, flexible hierarchy, single-writer state,
  markdown-on-disk state model. Nothing observed invalidates it.
- **F14 — Tool-restriction over prompt-restriction** (locator has no Read;
  workers have no Bash) remains Fowlcon's own innovation — the RPI
  lineage uses prompt-based restriction. Keep. A related refinement worth
  evaluating in rework: analyzer and pattern-finder share tools and
  differ only in role — they could collapse into one subagent *type*
  dispatched with different role prompts, while the locator's no-Read
  guardrail stays a hard type boundary.
- **F15 — Format contracts + script tests.** The two templates are
  precise, script behavior is well-tested, atomic-write discipline is
  consistent. The formats are the most trustworthy layer of the system —
  though F7 caveats apply until a real run round-trips them.
- **F16 — The guides.** `docs/guides/agent-prompt-design.md` and
  `agent-prompt-principles.md` are evidence-based and mostly
  model-agnostic: description-as-trigger, format constraints as the
  strongest quality lever, primacy+recency placement, minimum-viable
  inline context, work-discipline vs output-discipline separation. Still
  canonical. One flagged brittleness — reasoning-pause keyword behavior —
  explicitly anticipated model drift and should be re-tested.

## Docs status map

Note: `docs/private/` paths below are local workbench files — untracked
by design, present only on the primary working machine.

| Docs | Status |
|------|--------|
| `docs/guides/*` | Canonical. Constraints for all prompt work. |
| `docs/private/reconstruction/05-open-questions-index.md` | Source of truth for the 43 resolved questions. |
| `docs/private/reconstruction/v1-forward-plan-draft.md` | Superseded by `docs/v1/2026-03-15-v1-implementation-plan.md`, but its "Flags and Assumptions to Challenge" section is the standing rework agenda. |
| `docs/private/reconstruction/01–04` | Historical; contains known errors cataloged in `research/fresh-eyes-review.md`. Do not treat as authority. |
| `docs/private/reconstruction/02-rpi-ghost-analysis.md` | Superseded outright — RPI is public (HumanLayer framework, implemented in Block's goose). Use goose docs. |
| `docs/private/reconstruction/research/current-best-practices-2026-03.md` | Structural findings likely durable; all model-specific numbers stale. |

## Design thesis (sharpened during reactivation)

Fowlcon is AI code review that **builds human cognition rather than
replacing it**. It works only when the reviewer is curious — "what is
this change doing, and why?" — and its job is to make that curiosity
cheap to act on. Progressive disclosure works on humans too: the tree
unwraps the change one layer at a time, the reviewer drives, the agent
plays the role a PR author plays in a good walkthrough — expert on the
change, never the judge of it.

Counter-evidence the design must answer: JetBrains reports that
surface-level plausibility in AI-assisted review appeared to *reduce*
critical engagement
(<https://blog.jetbrains.com/ai/2026/05/stop-sending-ide-catchable-ai-code-errors-to-review/>).
Fowlcon's structural answer is that it never presents conclusions to
passively accept — every node demands an explicit reviewer decision
(reviewed / accepted / pending), and coverage is only ever claimed from
those decisions. Whether that answer holds in practice is a core thing
the walkthrough UX must be evaluated against, not assumed.

Positioning stances:

- Fowlcon takes no stance on how much work AI should do on its own.
  Whatever that share becomes, there will always be changes someone needs
  to genuinely understand — high-stakes code, unfamiliar systems,
  high-level decisions worth monitoring.
- Analysis quality doubles as **backpressure**: if an agent expert can't
  produce a coherent concept tree in a reasonable amount of time/tokens, that is evidence the change is too
  confusing or poorly decomposed — specific, actionable feedback for the
  author, not a vague "please split this."
- Prompts stay concise, broad, and high-level with structure — recipes
  rather than rules — so they can be massaged as agents evolve. Sizing
  discipline: start smaller than the acceptable maximum — the smallest
  prompt that could work — and grow only when evidence demands it, one
  PR at a time. "A good size for an agent" is a ceiling, not a
  starting point.
- A useful external frame: the "AI cognitive debt" discussion, e.g.
  <https://nayeemzen.github.io/writings/long-running-agents/> and
  <https://youtu.be/Tk0hIOAwf6M>.

## Positioning and competitive landscape (needs a dedicated pass)

The finding-generator category has matured: Anthropic ships a
pr-review-toolkit plugin
(<https://github.com/anthropics/claude-code/tree/main/plugins/pr-review-toolkit>)
and Claude Code itself now bundles `/code-review` with a multi-agent
"ultra" mode. These tools *produce findings* (bugs, issues, review
comments). Fowlcon's claim is different and so far unoccupied: it
*produces comprehension* — a coverage-complete walkthrough that leaves
the human genuinely understanding the change, with confidence they saw
all of it. The differentiation thesis: complementary, not competing —
findings tools answer "what's wrong with this PR?", Fowlcon answers "do I
understand this PR, and did I see all of it?" This needs a real
differentiation write-up with current data (RQ-4).

## Consolidated open questions

Carried forward from V1 flags plus new questions raised during
reactivation:

- **RQ-1** Is "decrypting intent" the right core framing for what the
  analysis phase does? (Understanding-oriented, pairs with the cognition
  thesis.)
  *Resolved 2026-07-13:* intent is part of it, but what the change
  **does** matters more. Framing candidates (S4) must lead with
  behavior — "what is this change doing" — with intent as the second
  layer, not the headline.
- **RQ-2** Is rendered HTML (e.g. an artifact view of the tree) enough
  interactivity for the walkthrough, or does V1.01 need a real TUI/side
  app? Cheap spike possible with current artifact support before any TUI
  framework decision.
  *Resolved 2026-07-13:* the rendering/interaction surface is
  orthogonal to the analysis pipeline and must be designed as a
  contained boundary so it can be iterated on separately. The cheap
  artifact spike is the agreed starting point (S3b); the TUI-vs-HTML
  question stays open behind that boundary.
- **RQ-3** Depth configuration: reviewer declares depth sentiment at
  review start (quick scan that won't push back on complexity /
  best-effort medium / deep check for mission-critical code). Was a
  Future Idea; promote into rework design?
  *Resolved 2026-07-13:* not promoted — YAGNI. It was on the table only
  because it appeared in captured design notes, not because of observed
  need. The tool ships one good default depth (opinionated defaults,
  principle 10); depth config returns only if real usage shows the
  default failing someone.
- **RQ-4** Differentiation write-up vs finding-generators (see above).
  *Context:* a short doc stating, with July-2026 evidence, how Fowlcon
  differs from tools that generate review findings (Anthropic's
  pr-review-toolkit, Claude Code's built-in /code-review): what each
  produces, where they complement Fowlcon, where Fowlcon's
  comprehension-and-coverage claim stands alone. It exists to keep
  scope honest and to seed README/publicity wording; produced in S4.
  The open part is only whether the complementary-not-competing thesis
  survives contact with current evidence.
- **RQ-5** Agent instrumentation: a lightweight way for agents to report
  where they struggled or felt tension during analysis — both for tool
  improvement and as a review-quality signal shown to the reviewer.
  Related: marking which review content was agent-produced.
  *Context:* the captured idea — agents self-report where they
  struggled (tension, uncertainty, excessive effort), and the tool (a)
  uses those reports to improve prompts, (b) surfaces them to the
  reviewer as a review-quality signal, (c) marks which content was
  agent-produced. Pairs directly with the backpressure stance: effort
  spent (time/tokens) is one measurable tension signal. The question:
  does a lightweight version enter the rework design (S3), or does it
  stay backlog until the eval loop exists?
- **RQ-6** An "augment layer" — a thin, concise layer added to an
  existing agent (idea captured incomplete; needs a definition pass
  before it can be evaluated).
  *Working definition (2026-07-13):* the leading interpretation is
  customer extension points — a thin, concise layer a customer adds to
  the shipped agents to fit their codebase and conventions without
  forking the prompts. (The original note was captured mid-sentence;
  this is the best reconstruction.) Stays backlog until a real need
  appears.
- **RQ-7 (= old Q7)** Purposeful tone — still a starting point; eval in
  V1.001.
  *Context:* "purposeful tone" = worker prompts open with a stakes
  paragraph ("Your findings shape how a reviewer understands a
  codebase. Completeness matters — a missed file is a blind spot…")
  rather than bare instructions. The hackweek decision kept it on the
  hypothesis that telling the agent *why* quality matters improves
  thoroughness — never measured. The eval question: does the stakes
  paragraph change output quality, or is it token weight?
- **RQ-8 (= old Q8)** Change-boundary split (analyzer reports /
  researcher synthesizes) — still a starting point; eval in V1.001.
  *Context:* every concept description carries a "change boundary" —
  what connects to the concept (imports, callers, interfaces, tests)
  and what would break if it changed. Q8 split the work: the analyzer
  *reports* raw connections; the concept-researcher *synthesizes* them
  into the boundary. The alternative is the analyzer synthesizing
  directly, closer to the code. The split was a judgment call, never
  tested; the eval question is which placement produces better
  boundaries.
- **RQ-9** Concept-researcher Bash access — contradictory across old
  docs; decide with eval evidence.
- **RQ-10** Concept-researcher as bottleneck — investigate with real
  transcripts.
- **RQ-11** Resumability mechanics — "should work via persisted state" is
  a hypothesis; test in the shakedown.
- **RQ-12** Documentarian mandate item count (5 vs 6) — reconcile.
- **RQ-13** Full-file reads for workers (no offset/limit) — quality
  guard or over-prescription for current models? Decide with evals.
  *Resolved 2026-07-13:* hold the mandate — partial reads risk blind
  spots, and blind spots are the one failure mode Fowlcon exists to
  prevent. It may be an old-model restriction, but relaxation requires
  thorough eval evidence, not a capability assumption. Held ground for
  S3; a low-priority S7/L1 hypothesis at most.

## Candidate research (unvetted, for a later research pass)

- <https://github.com/zarazhangrui/codebase-to-course> — codebase →
  interactive HTML course; progressive-disclosure UX prior art.
- <https://github.com/prime-radiant-inc/hearthstone> and
  <https://primeradiant.com/projects/> — agentic software structure.
- <https://www.augusteo.com/blog/inside-gas-town> — multi-agent
  orchestration writeup.
- <https://github.com/stephenturner/skill-deslop/blob/main/references/structures.md>
  — prompt/skill structure hygiene.
- <https://medium.com/jonathans-musings/what-warps-open-source-release-tells-us-about-the-future-of-agentic-software-development-5d4409726bf1>
  — Warp's open-source release; relevant to RQ-2's UI question.

Caveat on all of the above: these are datapoints, not law. Claims in
public write-ups may not hold in real life — there's a viewership
incentive to declare things true/right/solved, and the agentic space is
too new for anyone to *know* what works entirely. Treat every claim as
unverified until tested here.