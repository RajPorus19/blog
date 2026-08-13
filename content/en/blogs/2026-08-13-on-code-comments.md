---
title: "On comments — the information only a code comment can carry, and why AI agents need it most"
description: "Helsing.ai's Jon Gjengset wrote the definitive taxonomy of comments that actually earn their place — TODOs, correctness arguments, load-bearing choices, Y-Statements. In the LLM era, the comment stopped being a nicety and became the context window your agents read."
date: 2026-08-13
translationKey: "2026-08-13-on-code-comments"
image: "/images/2026-08-13-on-code-comments.svg"
tags:
  - "code-comments"
  - "documentation"
  - "craft"
  - "llm"
  - "adr"
mathjax: false
---

## Bright orange, on purpose

There's an essay that's been doing the rounds on r/programming this week, and it opens with the kind of personal detail that tells you the author means it. Jon Gjengset — better known in Rust circles as jonhoo, the person behind crates like `haphazard`, `evmap`, and `async-ssh` — has, for the past decade, configured his syntax highlighter so that **code comments are bright orange** instead of the standard faded grey.

Not an accident. A forcing function. He wanted to get better at documenting code, and the best way to do that was to make comments so in-your-face that any extraneous noise becomes *painfully visible*.

I love this because it reframes the whole debate. The industry has spent years treating comments as a mild embarrassment — something you're supposed to grow out of, a sign you couldn't write "self-documenting code." Gjengset makes them bright orange and stares at the noise until it hurts. That's the energy I want in a blog post about documentation.

