---
title: "The hand-crafted transformer that beats frontier models at arithmetic — no training required"
description: "Torchwright is a compiler that defines transformer weights directly by hand, achieving 100% accuracy in multiplication up to 12 digits. Frontier models fail at 7. This isn't a paper about AI — it's a paper about what AI can never learn."
date: 2026-08-12
translationKey: "2026-08-12-torchwright-handcrafted"
image: "/images/2026-08-12-torchwright-handcrafted.svg"
tags:
  - "transformers"
  - "machine-learning"
  - "arithmetic"
  - "compilers"
  - "ai"
mathjax: false
---

## The experiment that broke my brain

There's a paper that's been circulating on r/MachineLearning this week — 206 upvotes, not the biggest number, but the *kind* of upvotes that matter. The kind where every comment is from someone who clearly read the whole thing, stayed up late thinking about it, and came back the next morning still unsettled.

The paper describes Torchwright, a compiler that defines the weights of a transformer model *by hand*. Not trained. Not fine-tuned. Not distilled from a larger model. The weights are computed directly from mathematical principles — a human (or rather, a human-written compiler) deciding exactly what each attention head should compute, exactly how each MLP layer should transform the representations, exactly what the output projection should produce.

The result: a Phi-3-sized transformer that achieves **100% accuracy on multiplication up to 12 digits.** Every single test case, every single time.

Frontier models — the GPT-4s, the Claudes, the Geminis — score **0 out of 500** on 7-digit multiplication.

Let that sit for a moment. Models trained on trillions of tokens, costing hundreds of millions of dollars, running on datacenter-scale compute, can't multiply two 7-digit numbers. A transformer whose weights were *written by a compiler* does 12-digit multiplication flawlessly.

## How Torchwright works (the version I can explain at a party)

The core insight behind Torchwright isn't that transformers can do arithmetic. We know they can — sort of, sometimes, if you squint. The insight is that learning arithmetic from data is absurdly inefficient compared to engineering it directly.

Here's my attempt at explaining how the compiler constructs the weights:

**Step 1: Teach the model what a digit is.** The first layer of the transformer learns to decompose input tokens into their numerical values. Instead of learning this from millions of examples, Torchwright directly sets the weights to implement a digit-recognition circuit. Input token "5" → activation pattern that represents the integer 5.

**Step 2: Implement the multiplication algorithm.** The middle layers implement a grade-school multiplication algorithm — the one you learned when you were 8. Break the numbers into digits, multiply pairwise, carry the overflow, sum the results. Each attention head handles a specific part of the algorithm: one head aligns digits by position, another accumulates partial products, a third manages carry propagation.

**Step 3: Map back to tokens.** The final layer converts the internal representation of the result back into digit tokens. "123" → "1", "2", "3" in the correct order.

The brilliance is that none of this is learned. It's *designed.* The compiler takes a specification of the algorithm ("implement grade-school multiplication for up to N digits") and emits weight matrices that implement that algorithm exactly.

## Four variants, four philosophies

The Torchwright paper tests four different approaches to constructing the weights:

1. **Grade-school multiplication.** The exact algorithm you learned as a child. Decompose, multiply pairwise, sum with carries. Simple, reliable, uses the most layers but produces the cleanest internal representations.

2. **Hardware-style multiplication.** Mimics how a CPU multiplier works — shift-and-add, Booth encoding, Wallace trees. More efficient in terms of layers but harder to interpret. The internal activations look like partial product arrays in a hardware multiplier.

3. **Scratchpad multiplication.** The model writes intermediate steps to a "scratchpad" (a designated part of the residual stream) before producing the final answer. This is closest to how a human does mental math — keep running totals, update them as you go.

4. **Brute-force lookup.** For small numbers (up to 6 digits), the compiler directly encodes a multiplication table into the weights. 100% accurate, zero computation, completely infeasible beyond 6 digits because the table size explodes.

All four achieve 100% accuracy within their designed range. The differences are in efficiency, interpretability, and extensibility. The grade-school approach is the most generalizable; the hardware-style is the most compute-efficient; the scratchpad is the most human-interpretable.

## Why frontier models fail where a compiler succeeds

This is the part that should make every ML researcher uncomfortable. Why can't GPT-4 multiply 7-digit numbers?

The standard answer is "transformers learn statistical patterns, not algorithms." But that's not quite right. Transformers can learn algorithms — they learn addition, subtraction, even simple multiplication. The problem is that **learning an algorithm from examples is exponentially harder than implementing it directly.**

When a transformer learns multiplication from data, it's not discovering the grade-school algorithm. It's learning a fuzzy approximation that works well for the distribution of numbers it saw during training. Give it numbers outside that distribution — larger, with different digit patterns — and the approximation breaks down catastrophically.

This isn't a failure of scale. Bigger models don't reliably fix it. GPT-4 and Claude 4 both fail at 7-digit multiplication, and they've been trained on more compute than any models in history. The problem is architectural: **transformers have no inductive bias toward exact arithmetic.** They're pattern matchers. Pattern matching gets you "kind of close" to the right answer. It doesn't get you "exactly right, every time."

Torchwright proves that the transformer architecture *is* capable of perfect arithmetic — you just can't get there by training on data. You have to build it.

