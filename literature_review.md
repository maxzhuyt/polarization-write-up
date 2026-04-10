# How This Study Situates in the Landscape of Public Opinion Research

## A Comprehensive Report for Introduction and Literature Review

---

## 1. The Opening Move: What Problem Are We Solving?

The paper should open not with LLMs but with a problem that public opinion researchers already recognize as urgent: **the measurement of partisan divergence is expensive, slow, and topically constrained.** The entire infrastructure of survey-based opinion research --- from the GSS to the ANES to the CCES --- covers at most a few hundred topics, fielded at irregular intervals, at costs that make comprehensive coverage of the cultural terrain impossible. Yet the political landscape is vastly larger than what surveys can map. Campaigns spend millions on focus groups to discover which of thousands of potential wedge issues will resonate. Scholars know that partisan gaps exist on topics they have never measured. The question that motivates the paper is whether there exists a scalable, low-cost instrument that can approximate survey-measured partisan divergence across an arbitrarily large set of topics --- and, crucially, whether the structure of such an instrument can be validated against existing survey data.

The proposed instrument is the internal representational geometry of large language models. The key claim is not that LLMs *replace* surveys, but that they encode --- in their activation patterns when prompted with elite political names --- a map of the partisan opinion landscape that correlates at r ~ 0.75 with the map produced by decades of survey research. This makes the tool useful for **discovery**: identifying topics where partisan gaps likely exist but have never been measured, at a marginal cost of essentially zero per topic.

---

## 2. The Intellectual Lineage: Five Conversations This Paper Enters

### 2A. The Measurement of Latent Opinion (Stimson; Caughey & Warshaw; Fowler et al.)

The deepest intellectual ancestor of this work is the tradition of extracting latent opinion constructs from noisy survey data. **Erikson, MacKuen, and Stimson (2002)** developed **Policy Mood** --- the aggregate of hundreds of survey items into a single time-varying signal about preferences for government activity. Their Dyad Ratios algorithm solved the sparse-data problem by using ratios of liberal responses across time points, producing a measure that captures "a single underlying disagreement over the scope of governmental activity" explaining 38% of variance across 133 policy series. The insight was methodological and conceptual: individual survey responses are noisy (following the Zaller-Feldman model), but aggregation reveals coherent structure.

**Caughey and Warshaw (2015)** extended this logic with a group-level IRT model that estimates latent policy liberalism at the state level from 47 questions across 350 surveys and 570,000 respondents. Their innovation was to show that a respondent need answer only *a single question* to contribute to a latent trait estimate, opening vast amounts of sparse historical data to scaling.

**Fowler et al. (2023)** developed a mixture model that decomposes respondents into Downsians (73%, with preferences on a single ideological dimension), Conversians (21%, with genuine but non-spatial views), and Inattentives (6.5%, giving essentially random responses). This work established that most Americans *do* have views well-described by a single dimension, and that these moderate and cross-pressured respondents drive electoral outcomes.

**Our paper enters this conversation by proposing a fundamentally different source of data for the same inferential task.** Where Stimson uses ratios across survey items, Caughey and Warshaw use IRT on survey responses, and Fowler et al. use mixture models on binary policy questions, we use the activation geometry of LLMs prompted with elite political names. The underlying question is the same --- what is the structure of partisan divergence across issues? --- but the data source is the model's parametric encoding of public discourse rather than direct survey responses.

The key connection to Stimson's Mood is this: just as Mood extracts a common factor from the covariation of survey items over time, our Mahalanobis distance extracts a common factor from the covariation of attention-head activations across political actors. Both are trying to measure the same latent construct --- the degree to which a topic divides the parties --- but from different observational vantage points. The r ~ 0.75 correlation between our measure and GSS partisan gaps is the empirical validation that these vantage points converge.

The connection to Caughey and Warshaw is methodological: both approaches exploit the fact that sparse, noisy individual-level data can yield meaningful aggregate signals when properly pooled. In their case, pooling is across survey respondents within demographic-geographic groups; in ours, pooling is across attention heads and congressional names within party labels. Both approaches convert high-dimensional, noisy observations into a single scalar summary of partisan position on each topic.

### 2B. What Survey Responses Actually Measure (Zaller & Feldman; Achen; Alvarez & Brehm)

