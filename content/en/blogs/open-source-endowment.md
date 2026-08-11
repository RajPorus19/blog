---
title: "The Open Source Endowment — a $750K bet that FOSS doesn't need VC money"
description: "The first endowment fund for open source just launched with $750K+ from 60 donors including HashiCorp, Elastic, Supabase, and cURL founders. This might be the most important FOSS funding experiment in a decade."
date: 2026-08-11
translationKey: "open-source-endowment"
image: "/images/open-source-endowment.svg"
tags:
  - "open-source"
  - "funding"
  - "sustainability"
  - "community"
  - "foss"
mathjax: false
---

## The problem we've been pretending doesn't exist

Open source has a funding problem. Not a small one. A structural one.

The numbers are absurd. cURL — probably the most deployed HTTP client in human history, running on billions of devices — was maintained by one person, Daniel Stenberg, who until recently funded his work through commercial support contracts. OpenSSL, the library that secures roughly 90% of the internet's TLS connections, was maintained by two people in 2014 when Heartbleed happened. XZ Utils, a compression library used by essentially every Linux distribution, was maintained by a single burned-out developer who got socially engineered by a state actor last year.

The pattern is the same: critical infrastructure maintained by individuals or tiny teams with no financial safety net. The software runs the world. The maintainers can barely run their own lives.

We've had solutions. GitHub Sponsors. Open Collective. Tidelift. Venture-funded open source companies (which inevitably pivot to "open core" and then "actually, the good parts aren't open anymore"). None of them have solved the fundamental problem: **open source infrastructure is a public good, and public goods are systematically underfunded in market economies.**

This is why the Open Source Endowment matters.

---

## What is the Open Source Endowment?

Announced this week on r/opensource and launched officially, the Open Source Endowment is the first dedicated endowment fund for free and open source software. It's modeled after university endowments: a pool of capital that's invested, with the returns used to fund FOSS projects in perpetuity.

The numbers so far: $750,000+ in committed capital, 60+ donors including founders of HashiCorp, Elastic, Supabase, cURL, Vue.js, and other significant FOSS projects. These aren't corporate donations from PR budgets — these are individuals who built their careers on open source, putting their own money into a structure designed to outlast them.

The mechanism is simple and radical: **don't spend the principal. Spend the returns. Forever.**

A $10 million endowment generating 5% annually produces $500,000 per year in sustainable funding. A $100 million endowment produces $5 million. These aren't VC-scale numbers, but they're *permanent*. No fundraising cycles. No grant applications. No dependency on a single corporate sponsor who might change their mind next quarter.

---

## Why endowments make sense for FOSS

I've spent years watching open source funding models come and go. Every single one has the same flaw: **they're not permanent.**

Venture funding runs out. Corporate sponsors get acquired or change strategy. Individual donations fluctuate with the economy. Even successful models like Open Collective are vulnerable — one bad year, one donor pulling out, and a project's funding disappears.

An endowment is different. The money is legally structured to exist in perpetuity. The principal is invested conservatively. The returns are distributed to projects based on a transparent process. It's boring finance applied to a problem that desperately needs boring solutions.

This matters because the software we depend on should not be funded like a startup. Startups are supposed to either grow or die. Core infrastructure is supposed to be stable, reliable, boring. The funding mechanism should match the mission.

Consider the alternative: every few years, we have a crisis where a critical piece of infrastructure is discovered to be maintained by one person with no funding, and the tech industry collectively panics for two weeks, donates some money, and then forgets until the next crisis. The Open Source Endowment is an attempt to break that cycle.

---

## The VC trap and why this is different

Let me say something controversial: **venture capital is incompatible with open source sustainability.**

VC funding requires exponential growth — 10x returns in 5-7 years. Open source projects, by their nature, do not generate exponential revenue. You cannot 10x the revenue of a library that's given away for free. So VC-funded open source companies inevitably do one of two things:

1. **Go open core** — keep the base open, charge for the good parts. This is the HashiCorp playbook (before they went full BUSL), the Elastic playbook (before they went SSPL), the GitLab playbook (which worked, somehow).
2. **Pivot to SaaS** — stop being an open source company and become a cloud company that happens to publish some code. MongoDB, Confluent, Databricks.

