---
date: 2026-06-04
title: "Every Technical Term in the YAKSHA Rubric, Explained"
tag: Ideas
author: "Claude / discussion"
source: "YAKSHA_INTERVIEW_RUBRIC_V2 & YAKSHA_INTERAGENT_RELIABILITY_PROTOCOL"
source_url: ""
summary: "A full glossary of every technical concept in the YAKSHA AI interview rubric — statistics, AI agent architecture, and evaluation design, all explained in plain language."
notes: |
  What struck me reading this is how carefully it was designed to fail gracefully. Every part of the system has an explicit acknowledgment of where it might be wrong — the weights are labeled "hypothesised," the sample size is labeled "directional," the ensemble agreement is explicitly warned against being mistaken for accuracy. That level of epistemic honesty in a technical document is rarer than it should be.
---

### 📊 Statistics & Measurement

- **Cohen's κ (kappa)** — A number between -1 and 1 that measures how much two raters agree *beyond pure chance*. If two people flip coins and happen to agree, that's luck — kappa removes luck and measures real agreement. Used here to check whether two AI agents scoring the same interview land on similar scores. Target in this system: κ ≥ 0.60.

- **Weighted Cohen's κ** — Same as kappa, but smarter about disagreements. A 1-point disagreement (scoring 3 vs 4) is treated as less serious than a 3-point disagreement (1 vs 4). Makes sense for rubrics where being *close* is much better than being wildly off. Used to check agent agreement on each scoring dimension.

- **Inter-rater reliability** — When multiple people score the same thing, do they agree? High reliability means the rubric is clear enough that different scorers reach similar conclusions. Low reliability means the score definitions are too vague. The entire reliability protocol exists to measure and improve this.

- **OLS regression (Ordinary Least Squares)** — A statistical method that finds the best straight-line relationship between an outcome and one or more inputs. Here: once 50+ students have completed internships, it tells you how much each dimension (GR, PO, LO) *actually* predicts real performance — replacing the educated guesses with real-data weights.

- **Beta coefficients / empirical weights** — The numbers OLS produces. If GR's beta is 0.55, it means GR explains more of internship performance than assumed. Right now the weights (0.40 / 0.40 / 0.20) are hypothesised. Beta coefficients replace guesses with evidence.

- **Collinearity / structurally collinear** — When two things you're measuring are actually measuring the same underlying thing. In v1 of this rubric, three separate dimensions all turned out to say the same thing about a student — if you scored high on one, you scored high on all three. They collapsed them into one dimension because of this.

- **Latent factor** — The hidden, unmeasurable thing that's actually driving what you can observe. You can't directly measure "grounded technical articulation" — but it shows up across multiple signals: explains tradeoffs, grounds claims in experience, answers episodic questions. When multiple surface measurements all point at the same latent thing, collapsing them makes sense.

- **Factor analysis** — A statistical technique that detects latent factors by looking for which measured variables move together. Referenced at the end of the rubric: if analysis on N≥50 reveals the GR sub-components separating cleanly, they'll re-split in v3.

- **N** — Sample size. N=9 means 9 students. The rubric is explicit that with N=9, scores are directional only — don't make high-stakes decisions until N≥50.

- **Decile** — Splitting data into 10 equal groups ranked low to high. "Top decile Spurti" = top 10% of performers. Used to find cases where the rubric severely mis-scored a student relative to their actual internship performance.

- **Median** — The middle value when all scores are sorted. Three agents score a student 2, 3, 4 — the median is 3. Used instead of average because it's more resistant to one outlier agent being way off.

- **Precision vs. accuracy** — *Precision* = consistent, reproducible. *Accuracy* = correct. Three AI agents sharing the same model can be *precisely wrong* — they all agree on 4 when the right answer is 2. High inter-agent agreement (precision) does NOT mean they're right (accurate). That's why you always compare against a human expert, not just against other agents.

### 🤖 AI & Agents

- **LLM (Large Language Model)** — The underlying technology powering agents like Claude or GPT. Very large neural networks trained on text. The rubric is designed to be *run by* an LLM agent — the model reads a transcript and produces structured scores. The documents explicitly acknowledge LLM failure modes: hallucinating quotes, agreeing on the wrong answer, etc.

- **Temperature** — A setting that controls how random/creative an AI's outputs are. Temperature 0 = as deterministic as possible, always picks the most likely response. Temperature 1+ = more random. For scoring interviews you want temperature 0 so the same transcript produces the same score every time. Caveat: even at temperature 0, outputs aren't guaranteed identical — which is why every transcript is run twice.

- **Stochastic variance** — "Stochastic" means random. The unpredictable variation in LLM outputs even with identical inputs. Even with temperature 0, an LLM might score a student 3 one run and 2 another. The ensemble design and mechanical aggregation (take the median) exist to reduce this.

