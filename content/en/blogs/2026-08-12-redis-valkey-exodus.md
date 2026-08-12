---
title: "Redis → Valkey: the exodus has begun — and it's bigger than a database migration"
description: "STACKIT deprecates Redis the same day it launches Valkey. IONOS goes GA with Valkey-backed in-memory DB. European clouds are voting with their infrastructure. Here's what this means for open source governance, the fork phenomenon, and why Redis's license change might be the best thing that ever happened to the ecosystem."
date: 2026-08-12
translationKey: "2026-08-12-redis-valkey-exodus"
image: "/images/2026-08-12-redis-valkey-exodus.svg"
tags:
  - "redis"
  - "valkey"
  - "open-source"
  - "database"
  - "fork"
  - "governance"
mathjax: false
---

## The moment the dam broke

On March 20, 2024, Redis Ltd. announced that Redis would no longer be released under the BSD license. Future versions would use a dual license: the Redis Source Available License (RSALv2) and the Server Side Public License (SSPLv1). The blog post framed it as a natural evolution. The community heard something different: *the most popular in-memory database in the world is no longer open source.*

Within days, the Linux Foundation announced Valkey — a community fork of Redis 7.2.4, the last BSD-licensed release. Amazon, Google, Oracle, Ericsson, and Snap all backed it immediately. The message was unmistakable: the industry was not going to pay Redis Ltd.'s licensing tax.

Fast forward to August 2026. The fork has been alive for 28 months. And this week, something changed. The migration stopped being a theoretical commitment and started being *infrastructure in production.*

## STACKIT drops the hammer

STACKIT, the cloud arm of the Schwarz Group (you might know them as the company behind Lidl and Kaufland), made an announcement this week that went largely unnoticed in English-language tech media but lit up r/devops in Europe: they're deprecating their managed Redis offering on the exact same day they launch their managed Valkey store.

Not "we're evaluating Valkey." Not "we plan to offer both." Redis is gone. Valkey is the replacement. Zero transition period where both coexist. If you're a STACKIT customer running Redis, you have a clear migration path — and it ends at Valkey.

This is the kind of move that sends shivers through product teams. STACKIT serves a significant portion of the German enterprise market. These aren't hobby projects running on free tiers. These are production systems at companies that sell groceries to half of Europe. And their cloud provider just told them: Redis is dead here, long live Valkey.

## IONOS goes GA

The same week, IONOS — one of Europe's largest hosting providers — announced that their In-Memory Database v2 has reached General Availability. The v2 product is backed entirely by Valkey. Redis support is still available in v1, but the writing is on the wall: new customers get Valkey, existing customers are being nudged toward migration, and IONOS's engineering team isn't hiding the fact that their future investment goes into the fork.

IONOS is interesting because they're not just a cloud provider. They're a hosting company that serves millions of small and medium businesses across Europe — the kind of customers who don't read Hacker News and don't care about licensing drama. They just want their WordPress cache to work. And IONOS has decided that Valkey is the safer long-term bet for those customers.

## OVHcloud: patches and positioning

OVHcloud hasn't announced a Redis-to-Valkey migration (yet), but their actions this week tell a related story. They patched tens of thousands of machines against Januscape (CVE-2026-53359), a KVM escape vulnerability at the hypervisor level, in under a week. This is relevant because it demonstrates something about European cloud providers: when they decide to move, they move fast. The same operational machinery that patched 40,000 hypervisors in 7 days could migrate thousands of Redis instances to Valkey in a similar time frame if the strategic decision is made.

And the strategic case is getting harder to ignore. When your European competitors — STACKIT, IONOS, Scaleway — are all backing Valkey, staying on Redis becomes a positioning liability. European cloud customers increasingly care about digital sovereignty and open source purity. Running a source-available database from an American company doesn't align with that pitch.

## Why the license change backfired

Let's rewind to 2024 and look at what Redis Ltd. actually did. The RSALv2 license allows you to use, modify, and distribute Redis — but you can't offer it as a managed service. The SSPLv1, made famous by MongoDB in 2018, requires anyone offering Redis as a service to open-source their entire infrastructure stack.

The intent was clear: cloud providers (especially AWS) were making billions offering managed Redis while contributing nothing back to Redis Ltd. The license change was designed to force them to either pay for a commercial license or stop offering managed Redis.

The result: cloud providers didn't pay. They forked. And because Redis is a relatively simple piece of software (compared to, say, a full database engine like PostgreSQL), forking it and maintaining a compatible drop-in replacement is entirely feasible.

The fork also benefited from something Redis Ltd. didn't anticipate: **the community had been waiting for a reason to leave.** Redis's development had been increasingly driven by Redis Ltd.'s commercial priorities — modules, enterprise features, cloud integrations — rather than the core in-memory data store that made Redis popular in the first place. Valkey promised to return to those roots: fast, simple, open.

## The numbers that matter

Nobody's publishing hard migration numbers publicly — competitive intelligence and all that — but the signals are unambiguous:

