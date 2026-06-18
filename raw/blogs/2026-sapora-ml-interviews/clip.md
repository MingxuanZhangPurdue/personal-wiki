# ML Job Interviews: The Ultimate Guide

> **Note:** This is an AI-extracted clip via WebFetch, not a verbatim crawl. Treat the body as a faithful paraphrase + verbatim lists (technical topics, books, etc.). For exact wording of any quote, check the live URL or Wayback snapshot.
>
> **Source:** https://silviasapora.github.io/blog/ml-interviews.html
> **Fetched:** 2026-06-17

---

## Introduction

Silvia Sapora shares her experience securing a Research Scientist role after completing a PhD in Machine Learning. She received offers from DeepMind (accepted), Isomorphic Labs, Cohere, Meta, and a stealth startup. Anthropic, Mistral, and TeslaAI responded too late; ReflectionAI rejected her for the RS role but offered an Engineering track. Most companies she applied to invited her to interview, except SpaceXAI, Waymo, and Wayve.

---

## Getting Interviews

To consistently get callbacks at top labs, the benchmarks are: **3+ first-author papers and at least one internship or industry role**. Sapora had 4 first-author papers from her PhD (ICLR/NeurIPS/ICML, covering LLMs, RL, Meta-Learning, Evolution Strategies) plus internships at Apple and Meta.

> Stop focusing on research once you're getting interviews. You are never going to feel ready, so just start prepping **now**.

### Application Details

- **LinkedIn/X:** Many roles advertised here, requiring Google Form submissions.
- **Referrals:** Helpful but not necessary. Mixed results — sometimes referred roles led nowhere, unreferred ones succeeded.
- **Cold emails:** Worth doing. Explain why you're a good fit for that specific team and what excites you about their work.
- **Cover letters:** Write them yourself, then have an LLM polish them. *"Please do not just ask Claude/Gemini/ChatGPT to write it for you."*

---

## Companies: Startups vs Big Tech

- Finding startups is harder; word of mouth is the best discovery method.
- Interview processes vary more at startups; difficulty comparable on average but high-variance.
- Right startup = more impactful research, faster growth; wrong one = unexpected engineering, shifting priorities.
- CV recognition matters: OpenAI/Anthropic immediately recognized; unknown stealth startups require explanation.

### RSUs vs Stock Options

- **RSUs (big tech standard):** actual shares on a vesting schedule; ~half sell automatically to cover income tax.
- **Stock options (startup standard):** opportunity to buy shares at fixed price X regardless of market price Y. Profit if Y > X; worthless if Y < X. Typically expire 90 days after leaving. In the UK, exercising triggers income tax on Y−X *before* selling.

> When a recruiter quotes total compensation including startup equity, smile politely and mentally discount it significantly.

---

## Interview Structure

- **Recruiter screen:** low-stakes chat confirming skill relevance and knowledge of your papers.
- **Technical interviews (3–8 depending on company):**
  - Coding: LeetCode-style Medium/Hard.
  - ML coding and debugging: implementing attention, backward passes, spotting training-loop bugs.
  - ML knowledge: fundamentals, theory, applied ML, system design.
- **Behavioral interviews:** classic ("tell me about a conflict") and research-style ("what topics interest you?"). More casual but still important.

---

## How I Prepared: Technical

> This is the key part! Don't skip this.

Allocate at least **one month** of regular study time. Meta-strategy: target prep for each specific interview rather than generic preparation. Keeps material fresh and ensures comprehensive coverage.

### Flashcards
For ML fundamentals, applied ML, research discussions. *"Writing your own cards is half the learning."* Physical flashcards worked better for her than Anki.

### LLM Mock Interviews
Before each interview, paste the role description into Claude or Gemini and request practice questions. Reports *"surprisingly frequent overlap between those practice questions and what interviewers actually asked."* Found Claude better for learning; Gemini *"too flattering."*

### LeetCode / NeetCode
Blind 75 and optionally NeetCode 150, focusing on Mediums with optimal solutions. **Target ≤20 minutes per Medium.** *"Breadth matters more than depth here!"* Completed ~150 Mediums total.

### Books
- *Designing Machine Learning Systems* — Chip Huyen
- *The JAX Scaling Book*
- *Reinforcement Learning* — Sutton & Barto (only if new to RL)

### Courses
- **Linear algebra:** Gilbert Strang's YouTube lectures (*"He's the only reason I passed linear algebra in my undergrad"*)
- **Diffusion / Flow Matching:** MIT and Stanford courses (math-heavy; superficial knowledge may suffice)

### ML Coding and Debugging Baseline
Implement from scratch under time pressure:
- A transformer end-to-end
- Causal, cross, and self attention
- Flash attention
- The attention backward pass
- MLP forward and backward pass
- A simple SGD training loop in PyTorch or JAX

---

## How I Prepared: Emotional

Section recommended only for those struggling emotionally. Biggest challenge: sleep deprivation before interviews.

### Practical
- Unable to sleep before interviews; *"chucked litres of Coke for the sugar."*
- Regular exercise, consistent evening routines, social dinners on non-interview nights.
- *"Running before interviews helped me a lot, it burned off nervous energy and reset my head."*

### Pre-Interview Ritual
Comfort in consistency: fresh flowers in background, makeup application, comfort videos. *"My rotation: Alysa Liu's ice skating and life teachings, alongside the classic Lord of the Rings."*

### Deeper Emotional Work
Considered starting therapy but ran out of time.

> Your worth as a human being is not going to be decided by these interviews… The process is inherently stochastic.

