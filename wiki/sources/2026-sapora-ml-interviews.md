---
title: "ML Job Interviews: The Ultimate Guide"
authors: ["Silvia Sapora"]
year: 2026
type: blog
raw: ../../raw/blogs/2026-sapora-ml-interviews/clip.md
url: https://silviasapora.github.io/blog/ml-interviews.html
added: 2026-06-17
status: read
tags: [careers, interviews, ml-hiring, research-scientist, negotiation, personal-blog]
topics: [careers]
---

# ML Job Interviews: The Ultimate Guide

> Source: [[../../raw/blogs/2026-sapora-ml-interviews/clip.md|raw clip]] · [original](https://silviasapora.github.io/blog/ml-interviews.html)
> Kept as a **bookmark** of one (well-credentialed) candidate's snapshot of the 2026 ML hiring funnel — primarily for the *meta* signal about what the field treats as load-bearing, not as a how-to.

## TL;DR

Silvia Sapora (new PhD, ML) recounts her job search and landing an RS role at DeepMind, with offers also from Isomorphic Labs, Cohere, Meta, and a stealth startup. The post is structured as a process walkthrough — getting interviews, interview structure, three-pillar prep (technical / emotional / logistical), negotiation, decision making, "what I'd do differently" — capped with a long verbatim list of technical topics RS interviews drew from. The advice is concrete and first-person; the credentialing context (4 first-author top-venue papers, Apple + Meta internships) is unusually transparent for this genre.

## Key claims (the load-bearing ones)

- **Callback bar at top labs:** 3+ first-author papers (ICLR/NeurIPS/ICML) and ≥1 industry internship.
- **Prep budget:** allocate ≥1 month of regular study; target-prep per interview rather than generic study.
- **LeetCode target:** ~150 Mediums total; ≤20 min/Medium; *"breadth matters more than depth here."*
- **LLM mock interviews work:** reports *"surprisingly frequent overlap"* between LLM-generated practice questions and what interviewers actually asked. Prefers Claude over Gemini for learning.
- **Implement-from-scratch baseline:** transformer end-to-end, causal/cross/self/flash attention, attention backward pass, MLP forward+backward, SGD loop in PyTorch or JAX.
- **One interview per day** when possible; start with companies you care less about; stagger so offers cluster.
- **Negotiation:** peer-lab competing offers move numbers, random non-peer ones don't; deadlines are 1–2 weeks and not flexible.
- **Equity:** RSUs ≈ real shares (big tech); options ≈ optionality that may be worthless (startups). UK options trigger income tax on exercise. *"When a recruiter quotes total compensation including startup equity, smile politely and mentally discount it significantly."*

## Notable quotes

> [!quote] On readiness
> "You are never going to feel ready, so just start prepping now."

> [!quote] On talking to people at competing companies
> "In a shocking twist of events, every single person at DeepMind told me they'd choose DeepMind, and every single person at Isomorphic told me they'd choose Isomorphic. Extremely helpful."

> [!quote] On stochasticity
> "Your worth as a human being is not going to be decided by these interviews… The process is inherently stochastic."

## Technical topics list

See the raw clip for the full verbatim list, grouped by: **Reinforcement Learning · LLMs · Generative Modelling · Applied ML · General ML · Linear Algebra**. Useful as a 2026 snapshot of "what an RS hire was expected to be conversant in." Re-read against a future post to track drift.

## Connections

- Topics: [[../topics/careers]]
- Loose: [[../topics/evaluation]] — the technical-topics list overlaps with what research evaluations test for; both reflect "the current canon."

## My take

> [!info] Reading this for the meta-signal, not the tactics.

What this post is actually evidence of (separate from its explicit advice):

- **The "top labs" candidate funnel is a small world with a publicly legible bar.** "3+ top-venue first-author papers + ≥1 internship" is restating, in plain English, the implicit prefilter that the field has converged on. The fact that an individual hire writes it down this directly is itself a useful data point — it confirms the bar is consensus enough to be stated without hedging.
- **LLM-prep for LLM-research-interviews is closing the loop on itself.** The author treats Claude/Gemini as straightforwardly effective for generating mock interview questions, and reports the resulting questions overlap with what was actually asked. The interesting question this raises is whether interviewers will start *intentionally* asking things LLMs are bad at producing — i.e., is signal inflation going to force the canon to shift toward harder-to-mock material?
- **Compensation literacy is the most underrated section.** The RSU vs. options framing is correct and is the kind of thing many engineering candidates discover *after* signing. Worth treating as the reference paragraph any time someone asks "should I take the startup offer?"
- **The "talked to everyone at both companies" anecdote is the most epistemically honest line in the post.** It correctly identifies that within-company sentiment is near-useless as a tie-breaker because of obvious selection bias — but most candidate advice still recommends doing exactly this. Sapora's framing (do it anyway, but lean on people who know *you*) is the right reconciliation.
- **What's missing / what to read alongside:**
  - The MLE / RE side. This is RS-specific; the system-design-heavy interviews common at non-research roles aren't represented.
  - The interviewer / hiring-manager side. Recruiter incentives, calibration practices, how offer numbers actually get set inside companies.
  - The 2027+ update. The technical-topics list is a snapshot; treat it as a dated artifact rather than evergreen curriculum.
- **Where to be skeptical:** the post is n=1 from a strong-credential candidate at a moment of high demand. Generalizations like "companies can move numbers significantly" and "competing offers from peer labs carry real weight" probably hold for similarly-credentialed candidates and may not transfer to less leverage-rich situations.

Net: useful as a reference snapshot of the 2026 ML RS hiring funnel, and as one well-credentialed candidate's honest description of the prep loop. Less useful as a how-to for candidates with materially different positioning. The most reusable artifacts are the technical-topics list (canon snapshot), the RSU/options paragraph, and the "peer-offer leverage" claim about negotiation.

## Log

- 2026-06-17: ingested (compact / bookmark-style); created [[../topics/careers]] as new topic hub