- **Sub-agent architecture / ensemble scoring** — Using multiple AI agents in parallel, each independently scoring the same interview, then combining their outputs mechanically (take the median). Like having multiple doctors independently read the same X-ray. Key rule: don't use a *fourth* LLM to "reason across" the three scores — that reintroduces the variance you were trying to eliminate.

- **Correlated errors** — When multiple agents share the same underlying model and prompt, they don't make *independent* mistakes — they make the *same* mistakes. If a rubric anchor is ambiguous in a particular way, all agents will misread it the same way and agree on the wrong answer. This is why inter-agent agreement ≠ accuracy. Calibration against a human is the only way to catch it.

- **Calibration** — Checking and adjusting a scoring system so its outputs match a trusted standard (here: a human expert's scores). Pre-calibration, you don't know if your agents are actually measuring what you think. Post-calibration with κ ≥ 0.60 against a human expert, you have evidence the system works.

- **Hallucination (implied)** — The rubric references this directly: "LLMs will reconstruct plausible-sounding quotes when no exact match exists." An LLM that can't find a quote to support a score will invent a convincing-sounding one. The span-match verification rule (verbatim quotes only, character-for-character) is the countermeasure.

### 📋 Rubric & Evaluation Design

- **BARS (Behaviorally Anchored Rating Scales)** — A rating system where each score level is defined by *specific observable behaviors*, not vague adjectives. Instead of "good communicator = 4," a BARS anchor says "explains unprompted tradeoffs grounded in their own project = 4." This rubric is BARS-style — it reduces interpretation drift because scorers match behavior to anchor rather than deciding what "good" means.

- **Construct** — What you're *actually* trying to measure, as opposed to what you're observing. The construct for GR is "grounded technical articulation." You can't observe this directly, so you observe proxies: can they explain what broke? Can they ground claims under follow-up? The rubric names each dimension's construct explicitly.

- **Halo effect** — A psychological bias where a strong positive impression in one area bleeds into ratings in others. If a student impresses you with confidence, you unconsciously rate their technical depth higher too. The rubric collapsed three dimensions *because* halo was making them functionally identical — a student who impressed on one inflated the others.

- **False positive** — Flagging a problem that isn't actually a problem. Here: incorrectly flagging a student as a grounding failure when they're just verbally concise. The protocol sets a threshold: if more than 20% of grounding-failure flags are false positives on human review, the criteria are too broad.

- **Span-match verification** — Requiring that any evidence quote must be copied *verbatim* from the source transcript, character for character. No paraphrasing. If the agent can't find an exact matching quote for the score it wants to give, it must lower the score to the level it *can* support with a real quote. Direct countermeasure to LLM hallucination.

- **Episodic questions / episodic grounding** — Questions about specific lived moments: "what broke when you deployed this?" These can only be answered from genuine personal experience. Generic domain knowledge answers conceptual questions but falls apart on episodic ones. The rubric weights episodic answers heavily because they distinguish real ownership from borrowed fluency.

- **Grounding / grounded reasoning** — Anchoring your claims in your own specific experience, not general domain knowledge. "JWT is stateless and helps with scalability" is domain knowledge — anyone who read the docs can say it. "When I implemented JWT, the token expiry logic broke session handling in a way I didn't expect" is grounded. Fluent recitation without personal grounding should not exceed GR=2.

- **Connectivity gate** — A pre-screening check that runs before the AI-assist flag. If a student had connectivity problems (multiple sessions, re-logged in mid-interview), the system checks whether apparent grounding failures might just be the interview breaking apart technically — preventing penalizing students for internet problems.

- **Dimensional profile** — Looking at individual scores (GR=4, PO=1, LO=4) rather than just the combined total. The rubric warns: don't use a single weighted total for selection at small N. A student with perfect reasoning but zero ownership is a different risk from a mediocre-all-around student, even if totals are similar.

- **Anomaly flags** — Automated triggers that catch internally inconsistent scores. Example: HIGH_SCORE_LOW_OWNERSHIP fires when a student has a high overall score but an ownership score ≤2 — flagging it for human review, not automatic rejection.

- **Spurti points** — The internship performance metric used to validate the rubric after the fact. Once students are in the internship, Spurti scores reveal who performed well. The SQL query joins rubric scores with Spurti outcomes to find cases where the rubric was badly wrong — and those mismatches drive future refinement.

### 📚 Where these concepts come from

- *Cohen's kappa* — Jacob Cohen (1960), "A coefficient of agreement for nominal scales"
- *Halo effect* — Edward Thorndike (1920), original study in military officer ratings
- *BARS* — Smith & Kendall (1963), performance appraisal methodology
- *OLS regression* — standard statistics; no single author
- *Ensemble methods* — Francis Galton (1907, "wisdom of crowds"); formalized in machine learning
