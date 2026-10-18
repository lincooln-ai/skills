# Codex edition (PR #286): findings for review

Written 2026-09-05 by the Claude Code orchestrator session for Tim Harris (the decider), to share
with the Codex-side author (GPT Astra) of PR #286. Every claim below carries a file:line into the
merged repository at `timharris707/skills` so it can be checked, refuted, or fixed. Nothing on the
Codex side has been changed; the decider wants the author's response first.

Method: one Opus agent read `editions/codex/CODEX.md`, `editions/codex/README.md`,
`editions/codex/runtime/CONTINUITY.md`, and every hunk of `editions/codex/skills.patch`, classified
each hunk, probed the installed Codex CLI (`codex-cli 0.152.1`) for the tool and model names the
edition cites, ran `verify_codex_install.py --profiles 1`, and ran every repo check. A second pass
diffed all 23 descriptions before and after. Line numbers refer to `editions/codex/skills.patch`
at commit `c7e0ad4` unless another file is named.

## What the edition gets right

Stated first so the findings below are read in proportion.

- The harness swaps are accurate: Codex has no hooks and no `.claude/handoff.md`, and the edition
  replaces both with task-owned checkpoints (`runtime/CONTINUITY.md`) correctly.
- The subagent tool names it relies on exist in the CLI binary: `collaboration.spawn_agent`,
  `list_agents`, `followup_task`, `wait_agent`, `wait_threads`, `send_message_to_thread`,
  `create_thread`, `fork_turns`, `CODEX_THREAD_ID`, `allow_implicit_invocation`.
- `verify_codex_install.py` passes: 23 skills discovered, 0 parser errors, user settings preserved,
  0 model calls. `build_codex_plugin.py --check`, `check_codex_publication.py`, and the 19
  publication tests all pass.
- `editions/codex/upstream.json` pins a SHA-256 of every adapted source, so any upstream edit forces
  the adaptation back through review. That guard is what made the fixes in section 4 safe to port.
- Two adaptations are improvements over the Claude original: grilling asks in small batches instead
  of one whole-frontier round (Claude's four-card limit does not apply, lines 562, 589-599, 607),
  and show-me-your-work appends corrections instead of deleting rows (lines 1495-1502), which is
  what an audit log should do.

## 1. Every skill lost its trigger clause (highest priority)

All 23 adapted `SKILL.md` descriptions were shortened to roughly a third of their length and every
one dropped its "Use when ..." clause. 24 of 24 source skills carry that clause; 0 of 23 in the
Codex build do.

Example, grilling (line 547):

- Source: "Interview the decider relentlessly to turn a half-formed plan, design, or idea into
  shared understanding: a design tree worked in rounds until nothing is silently assumed. Use when
  asked to grill, stress-test, or pressure-test this..."
- Codex: "Pressure-test a plan with the decision maker through small rounds of unresolved decisions
  backed by facts."

Why it matters: the description is the only part of a skill loaded into every session, and it is
how an agent decides on its own to reach for the skill. The repo's own authoring reference
(`skills/author/writing-for-agents/references/skill-mechanics.md:12,16,20,31`) states the house
shape as "what it does, then when to reach for it" and says the `Use when` clause "is where the
branches live." Codex already has a separate human-facing picker line (`agents/openai.yaml` →
`short_description`, same reference, line 88-94), so the trigger clause was not competing for that
slot. Nothing in `CODEX.md`, `README.md`, or the #286 close-out explains the shortening, and no
Codex description length limit was found.

Effect for a Codex user: the skills are less likely to fire unprompted; they wait to be named.

Ask: restore the `Use when` clause in all 23 descriptions (or drop the description rewrite from
the patch entirely), and add a CI assertion that every `plugins/clickai-codex/**/SKILL.md`
description contains a when-clause. If there is a Codex-side reason the clause must go, state it.

Affected lines: 33, 49, 207, 499, 547, 616, 690, 734, 792, 821, 872, 1055, 1089, 1114, 1130,
1291, 1365, 1479, 1511, 1554 (plus huh, plainspoken, and writing-for-humans at 26-33).

## 2. Rules loosened without a Codex reason

