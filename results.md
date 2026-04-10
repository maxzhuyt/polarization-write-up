# Results

## Politician Simulation

### Public Issues

We correlate activation-space partisan divergence with GSS survey partisanship across all 126 public-issue topics for 17 models and three prompt conditions. Three patterns emerge.

**Scale and alignment.** Larger models achieve higher correlations. The two biggest models tested — Qwen2.5-72B and Llama-3.3-70B — both surpass r = 0.65 on public issues (best conditions: 0.708 and 0.652 respectively). Standard instruct models in the 24–32B range (Mistral-Small-24B and Qwen3-32B) peak around r = 0.63. Smaller 3–9B models are noisier and more sensitive to which prompt condition is used, but the best of them still reach r = 0.65: Gemma-2-9b instruct achieves r = 0.647 on the rhetorical condition, and Llama-3.1-8B instruct reaches r = 0.645 on the survey condition.

**Prompt framing.** For models above roughly 30 billion parameters, the stance condition — asking the model what a politician's position is — tends to produce the highest or near-highest correlation. At smaller scales, the picture is noisier: for Qwen3-4B models, stance and survey conditions both work well depending on the model variant, while the rhetorical condition tends to underperform. DeepSeek-R1-32B is an outlier in the large-model group: its best correlation (0.538, stance) is substantially below other large models, suggesting that the reasoning-oriented training makes political-stance retrieval less reliable in activation space.

**Uncensored fine-tuning.** The strongest result in the study comes from Dolphin3.0-Mistral-24B (r = 0.741, stance with roleplay system message; see Appendix), a fine-tune of Mistral-Small-24B with RLHF safety alignment removed. This surpasses every safety-aligned model tested, including Qwen2.5-72B (r = 0.708) and Llama-3.3-70B (r = 0.652), which have three times the parameters. The comparison to its Venice variant is especially informative: Dolphin-24B-Venice, a separately fine-tuned 24B Mistral model with partial safety alignment restored, reaches only r = 0.635 at the same scale — a 0.106-point gap. Yi-34B-dolphin (r = 0.723, stance) is the second-strongest and the highest result among the main 17-model comparison. The uncensored Llama-3-8B dolphin fine-tune (r = 0.662) also outperforms its standard instruct counterpart (r = 0.645), extending the pattern to smaller scale. Two additional large Dolphin models — Llama-3-70B-dolphin (r = 0.625) and Qwen2-72B-dolphin (r = 0.575) — fall below the 34B Dolphin models despite their larger scale, confirming that the RLHF-suppression benefit does not compound with size and varies across base architectures.

The interpretation is that RLHF safety training attenuates the partisan structure encoded in per-head activations during pretraining, without erasing the underlying political knowledge. Removing that training allows the partisan geometry to manifest more clearly.

### Private-Life Topics

On the 73 private-life topics — religious practice, sexual attitudes, moral views, family structure — alignment is generally lower than on public issues, with instruct models averaging around r = 0.54 across models with complete data. This is consistent with private behavior being less explicitly politicized in the text corpora on which these models were trained. Base models average around r = 0.34 on private topics.

That said, several models match or exceed their public-issue scores on private life. Llama-3.1-8B instruct achieves its best correlation anywhere in the study on private-life topics: r = 0.693 (stance), compared to r = 0.645 on public issues. Gemma-2-9b instruct reaches r = 0.647 on the rhetorical condition, essentially matching its public-issue score. The instruct-over-base advantage is even larger on private topics than on public ones, suggesting that instruction fine-tuning helps models draw on cultural sorting patterns (not just explicit policy knowledge) when simulating personas.

The uncensored models again lead at the top. Among models with all three conditions tested, Yi-34B-dolphin achieves the highest stance correlation on private topics (r = 0.709). Dolphin3.0-Mistral-24B reaches r = 0.667 (stance, roleplay system message), comparable to Yi-34B-dolphin at fewer parameters. The single highest private-topic correlation among safety-aligned models belongs to Qwen2.5-72B under the survey condition at r = 0.719 — a notable outlier given that this model's stance score on private topics is only r = 0.517. DeepSeek-R1-32B shows an unusually steep drop between its stance (r = 0.678) and survey (r = 0.230) conditions on private topics, a sharper version of the prompt-framing sensitivity seen on public issues.