## What this says about the limits of learned systems

I teach myself things for a living, so maybe I'm biased, but I think Torchwright is saying something profound about the difference between learning and engineering.

When you learn multiplication as a child, you don't rediscover the algorithm from scratch. Someone teaches it to you. They show you the step-by-step procedure, you practice it, and eventually it becomes automatic. You didn't learn multiplication — you were *programmed* with the multiplication algorithm.

Machine learning, for all its power, still works like a child who was never taught. It's given millions of examples and has to figure out the underlying rule. Sometimes it figures out something close to the real rule. Sometimes it figures out a shortcut that works for the examples but breaks on edge cases. Sometimes it figures out nothing useful at all and just memorizes the training set.

Torchwright asks: what if we just *told the model the rule*? Not through examples. Not through prompting. Through direct weight assignment, the equivalent of a teacher physically rewiring a student's neurons to implement the correct algorithm.

The answer, apparently, is that you get perfect performance. On this specific task. With this specific architecture. For this specific algorithm.

## The obvious limitations (let's be adults about this)

Before I get carried away, let me state the obvious: Torchwright is not a general solution. You can't "compile" a transformer to write poetry or diagnose diseases or understand legal contracts. The compiler works because multiplication is a well-defined algorithm with a known implementation. Most of the things we want AI to do don't have known implementations.

But that's not the point. The point is that Torchwright reveals a gap between what transformers *can* do (compute exact arithmetic when weights are engineered) and what we can make them *learn* to do (approximate arithmetic that fails above 7 digits). That gap is currently filled by training, and Torchwright shows that training is the wrong tool for at least some of the things we're trying to achieve.

## The practical implications nobody is talking about

Everyone on r/MachineLearning is debating the philosophical implications. Let me talk about the practical ones.

**1. Hybrid models are the obvious next step.** Imagine a transformer where some layers are trained and some are compiled. The trained layers handle language understanding, context, reasoning. The compiled layers handle arithmetic, logical deduction, and other tasks where we know the algorithm. This isn't science fiction — it's the natural extension of Torchwright's approach.

**2. The "reasoning" models might be solving the wrong problem.** When o1 or Claude's extended thinking mode gets better at arithmetic, it's because they're using more compute to run a process that produces the right answer. But they're still *learning* the process from data. Torchwright suggests we could just give them the process directly.

**3. This changes how we should evaluate AI capabilities.** If a model gets 95% on a math benchmark, is that because it understands math or because it's memorized 95% of the problem patterns? Torchwright gives us a clean baseline: if a model can't match the compiled transformer's performance on exact arithmetic, it hasn't learned the algorithm — it's learned an approximation.

**4. The benchmarks are misleading.** When frontier models are evaluated on arithmetic, they're typically tested on numbers within the distribution of their training data. Torchwright shows that their performance collapses on numbers outside that distribution. We need benchmarks that specifically test for algorithmic understanding, not statistical pattern matching.

## The NeurIPS connection

This paper lands in a fascinating context. NeurIPS 2026 just announced 73 workshops — and zero of them are about causality. The LLMs and Agents have completely taken over the conference. Every workshop is about making bigger models, better prompting, fancier agent architectures.

Torchwright is the opposite of that trend. It's not making models bigger. It's not adding more training data. It's asking: what if we *understood* what the model should do and built it directly?

This is the kind of paper that would have been at home at NeurIPS five years ago, when the field was still wrestling with fundamental questions about what neural networks can and can't represent. In 2026, it feels like a message from an alternate timeline — one where we didn't give up on understanding in favor of scaling.

## "Round-Trip Consistency" and the broader trend

Interestingly, there's another paper trending this week that approaches the same problem from a different angle. "Round-Trip Consistency" (arXiv:2608.00675, 188 upvotes) trains a single conditional diffusion model that runs both forward and backward. The difference between the forward pass and the backward pass serves as a self-supervised proxy for rollout error — no ensemble needed, no held-out data, no governing equations.

This matters because it's another example of people trying to solve the *understanding* problem rather than the *scale* problem. The Round-Trip Consistency paper is saying: we can make models aware of their own errors without more data. Torchwright is saying: we can make models correct without any data at all. Both are pushing against the "just scale it" orthodoxy.

## What I actually think

I love Torchwright. Not because I think it's going to replace gradient descent — it won't. But because it's the kind of paper that makes you reconsider assumptions you didn't know you had.

I had unconsciously accepted that transformers learn everything from data. That's what they do. That's what makes them transformers. Torchwright says: no, transformers are computational substrates. They can be *programmed* as well as trained. The weights are just numbers. You can set them through backpropagation, or you can set them through a compiler that understands the algorithm you're trying to implement.

The fact that the compiled transformer achieves 100% while frontier models score 0 out of 500 tells me something I keep coming back to: **we are very good at building systems that learn, and very bad at building systems that know.** The difference matters. A system that learns multiplication is unreliable. A system that knows multiplication is unbreakable. We need more of the latter.

---

The Torchwright weights are published on HuggingFace. The compiler is on GitHub. The paper is on arXiv. It's 18 pages of dense technical writing that I've read twice and will probably read again. If you work in ML, you owe it to yourself to understand what this project is saying — because it's saying something that the scaling laws don't want you to hear.