Each hunk of the patch was sorted into three piles. Only the first pile is a request.

### 2a. Regressions (nothing in Codex prevents the original rule)

| Where | Source rule | Codex text | Note |
| --- | --- | --- | --- |
| setup posture, lines 883, 893, 894 | "infer first, confirm everything"; the decider and binding-doc home are always asked | "inspect first, reuse decisions"; reuse the recorded decider, ask only if unbound or changed | Naming the decider is the one binding an agent should never infer. Reusing a recorded home is fine; the decider should stay an always-ask. |
| codebase-review, lines 625, 657, 672, 681 | "ends only when the decider has dispositioned every survivor"; "an undispositioned report is a review that changed nothing" | the report can be complete while decisions remain pending | The skill's whole premise is that an unactioned report is worthless. Harness-neutral. |
| adversarial-review, line 1080 | "Two consecutive runs with nothing new confirmed is a stop signal" | "do not require two clean runs"; stop when the reviewer judges the review complete | Replaces a falsifiable stop condition with the reviewer's own judgement, the drift the rule existed to block. |
| implement, lines 1312, 1346 | "Code at no named seam ships WITH its tests, in the same commit" | "validate the actual risk; use existing checks for small reversible edits" | A hard bar became a judgement call. Commit mechanics (see 2b) are a separate matter from whether tests travel with code. |
| handoff, lines 1177, 1200, 1225 | rule 5's procedure: scan the composed handoff for tokens, replace with pointers, re-read the saved file | outcome only: "secret values are absent" | The check that made the rule enforceable is gone. |
| writing-for-humans, lines 119-123, 132, 143, 148, 156 | a countable em-dash budget and "never swap a dash for another mark in place" | "punctuation follows the requested voice" | A checkable rule became unfalsifiable. |
| to-tickets, line 1545 | "no item carries both" (a native edge and the `blocked` label) | "carries both until the respective blockers clear" | Reverses a stated invariant; no Codex justification given. |

### 2b. Defensible product choices for Codex (no change requested; noting for the record)

- router, line 832: run setup only on request or when a missing binding gates work. Reasonable for
  a tool used for quick questions. Combined with the setup row above, though, a repo can now be
  worked with no decider ever named.
- advisory-board intake, lines 223, 240, 276, 311-312, 319-325: reuse an existing explicit approval
  of the same plan. Data-handling consent stays unwaivable; the reuse is scoped narrowly.
- research, lines 743-783: deliver in the current task; no unsolicited ticket.
- implement green checkpoints, lines 1319, 1347: checkpoint "within the user's commit instructions."
  Codex's no-commit-without-asking posture is real.
- implement ambiguity, line 1337: pause only dependent work. "Material" is now agent-judged where
  the original was not.
- to-tickets sign-off, line 1536, and wizard scoping, line 1574: reuse an exact prior approval.

### 2c. Necessities or improvements (keep)

- grilling round shape (lines 562, 589-599, 607) and show-me-your-work append-only corrections
  (lines 1495-1502), both named above.
- advisory-board doctor (lines 232, 284-285, 344, 362): announcing smoke-call cost before contacting
  providers is right. One contradiction remains: line 285 still demands "every offered seat must be
  verified GO" while line 232 allows reporting GO/NO-GO from registered capabilities without a probe.
  Pick one.

Ask: revert 2a (seven items), or state the Codex-side reason for each. Keep 2b and 2c.

## 3. Capability names not found in the installed Codex CLI

Probed `codex-cli 0.152.1` at `/opt/homebrew/bin/codex` with `strings`. The tool names listed in
"What the edition gets right" are all present. These three are not:

| Name | Where cited | Probe result |
| --- | --- | --- |
| `request_user_input_async` | `CODEX.md:92`; patch lines 279, 466, 538, 583 | 0 matches. The binary has `request_user_input` and, separately, `send_async_message`. |
| `handoff_thread` | `CODEX.md:65` | 0 matches. The only `handoff` symbols belong to the realtime-voice path. |
| `gpt-6-astra` as a `--model codex=` value | patch line 248 (advisory-board SKILL.md) | 0 matches for `gpt-6` or `astra`. |

