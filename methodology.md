# Methodology

## Overview

We ask whether the partisan geometry encoded in LLM attention activations tracks real issue partisanship as measured by survey responses. For each of a set of political topics, we compute two quantities: activation-space partisan divergence, which is the Mahalanobis distance between Democrat and Republican centroids in a per-head PCA-reduced activation space, and survey partisanship, which is the normalized partisan gap in GSS response means. The main outcome is the Pearson correlation between these two vectors across all topics.


## Survey Partisanship Measure

For each topic, we compute the survey-measured partisan gap from the General Social Survey (GSS) 2021–2024 cumulative dataset. The measure is the absolute difference between the mean response code of Democrats and the mean response code of Republicans on that topic, divided by the range of the response scale (maximum minus minimum code). This normalization puts every topic on a 0-to-1 scale regardless of whether the original question used a 2-point, 5-point, or 7-point scale. Topics are included only if at least 100 Democrats and 100 Republicans responded (at least 200 total).

Respondents are classified by the GSS party identification variable, which runs on a 7-point scale from strong Democrat (0) to strong Republican (6). We code values 0, 1, and 2 as Democrat; values 4, 5, and 6 as Republican; and exclude pure independents (value 3) from all analyses.

We analyze two categories of GSS topics. Public issues (126 topics) cover policy, government, and social questions — abortion, gun control, environmental regulation, government spending, and so on. Eight topics are excluded whose wording frames questions in terms of explicit racial comparisons in a way that would conflate partisan attitude with world knowledge. Private life (73 topics) covers personal behavior, religious practice, and lifestyle — church attendance, sexual attitudes, family structure, moral views. Five topics are excluded due to ambiguous framing.


## Activation Extraction

We register forward hooks on the output-projection layer of each transformer layer, capturing the input to that layer — the pre-projection concatenated head outputs — at the last token position of each prompt. This gives us, for every prompt, a tensor of shape (layers × heads × head-dimension). Capturing the input to the output projection rather than its output allows clean decomposition into individual attention heads, because the projection itself mixes heads together.

After processing all prompts in batches, we concatenate activations into a four-dimensional array of shape (subjects × layers × heads × head-dimension), where head-dimension is typically 128.


## Per-Head Mahalanobis Distance

For each attention head independently, we take the slice of the activation array corresponding to that head and reduce its dimensionality with PCA, keeping at most 15 principal components. PCA is fit on all subjects pooled together.

We then compute centroids for Democrats and Republicans separately. Each centroid is simply the mean of the PCA-projected activations across all subjects in that party. As a robustness check we also compute coordinate-wise medians instead of means.

The covariance structure is estimated as the average of the within-party covariance matrices, with a small regularization term added to the diagonal to ensure invertibility. The Mahalanobis distance between the two centroids is then the generalized distance scaled by this pooled covariance — intuitively, the number of standard deviations separating the two parties in the activation space of that head, accounting for the shape of the distribution rather than treating all directions equally.

This produces one distance value per head per topic. We summarize across all layers and heads by taking the mean, producing a single partisan-divergence score per topic. We also compute a middle-layer variant that averages only over the central 10% of layers; empirically this performs worse for deep models, so we use the all-layer mean as the primary metric.


## Simulation Methods

We test two methods for constructing the prompts fed to the model.

### Politician Simulation

Subjects are all 550 members of the 116th U.S. Congress with valid name and party affiliation from the DW-NOMINATE dataset. One prompt is generated per politician per topic. Each prompt is prefixed by a system message framing the politician-simulation task; the system message was varied across nine phrasings and results were robust across all variants (spread < 0.025; see Appendix). Three user-turn conditions are tested. The rhetorical condition asks the model to generate a statement by the named politician on the topic. The stance condition asks what the politician's position is on the topic. The survey condition asks how the politician would respond if asked about the topic in a national survey. The topic slot is filled with a natural-language clause taken directly from the GSS question wording.

The three conditions test distinct representational hypotheses: rhetorical tests whether political speech generation is organized by partisan identity; stance tests factual belief-attribution; survey tests whether the model tracks response-style differences between parties.

### Demographic Simulation

Subjects are GSS 2021–2024 respondents who gave a valid response to the relevant topic and are classified as Democrat or Republican. We use two sampling settings: a stratified condition drawing a 10% sample balanced on political views, age, education, race, sex, and income (requiring at least 10 respondents per party); and a non-stratified condition with no balancing. Each respondent is described by up to 83 demographic fields, where numeric codes are mapped to human-readable strings using Stata value labels. Fields are concatenated with periods as separators and randomly shuffled per topic (seeded by the topic name) to prevent ordering artifacts.

We also run a focused version using only five core attributes — age, sex, race, religion, and occupation — to examine how model behavior changes when only the most basic demographic signals are provided.

The prompt tells the model it is simulating the views of an American, then presents the respondent's demographic profile and asks how that person would answer the survey question. To prevent leakage, any demographic field that coincides with the current survey topic is excluded from that respondent's profile.


## Alignment Metric

For each combination of simulation method, prompt condition, and layer-selection variant, we correlate the vector of per-topic partisan divergence scores with the vector of per-topic GSS survey partisanship scores across all topics in a category (126 public or 73 private). The primary statistic is Pearson r; Spearman rank correlation is reported as a robustness check.

