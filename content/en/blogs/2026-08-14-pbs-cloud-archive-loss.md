---
title: "Nine PBS lost 50TB and 70 years of history — and the cloud isn't the villain you think"
description: "A public TV station trusted a dying vendor, a data center, and a contract. Now 70 years of archives hang on a lawsuit against Iron Mountain. The lesson isn't 'never use the cloud' — it's that custody and ownership are not the same thing."
date: 2026-08-14
translationKey: "2026-08-14-pbs-cloud-archive-loss"
image: "/images/2026-08-14-pbs-cloud-archive-loss.svg"
tags:
  - "cloud"
  - "backup"
  - "data"
  - "archiving"
  - "infrastructure"
mathjax: false
---

## A story Kafka would have rejected as too implausible

Let me walk you through what happened to Nine PBS, the public television station in St. Louis, because every time I re-read the timeline I find a new layer of absurdity.

The station stored **50 terabytes** of archival material — roughly **70 years of programming** — with a cloud storage vendor called Open Source Storage (OSS), whom they'd worked with since 2019. In March of this year, the contract was up for renewal. OSS never responded, and then abruptly cut off the station's access to its own data, even though there were 30 days left to retrieve it.

So Nine PBS started digging. The vendor's website was gone. The company was listed as *delinquent* with the Colorado Secretary of State. And it turned out OSS didn't even store the data itself — it was sitting on **Iron Mountain's** infrastructure.

What follows is a chain of events that reads like a screenplay: a demand letter to Iron Mountain that goes unanswered. A lawsuit against OSS, paused when a man named James Tramel — claiming to be a managing partner of the group that "officially acquired" the company — reached out, communicated for a month, then vanished, later telling the station he'd "been defrauded" into buying OSS and that ownership had reverted to the previous owners. A court rules Nine PBS has every right to its data. Iron Mountain finally admits it holds the data, agrees to hand it over... then backtracks, claiming OSS owns it. Now Nine PBS is suing Iron Mountain too, just to keep the data from being deleted.

Seventy years of a public institution's history, held hostage between a dead company and a data center that won't release it without a court order, because *it's afraid of being sued by the dead company.*

## The easy take is wrong, and I want to say so up front

The internet's reaction to this story — and I've read the Reddit threads — is a smug chorus of "the cloud is someone else's computer" and "if you don't own the storage, you don't own the data" and "what did they expect?"

That take is lazy, and it's *wrong* in a specific way I want to be precise about.

This is not a story about the cloud failing. The data isn't corrupted. It isn't encrypted by ransomware. It isn't lost to a bit-rot or a drive failure or an AWS us-east-1 outage. The data is *fine*. It's sitting on tape in an Iron Mountain facility right now, perfectly intact, and Nine PBS can't get to it because a **legal** question — who has the right to *direct* the transfer — has no clean answer when the company in the middle dies.

The failure here isn't technological. It's **custodial**. The cloud isn't "someone else's computer" — that's a trite line that obscures more than it reveals. The cloud is someone else's *company*, with a balance sheet, a board, a Secretary of State filing, and a lifespan. When the company dies, your data becomes an asset in an estate nobody's administering. Your bits are fine. Your *title* to them is suddenly contested.

That's the real lesson, and it's much harder to tweet: **you can have perfect ownership of your data and zero custody of it.** Those are different things, and we conflate them constantly.

## The 3-2-1 rule isn't advice, it's a form of respect

I'll say the obvious thing that everyone else is saying, but I'll say it with the anger it deserves: this should never have been possible.

50 terabytes. For seventy years of a TV station's output. Let me put that number in perspective, because a few commenters noticed something I noticed too: 50TB is *small*. It's a single six-bay NAS with high-capacity drives and a modest budget — the kind of thing a determined hobbyist assembles in a weekend. Seventy years of archives, and it all fit in a box you could buy on a credit card.