On the model: GPT Astra exists as a product. What is unverified is that `gpt-6-astra` is the exact
model ID the CLI accepts for `--model`. An agent passing a wrong ID to a board seat fails that seat.
If the ID is right for the desktop app and only absent from this CLI build, say so and cite it.

Separately: no OpenAI or Codex documentation URL appears anywhere under `editions/codex/`. The
only cited page is `clickai.dev/install#codex`, which repeats the same claims. Compaction
behaviour ("continue in the same task through native compaction"), subagent filesystem sharing,
and the desktop Plugins panel flow are plausible but rest on the author's word. "Tuned for Astra
at medium and extra high" has no benchmark behind it, and `editions/codex/README.md` says as much.

Ask: fix or remove the three names, and add the citing URL for each capability `CODEX.md`
asserts.

## 4. Real bug fixes that were applied only to the Codex copy (now ported; FYI)

The patch carried ten genuine fixes that are not harness adaptations. Since the Claude sources
are the truth the Codex edition is built from, leaving them Codex-only meant two copies drifting.
The decider approved porting them; they are on branch `fix/upstream-codex-edition-fixes`
(advisory-board v1.18.3, team-workflow v1.7.1), reviewed and tested (1,689 advisory-board tests
pass). After the port, nine of the files are byte-identical between editions and their patch
hunks are gone. No action needed on the Codex side; listed so the author knows where they went.

| Fix | Source file |
| --- | --- |
| Gate counted seat-rounds, not seats (`grep -c` gave 6 on a three-seat two-round run) | `references/execution-harness.md` |
| stakeholder-panel said "three" archetypes, listed four | `references/lens-presets.md` |
| `echo_score`: None verdict counted as a flip; docstring vs `>= 0.5` rule | `scripts/_conductor/echo_score.py` |
| `rounds.py`: injection and egress guard keyed on different predicates | `scripts/_conductor/rounds.py` |
| `_embed_fonts.py` wrote to a pre-restructure path | `scripts/_embed_fonts.py` |
| `redline.py` docstring missing the `gap` row it emits | `scripts/_conductor/redline.py` |
| `revision.py` f-string with no placeholders | `scripts/_conductor/revision.py` |
| `render_handoff.py` invisible zero-width literal | `scripts/render_handoff.py` |
| `render_tracker.py` named a person instead of the role | `scripts/render_tracker.py` |
| wizard `write_env` treated a read failure as "no match" | `skills/run/wizard/template.sh` |

Not ported, deliberately: the tracker-claim wording change (lines 997-1010, "atomic" to
"optimistic") and the frontier query rewrite (lines 1019-1047, REST to GraphQL). See section 5.

## 5. The frontier query rewrite: verified live, but it drops a filter

Lines 1019-1047 replace the REST frontier query with a GraphQL one. Both were run against
`timharris707/skills` on 2026-09-05:

- GraphQL: `issueDependenciesSummary { blockedBy }` is a valid field; the query returns every open
  issue with a numeric `blockedBy`. It works.
- REST: `issue_dependencies_summary` is present on all 19 open non-PR issues. The original also
  works, so the stated reason for the rewrite ("the plain issue list omits" the summary) does not
  hold today.

One thing the rewrite loses: the REST query filters out pull requests
(`select(has("pull_request") | not)`); the GraphQL `issues` connection excludes them by type, so
that is fine. But the GraphQL version caps `labels(first: 100)` and `assignees(first: 100)` per
issue without pagination, which is harmless in practice. No defect found; the Claude edition keeps
REST because both work and the source has a live test history behind it.

## Summary of asks, ranked

1. Restore the `Use when` clause in all 23 descriptions; add the CI assertion. (Section 1)
2. Fix or remove `request_user_input_async`, `handoff_thread`, and confirm the `gpt-6-astra`
   model ID; cite OpenAI docs for each `CODEX.md` capability claim. (Section 3)
3. Revert the seven regressions in 2a, or state the Codex-side reason for each. (Section 2)
4. Resolve the preflight contradiction (line 232 vs 285). (Section 2c)

Everything in section 4 is already handled on the source side.
