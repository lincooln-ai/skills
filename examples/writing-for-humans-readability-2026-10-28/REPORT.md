# Writing for Humans: research, ablation and a narrower revision

The registered additions did **not** improve source retention on this pilot. They were rejected as a combined production change. A narrower conditional pass was then corrected against concrete failures; its final exploratory regression covers 84/84 predefined question points in 14 outputs. This does not establish that the final pass outperforms upstream or improves human comprehension.

## Request and scope

User request: “fork 仓库。根据最新研究，做消融测试，进行修改。用github账号”. The user selected `lincooln-ai` after two connected identities were discovered. Fork: https://github.com/lincooln-ai/skills . Work is on `feat/writing-for-humans-readability`; no upstream PR, release, production deployment or personal skill installation was requested or performed.

Target: `skills/author/writing-for-humans/SKILL.md`. Baseline upstream commit: `fd4b6d1d012d8b1ca0db68c035162e10c455c053`. The protocol was committed as `d8808b1` before generation. Research is in [RESEARCH.md](RESEARCH.md), protocol in [PLAN.md](PLAN.md), inputs and hashes in [protocol.json](protocol.json). Hashes refer to normalized UTF-8 text actually supplied to the CLI.

## Registered 2 x 2 ablation

B = unchanged upstream skill and referenced scrub/catalog; R adds relationship clarity; F adds a preservation pass; RF adds both. Four English and two Chinese synthetic cases, two generations per arm. Eight fresh generation contexts each contain six cases, with reversed case order in repetition two. Expected answers were withheld from the writer.

Each case was graded in two fresh contexts with opaque labels and reversed presentation order. Scores below average those judgments. All question-level judgments agreed across orders; subjective rubric judgments can differ. A material claim warning is a model flag, not an independently established factual error. Both copies of the exact deliberate voice sentence survived every arm.

| Arm | Question coverage | Outputs with claim warnings | Relationship clarity /4 | Coherence /4 | Voice fit /4 |
|---|---:|---:|---:|---:|---:|
| B | 72/72 | 0/12 | 3.750 | 4.000 | 4.000 |
| R | 71/72 | 2/12 | 3.792 | 3.917 | 3.917 |
| F | 70/72 | 2/12 | 3.667 | 4.000 | 3.958 |
| RF | 71/72 | 3/12 | 3.875 | 3.917 | 3.917 |

Descriptive retention differences: R−B = −1 point; RF−F = +1; F−B = −2; RF−R = 0. No significance testing or population effect is claimed. Chinese: B 24/24, R 24/24, F 23/24, RF 24/24; English: B 48/48 and each added arm 47/48. [Raw summary and all rows](summary.json), [blind key](blind-key.json), [generation prompts](prompts/), [responses](responses/), [judge prompts](judge-prompts/), [judgments](judgments/).

## Concrete failures and the decision

- `en-release`, second generation in R/F/RF: the source identifies owners as approvers. Edits identify owners as viewers but say only “approved requests”, omitting who approved them. This loses an action-specific actor even though that person appears elsewhere.
- `en-technical`, second generation in R/F/RF: “If your app returns a non-2xx response” became “only after/Only a non-2xx response”. That strengthens a sufficient condition into an exclusive trigger. R first/second and RF first also have a possible duplicate-delivery ambiguity; this is not treated as a definite instruction to ignore first deliveries.
- `en-onboarding`, RF first generation: “weekday delivery window” introduces a schedule distinction absent from the source. Its extra Preview reminder also repeats a completed step.
- `zh-comparison`, F first generation: the page's inability to predict success can attach grammatically to the administrator instead, and the page's exclusive display scope becomes unclear.

The small clarity differences do not outweigh the retention gate. The broad additions remain evidence files, not production instructions. The final pass is conditional on dense or qualified prose. It inventories each action's actor, preserves condition direction, puts a feature limitation's feature in subject position, grounds instructions as well as facts, and compares the final text after the scrub. It permits useful connectors and preserves established terms and intentional voice. It does not ban grammatical constructions or set a sentence-length quota.