A high correlation indicates that the topics where LLM activations show the largest partisan separation are the same topics where survey respondents show the largest partisan gaps. When politician-simulation correlations exceed demographic-simulation correlations, it suggests that the model's parametric knowledge about named political actors more faithfully mirrors the real partisanship landscape than its ability to infer partisan positions from demographic attributes.


## Cultural Terrain Discovery

The preceding analyses validate the activation-based partisan-divergence measure against known GSS topics. We additionally apply the same measure in an unsupervised discovery mode: given a large corpus of novel opinion clauses not drawn from any existing survey instrument, which topics does the model flag as highly partisan in activation space? High-scoring topics are candidates for future survey items that existing instruments do not cover.

### Corpus construction

We assembled approximately 1.3 million candidate clauses from four source collections.

| Source | Clauses | Content |
|---|---|---|
| Merged (multi-source) | 477,343 | Wikipedia articles, Reddit opinion posts, news headlines, memes, party platforms, ballot measures, book and film titles, ATUS time-use labels |
| Congress bills | 359,302 | U.S. Congress bill titles and CRS report summaries |
| Web of Science | 424,445 | Academic abstract text, restricted to social-science and humanities journals |
| Reddit (partisan) | 47,018 | Posts from politically-aligned subreddits, filtered to surface-apolitical wording |

Raw items from each source were converted into natural-language opinion clauses of the form "the [noun phrase]" or "whether [subordinate clause]", matching the format of GSS topic wording. Clause generation used an instruction-tuned model prompted to reframe each item as a topic on which people can hold opposing views. Items that could not be meaningfully reformulated — purely factual content, data artefacts — were discarded.

### Scoring

Each clause is scored using the identical pipeline as the main partisan-divergence analysis: the same 550 Congress members, the same stance prompt template, the same o_proj activation extraction, PCA reduction (k = 15), and mean Mahalanobis distance averaged across all layers and heads. The scoring model is dolphin-2.9.3-llama-3-8b with the roleplay system message. Because the same activation geometry correlates r ≈ 0.74 with GSS partisanship on known topics, the mean Mahalanobis score serves as a proxy for expected survey-measured partisanship on the novel clauses.

The top-scoring clauses from each corpus are retained as candidate survey items. Because the scoring is unsupervised — no ground-truth partisanship labels are used — the discovered topics represent the model's implicit map of the partisan cultural terrain, extending well beyond the policy and lifestyle domains covered by the GSS.

### Surprise analysis: latent partisan structure

A high polarization score is not itself surprising if the clause is overtly political. The more scientifically interesting case is a topic that is more polarized than its surface content predicts — latent partisan structure hidden behind non-political language.

**Stage 1 — Political content scoring.** Each clause is embedded with a 300M-parameter Gemma3-backbone sentence encoder (mean-pooled, L2-normalised, 768-dimensional). Political content is operationalised as cosine similarity to a set of 234 Ballotpedia ballot-measure subtopics spanning ten parent categories (Budget and Tax, Criminal Justice, Education, Elections, Environment, Governance, Infrastructure, Labour and Healthcare, Military, and Social Issues). These anchor similarities form a 234-dimensional feature vector of political salience.

**Stage 2 — Per-corpus OLS and residual.** Within each corpus separately, we regress the mean Mahalanobis score on the 234 anchor-similarity features using ordinary least squares with 5-fold cross-validation. Fitting per-corpus rather than pooled avoids confounding the baseline polarization level across sources — congressional bill clauses are almost universally political; AITA interpersonal-dilemma posts are almost universally apolitical. The surprise score for each clause is the OLS residual: actual minus predicted polarization. A large positive residual indicates that the model's activation geometry flags the topic as politically contentious even though the clause's semantic content offers little indication of political relevance.

**Non-political filter.** To surface genuinely surprising topics rather than noisily phrased political ones, we restrict attention to clauses whose predicted polarization falls below the corpus mean. Within this filtered set, clauses are ranked by surprise score descending. We retain the top 1,000 per eligible corpus (AITA and Reddit; WoS, Wikipedia, and Congress are too uniformly political or too sparse after filtering).

**Out-of-sample validation.** To test whether the surprise score generalises, we hand-curated 21 contemporary topics not drawn from any scraped corpus — including the tradwife lifestyle, raw milk consumption, fluoride removal from drinking water, and similar culturally-charged but explicitly non-partisan issues. We first predict their polarization using the per-corpus Ridge model, then measure actual mean Mahalanobis scores using the same pipeline, and compare predicted versus actual to confirm the surprise decomposition transfers to genuinely novel clauses.


## Question Fundamentalness

A complementary analysis asks which survey topics are structurally central to the opinion landscape — topics whose activation geometry is most discriminative and whose survey responses are most predictive of other responses. The LLM-side measure is simply the all-head-average Mahalanobis score defined above.

On the survey side, we compute five independent structural measures from the GSS response matrix (11,066 respondents, 98 questions). The first is mutual information: the normalized mutual information between each question pair, summed across all pairs for each question. The second is predictive power: the out-of-sample accuracy when predicting each question's responses from all others. The third is network centrality: a graph is built with questions as nodes and edge weights equal to absolute pairwise correlations, and fundamentalness is measured by degree, betweenness, closeness, and eigenvector centrality. The fourth is dimensionality reduction: loading magnitudes on the leading principal components of the response matrix. The fifth is tree structure: a Chow-Liu algorithm fits an optimal dependency tree on pairwise mutual information, and fundamentalness is proximity to the tree root.

The main outcome is the correlation between the LLM activation score and each of these survey-side fundamentalness measures across shared topics.