Anecdote: woke at 5am before DeepMind interview in panic, reviewed a topic with Claude, interviewer's first question was about exactly that topic.

> You will mess up, even on things you know, and that's okay.

*"Absolutely bawled my eyes out after messing up"* a question about forward vs reverse KL divergence despite working on it in two separate papers.

### Helpful Books
- *The Now Habit* — Neil Fiore
- *The Gifts of Imperfection* — Brené Brown
- *Mindset* — Carol Dweck
- *The Tyranny of Merit* — Michael Sandel

---

## How I Prepared: Logistics

- **One interview per day** when possible. *"Interviews are exhausting and you are naturally going to underperform in your third interview of the day."* Rhythm: interview in the morning, prepare for the next one all day.
- **Start with companies you care less about.** Smaller startups, undesired locations, interesting-but-not-top-choice roles. Calibrates confidence and provides realistic compensation benchmarks before negotiating top choices.
- **Think about timing.** Stagger interview processes so offers arrive in roughly the same window. Tell every company about your other processes.

---

## Negotiation

Found [Haseeb Qureshi's negotiation post](https://haseebq.com/my-ten-rules-for-negotiating-a-job-offer/) helpful but didn't follow its poker-face advice. Some companies explicitly asked for proof of competing offers before increasing theirs.

- Companies can move numbers significantly if they want you — more than expected. Always worth asking.
- Deadlines varied (1–2 weeks); companies weren't flexible about extending them.
- Recruiters are good at reading you. Small signals matter — how often you mention a company, how you talk about them.
- Companies track historical data. Competing offers from peer companies (OpenAI, top labs) carry real weight; random non-peer offers don't.

---

## Decision Making Process

Sapora was tempted early on to accept offers out of fear of finding nothing else. *"I did find better."*

For choosing between offers, everyone weights differently: location, compensation, prestige, type of work. She spoke to essentially everyone at both companies she was deciding between (DeepMind and Isomorphic Labs).

> In a shocking twist of events, every single person at DeepMind told me they'd choose DeepMind, and every single person at Isomorphic told me they'd choose Isomorphic. Extremely helpful.

Most useful thing: talking it through with people who actually know her.

---

## What I'd Do Differently

- **Keep a spreadsheet** of companies, process status, deadlines, contacts. *"A simple spreadsheet would have stopped me from forgetting to apply to places I was actually interested in."*
- **Prepare emotionally, not just technically.** The interview process feels like a final verdict on your abilities. *"I didn't handle it well, and I think some therapy or at least serious introspection before starting (rather than during) would have helped a lot."*
- **Be more proactive about companies that ignored you.** Cold-email someone, express interest directly, get on someone's radar rather than hoping the application form works.

---

## Technical Topics (verbatim list)

### Reinforcement Learning
Q-Learning, TD Learning, Bellman Equations, PPO, GRPO, GAE, Variance Reduction in RL, DPO (Direct Preference Optimisation), Policy Gradient Theorem, On-Policy vs Off-Policy, Exploration vs Exploitation Dilemma, Credit Assignment Problem, MuZero, World Models/Dreamer, AlphaGo, Soft Actor-Critic, Model-Based vs Model-Free, Markov Property, Monte Carlo vs TD, Actor Critic, SARSA, Importance Sampling, Markov Decision Process, Curriculum Learning

### LLMs
Flash Attention, LoRA, TransformerXL, Griffin, Perceiver, Scaling Laws, Mixture of Experts, LLM scaling factor, RoPE, Sinusoidal embeddings, Relative positional embeddings, LLM vs RNN vs S4, Tokenisation, Pretraining, Finetuning, RLHF, Decoding techniques, Causal Attention, Cross Attention

### Generative Modelling
GANs, VAEs and VAE ELBO, Score Function, Diffusion Forward Process, Diffusion Reverse Process (DDIM/DDPM), Diffusion Forward/Reverse SDE, Flow Matching ODE, Classifier Free Guidance

### Applied ML
Tensor Parallelism, FSDP, DDP, Pipeline Parallelism, Communication Primitives, Mixed precision training, Gradient checkpointing, Gradient accumulation, Profiling, Gradient clipping, Numerical precision tricks, Exploding/vanishing gradients, Floating point representation, JIT compiling, JAX/PyTorch/TensorFlow

### General ML
Curse of dimensionality, S4, CNNs, RNNs/LSTMs, Autoencoders, Gumbel-Softmax, MLE vs MAP, Newton's Method, Linear Regression, Activation Functions, Loss Functions, No Free Lunch Theorem, BatchNorm/LayerNorm/RMSNorm, Variance and Covariance, Adam/AdamW/Adagrad, Bias-Variance Tradeoff, Backprop, Regularisation Methods, Unsupervised vs Supervised, Clustering Algorithms (k-means), K-Nearest Neighbours, SVMs, Boosting, Bagging, Decision Trees, Ensembles, Bayes Theorem, Precision/Recall/F1/AUC-ROC, KL Divergence, Jensen-Shannon Divergence, Weight initialisation, Gradient Descent/SGD, Overfitting/Underfitting, Cross validation, Data Whitening, Convex functions, Early Stopping, Domain Adaptation, Dimensionality Reduction, Transfer Learning, Few shot/Zero shot learning, Second Order Methods, Expectation, Entropy, PDF/PMF, Confidence Intervals

### Linear Algebra
Positive Semi-Definite, Jacobian, Eigenvectors/Eigenvalues, Hessian, Inverse of a matrix, Dot product, Null space/Image space, Orthogonality, Linear independence, Singular matrices, Rank/Span, Determinant