The essay is called ["On comments"](https://blog.helsing.ai/posts/on-comments/), it's on the Helsing.ai blog, and I think it's the most useful thing written about code comments in years. Not because it's controversial, but because it's *precise* — it refuses both dogmas at once.

---

## The two dogmas that both miss the point

There are two camps on comments, and Gjengset dismisses both in the same breath.

The first is the one we've all lived through: comments as noise. The `// increment i` above `i++`, the paragraph explaining the obvious, the stale comment from three refactors ago that now actively lies to you and makes you doubt your own reading of the code. We've all written them, we've all been burned by them. The reaction was predictable: *write fewer comments, write more readable code.*

The second camp is subtler, and it's the one senior engineers drift into. Make the code readable by breaking it up, using descriptive names, leaning on the type system. Gjengset's point is that these techniques are all good — and they *don't replace comments.* They make the **how** clearer. They do almost nothing for the **why.**

This is the sentence I want framed on the wall: **"Information that was never written down cannot be recovered by reading harder."**

Readable code tells you what it's doing and how. It cannot tell you *why the obvious alternative won't work*, or *why that constant is 1492 and not 1500*, or *why this function must never be exported.* That information only exists in a comment, or it doesn't exist at all.

---

## Documentation is for consumers. Comments are for collaborators.

Early in the essay there's a distinction that reframes everything: Gjengset is talking about *code comments*, not in-code documentation like Rust's `///` doc comments. "Documentation is written for your consumers. Comments are written for your collaborators."

That's the cleanest split I've ever seen for this argument. `///` comments have a defined audience — someone using your API from the outside, who will never read your source. Regular `//` comments have a different audience: the person who has to *change* the code. Their needs are entirely different. Conflating the two is how you end up with a crate full of polished doc comments and not a single note about why the gnarly bit is gnarly.

Then he does the thing that makes the essay actually useful: he gives a taxonomy. Not "write good comments" as a vibe, but *specific categories* of comments that are nearly always worth writing. Let me walk through them, because each one maps to a real failure I've personally committed.

---

## The taxonomy: nine comments that earn their keep

**TODOs.** You know the code isn't finished and it's fine. The rule isn't "write TODOs" — it's *write enough information that the TODO is usable six months later.* When everything's fresh, you jot two keywords. When a junior engineer finds it after you've left the team, those two keywords are useless. Multi-line TODOs are fine. Outlines of the resolution are fine. The only unforgivable TODO is the one that's a mystery to its future reader.

**References.** Code with a strong connection to an external source — a lightly-modified copy of code elsewhere, an encoding of an algorithm from a paper or book — deserves that connection written down. And it should be a **permalink**, not a `main` branch link that'll rot. Press `y` on GitHub before copying the URL. Document any divergence from the reference too, and *why* you diverged.

**Correctness arguments.** Proofs, informal or formal, that non-trivial code does the right thing. Tests verify the *what*, code shows the *how*, but the reasoning for *why those steps reliably produce the outcome* lives only in a comment. Gjengset pairs this with assertions like `unreachable!` where possible. And he drops a gloriously honest pro-tip: *don't stop in the middle of a correctness argument when you realize it doesn't hold and commit the partial proof.* He links to a real commit where he did exactly that.

**Hard-learned lessons.** The rule is beautifully simple: if you spent more than thirty minutes getting something to work, and the fix was a brief, unintuitive incantation, *comment it.* You didn't realize it was needed thirty minutes ago — so the next person won't either. Even if you don't know *why* it's needed, document how you arrived at it and what breaks without it.

**Rationale for constants.** We've all seen `max_packet_size = 1492` and not known whether that number was load-bearing or arbitrary. A comment should say what the constant represents, how it was chosen, and the consequences of changing it. And — this is my favorite line in the whole essay — *it's fine to say a value was chosen mostly at random.* That's still information the next person desperately needs.

**Load-bearing choices.** When correctness depends on a seemingly-innocuous detail elsewhere — "we must collect into a BTreeSet here because the code below assumes ordered iteration" — that detail needs a comment, or an innocent future refactor will swap it for a HashSet and quietly break prod.

**Algorithm outlines.** When a simple algorithm gets lost among the syntax, a high-level outline — up top or interspersed — tells the reader which "part" they're in. A lightweight form of literate programming.

**"Why not"s.** When code *deliberately* violates convention — eschews the obvious type or helper — write down why. Otherwise everyone re-learns it the hard way. This is the corollary to hard-learned lessons: not "why is this line necessary," but "why didn't you do it the obvious way."

**Intentional trade-offs.** When you evaluate options and pick one, document the decision. Otherwise the discussion gets re-threaded every few years. This is where Gjengset introduces something genuinely new.

---

## Y-Statements: the ADR that lives next to the code

Trade-offs are traditionally captured in Architecture Decision Records — which is great, except ADRs live *separate from the code*, get forgotten, and go stale. Gjengset's attempt to square that circle is a stricter, more concise format written *directly in comments next to the code they justify*, using Y-Statements:

> In the context of `<use case>`, facing `<concern>` we decided for `<option>` to achieve `<quality>`, accepting `<downside>`.

The format is deliberately strict so every part is considered every time. You can't skip a field, and you're supposed to write *concise fragments*, not paragraphs.

And here's the part that made me grin: Helsing open-sourced a tiny tool to go with it. [`yadr`](https://github.com/helsing-ai/yadr) walks a source tree and lists every decision, lets you print one in full, and — in CI — *asserts that all Y-Statements follow the convention, down to punctuation.* A linter for architectural reasoning. I find that absurd and wonderful in equal measure.

---

## What changed with LLMs: everything

Here's where the essay becomes essential reading for 2026 rather than just good advice.

Gjengset's closing argument is that LLM-assisted development doesn't reduce the importance of comments — it *inverts* it. A coding agent arrives at your file with zero knowledge of why that retry loop is there, what happened in the design discussion, or which choices were deliberate versus arbitrary. It's "a new engineer who is fast and confident (and has endless energy), but knows nothing about why your codebase is the way it is." Which makes it *exactly* the target audience for comments that convey information beyond the code.

There are two reasons this matters. The first is that comments get injected directly into the agent's context window — it doesn't have to notice there's an ADR somewhere else, or stumble on the right commit message or Jira ticket. The comment is *right there*, in the file it's editing.

The second is the heuristic that closes the essay, and I think it's going to become standard practice: **look for the corrections you repeatedly have to give your agents, and embed those as strategically placed comments instead of additional stanzas in your `AGENTS.md`.** The corrections you're making over and over are precisely the "why" knowledge the code can't express. Writing it down once, in the file, is the durable version of the thing you keep having to say.

---

## Three writing rules for comments

Gjengset ends the craft section with three rules for writing comments *as technical writing*, and they're worth repeating verbatim-ish because they're the difference between a comment that helps and one that doesn't:

**Bytes are cheap.** Don't be terse. Use full sentences and punctuation. The tired, undercaffeinated, in-a-rush version of you from two years ago will thank you.

**Remember the reader.** Suppress your own knowledge and read the comment back. Words like "of course," "trivial," "obviously," and "just" are red flags that you're assuming context the reader doesn't have.

**Precision matters.** Typos, ambiguous punctuation, and invalid references to variables or functions can lead the reader to the wrong conclusion. Read your comments an extra time.

And then the genuinely mature closing note, which most essays on this topic would never bother to include: **don't mandate comment perfection.** Developers have a limited number of spoons. Sometimes landing good code with sub-par comments is right, and improving the comments is a separate task — ideally one someone else does, since they lack the author's implicit knowledge and will write clearer for it. There's no correct ratio of comments to code. Good commenting is judgment, empathy, and foresight — not a mechanical task.

---

## What I actually think

I've been the person who wrote no comments because the code was "obvious." I've been the person who wrote a `// TODO: fix this` and then couldn't remember what "this" was a month later. I've been the person who deleted a "stale" comment only to rediscover, painfully, that it was load-bearing. The essay reads like it was written by someone who has been all three people too, which is why it lands.

But the reason I think this essay matters *right now*, specifically, is the LLM section. We're all figuring out how to work with agents that have infinite energy and zero context. We're all discovering that the bottleneck isn't the model's intelligence — it's the *context we hand it*. And the cheapest, most durable way to hand an agent context is the thing we've been neglecting for a decade: a comment, in the file, explaining the why.

The industry spent years trying to make comments obsolete through readability and naming and types. It turns out they were never optional. They were just waiting for a reader who would actually appreciate them — and that reader turns out to be a tireless, contextless, slightly-too-confident machine that will happily delete your load-bearing `BTreeSet` unless you wrote down why it was there.

Bright orange, it turns out, was ahead of its time.

---

*"On comments" is by Jon Gjengset, published on the [Helsing.ai blog](https://blog.helsing.ai/posts/on-comments/). The companion tool, `yadr`, is open source at [github.com/helsing-ai/yadr](https://github.com/helsing-ai/yadr).*