A foundational question for our paper is: **what are we actually measuring, and how does it relate to what surveys measure?**

**Zaller and Feldman (1992)** established that survey responses are not passive readouts of fixed attitudes but "samples from a host of competing considerations." A respondent who is asked about education spending may draw on considerations about education (favorable), about government spending (unfavorable), or about federal power (unfavorable) --- "and all these reports can be genuine." The Response Axiom states that people "average across the considerations that happen to be salient at the moment of response." This means that any single survey response is a noisy draw from a distribution of potential responses, and the distribution is shaped by whatever considerations have been recently primed.

**Achen (1975)** demonstrated that the low test-retest correlations Converse reported (0.30--0.40 for policy items) are substantially attributable to measurement error in the survey instruments themselves, not to genuine voter incoherence. When corrected for attenuation, correlations jump to 0.70--0.99. The implication: voters are more stable than surveys suggest, but the instruments are imprecise.

**Alvarez and Brehm (2002)** further refined this by distinguishing three sources of response variability --- ambivalence (irreconcilable value conflict), uncertainty (resolvable with information), and equivocation (reinforcing predispositions) --- each producing a distinct signature in heteroskedastic choice models.

**Our paper must be explicit about what LLM activations encode relative to these distinctions.** When we prompt a model with "Respond as if you are [Senator X] on [topic Y]" and extract the activation pattern, we are not measuring any single individual's survey response. We are measuring the model's parametric encoding of the *rhetorical and discursive landscape* surrounding that politician-topic pair. This encoding is trained on vast corpora of political text --- speeches, news coverage, social media, legislative records --- and reflects the aggregate statistical regularities of how political figures are associated with positions in public discourse.

This means our measure conflates several things that the survey tradition carefully distinguishes:

- **Rhetorical positioning** (what politicians say about an issue)
- **Attributed opinion** (what the public discourse associates with a politician on an issue)
- **Affective valence** (the emotional charge of the politician-topic association)
- **Policy substance** (the actual policy content of the position)

We cannot separate these. An LLM activation is not a survey response; it is a compressed summary of everything the model learned about the statistical co-occurrence of a political name and a topic in its training corpus. This is a limitation but also a strength: the measure captures the *full discursive footprint* of partisan association, which may be more comprehensive than any single survey item.

The crucial empirical finding is that despite this conflation, the activation-based measure correlates at r ~ 0.75 with the survey-based partisan gap. This suggests that the components we cannot separate (rhetoric, attribution, affect, policy) are themselves correlated with the survey-measured partisan divide --- which is what we would expect if public discourse is organized around the same partisan axis that survey responses measure. The model is, in effect, picking up on the same "single underlying disagreement" that Stimson identified as Policy Mood, but from the vantage point of how that disagreement is encoded in the structure of public language.

### 2C. Party Identification as Social Identity (Green, Palmquist & Schickler; Mason; Iyengar et al.)

The party-week readings establish that partisan identity is a **social identity** (Green, Palmquist & Schickler 2002) that has become a "**mega-identity**" (Mason 2018) as multiple social identities --- race, religion, class, geography --- have aligned with partisanship. **Iyengar, Sood, and Lelkes (2012)** demonstrated that affective polarization has grown dramatically since the 1960s and is "inconsistently related to policy preferences," suggesting that partisan divisions are driven by identity dynamics rather than (or in addition to) ideological disagreement.

**Dias and Lelkes (2022)** provided the experimental resolution: partisan identity is the "principal mechanism" of affective polarization, with policy preferences factoring in "largely by signaling partisan identity." Their distinction between "party-branded" and "unbranded" issues showed that party labels do much of the work in generating interpersonal affect --- when issue positions are stated directly (removing the identity signal), the partisanship effect on affect drops by 57%.

**This literature is crucial for understanding what our method captures and why it works.** When we prompt an LLM with a congressional name, we are activating exactly the kind of high-information partisan signal that Dias and Lelkes identified as the "principal mechanism" of affective polarization. Congressional names are the ultimate "party-branded" stimuli: they carry dense associations with partisan group membership, policy positions, rhetorical style, and cultural affiliation. The fact that politician-simulation produces substantially higher correlations with survey partisanship than demographic-simulation (r ~ 0.72 vs. r ~ 0.31--0.50) is directly consistent with Dias and Lelkes's finding that party identity signals are more powerful organizers of partisan structure than demographic attributes.

