---
title: "Bad Apple in a 3MB neural network, and the NeurIPS reproducibility crisis"
description: "Someone compressed the entire Bad Apple music video into a 3.5M-parameter LLaMA. Meanwhile, ML researchers at NeurIPS 2026 are debating whether papers without code should be desk-rejected. These stories are the same story."
date: 2026-08-10
translationKey: "bad-apple-neurips-reproducibility"
image: "/images/project-placeholder.svg"
tags:
  - "ai"
  - "machine-learning"
  - "reproducibility"
  - "neurips"
  - "bad-apple"
mathjax: false
---

## The 3MB neural network that plays Bad Apple

Two things happened in the ML world this week that seem unrelated but aren't.

First: someone on r/MachineLearning posted a project where they compressed the entire Bad Apple!! music video — all 1,745 frames of shadow-art silhouettes — into a **3.5-million-parameter LLaMA model weighing 13.4 MB**. Give it a frame number, it spits out 58 rows of `.` and `@` characters that perfectly reproduce that frame. 99.78% average accuracy. 53 out of 71 test frames are pixel-perfect.

Second: a discussion thread on r/MachineLearning titled "It's time to desk reject papers that don't include code that can reproduce the results" hit 282 upvotes and 65 comments. NeurIPS 2026 reviewers are debating whether code submission should be mandatory, not just "strongly encouraged."

These are the same conversation.

## Why Bad Apple in a neural net matters

The Bad Apple project works because it ships *everything*. The model weights. The training script. The tokenizer. The frame data. The exact hyperparameters (AdamW, lr=3e-4, CosineAnnealingWarmRestarts). The hardware used. The inference benchmarks (2,800 tok/s on a 7950X with GGUF).

Anyone with a GPU can clone the repo and verify every claim in the README. There are no missing steps, no "available upon reasonable request," no link to a dead university hosting page. The project is small enough that **reproducibility is not a promise — it's a property of the artifact itself.**

Compare this to the typical NeurIPS paper. A 9-page PDF describing a novel architecture. A GitHub link that 404s. Training details hidden in Appendix C which wasn't included because of the page limit. "We used 256 A100s for 3 weeks" as a throwaway sentence that makes reproduction impossible for anyone without seven figures of cloud credit.

The Bad Apple project would probably get desk-rejected from NeurIPS. It has no novelty claim. It solves no benchmark. It advances no theoretical framework. But it has something that most NeurIPS papers don't: **you can run it and it does exactly what it says.**

## The reproducibility debate is heating up

The r/MachineLearning thread captured something real. Here's the sentiment:

> "If your paper makes empirical claims and doesn't include code, it should be desk rejected. Not 'strongly encouraged.' Not 'reviewers should consider it.' Rejected."

This isn't a fringe position anymore. The pushback used to be "but theory papers don't need code" — and that's fair. But the thread makes a clean distinction: papers making **empirical claims** (we beat SOTA on benchmark X, our method is 3x faster than Y) need reproducible evidence. Papers making **theoretical claims** (we prove convergence under these assumptions) don't.

NeurIPS 2026 is already moving in this direction. The Evaluations & Datasets Track now has mandatory code submission for "reusable executable artifacts." The justification field for papers without code is scrutinized. Reviewers are explicitly told: "If the code justification is not convincing, the submission may be subject to rejection."

But it's not enough. The policy still treats code as "strongly encouraged" for regular papers, and the "convincing justification" loophole is wide enough to drive a truck through. Every reviewer has seen "code will be released upon acceptance" and then... nothing. Or a repo appears with a README, a requirements.txt, and zero actual implementation.

## What Bad Apple teaches us about good ML research

The Bad Apple project isn't trying to be research. It's a labor of love by someone who wanted to see if a tiny LLaMA could memorize a music video. But it accidentally demonstrates everything that good ML research should be:

1. **Complete.** Everything needed to reproduce the result is in one repo.
2. **Honest.** The README tells you the accuracy (99.78%), not just "state of the art." It tells you which frames failed (minimum accuracy: 94.19%).
3. **Accessible.** It runs on consumer hardware. 2,800 tok/s on a 7950X. No cluster required.
4. **Weird.** It doesn't optimize for a benchmark. It optimizes for something *interesting.*

That last point matters more than it seems. So much ML research is about squeezing an extra 0.3% on ImageNet or MMLU. The Bad Apple project is about answering a question that someone genuinely had: "Can I fit this entire music video into a tiny transformer?" The answer turned out to be yes, and the process of getting there taught us something about memorization capacity, tokenization strategies, and the relationship between model size and information density.

Contrast that with a paper that gets 87.3% instead of 87.0% on some benchmark by using a slightly different learning rate schedule and 10x the compute. Which one would you rather read?

## The way forward

I don't think we need to desk-reject every paper without code. That's too blunt. But I do think we need to raise the bar for what "empirical evidence" means.

Here's my proposal, for what it's worth:

1. **If you claim to beat a benchmark, you must release code.** No exceptions. If you can't release the code (proprietary data, legal restrictions), you can't claim to beat the benchmark. Submit a theory paper instead.
2. **"Available upon request" should be grounds for desk rejection.** It's 2026. If your code exists, put it on GitHub. If it doesn't exist, don't pretend it does.
3. **Reproducibility should be weighted in acceptance decisions.** A paper with complete, runnable code that demonstrates every claim should get a meaningful boost. A paper with no code and empirical claims should get a meaningful penalty.

The Bad Apple project got 225 upvotes on r/MachineLearning. A video compression trick got more engagement than most NeurIPS papers. Maybe that's because it's fun. But I think it's also because it's **real** — you can touch it, run it, verify it. In a field drowning in unreproducible claims, that's worth more than another SOTA number.

---

*The Bad Apple LLaMA: [nyuuzyou/BadApple-LLaMA-nano on Hugging Face](https://huggingface.co/nyuuzyou/BadApple-LLaMA-nano)*