- **Valkey GitHub:** 25,000+ stars, active development with contributions from Amazon, Google, and Oracle engineers
- **Package downloads:** Valkey's Docker image pulls have been growing 15-20% month-over-month for the past six months
- **Cloud adoption:** Every major cloud provider now offers a managed Valkey service, and several (like STACKIT) are making it their *only* in-memory offering
- **Enterprise tools:** Monitoring platforms, backup tools, and ORMs are adding Valkey support — the ecosystem is following the users

I've been tracking this migration since the fork was announced. In March 2024, I thought it would take 5 years for Valkey to become the default. In August 2026, I think it'll happen by mid-2027. The STACKIT announcement accelerates the timeline by at least a year.

## What this means for Redis Ltd.

I want to be fair to Redis Ltd. They built something extraordinary. Redis is one of the most elegant pieces of software ever written — a data structure server that redefined what an in-memory database could be. Salvatore Sanfilippo (antirez), Redis's original creator, is a genuine genius of systems programming.

But Redis Ltd. the company made a bet that didn't pay off. They bet that the cloud providers would rather pay a license fee than maintain a fork. That bet assumed that maintaining a Redis-compatible database is hard. It's not. It's work, but it's not hard — especially when the biggest cloud providers in the world are sharing the maintenance burden.

The real tragedy here is that Redis Ltd. might end up as a niche enterprise vendor selling Redis Enterprise to companies that need specific modules (RedisJSON, RediSearch, RedisGraph) while the open source world moves on to Valkey. That's not a bad business. It's just a much smaller business than "the database that powers the internet."

## The governance lesson

Every open source company is watching this play out. The Redis → Valkey migration is the most significant fork event since MySQL → MariaDB (2009), and it's teaching the same lesson: **the community will maintain the software if the license change gives them no other choice.**

But there's a difference this time. MariaDB was maintained primarily by the original MySQL developers who left Oracle. Valkey is maintained by *the users* — the cloud providers who needed Redis to stay open. This is a new model of open source governance: not benevolent dictator, not foundation, but **consortium of motivated users.**

Is this good? I think it's complicated. The consortium model ensures the software reflects user needs because the users are the maintainers. But it also means the software reflects the needs of *those specific users* — large cloud providers — which might not align with the needs of small companies or individual developers. Valkey's roadmap is set by Amazon, Google, and Oracle. That's better than a single vendor with a commercial agenda, but it's not the same as a community-driven project.

## The European angle

There's a specifically European dimension to this migration that the American tech press keeps missing. STACKIT, IONOS, OVHcloud, Scaleway — these are European companies choosing a fork over an American vendor's license change. In the context of GDPR, the European Cloud Services scheme (EUCS), and the broader push for digital sovereignty, this isn't just a technical decision. It's a geopolitical one.

When a German cloud provider serving German enterprises drops an American company's database in favor of a community fork, regulatory compliance is part of the calculus. The RSALv2 and SSPLv1 create legal uncertainty — especially the SSPL's "you must open-source your entire stack" provision, which has never been tested in European courts. Valkey, under the BSD license, has no such uncertainty.

European clouds are building a competitive advantage around regulatory clarity. Choosing Valkey over Redis is a signal to customers: "We've removed a legal risk from your stack." That message resonates in a market where GDPR fines can reach 4% of global revenue.

## The technical reality check

Let me add a note of technical honesty. Valkey today is essentially Redis 7.2.4 plus community patches. It's not better. It's not worse. It's the same thing with a different name and a different governance model.

But that's exactly the point. Nobody needed Redis to be *better*. Redis was already excellent. What the community needed was Redis to stay *open*. Valkey provides that. The feature differentiation will come later — Valkey's roadmap includes improvements to clustering, memory efficiency, and module APIs that Redis Ltd. was deprioritizing in favor of enterprise features.

For now, if you're running Redis in production, you can switch to Valkey with zero code changes. The wire protocol is identical. The commands are identical. The configuration is identical. It's the most boring migration in the history of database migrations, and that's exactly what makes it unstoppable.

## My prediction

Here's what I think happens next:

1. **By Q1 2027**, at least one of the Big Three cloud providers (AWS, Azure, GCP) will make Valkey their *default* in-memory offering, with Redis as a "legacy" option.

2. **By Q3 2027**, Redis Ltd. will announce a "Redis Community Edition" under a truly open license — not because they want to, but because the commercial logic of the RSALv2 will have collapsed.

3. **Valkey will develop its own identity.** The fork will diverge technically — new data structures, new clustering models, new persistence engines. By 2028, Valkey won't be "the Redis fork." It'll just be Valkey.

4. **The consortium model will spread.** Other open source projects facing similar licensing threats will adopt the Valkey playbook: find the last open version, fork it, and pool maintenance resources across motivated users.

The Redis → Valkey migration isn't just a database story. It's a template for how the open source community responds when a vendor closes the gates. And based on how fast STACKIT, IONOS, and others are moving, the template works.
