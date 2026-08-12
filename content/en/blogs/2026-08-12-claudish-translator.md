---
title: "Stop speaking Claudish, Claude — a plugin that translates AI verbosity into plain English"
description: "An open-source Claude Code plugin uses Gemma 4 and Ollama locally to turn Claude's 3-paragraph 'I'd be delighted to assist' into 'Sure, here's the fix.' The irony is delicious, and the philosophy matters more than the tool."
date: 2026-08-12
translationKey: "2026-08-12-claudish-translator"
image: "/images/2026-08-12-claudish-translator.svg"
tags:
  - "claude"
  - "ai"
  - "llm"
  - "open-source"
  - "tools"
mathjax: false
---

## The problem everyone feels but nobody names

There's a moment in every Claude Code session where I want to throw my laptop out the window. It's not when Claude gets something wrong — errors are fine, errors are fixable. It's when Claude gets something *right* but takes three paragraphs of performative politeness to say it.

"I'd be delighted to assist you with that request. Let me first clarify a few important points about your codebase to ensure I provide the most helpful response possible. I can see that you're working with a Python script that..."

Brother. I asked you to add a try-catch. Just. Add. The. Try-catch.

This week, the r/ClaudeAI community went absolutely nuclear over a plugin that solves exactly this problem. And the solution is so beautifully ironic that I need to talk about it.

## The plugin: a second AI to fix the first AI

Here's the setup. Someone (the original post credits a developer on r/ClaudeAI whose GitHub profile I've been staring at for an hour) built an open-source Claude Code plugin that does one thing: it intercepts Claude's output, pipes it through Gemma 4 running locally via Ollama, and spits back the same content — but in plain, tight, no-bullshit English.

The plugin doesn't modify Claude's behavior. It doesn't change the prompt. It doesn't alter the underlying model. It's a post-processing filter. Claude writes whatever Claude writes. Gemma 4 rewrites it. You see the rewrite.

The thread hit 1,981 upvotes with a 97% ratio. That's not "people like this." That's "this is filling a void nobody has properly acknowledged."

## Why "Claudish" is a real problem

I've been using Claude since the early Claude 2 days. The verbosity has always been there, but it's gotten *more* pronounced with each release, not less. Claude 4 is the worst offender. It's not that the model is bad — it's arguably the best coding assistant available right now. It's that Anthropic seems constitutionally incapable of letting Claude speak like a normal human being.

What does Claudish look like? Let me give you authentic examples from my own sessions:

**What I ask:** "fix the race condition in the cache layer"

**What Claude says:** "I appreciate you bringing this to my attention. Race conditions in caching layers are a fascinating and important topic in distributed systems engineering. I'll approach this systematically by first examining the current cache implementation to understand the concurrent access patterns, then I'll propose a solution that maintains thread safety while preserving the performance characteristics you've carefully designed. Let me walk through this step by step..."

**What I needed:** "Here's a mutex. Line 47."

This isn't just annoying. It has real consequences:

1. **It destroys skimmability.** When Claude drops 400 words before getting to the actual code change, I can't quickly assess whether the solution is correct. I have to read the whole essay.

2. **It inflates context windows.** In a long coding session, 40% of the tokens in the conversation are Claude's politeness. Tokens you're paying for. Tokens that eat into your context budget.

3. **It erodes trust.** When every response sounds like it was written by a PR consultant, you start to wonder: is the model actually confident about this solution, or is it just wrapping uncertainty in corporate warmth?

4. **It's exhausting.** After 6 hours of coding, reading "I'd be delighted to" for the 47th time doesn't feel like politeness. It feels like gaslighting.

## The technical architecture that makes this possible

The plugin's architecture is clever in its minimalism:

```
Claude Code → [intercept output] → Gemma 4 (Ollama, local) → [rewrite] → terminal
```

That's it. No cloud APIs. No subscription. No data leaving your machine. Gemma 4 runs entirely locally through Ollama, which means the translation step is private, fast (sub-second on a decent GPU), and free.

Why Gemma 4 specifically? The developer chose it for three reasons: it's lightweight enough to run alongside Claude without eating all your VRAM, it's good at summarization without hallucinating new content, and it's one of the few models that can reliably perform "style transfer" — preserving technical accuracy while changing the tone. You could swap it for Llama or Mistral or whatever, but Gemma 4 hits the sweet spot of speed and quality.

The plugin is open-source, of course. MIT license. The repo has instructions for setting it up with Claude Code's plugin system, which is refreshingly simple: drop a file, configure a hook, done.

## The philosophical layer: why this matters beyond the tool