This also connects to Green et al.'s finding that partisan stereotypes persist for decades (Democrats = "working class" in both 1951 and 1995; Republicans = "big business" and "rich people" throughout). If LLMs are trained on text corpora spanning decades, they would encode these stable stereotypic associations, which in turn organize the activation geometry along partisan lines. The stability of partisan group images that Green et al. documented is precisely the kind of regularity that would be captured in model parameters.

Mason's "mega-identity" concept is particularly relevant for the Cultural Terrain Discovery component of our study. If partisan identity has absorbed racial, religious, class, and cultural identities, then partisan activation distances should be detectable not only on explicitly political topics but also on lifestyle, consumption, and cultural topics that have become markers of partisan affiliation. This is exactly what the surprise analysis finds: topics like child-rearing practices, dietary preferences, and relationship norms --- carrying "no explicit partisan vocabulary" --- are nevertheless assigned large partisan activation distances. These are the topics that Mason's framework predicts would become partisan as social sorting progresses.

### 2D. The Causal Limitation: What We Cannot Do (Niemi & Jennings; Luskin, McIver & Carmines; Gadarian et al.)

The party-week readings also highlight an important limitation of our approach. **Niemi and Jennings (1991)** and **Luskin, McIver, and Carmines (1989)** studied *how* partisanship forms and changes --- through intergenerational transmission, issue proximity, and the interaction of issue type (hard/easy) with change type (conversion/unrealization/mobilization). These are causal questions about the *mechanisms* that produce partisan divergence. Our method cannot address them.

Luskin et al.'s finding that different types of partisan change are driven by different types of issues --- economic issues drive conversions while racial issues drive unrealizations --- is a mechanistic insight about the *process* by which partisan gaps form. Our measure captures the *outcome* of that process (the size of the gap on each topic) but says nothing about how the gap got there. We can tell you that abortion is highly partisan in activation space, but we cannot tell you whether that partisanship was driven by elite polarization, social sorting, or issue-specific events like *Roe v. Wade* (which Erikson, MacKuen, and Stimson identify as responsible for abortion's unique trajectory).

Similarly, **Gadarian, Goodman, and Pepinsky (2021)** showed that partisanship shaped COVID health behavior from the pandemic's earliest days, with partisan differences dwarfing income and education effects. Their finding that "partisanship was the best predictor of differences in behaviors, attitudes, and preferences than anything else that we measure" validates the idea that partisan identity organizes a vast range of attitudes and behaviors. But their study could trace the *timing* of partisan divergence and test whether it preceded or followed elite messaging. Our method, working from a static language model, cannot distinguish between topics where partisan gaps are driven by elite cues and topics where they emerge from bottom-up sorting.

