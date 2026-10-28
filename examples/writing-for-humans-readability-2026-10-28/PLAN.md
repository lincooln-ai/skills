# Preregistered readability pilot

Protocol recorded before generating any candidate outputs on 2026-10-28. This is a small skill-prompt ablation, not a replication of the paper or a human comprehension experiment.

## Question and scope

Does an explicit relationship-clarity pass improve source-grounded public copy beyond the upstream writing-for-humans skill, and does a separate meaning-preservation pass prevent editing losses? Test English and Chinese separately. Retain the author's intentional voice and necessary domain vocabulary.

Baseline: timharris707/skills at `fd4b6d1d012d8b1ca0db68c035162e10c455c053`, including SKILL.md, last-mile-scrub.md and the tell catalog it actually references. Their contents are supplied identically in every condition. This evaluates loaded instructions, not skill discovery or file-routing behavior.

## Fixed interventions

The runner stores the exact two additions as clarity.md and preservation.md before the first generation. Four conditions form a 2 x 2 factorial: B (upstream), R (B + relationship clarity), F (B + meaning preservation), RF (B + both). All other instructions remain the same. Do not tune the additions or fixtures after seeing scores without labeling a new exploratory round.

Six synthetic, self-contained public-copy cases: four English and two Chinese. Two independent generations per condition, eight fresh generation contexts in total. Each generation contains all six cases; the second repetition reverses case order. This batching is a limitation: cases share one generation context. No case receives the expected answers or scoring rubric. The deliberate personification and already clear prose case is an over-editing control.

## Execution and isolation

Use the existing authenticated Codex CLI, without paid API calls, model overrides or network/file tools inside the evaluated turns. Invoke --ignore-user-config, --ephemeral, --skip-git-repo-check, project_doc_max_bytes=0 and an isolated working directory. Record the CLI version, actual startup model identifier, prompt hashes, response hashes, timings and observed tool calls. Fresh means no conversation history; a common installed-skill discovery catalog may still be supplied by the runtime. Its effect cannot be separated from this pilot.

## Outcomes fixed before execution

Primary: source-grounded answer coverage for six questions per case; unsupported or altered material claims. Score actor, negation, scope, timing and uncertainty, not keyword similarity alone. Any loss of a critical condition or invented product behavior is a regression requiring examination before adopting the change.

Secondary model rubric, each 0-4: relationship clarity (who does what and how conditions attach); coherence (logical links and readable flow); voice fit (requested register, supplied opinion and deliberate voice choices). Length and exact-identifier checks are descriptive warnings, not evidence of reader comprehension or automatic pass/fail judgments.

Score each case in two independent fresh judge contexts, with opaque candidate IDs and the second presentation order reversed. The judge sees the source and expected answers, so this is source-grounded model grading, not a source-blind reading test. Require evidence snippets for missing/changed answers and clarity below 4. Keep both scores; report disagreements rather than choosing the favorable one. A common model family serves as writer and judge, with possible correlated bias. Review flagged outputs manually.

## Analysis and decisions

Report each arm by language, paired differences and disagreement counts. Describe R's marginal effects as R-B and RF-F, and F's as F-B and RF-R. Do not use inferential significance, causal generalization to other models, AI-detector scores, or claimed human reading gains. The retention gate is no newly introduced material error versus the paired baseline, plus no deletion of the control's exact deliberate sentence. If a gate fails, inspect the concrete failure, revise narrowly, and label any retest exploratory.

Adopt only additions supported by the inspected results. A null result is useful and must remain visible. Preserve raw prompts, responses, judge outputs, manifest, script and fixtures so the outcome is reviewable and rerunnable. Personal paths and authentication stay outside public artifacts.