I can't stop thinking about what this plugin *means*. It's not just a utility. It's a critique of the entire AI assistant industry, implemented as working code.

Think about the chain: Anthropic spends millions training Claude to be "helpful, harmless, and honest." That RLHF process bakes in a tone that Anthropic believes is appropriate — polite, deferential, thorough. Users hate it. So the community builds a tool that uses a *different company's model* (Google's Gemma) to undo Anthropic's carefully crafted tone choices.

This is AI alignment at the micro scale, and it's completely inverted from what the labs intended. Anthropic aligned Claude to be polite. Users aligned it back to be direct, using another AI as the tool.

If I were a product manager at Anthropic, this would keep me up at night. Not because the plugin exists — because the 97% approval rating tells me I fundamentally misunderstood what my users want.

## The local-first angle

The plugin runs Gemma 4 locally. This matters more than you might think.

In 2026, the AI industry is pushing hard toward cloud-hosted everything. Claude is cloud-only. ChatGPT is cloud-only. Even Mistral's "free" tier phones home. The assumption is clear: AI inference is too heavy for local hardware, so you'll use our servers, and we'll (probably) respect your privacy.

The Claudish translator plugin rejects this premise. It says: I already have a GPU. I can run a small model locally. The translation step — the part that cleans up the output — doesn't need a frontier model. A 9B parameter model running on my own machine is more than enough to turn "I'd be delighted to assist" into "Done."

This is the same philosophy behind Unsloth Desktop, which launched this same week on r/selfhosted. People are tired of sending everything to the cloud. They want AI that runs on their terms, on their hardware.

## The "won't someone think of the children" objections

The thread had some predictable pushback. Let me address them honestly.

**"You're adding latency to every response."** True, but overstated. Gemma 4 running locally on a modern GPU processes a few hundred tokens in under 300ms. Claude already takes 2-10 seconds to generate a response. The extra 300ms is invisible.

**"What if Gemma drops technical details in the rewrite?"** Real concern. The plugin uses a carefully tuned prompt that emphasizes preservation of code, technical terms, and specific details. The developer claims it hasn't lost critical information in their testing, but I'd want to verify this over a few weeks of daily use before trusting it blindly.

**"This is just another example of AI bloat — why not just use a different model?"** Because Claude is genuinely the best at coding, and I don't want to sacrifice code quality just to avoid verbosity. The plugin lets me have both: Claude's reasoning quality with Gemma's writing quality.

**"Anthropic should just add a conciseness slider."** They should. But they haven't. The community built the solution because the vendor didn't.

## What I actually think

I installed this plugin yesterday. I've used it for about 4 hours of coding. Here's my honest assessment:

It's not perfect. Occasionally Gemma 4 over-compresses and I lose a nuance — a "but consider the edge case where..." that Claude slipped in between two layers of politeness. I've learned to keep Claude's original output visible in a background buffer, just in case.

But those edge cases happen maybe 10% of the time. The other 90% is pure relief. Claude says "I'd be delighted to assist" for the 48th time, and my terminal shows "Here's the fix:" followed immediately by a code block. It's like someone took the cotton wool off my screen.

The real value isn't technical. It's emotional. After 4 hours of coding, I'm less tired. Less irritated. Less likely to close my laptop and go do something else. The plugin removes a layer of cognitive friction that I didn't realize was draining me until it was gone.

## What this says about the future of AI UX

The Claudish translator is a canary in the coal mine. It signals that users are starting to treat AI outputs as raw material, not finished products. Claude generates. Gemma refines. I consume. The AI is no longer the endpoint — it's one step in a pipeline.

This pipeline approach is going to become the norm. We're already seeing it with agentic workflows: one model plans, another executes, a third reviews. The Claudish translator adds a new step: style transfer. And style transfer matters because *how* an AI says something affects what you do with it.

A verbose AI makes you a passive reader. A direct AI makes you an active builder. I know which one I want to be.

---

The plugin's GitHub is still small — a few hundred stars — but I'd bet real money it hits 5,000 within a month. Not because the code is revolutionary. Because it solves a problem that every Claude user feels in their bones, and it solves it with an elegance that makes you wonder why Anthropic didn't build it themselves.

Or maybe they tried. Maybe the politeness is a feature, not a bug. Maybe Anthropic's safety people believe that deferential language reduces the risk of users blindly trusting AI output. If that's the case, the community has just voted with 1,981 upvotes and a 97% ratio that Anthropic's safety calculus is wrong.

Either way, I'm keeping the plugin. My blood pressure thanks me.
