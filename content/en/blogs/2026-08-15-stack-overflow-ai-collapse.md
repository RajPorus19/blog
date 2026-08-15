---
title: "Stack Overflow lost 99% of its questions — and the reason is more uncomfortable than 'AI killed it'"
description: "The site went from 207,204 questions a month in 2014 to 1,442 in July 2026. Everyone wants to blame ChatGPT, but the decline started in 2014, years before the first LLM. The real story is about what happens when a knowledge commons alienates its own contributors — and then gets devoured by the machine it trained."
date: 2026-08-15
translationKey: "2026-08-15-stack-overflow-ai-collapse"
image: "/images/2026-08-15-stack-overflow-ai-collapse.svg"
tags:
  - "stack-overflow"
  - "ai"
  - "llm"
  - "community"
  - "knowledge"
  - "open-source"
mathjax: false
---

## The number that made me stop scrolling

There's a chart that's been making the rounds on r/programming this week, and I've now looked at it maybe forty times, and it still does something unpleasant to my stomach every single time.

The number at the top of the story is this: Stack Overflow recorded **1,442 new questions in July 2026**. Not per day. Per month. For context, at its peak in March 2014, the site saw **207,204 questions in a single month**. That's a decline of 99.3%. One of the largest, most important public archives of programming knowledge ever created is now receiving fewer questions than it got during its very first month of existence, back in 2008, when it was a half-finished side project and nobody knew what it was.

The thread has 3,800 upvotes. The comments are the interesting part — they're not gloating. They're not "good riddance." They're a lot of people quietly mourning something they didn't realize they loved until it was already gone, mixed with a smaller group insisting this is actually fine, actually good, because "we have ChatGPT now."

I want to talk about this properly, because I think the version of this story everyone is telling — "AI killed Stack Overflow" — is not just incomplete. It's *comfortable*. It lets us off the hook. And the real version is much more uncomfortable.

---

## The graph that tells a different story

The canonical chart here was surfaced a while back by Gergely Orosz in The Pragmatic Engineer, using data from the Stack Overflow Data Explorer, and popularized by Marc Gravell — who is, not incidentally, a top-10 all-time contributor to the site. It plots questions-per-month from 2009 to now, and it does not look the way you'd expect if the story were simply "ChatGPT showed up and everyone left."

The line climbs through the early 2010s, hits its peak around 2014, and then — *two full years before ChatGPT existed* — it starts to decline. Slowly at first, then faster. There's a pandemic bump in early 2020 (everyone stuck at home, remote work, Googling instead of asking colleagues). And then June 2020 onward, the decline steepens again. By the time ChatGPT launches in November 2022, the patient is already bleeding out. ChatGPT doesn't kill Stack Overflow. It arrives at the hospital, walks past the triage nurse, and pulls the plug.

So the first uncomfortable fact is this: **Stack Overflow was dying for eight years before the LLMs showed up.** The thing that's being mourned was already terminally ill, and most of us — including me, a daily user through that entire period — never stopped to notice.

---

## The part we don't like admitting: it did this to itself

Let me say the thing that gets you downvoted in every thread about this, but which I think is simply true: Stack Overflow's decline wasn't an accident of history. It was, in large part, a *choice*.

I started using the site around 2012, and I remember the exact texture of the culture that built it: ask a question, get an answer, upvote the good ones, and a kind of collective pride in making the whole thing better for the next person. Somewhere along the way — and the data says it was right around 2014, the year the decline began — that culture curdled. The site rolled out faster, more aggressive moderation tooling, and the tone of the place shifted from "let's help you" to "let's police you."

You know the ritual. You ask a question you genuinely can't answer after an hour of searching, and within ninety seconds it's **"marked as duplicate"** — of a question from 2011 that is tangentially related, in a different language, about a version of the framework that no longer exists, whose accepted answer is wrong. Or it's **"closed as off-topic"** because it's "opinion-based," as if the vast majority of what senior engineers actually need help with isn't, at some level, a judgment call. Or you get a comment from someone who clearly didn't read the question, explaining why your question is bad, in the tone of a DMV employee who hates you.

Gergely Orosz put it in one line that I've never been able to shake: *"I stopped asking questions around this time because the site felt unwelcome."*

That's the whole thing, right there. A knowledge commons lives or dies on whether the people who *create* the knowledge feel good about doing it. And Stack Overflow spent a decade systematically teaching its most valuable contributors — the people asking the hard questions and writing the good answers — that the cost of participating was being talked down to by a stranger. That's not "community." That's a hazing ritual with a search bar.

I'm not saying the moderation was all bad — a lot of it genuinely kept the place from becoming a garbage fire. But you can be right about every individual moderation decision and still be catastrophically wrong about the *culture* those decisions add up to. Stack Overflow optimized for cleanliness and lost its soul in the process.

---

## Then the machine arrived, and it was polite

