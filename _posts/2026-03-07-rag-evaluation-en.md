---
layout: post
title: "RAG Evaluation: Building a Reliable Automated Testing System"
date: 2026-03-07
categories: [tech, RAG]
---

# RAG Evaluation: Building a Reliable Automated Testing System

Last summer, I took over a RAG project for a financial client. The goal was to extract structured data from earnings reports and contracts. The system had been struggling for months—client feedback was always “the extraction is wrong,” but no one could pinpoint exactly where or why.

The team was stuck in firefighting mode: fix one field with a prompt tweak today, adjust a parameter for another field tomorrow. Problems kept resurfacing. Everyone worked hard, but it felt like groping in the dark.

When I came in, I didn’t immediately start changing code. Instead, I asked a fundamental question: **How do we know how well the system is performing?**

The answer: we didn’t. No recall monitoring, no systematic test sets. Every change was based on intuition.

It’s a cliché, but it’s true: if you can’t measure it, you can’t improve it. And that’s especially relevant for RAG.

## 1. Why Bother with Evaluation?

You’d think this is obvious, but in practice, evaluation is often an afterthought. People prefer tweaking models and prompts—it feels more like “real engineering.”

But without evaluation, all improvements are shots in the dark.

Take an example. The client once complained that the “employee count” field was often wrong. The team immediately adjusted the prompt, added few‑shot examples—two days of work. Result? Employee count improved, but another field—“registered capital”—started failing. Why? Because the change affected the whole system, and we had no way to see that.

What we lacked was **visibility**:
- What’s the recall rate? Are we missing key passages?
- What’s the accuracy? Which error types are most frequent?
- After each change, is the system better or worse?

Evaluation answers these questions. Only by knowing “where it fails” can we know “what to fix.”

## 2. Two Flavors of Evaluation

We soon realized there are two distinct scenarios.

**Scenario 1: With ground truth.** For fields like contract clauses, we can manually annotate a batch of data and use it to test accuracy. This is supervised evaluation.

**Scenario 2: Without ground truth.** Once live, the system processes millions of new documents daily—no manual labels. Yet we still need to monitor performance, especially to flag “suspicious” data for human review. This is unsupervised evaluation.

### 2.1 With Ground Truth: Never Mix Scenarios

A common pitfall: mixing data from different scenarios.

ESG extraction and contract extraction are completely different—ESG looks for carbon emissions, contracts look for signatories and amounts. Using the same test set for both models yields nonsense: a model good at ESG may fail miserably on contracts.

Our approach: **Build separate test sets per scenario.**

The process is straightforward:
1. Use AI to generate questions and answers (based on real documents)
2. Have humans review and correct them
3. Accumulate 200–1000 samples per scenario

Time‑consuming, but worth it. Without a quality test set, metrics are meaningless.

**Metrics:**

For retrieval, we only use recall. Missing key content costs more than retrieving some irrelevant chunks. Better to give too much context than too little.

For generation, we use LLM‑as‑judge. Feed the question, AI answer, and ground truth to a judge model, get a score from 0–100. We set a threshold at 90—anything below triggers an alert.

We tried existing tools like RAGAs, DeepEval, TruLens. RAGAs has comprehensive metrics (faithfulness, answer relevancy, context recall). But we found them either too heavy (need full deployment), too black‑box—the evaluation process is opaque; you don’t know how the score is derived—or too expensive. So we built a lightweight in‑house framework, a few hundred lines of code, flexible and controllable.

### 2.2 Without Ground Truth: Flagging “Suspicious” Data

This is the most practical scenario.

Live system: millions of records daily. Human review of everything is impossible. But we can’t ignore errors. So we run a no‑ground‑truth evaluation, assign each record a “suspicion score,” and send only low‑scoring ones for human review.

**We designed five dimensions** (each 0–100):
- **Relevance**: Does the answer address the question?
- **Faithfulness**: Is the answer grounded in context? Can each claim be traced back?
- **Completeness**: Does it cover all points required?
- **Clarity**: Is the expression clear and unambiguous?
- **Reasoning consistency**: More on this below.

We feed the judge LLM: the question (with detailed SOP), retrieved context, and the AI’s answer (including final result and reasoning). It scores the five dimensions. Total score below 60 → flagged as suspicious → human review.

Result: Humans review only ~10% of data, catching 90%+ of errors. Huge labor savings.

For the judge LLM, we use Qwen. Instruct models work better than thinking models—more stable, cheaper, faster. Evaluation needs consistency, not creativity. We also keep an eye on model updates, periodically testing new versions (like Qwen3.5) and different sizes to see which performs best as a judge. A key principle: **the generation model and the judge model should ideally be different** to avoid bias.

## 3. An Often Overlooked Check: Reasoning Consistency

This is a subtle but important issue we discovered.

Many evaluations look only at the final answer, missing critical errors.

Example:
- Q: “Employee count of Company X in 2023?”
- A: “125 people”

Looks correct? But check the reasoning: “According to row 3 of the table, 2023 employees: 125 people.” The table actually says “2022.” Answer is right, reasoning is wrong—the model guessed.

Even more common: unit mismatches. Answer says “125 people,” reasoning says “125 million yuan.” In finance, such errors are unacceptable.

Our approach: **Force structured output**—return both answer and reasoning (Chain of Thought, CoT). Then we run a consistency checker: does the reasoning support the answer? Do dates, units, numbers match? If inconsistent, flag as suspicious even if the answer looks right.

This catches about 30% of potential issues, especially unit errors and logical jumps. For low‑tolerance financial scenarios, “answer correct is not enough” is exactly the rigor needed.

Some may ask: CoT output is standard, but checking consistency—is that overkill? My view: when errors cost real money, an extra safeguard is worth it.

## 4. Putting It into Practice: From Gut Feel to Data‑Driven

After building the evaluation framework, we started integrating it into daily work. For major changes, we run the test sets and watch metrics. We set up a simple dashboard showing historical accuracy trends and suspicious data distribution. We’re not yet at the point of automatically identifying error types—that’s hard—but at least we see overall direction.

Thresholds aren’t static. We adjust the “suspicious” cutoff based on manual review results, keeping the review volume manageable.

None of this is fancy, but it shifts the team from “gut feel” to “data‑driven.” Now when we change code, we have more confidence.

## 5. Lessons Learned (Pitfalls to Avoid)

**1. Never mix test sets per scenario.** The most common and deadly pitfall. Build per‑scenario.

**2. LLM judges have biases.** A judge model may favor outputs from similar models. We rotate judges, use multiple judges for critical samples, and spot‑check manually.

**3. Long documents exceed token limits.** Long context may cause the judge to miss key info. Chunk, summarize, or feed only the most relevant parts.

**4. Cost control.** Use Qwen or GPT‑4o‑mini for judging. Thinking models are slower, more expensive, and not necessarily better. Evaluation needs stability, not innovation.

## 6. Final Thoughts

From “client keeps saying it’s wrong” to “knowing where it’s wrong,” evaluation helped us see the gap.

From reviewing millions of records to reviewing only 10%, evaluation saved us labor.

But evaluation itself needs iteration—metrics change with business needs, thresholds adjust with experience. It’s not a one‑time build; it grows with the system.

Today, the system’s accuracy is stable above 95%. Client complaints are now occasional queries. Looking back, the decision to “build evaluation first” was the project’s turning point.

If you’re building RAG evaluation, let’s connect. This field has no standard answers yet—we’re all exploring.

---

*Based on real project experience, client information anonymized. Feel free to share with attribution.*