**The paper should be explicit about this causal agnosticism.** We are proposing a measurement tool, not a causal model. Our measure tells you *how large* the partisan gap is on any topic (within the model's representational space), but not *why* the gap exists, *when* it formed, or *what* mechanisms sustain it. These are questions that only panel data, experiments, and longitudinal survey designs can address --- the very tools that the readings in this syllabus deploy. Our contribution is to the *first stage* of the research pipeline: identifying where partisan gaps exist, including in domains that have never been surveyed.

### 2E. The Dimensionality Question (Converse; Stimson; Campos & Federico)

**Converse (1964)** established that mass belief systems are weakly constrained: knowing one attitude poorly predicts others. **Stimson** found that a single dimension (Policy Mood) explains 38% of variance across domestic policy items, with a weaker second dimension (Social Compassion) at 16% and abortion standing alone. **Campos and Federico (2026)** showed that affective polarization itself is multidimensional --- othering, aversion, and moralization have distinct correlates and sometimes opposing democratic implications.

Our measure is unidimensional by construction: for each topic, we compute a single scalar (mean Mahalanobis distance between Democratic and Republican centroids). This captures the magnitude of partisan divergence but not its structure. We cannot distinguish, for a given topic, whether the partisan gap reflects policy disagreement, affective hostility, identity signaling, or rhetorical convention. These are the kinds of distinctions that Campos and Federico's multidimensional APS scale was designed to capture.

However, the fact that our unidimensional measure correlates at r ~ 0.75 with the unidimensional GSS partisan gap suggests that the *magnitude* of partisan divergence is itself a coherent construct --- one that is robustly captured regardless of whether the underlying mechanism is ideological, affective, or identity-based. This is consistent with the finding across the literature that ideological divergence, affective polarization, and identity sorting are empirically correlated even if theoretically distinct.

---

## 3. The Positive Case: What This Method Adds

### 3A. Scalability: From Hundreds to Millions of Topics

The most consequential advantage is scale. The GSS covers roughly 200 topics in a given wave. The ANES covers fewer. The CCES covers more but is still limited to a fixed battery. Our method was applied to **1.3 million candidate clauses** drawn from Wikipedia, Reddit, congressional bills, and academic abstracts. The marginal cost of scoring an additional topic is essentially zero (one forward pass through the model per politician per topic, amounting to minutes of GPU time for the full set of 550 Congress members).

This is the scale at which campaigns and parties currently operate through focus groups and internal polling --- except those efforts cost millions of dollars and cover at most a few thousand topics. The activation-based approach provides a first-pass estimate of partisan divergence on any topic that can be expressed as a natural-language clause, at a cost that makes comprehensive mapping feasible.

### 3B. Discovery of Latent Partisan Structure

The surprise analysis component is the most scientifically novel contribution. By regressing activation-based partisan divergence on political content features and examining the residuals, the method identifies topics that are **more partisan than their surface content predicts**. These are topics where partisan sorting has outpaced public awareness --- where the cultural terrain has been carved up by partisan identity in ways that explicit political framing does not capture.

This speaks directly to Mason's theoretical framework: if social sorting has turned partisanship into a "mega-identity" that encompasses lifestyle, consumption, and cultural choices, then the activation geometry should detect partisan structure in domains that have nothing to do with policy. The out-of-sample validation on 21 contemporary topics (tradwife lifestyle, raw milk consumption, fluoride removal) confirms that the method detects exactly this kind of latent partisan structure.

From a survey-design perspective, these high-surprise topics are **candidates for new survey items**. They represent the unknown unknowns of the partisan landscape --- topics that survey designers would not think to include because they are not overtly political, but that may in fact reveal deep partisan divisions. This is where the method transitions from measurement to discovery, and where its practical value for survey research is greatest.

### 3C. Elite Anchoring as a Feature, Not a Bug

A natural objection is that prompting with congressional names measures *elite* partisan structure, not *mass* opinion. But the r ~ 0.75 correlation with GSS mass-level partisan gaps suggests that elite and mass partisan structure are substantially aligned --- at least on the topics the GSS covers. This is consistent with the broader literature:

- **Key (1961)** argued that "the voice of the people is but an echo" of elite discourse. If mass opinion echoes elite positions, then elite-anchored measures should predict mass-level gaps.
- **Zaller (1992)** formalized this: mass opinion is the joint product of elite discourse, individual political awareness, and predispositions. The RAS model predicts that on topics where elite discourse is two-sided and clearly partisan, mass opinion will polarize along the same lines --- especially among the more aware. Our elite-anchored measure captures the partisan structure of elite discourse directly.
- **Caughey and Warshaw (2015)** found that state-level policy liberalism correlated increasingly strongly with presidential vote share over time (r = 0.47 in 1972 to r = 0.89 in 2008), documenting the growing alignment of mass and elite partisan structure that makes our approach viable.

The fact that politician-simulation outperforms demographic-simulation is itself theoretically informative. It suggests that LLMs encode partisan structure more faithfully through their parametric knowledge of named political actors than through inference from demographic correlations. This is consistent with Dias and Lelkes's finding that partisan identity (signaled by party labels, not demographics) is the principal mechanism of affective polarization. The model "knows" the partisan landscape through the discursive associations of political names, not through demographic stereotypes.

---

## 4. How to Structure the Introduction

### Paragraph 1: The Problem
Open with the practical and scientific limitation: survey-based measurement of partisan opinion is comprehensive but slow, expensive, and topically bounded. Despite decades of investment, we have GSS data on perhaps 200 topics, covering policy and a subset of social attitudes. The full cultural terrain --- the thousands of lifestyle, consumption, and cultural choices that Mason's framework predicts have become partisan --- remains unmapped. Campaigns spend millions on focus groups to explore fragments of this terrain; scholars rely on anecdotal evidence or small-scale experiments.

### Paragraph 2: The Measurement Tradition
Situate the paper in the tradition of latent opinion measurement: Stimson's Policy Mood, Caughey and Warshaw's group-level IRT, Fowler et al.'s mixture model. All share the goal of extracting coherent signal from noisy, sparse data. All exploit aggregation --- across items, respondents, or demographic groups --- to overcome the Zaller-Feldman problem that individual responses are "samples from competing considerations." Introduce the idea that a new data source might offer the same kind of aggregation at radically lower cost.

### Paragraph 3: The Proposal
State the core proposal: treat the activation geometry of LLMs as an alternative measurement instrument for partisan divergence. When prompted with congressional names on a given topic, the model's internal representations encode the discursive association between partisan actors and issue positions. The Mahalanobis distance between Democratic and Republican centroids in activation space provides a scalar measure of partisan divergence that can be computed for any topic expressible in natural language.

### Paragraph 4: Validation and Results
Report the headline validation: r ~ 0.75 correlation with GSS partisan gaps across 126 public issues and 73 private-life topics. Politician-simulation outperforms demographic-simulation (r ~ 0.72 vs. r ~ 0.31--0.50), consistent with the finding in the partisan identity literature (Dias & Lelkes 2022) that party labels are more powerful organizers of partisan structure than demographic attributes. Uncensored models outperform safety-aligned models, suggesting RLHF attenuates but does not erase the partisan geometry.

### Paragraph 5: Discovery
Introduce the discovery application: 1.3 million novel clauses scored for partisan divergence, with surprise analysis identifying topics that are more partisan than their surface content predicts. These "latent partisan" topics --- lifestyle choices, cultural practices, interpersonal norms --- are candidates for new survey items and represent the method's primary practical contribution. Note the ongoing survey validation effort.

### Paragraph 6: Limitations and Scope
Be explicit about what the method cannot do: it cannot separate the components of partisan association (rhetorical, policy, affective, identity-based) that the survey tradition carefully distinguishes. It cannot address causal questions about how partisan gaps form or change. It provides a *correlational measurement tool*, not a causal model. Its value lies in the first stage of the research pipeline --- discovery and screening --- not in the explanation of mechanisms.

---

## 5. How to Structure the Literature Review

### Section A: The Nature of Survey Responses and What They Measure
- **Zaller & Feldman (1992):** Survey responses as samples from competing considerations; the ambivalence, response, and accessibility axioms.
- **Achen (1975):** Measurement error vs. genuine instability; corrected correlations dramatically higher than observed.
- **Alvarez & Brehm (2002):** Three states of response variability (ambivalence, uncertainty, equivocation).
- **Transition:** If survey responses are noisy samples from distributions of considerations, what other observational vantage points might capture the same underlying distributions? LLM activations, trained on the full corpus of public discourse, represent one such vantage point.

### Section B: Measuring Latent Opinion at Scale
- **Erikson, MacKuen & Stimson (2002):** Policy Mood as the aggregate of hundreds of survey items; Dyad Ratios algorithm; the micro-macro distinction.
- **Caughey & Warshaw (2015):** Group-level IRT for subnational latent opinion; exploiting sparse data through hierarchical modeling.
- **Fowler et al. (2023):** Mixture models decomposing respondents by type; the IRT framework for classifying ideological structure.
- **Transition:** These methods extract latent opinion from survey data. We propose extracting a related construct --- partisan divergence --- from the representational geometry of language models. The validation question is whether these two approaches converge.

### Section C: Partisan Identity, Social Sorting, and What "Partisan" Means
- **Green, Palmquist & Schickler (2002):** Party ID as social identity; stability of partisan stereotypes; measurement-error-corrected correlations near 1.0.
- **Mason (2018):** Social sorting creates "mega-identity"; partisan hostility driven by identity alignment, not policy divergence.
- **Iyengar, Sood & Lelkes (2012):** Affective polarization distinct from ideological polarization; partisan affect driven by identity, not ideology.
- **Dias & Lelkes (2022):** Partisan identity as principal mechanism; party-branded vs. unbranded issues.
- **Transition:** If partisan identity organizes the cultural landscape (Mason), and if party labels are more powerful organizers than demographics (Dias & Lelkes), then LLM activations prompted with congressional names should capture a broad and culturally extended measure of partisan divergence --- exactly what we observe.

### Section D: The Formation and Transmission of Partisan Attitudes
- **Niemi & Jennings (1991):** Intergenerational transmission + issue responsiveness across the lifespan.
- **Luskin, McIver & Carmines (1989):** Different issue types affect different change types; hard-easy distinction.
- **Key (1961):** Elite-mass linkage; "the voice of the people is but an echo."
- **Zaller (1992):** RAS model; mass opinion as joint product of elite discourse, awareness, and predispositions.
- **Transition:** These works establish that mass opinion is structured by elite discourse (Key, Zaller) and responsive to issue-specific dynamics (Niemi & Jennings, Luskin et al.). Our method captures the *discursive structure* through which elites organize the partisan landscape. It cannot address the causal mechanisms these works study, but it can map the *outcome* of those mechanisms at unprecedented scale.

### Section E: The Consequences of Partisan Identity and the Need for New Measurement
- **Gadarian, Goodman & Pepinsky (2021):** Partisanship as the master variable in COVID health behavior; partisan gaps larger than income or education effects.
- **Lee, Lelkes, Hawkins & Theodoridis (2022):** Positive partisanship at least as prevalent as negative; leaners as exception.
- **Campos & Federico (2026):** Multidimensional affective polarization; aversion most dangerous for democracy.
- **Transition:** These works document that partisan identity shapes an expanding range of attitudes and behaviors. The implication: the set of "partisan" topics is far larger than what current surveys cover. A method that can screen millions of potential topics for partisan divergence addresses this gap directly.

### Section F: LLMs as Measurement Instruments (Brief; Position Paper Within AI + Social Science)
- Cite the emerging literature on using LLMs to simulate survey responses (Argyle et al. 2023, "Out of one, many"; Santurkar et al. 2023; Bisbee et al. 2024).
- Distinguish our approach: we do not analyze generated text or treat LLM outputs as simulated survey responses. We extract internal activation patterns and treat them as encoding latent structure about the partisan organization of discourse. This is a measurement approach, not a simulation approach.
- Note that the finding that uncensored models outperform RLHF-aligned models contributes to the emerging understanding of how safety training affects the political structure encoded in model parameters.

---

## 6. Key Framing Principles for an Interdisciplinary Venue

1. **Lead with the phenomenon, not the method.** The paper is about partisan opinion structure and its measurement, not about transformer architectures. The LLM is the instrument; the object of study is the cultural terrain of partisan divergence.

2. **Speak the language of public opinion research.** Use concepts the field recognizes: latent opinion, measurement error, partisan gaps, policy mood, affective polarization, social sorting. Translate technical ML concepts (Mahalanobis distance, attention heads, PCA) into terms that map onto existing measurement traditions.

3. **Be explicit about what you cannot do.** The paper cannot adjudicate the causal questions the field cares most about (what drives partisan gaps, how they form, what moves them). It offers a measurement and discovery tool, not a theory of opinion formation. This modesty is a strength: it positions the work as infrastructure for the field rather than as a competitor to existing approaches.

4. **Emphasize the discovery application.** The 1.3 million scored topics and the surprise analysis are the most novel contributions. This is what survey researchers cannot currently do and what campaigns spend millions to approximate. Frame the method as a "telescope" for the partisan landscape --- it reveals structure that was always there but never measured.

5. **Validate against what the field trusts.** The r ~ 0.75 correlation with GSS partisan gaps is the paper's empirical foundation. Frame every technical choice (politician vs. demographic simulation, stance vs. rhetorical prompts, uncensored vs. aligned models) as contributing to or detracting from alignment with this trusted benchmark. The GSS is the common currency; everything else is denominated in terms of how well it predicts GSS structure.

6. **Connect to the ongoing survey.** The fact that you are actively fielding a survey on high-surprise topics (those identified by the model as latently partisan) closes the loop between discovery and validation. This is the strongest possible demonstration that the method generates actionable hypotheses for survey research.