Neither of these is evil. Companies need to make money. But they're not *sustainability* strategies for open source projects. They're exit strategies for investors.

The Open Source Endowment doesn't need exits. It doesn't need growth. It needs the S&P 500 to go up over 30 years. That's a bet with pretty good odds.

---

## The founders putting their money where their code is

The donor list is the most interesting part of this. You have Mitchell Hashimoto (HashiCorp co-founder), who watched his company's licensing controversy from the inside. You have Evan You (Vue.js creator), who has built one of the most popular frontend frameworks in the world while maintaining a remarkably sustainable funding model through sponsorships. You have Daniel Stenberg (cURL), who knows exactly what it's like to maintain essential infrastructure alone.

These aren't philanthropists writing checks from a distance. These are people who have lived the problem. They've been the maintainer at 2 AM fixing a security vulnerability that affects millions of users. They've had the uncomfortable conversation where a company offers "exposure" instead of money. They've watched their projects get forked, relicensed, and commercialized by people who contributed nothing.

The fact that they're putting their own money into an endowment — not a foundation with their name on it, not a PR-friendly initiative, but a boring financial instrument — tells me they understand that open source needs permanent solutions, not temporary band-aids.

---

## What could go wrong

I'm optimistic about this, but I'm not naive. Endowments have their own problems.

**Governance.** Who decides which projects get funded? How do you prevent the endowment from becoming a clique of popular projects while ignoring the unglamorous infrastructure that actually runs the internet? The initial governance structure is a board of respected FOSS figures, but board dynamics can go wrong. I've seen too many open source foundations become political battlegrounds.

**Scale.** $750K is a start. It's not a solution. At a 5% return rate, that's $37,500 per year to distribute — barely enough to fund one maintainer part-time. The endowment needs to reach $10M, $50M, $100M to become meaningful. That requires either massive individual donations or institutional support. Both are hard.

**Selection effects.** Endowments tend to fund established, visible projects — the ones with name recognition. But the projects that need funding most are the invisible ones: the compression libraries, the TLS implementations, the DNS resolvers. The things nobody thinks about until they break. The endowment's impact depends entirely on its ability to identify and fund these invisible pillars.

**The "one more foundation" problem.** We already have the Linux Foundation, the Apache Foundation, the Python Software Foundation, the Eclipse Foundation, the OpenJS Foundation, and a dozen others. Adding another entity to the FOSS governance landscape creates coordination problems. The Open Source Endowment is different (it's a funding vehicle, not a project host), but the distinction will be lost on most people.

---

## Why I'm still hopeful

Despite all that, I think this is the most important FOSS funding experiment since GitHub Sponsors launched in 2019. Here's why:

First, **it changes the conversation.** For two decades, open source funding has been framed as a charity problem: "please donate to your favorite project." Endowments reframe it as an infrastructure problem: "we need permanent funding for permanent infrastructure." That's a more honest framing and a more solvable one.

Second, **it creates a template.** Even if the Open Source Endowment itself stays small, it proves the model. University endowments didn't start at $53 billion (Harvard's current endowment). They started small and grew over centuries. The first open source endowment creates a legal structure, a governance model, and a track record that others can replicate.

Third, **it's not asking for pity.** The "sad maintainer" narrative — the burned-out developer pleading for donations — is emotionally effective but strategically bankrupt. It frames open source as a charity case rather than what it actually is: critical infrastructure that the global economy depends on. The endowment model says: "this is infrastructure, fund it like infrastructure."

---

## What you should actually do

If you work at a company that depends on open source (that's every company), forward the Open Source Endowment announcement to your CTO. Ask what your company's FOSS sustainability strategy is. If the answer is "we don't have one," that's a problem.

If you're an individual developer, the important thing isn't donating $5 a month to a project (though that's nice). It's changing how you think about open source. The software you depend on is infrastructure. It needs permanent funding. The Open Source Endowment is one attempt at that. Support it, critique it, but don't ignore it.

The era of "open source is free" is ending. The question now is whether we build sustainable funding models, or wait for the next Heartbleed.
