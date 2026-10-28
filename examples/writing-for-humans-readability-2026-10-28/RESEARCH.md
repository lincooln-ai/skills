# Research note: readability without information loss

Checked 2026-10-28 (Asia/Shanghai). Targeted primary-source search, not a systematic review. No paid API calls or experiments were performed for this note. Recommendations below are hypotheses to test, not established effects of a writing skill.

## Screenshot paper: verified evidence and boundaries

[Clark, Martin & Mahowald, arXiv:2610.29162v1](https://arxiv.org/html/2610.29162v1), submitted 21 October 2026; [version history](https://arxiv.org/abs/2610.29162) lists only v1. The public record inspected is a preprint.

Its English corpus covers 14 model variants: 120 chats, 45 coding sessions, and 980 essays. Comparisons use genre-approximating human corpora and English–Japanese–English round-trip paraphrases. The six feature groups are rare n-grams, dependency length, object relative clauses, stacked nouns, denominal verbs, and inanimate subjects with usually animate-subject verbs. Effects vary by model and setting; essay effects are generally smaller. Features are measured with parsers and, for two groups, automated classification—not reader judgments.

The intervention is GRPO fine-tuning of Qwen2.5-1.5B-Instruct under a token-length penalty, using structured-event verbalization. It supports a compression mechanism in that setup; it does not identify the actual post-training cause in frontier models.

The paper reports no human reading-time or comprehension experiment, no writing-skill prompt intervention, and no Chinese evaluation. It does not justify banning every instance of these constructions. Denominal verbs mean **nouns used as verbs**; they are not nominalizations. The inspected paper did not provide a study-code/data repository link; this does not establish that none exists elsewhere.

## Complementary primary sources

- [Guidroz et al., arXiv:2505.01980v1, 4 May 2025](https://arxiv.org/abs/2505.01980): a randomized study of 4,563 participants and 31 texts across six domains found an overall 3.9 percentage-point improvement in comprehension questions for their simplified texts. This is direct reader evidence for that intervention, not the proposed skill. The [Google publication page](https://research.google/pubs/llm-based-lossless-text-simplification-and-its-effect-on-user-comprehension-and-cognitive-load/) retains “Lossless” in its title; the arXiv title does not. Neither title means every fact is guaranteed preserved.
- [Meng & Yang, SIMPLE, EMNLP 2026](https://aclanthology.org/2026.emnlp-main.2306/): 12 open-weight models across Wikipedia, medical, scientific, and news text. Prompting and fine-tuning have domain-dependent results; prompted simplification has limited difficulty control, and syntax/discourse remain important gaps. This argues against assuming one generic simplification prompt works everywhere.
- [Li & Xu, UIDPO, arXiv:2610.14578v1, 10 October 2026](https://arxiv.org/html/2610.14578v1): preference training over Llama3-8B-Instruct and Mistral-7B-Instruct-v0.2, evaluated on model-judged instruction-following benchmarks. The proposed objective permits global information-density variation while discouraging abrupt local changes. These are not human comprehension measurements. [Author code](https://github.com/CLCS-SUSTech/UIDPO).
- [Reinhart et al., PNAS 2025](https://doi.org/10.1073/pnas.2422455122): parallel corpora show grammatical/rhetorical differences across model families and instruction tuning, including a noun-heavy style. This supports testing beyond word blacklists; it does not prescribe a universal style or prove comprehension loss.
- [Agrawal & Carpuat, TACL 2024](https://aclanthology.org/2024.tacl-1.24/): evaluates meaning preservation with contextual reading-comprehension questions. Even the strongest supervised system leaves at least 14% of questions unanswerable from simplified content. This is a useful evaluation method, not a current-model performance estimate.

## Candidate skill changes — our inference to test

1. Optimize for a reader recovering the intended meaning on one pass. Allow a few extra words when they identify an actor, make a relation explicit, or preserve a condition.
2. Add a focused clarity pass: unpack ambiguous noun stacks; keep subjects near their verbs; restore connective/relative words where useful; prefer familiar literal wording when a novel verb or personification hides the mechanism. Established terms and intentional voice remain valid exceptions.
3. Add a separate preservation pass: compare actors, actions, quantities, dates, negation, scope, causal relations, uncertainty, and exceptions against the input. Retain necessary technical terms and explain them if the audience needs it.
4. Do not equate natural writing with fabricated first-person experience. Derive voice from supplied material; label illustrative examples as hypothetical where needed.
5. For Chinese, use equivalent reader-oriented principles provisionally, with Chinese examples and separate evaluation. Do not transplant English parser thresholds or grammar bans.

## Minimal ablation design — proposed, not run here

Use a fixed source revision, identical task inputs/model/settings, isolated fresh contexts, and a preregistered rubric. Conditions: original skill; original + clarity pass; original + preservation pass; original + both. Include English dense exposition, domain terms with necessary qualifications, and Chinese communication as separate cases. Keep word limits equal across conditions without forcing extreme brevity.

Primary outcomes: whether source-grounded comprehension questions remain answerable and correct; retention of critical qualifications; unsupported additions; observable ambiguity about who did what. Secondary descriptions: sentence length, noun-stack counts, lexical metrics, prompt overhead. These secondary proxies cannot establish human ease.

Record raw prompts/outputs and exact runtime/model identifiers. Blind the condition labels during scoring; for model judging, repeat with swapped presentation order and report disagreements. With small samples or one model, report directional pilot evidence only. Do not claim statistical significance or human comprehension improvement without the corresponding study.