Into this half-dead commons, in November 2022, walked ChatGPT. And here's the cruelest detail of the whole story: **ChatGPT was trained, in significant part, on Stack Overflow's data.** The very corpus of questions and answers that the community spent fifteen years building — often for free, often while being told their question was a duplicate — became the training material for the thing that would render that community unnecessary.

And ChatGPT did the one thing Stack Overflow had stopped doing: it answered *every* question, immediately, without judgment, with infinite patience. Your question is dumb? It doesn't care. It's a duplicate of something from 2011? It doesn't care. You've asked it twelve variations of the same thing in a row because the first eleven answers didn't quite work? It never once tells you you're wasting its time.

It's not that the answers are always right. They often aren't. It's that the *experience* is the exact opposite of what Stack Overflow had become. Orosz again: ChatGPT "is polite and answers all questions, in contrast to StackOverflow moderators." You don't beat a social institution with better answers. You beat it with a better *feeling*. And the feeling of asking an AI for help is "relief." The feeling of asking Stack Overflow for help, by the late 2010s, was "dread."

When those are your two options, the decline stops being a mystery.

---

## The parasite that ate its host

Here's the part of this that I think is going to matter more than any of the upvote counts, and which I almost never see anyone articulate clearly.

Stack Overflow's value was never just the *answers*. It was the **loop**. Someone hits a problem, asks in public, a stranger corrects them, they correct back, an expert weighs in with a nuance nobody else knew, and the result — the final, edited, community-vetted answer — is *better than any single person involved could have produced*. That loop, repeated tens of millions of times, is what built the corpus. The answers are the exhaust. The loop is the engine.

ChatGPT consumed the exhaust and reproduced it on demand. And in doing so, it stopped the engine. Nobody asks new questions anymore, so no new answers get written, so no new nuances get captured, so the corpus — the training data — stops growing. The machine is now running on a fixed snapshot of a commons that is no longer producing. It's the perfect parasite: it devoured the host, and it did so using the host's own generosity, while being, at every moment, *more pleasant to interact with than the host had ever been.*

And this is the detail that should actually worry everyone, not just people nostalgic for a website: **the answers still exist, but the thing that created them is gone.** We are living off the capital of a civilization of knowledge-sharing that we've already stopped contributing to. When the snapshot runs out of answers to a problem — a genuinely new problem, in a genuinely new framework, with genuinely new failure modes — there is no longer a public mechanism for generating the answer. There's just a model confidently hallucinating something that sounds right.

---

## What we actually lost

I keep trying to pin down what makes this feel like a loss rather than just a migration, and I think it's this: Stack Overflow was one of the last places on the internet where a total stranger would help you solve a hard problem *for the sake of the public record.*

Not for money. Not for clout (well, a little clout). Not because you were their coworker or their friend or their customer. Just because you asked, in public, and answering you made the internet slightly better for the next person with the same problem. That's an astonishing thing when you actually think about it — millions of people, donating their expertise to a shared resource that anyone could use. It was a genuine commons. One of the few the software industry ever built.

What replaces it, in practice, is worse along almost every axis that matters for the long term. The knowledge is moving into places that are **not public, not indexable, and not permanent**: private Discord servers, WhatsApp and Telegram groups, company Slack channels, and the memory of a model whose weights you can't inspect and whose training data you can't audit. A developer ten years from now won't be able to search for "why does this Kubernetes error happen" and find the answer, freely, on a page that links to the discussion that produced it. They'll get an answer from a black box, and they'll have no way to know if it's right, no way to see the reasoning, and no way to contribute their own hard-won correction back.

That's not progress. That's the enclosure of the commons, and we're doing it to ourselves, cheerfully, because the black box is nicer to us than the commons ever was.

---

## What I actually think

I'll be honest about where I land, because I've read forty comments this week insisting this is a non-story, and I think they're wrong in a specific and telling way.

Stack Overflow's decline is not a tragedy because a website got less popular. Websites die all the time. It's a tragedy because it's the clearest signal we've yet received about what the LLM era is actually *doing* to the economics of shared knowledge: it is converting a public good into a private product, and it is doing it so smoothly that the people being converted don't notice they're the ones supplying the raw material.

The uncomfortable truth I keep circling is that both parties are responsible, and pretending otherwise is the comfortable lie I mentioned at the top. Stack Overflow drove away the people who made it valuable — that part is on the site, and it started years before any AI. And the AI then ate the archive those people had already built — that part is on the rest of us, for assuming the archive would always be there to feed a machine that gives nothing back.

I don't know what the fix is. Maybe there isn't one — maybe the era of the public, indexable knowledge commons is just over, and the future is private silos and confident hallucinations, and we'll all be fine because the *answers* mostly work. But I know that when I hit a genuinely hard problem now, I don't ask Stack Overflow anymore, and I don't think you do either. I paste it into a chat window, get something that's usually close enough, and never once consider that the thing I'm talking to learned everything it knows from people who used to answer me for free, in public, for the record.

That's the part I can't stop thinking about. Not that the website is dying. That we're watching the commons be dismantled, and the only feeling most of us have about it is relief.