## Exploratory revision checks

The [first revision plan](REVISION-PLAN.md) added two unseen writer cases, alongside five regressions. Its 14 outputs covered 83/84 question points but failed the gate: unsupported advice and a Chinese page/administrator ambiguity remained. [All first-revision evidence](revision/) is retained.

The [second correction plan](REVISION-2-PLAN.md) added explicit feature-subject and source-grounded-instruction checks, then reran the same seven cases twice. It covered **84/84** question points, with no material claim flags and both exact voice controls intact. [Final inputs/prompts/outputs/judgment](revision-2/) and [summary](revision-2/summary.json) are retained. This is a targeted retest informed by failures, not an independent benchmark or the winning arm of the original ablation.

Manual review checked the concrete actor, conditional, page-limitation and next-action relationships in the flagged cases against their sources. The final Chinese copies explicitly make the page the subject of its capability limit; English retries remain conditional without added “only”. One final webhook copy still relies on context to interpret “deliveries with the same event_id” as duplicates; the judge and reviewer do not classify that as a definite meaning change.

## Runtime and limitations

All 26 evaluated turns recorded Codex CLI `0.161.0`, actual startup model `gpt-6.1-sol`, no model override and no observed tool calls. The runner uses fresh ephemeral contexts, ignores user configuration and suppresses project-document loading, but a common installed-skill discovery catalog can remain. Cases share a context within each generation. The writer and judge share a model family. The judge sees the source and expected answers. No human readers, independent model, routing test, syntactic parser benchmark, or statistical generalization was run. These limits matter especially for the small Chinese sample.

The screenshot [paper](https://arxiv.org/abs/2610.29162) is a 21 October 2026 preprint. Its language-feature comparisons and small-model length-penalty intervention do not validate a writing skill. The [2025 randomized reader study](https://arxiv.org/abs/2505.01980) motivates measuring understanding but supplies no effect size for this change. Meaning checks draw on the [TACL reading-question framework](https://aclanthology.org/2024.tacl-1.24/). See the research note for the primary-source search and its boundaries.

## Implementation and verification

The maintained common skill and its changelog were edited. The reviewed Codex patch was regenerated, its source hash refreshed, and the complete distributable rebuilt under Linux so executable bits and line endings match CI. Adapter discovery and the existing voice/scrub rules remain as in the baseline.

- 1,951 unit tests ran across eight existing suites: 1,950 passed, 1 optional-shell test skipped. Two initial suites failed solely because `jq` was missing; task-local extracted packages resolved that dependency and both suites passed on retest. No system package installation was made. [Initial logs](validation-linux.json), [dependency retest](validation-dependency-retest.json), [final acceptance](validation-final.json).
- Catalog/router/invocation, frontmatter, punctuation, positioning, lineage, disclosure, Python compilation, shell-mock syntax, generated-package identity and publication checks passed.
- Next.js 15.5.22 built 252 static pages. The modified skill's link-preview card, both editions of all 24 Markdown twins, and all font character/hash checks passed against the local built site.
- Skill Creator validation and Skill Quality Reviewer's structural/link checks passed. The clean-cache offline-font build also passed, generating 252 pages with the repository Google-font mock configured. Remote Actions did not start; attempting to enable ci.yml returned HTTP 404 for the workflow endpoint, even though the workflow file is present in the fork. No remote-CI pass is claimed.

Reproduce with an authenticated Codex CLI (uses account quota): `python tests/writing-for-humans/run_ablation.py prepare`, then `generate`, `judge`, `summarize`. This reconstructs the frozen upstream pilot even after the skill changes. For the final revision use `python tests/writing-for-humans/run_revision.py prepare --round revision-2`, then the same three stages with `--round revision-2`. Existing response hashes are checked and successful saved runs are reused; use a separate checkout/evidence location for a fresh rerun. Authentication and private runtime transcripts are excluded from public artifacts.