## Demographic Simulation

### 5-Trait Profiles

In the basic demographic simulation, we replace politician names with prompts describing real GSS respondents using five core demographic fields. Results are substantially weaker than in the politician simulation. Mistral-7B-v0.2 produces the strongest demographic-simulation correlations in both categories (around r = 0.31 on both public and private topics). Gemma-2-9b yields modest correlations (r = 0.23 on public, r = 0.11 on private). Llama-3.1-8B shows weak or near-zero alignment, including a negative correlation on private topics. Llama-3.1-70B achieves a notably high r = 0.52 on private topics while showing near-zero alignment on public issues, a pattern that warrants further investigation.

The gap between politician and demographic simulation indicates that models encode partisan structure more reliably through their parametric knowledge of named political actors than through inference from demographic correlations, even when those correlations are provided explicitly in the prompt.

### 83-Trait Profiles (Uncensored Models)

We also ran demographic simulation with an expanded 83-trait profile that adds lifestyle, belief, and behavioral attributes to the core sociodemographic fields, using uncensored dolphin fine-tunes. Results improve substantially over the 5-trait baseline.

The strongest demographic-simulation result in the entire study comes from the dolphin-tuned Mistral-7B (dphn-mistral7b) on public issues with the stance prompt: r = 0.505. This model also maintains unusually consistent correlations across all three prompt conditions on private-life topics, ranging from r = 0.446 to r = 0.455 — suggesting that when the model is both uncensored and given rich demographic context, prompt framing matters less. The dolphin-tuned Llama-3-8B (dphn-llama8b) reaches r = 0.441 on private topics (stance) and r = 0.425 on public issues (stance), a dramatic improvement over the standard Llama-3.1-8B in the 5-trait setup (r ≤ 0.132 on public issues).

The dolphin-tuned Qwen-3B (dphn-qwen3b) produces near-zero correlations across all conditions and both topic categories (all below r = 0.12 on public issues, near zero on private), indicating that model capacity is a necessary precondition: even a rich 83-trait persona profile cannot compensate for insufficient scale.

Taken together, the demographic simulation results suggest that the gain from uncensored fine-tuning is not limited to the politician simulation. Richer context and the removal of RLHF restrictions together push demographic-simulation alignment toward — though still below — the levels achieved by politician prompts.


## Cultural Terrain Discovery

Applying the activation-based partisan-divergence score to 1.3 million unseen clauses (Merged, Congress, WoS, and Reddit corpora), we identify candidate topics that the model flags as highly partisan but that existing survey instruments do not cover. The highest-scoring discovered items cluster around electoral politics and recent policy controversies — topics with explicit partisan framing in their source text. More notable are high-scoring items from sources with no overt partisan labelling, such as WoS abstracts and Reddit opinion posts, where the model's activation geometry detects latent partisan structure in topics that do not name parties or politicians. Full rankings by corpus are in `discovered_polarization.csv`.

### Surprise analysis

Regressing the mean Mahalanobis score on 234 Ballotpedia anchor similarities within each corpus, we find that political surface content explains a modest share of variance in activation-based polarization. Cross-validated R² ranges from near zero for the AITA interpersonal-dilemma corpus to moderate for the congressional bills corpus, where most clauses are explicitly political. This low explained variance is itself informative: the majority of the partisan-divergence signal is not attributable to the overtness of political language.

Filtering to below-average predicted polarization and ranking by surprise score, the top candidates from AITA and Reddit include everyday interpersonal and lifestyle topics — particular child-rearing practices, dietary preferences, relationship norms — that carry no explicit partisan vocabulary but are nonetheless assigned large Democrat–Republican activation distances by the model. These items are candidates for survey development in domains where standard political instruments have no coverage.

The out-of-sample validation on 21 hand-curated contemporary topics (tradwife lifestyle, raw milk consumption, fluoride removal, and related) confirms that the per-corpus model produces calibrated predictions: topics predicted to have low political content but measured with high actual polarization tend to be issues where cultural and lifestyle signals have become partisan markers in recent years, even though explicit party labels are absent from their framing.