The 3-2-1 rule — three copies, two different media, one off-site — isn't a nice-to-have for an institution whose entire job is to *preserve public memory*. It's the difference between "we had an incident" and "we lost our history." A fire in South Korea last year destroyed 858TB of government data. A German project to preserve 60,000 retro games collapsed this year when $1.8M in funding dried up. Preservation keeps failing, and it keeps failing *in the same way*: the archive was treated as a liability instead of an asset.

But — and this is where I'll defend the station against the schadenfreude — Nine PBS is public television. It's funded the way public television is always funded: begrudgingly, resentfully, and on the edge of being yanked away. I've worked places where "buy a backup NAS" was a request that took three quarters to clear. The engineer who argued for a second copy of everything probably lost the argument to a budget line. That's not an excuse; it's context. The *system* failed to value the archive, and then the vendor failed, and now a judge has to decide who gets to touch the tapes.

## Custody, escrow, and the boring fix nobody wants to fund

So what's the actual fix? It's not "never use the cloud," because self-hosting a data center has its own failure modes — fires, floods, drive failures, the accidental `rm -rf`, the person who leaves and takes the keys. The cloud vendors genuinely do replication and durability better than you will. The fix is to stop treating *any* single party as the custodian of last resort.

Three things, none of them sexy:

**One, the 3-2-1 rule with actual verification.** Having three copies is worthless if you've never restored from any of them. A backup you haven't tested is a rumor about a backup. Institutions with 70 years of history should be running restore drills the way schools run fire drills.

**Two, custody independence.** The off-site copy should not live behind the same vendor relationship as the primary copy. Nine PBS had *one* vendor (OSS) who had *one* vendor (Iron Mountain). That's not redundancy — that's a single point of legal failure dressed up as a supply chain. If your "backup" is just another SKU from the same dying company, you don't have a backup.

**Three, data escrow and exit clauses that actually work.** This is the part nobody talks about. A contract that says "you can retrieve your data within 30 days of termination" is only as good as the company that honors it — and as Nine PBS learned, the company may not *exist* by then. Real protection means escrow: a neutral third party holding the ability to release data, or at minimum a contractual right that survives bankruptcy. Most procurement people have never heard the word "escrow" in this context. They should have.

None of this is glamorous. It's the unglamorous plumbing of institutional memory, and it keeps losing the funding fight to things with dashboards and logos.

## Why this story matters beyond one TV station

I keep thinking about what "70 years of our organization's history" means in concrete terms. It's not just old episodes of a regional public-affairs show. It's the civil rights era recorded as it happened, local elections, the voices of people who are now dead, the unedited record of a city talking to itself for seven decades. Some of that tape exists nowhere else on earth.

When preservation fails, it fails *silently*. You don't get a pager alert. You get a Monday where someone finally checks and the data is gone, or locked, or — as here — legally quarantined. The cost of that loss isn't paid by the vendor or the data center or the acquiring company's fraud victim. It's paid by everyone who, twenty years from now, goes looking for a piece of their own past and finds a dead link.

That's the thing about archives: nobody funds them because nobody can measure the value of what they prevent. You can put a number on the cost of a data center outage. You can't put a number on the value of *still having* the recording of a 1965 town hall. So it gets line-itemed into irrelevance until it's gone.

## Where I land

I'm not mad at Iron Mountain, exactly — a data center refusing to release data without a court order, because it doesn't want to be sued by a possibly-fraudulent dead company, is being *reasonable*. I'm mad at the shape of the system that made this the reasonable move.

The story of Nine PBS is not a cautionary tale about the cloud. It's a cautionary tale about custody. About the difference between owning data and being able to reach it. About how an archive — the single most important thing a public broadcaster *has* — ended up legally entangled with a defunct vendor, a fraud victim, and a data center that just wants to not get sued.

The cloud didn't lose 70 years of television. A *chain of custody* broke, at the exact moment the chain included a company that no longer existed. And until we treat archives as what they are — infrastructure for the future's memory — we're going to keep reading versions of this story, with different names, until the tapes are gone for good.
