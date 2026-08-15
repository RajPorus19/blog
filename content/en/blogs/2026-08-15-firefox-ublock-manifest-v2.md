---
title: "Firefox is now the last major browser that still runs uBlock Origin — and that should scare everyone"
description: "Chrome and Edge have finished phasing out Manifest V2, which means the last browser that natively runs uBlock Origin is the one with 3% market share, funded almost entirely by Google. The quietest story in tech this week is actually the loudest warning we've gotten about who owns the web."
date: 2026-08-15
translationKey: "2026-08-15-firefox-ublock-manifest-v2"
image: "/images/2026-08-15-firefox-ublock-manifest-v2.svg"
tags:
  - "firefox"
  - "chrome"
  - "browsers"
  - "privacy"
  - "adblock"
  - "manifest-v3"
  - "governance"
mathjax: false
---

## The Bluesky post that ended an era

Somewhere in the middle of this week, Mozilla posted a sentence on Bluesky that would have been completely unremarkable a decade ago and is now, somehow, a political statement:

*"Our support for uBlock Origin isn't going anywhere."*

It landed the same week that Chrome 150 quietly removed the last flags that let users keep uBlock Origin running, and Chrome 151 started closing off the remaining escape hatches. Microsoft, which spent years pretending Edge was the privacy-respecting alternative to Chrome, had already joined Google in announcing the end of Manifest V2 support. And so, with the quiet efficiency of a flag being deleted from a codebase, the entire browsing public found itself in a strange new world: **there is now exactly one major browser left that still runs the most popular ad blocker on the planet — and it has about 3% market share.**

The r/technology thread on this hit ten and a half thousand upvotes before I stopped watching it climb. The top comments were a mix of grief, gallows humor, and people asking — genuinely — whether it was time to go back to Firefox. As if Firefox were a country they'd emigrated from years ago and were now, reluctantly, checking flight prices to.

I want to take this seriously, because I think this is the most important browser story in a decade, and most of the coverage is treating it like a niche nerdfight. It isn't. This is the moment the open web's immune system got removed, and almost nobody noticed because the removal was boring.

---

## What Manifest V3 actually did

Let me back up, because the whole story only makes sense if you understand what "Manifest V3" is technically doing. Most of the press coverage glosses it as "Google changed how extensions work," which is true in the way that "the doctor changed how your organs work" is true.

Browser extensions are governed by a "manifest" — a JSON file that declares what an extension is allowed to do. Version 2 of that manifest, which every extension ecosystem ran on for years, exposed an API called `webRequest`. This let an extension *intercept and inspect* every network request a page made, and — crucially — **block** requests before they happened. That's the mechanism uBlock Origin is built on. When you load a page, uBlock Origin sees the request for `tracker.js`, checks it against a list of millions of rules, and cancels it at the network layer, before a single byte of the tracker ever reaches your machine.

Manifest V3 removed that. In its place, Google shipped something called `declarativeNetRequest` — an API where the browser itself, not the extension, does the blocking, using rules the extension must declare *up front*. And there's a hard cap on how many rules you can declare. Early builds capped it around 30,000; it's crept up since, but the point stands: an ad blocker that needs to match against millions of filter rules, do dynamic pattern matching, and inject cosmetic fixes on the fly simply **does not fit** in that model.

The result is the thing Google called "uBlock Origin Lite" — an MV3-compliant, deliberately-weakened version that the author, Raymond Hill, has been blunt about. It's uBlock Origin with its hands tied behind its back. It blocks less, it can't update its rule lists freely, and it fundamentally trusts the browser — i.e., the ad company — to do the blocking faithfully.

Here's the part I want you to sit with: the company that earns the overwhelming majority of its revenue from advertising spent years designing a technical architecture whose explicit effect is to make it structurally impossible for third parties to block advertising at full strength. Google was never going to say "we're banning ad blockers" — that would be a PR disaster. So instead they rebuilt the platform so that ad blockers *still technically exist* but can no longer do their job. It's the same playbook as "we're not removing the headphone jack, we're just removing the reason you'd want it."

---

## Why "Chromium monopoly" stopped being a talking point

There's a reason Chrome dropping MV2 cascades to everything. Chrome's rendering engine and extension platform — Chromium — now powers Chrome, Edge, Brave, Opera, Vivaldi, Arc, and basically every browser you've ever heard of except Firefox and Safari. The moment Google decides the platform's direction, it isn't a Google decision; it's an *industry* decision that Microsoft, Opera, and the rest then have to implement whether they like it or not.

That's what happened with Edge. Microsoft's position on uBlock Origin has, historically, been quietly decent. But Edge is Chromium. Google removed MV2 from Chromium, and Microsoft — which could in theory maintain a fork — chose not to. Why would they? Forking the world's most complex browser platform to preserve an API their own ads business has no love for is a losing trade. So the "choice" the browser market offers you narrowed by one, for free, without anyone casting a vote.

This is the actual thing that scares me, and it's not really about ads. It's that the web's *neutrality* — the property that made it the web and not a collection of walled gardens — was never guaranteed by law or by goodwill. It was guaranteed by the fact that there were multiple independent browser engines, and no single company could unilaterally decide what a web page was allowed to do. We're now down to, essentially, two-and-a-half engines: Chromium, WebKit (Safari), and Gecko (Firefox). And WebKit's extension model is so different and so constrained that, for practical purposes, **Firefox is the only mainstream browser where an independent developer can still write software that sees and controls what the browser does with your data.**

That's not a feature ranking. That's a structural monopoly, and MV3 is just the first visible symptom of it.

---

## The Brave asterisk

I should be fair, because there's a counterpoint floating around every thread about this, and it deserves to be answered honestly. Brave still runs uBlock Origin. Sort of.

Brave is Chromium, but Brave is also a company whose entire brand is ad-blocking — its own ad-blocker is built into the browser rather than shipped as an extension. So Brave has a strong incentive to keep MV2 alive, and it does so by patching Chromium to leave the old APIs in place. The catch is that this is a *workaround*, not a guarantee. It depends on Brave's willingness to keep maintaining an increasingly divergent fork of Chromium, forever, against the current of every upstream change Google makes. It's the same calculus Microsoft faced, just with a different P&L — and the moment Brave decides the maintenance cost isn't worth it, the workaround evaporates and there's nothing any user can do about it.

Compare that to Firefox, where support for uBlock Origin is a *stated policy*, backed by an actual decision to keep both MV2 and MV3 coexisting, and by Mozilla's own product identity. One of these is a commitment. The other is a patch. They are not the same thing, and the people glibly replying "just use Brave" are recommending a bridge that could be dismantled next quarter without warning.

---

## The irony that should be on the front page

Now here's the part of the story that never fails to make me a little bit nauseous, and which I have never once seen a mainstream outlet mention.

Mozilla — the nonprofit, the last independent browser, the people now standing between you and a web where every page is a surveillance ad — is funded, to the tune of roughly **80% of its revenue**, by a deal that makes Google the default search engine in Firefox. Google pays Mozilla hundreds of millions of dollars a year for that privilege. The company that is *actively dismantling* the open web's ad-blocking infrastructure is, simultaneously, the single biggest donor keeping the last ad-blocking browser alive.

Both parties are trapped in this arrangement. Google keeps paying because, ironically, it *needs* Firefox to exist — a browser with 3% market share is a cheap fig leaf for every antitrust argument that Google is a monopoly. And Mozilla keeps taking the money because, without it, Firefox doesn't survive. So the browser defending the open web is financially dependent on the company attacking the open web, and the company attacking the open web is financially dependent on the browser defending it, so that it can argue in court that the open web is doing just fine.

If you can read that paragraph without your faith in "the market will sort it out" taking some damage, I genuinely don't know what to tell you. This is the kind of tangle that no amount of techno-optimism unties. It's not a bug in the system. It *is* the system.

---

## What we actually lose

I keep coming back to a question that's easy to dismiss as melodrama but which I think is the real heart of this: what does it actually mean to lose the ability to say no to a web page?

An ad blocker isn't really about ads. It's about **consent**. The modern web is a machine that, by default, runs arbitrary code on your computer, sends your behavior to dozens of third parties you've never heard of, and renders content whose primary purpose is to manipulate your attention. uBlock Origin is the one tool that lets you say "no" to all of it, wholesale, with a single install, for free, forever. It's not an accessory. It's the last remaining brake pedal on a vehicle that would very much prefer you didn't have one.

And here's the thing nobody wants to admit: **the people who need that brake the most are not the people reading r/technology.** They're my parents. They're the person who clicks the "download" button that is actually three download buttons. They're everyone who doesn't know what a "manifest" is and shouldn't have to. The technical users who'll migrate to Firefox and be fine are the minority. The majority just got silently moved, by an API deprecation they'll never hear about, into a web where the ads and trackers are now *beyond their reach to block*. They didn't consent to that. They were just never asked.

---

## So what do I actually think?

I think the honest takeaway is uncomfortable, so I'll just say it: **the open web is being dismantled by its own custodian, and its last line of defense is a browser kept alive by the company doing the dismantling.** Everything else — the Bluesky posts, the Chrome flags, the Brave workarounds — is noise on top of that one sentence.

What do you do about it, practically? You do the only thing that has ever actually mattered in this fight: you make a choice that has a *structural* effect, not a cosmetic one. Switching to Firefox isn't about getting a better browser — on some benchmarks and some sites, it'll be slower, and yes, some sites will nag you about "unsupported browser." It's about being counted as a user of the one engine that isn't owned by an advertising company. Market share is the only language any of this respects, and Firefox's 3% is the entire reason this story has a villain instead of a foregone conclusion.

But I want to be clear-eyed about the limit of that advice. Me telling you to switch to Firefox is a band-aid on a structural problem, and I'd be lying if I framed it as anything more. The real fix — the one nobody in tech wants to talk about — is that we have allowed the foundational infrastructure of human communication to become the private property of a single advertising company, and no amount of browser loyalty is going to un-ring that bell. MV3 didn't create that problem. It just finally made it impossible to ignore.

The Bluesky post is true. Firefox's support for uBlock Origin isn't going anywhere. What's going away is everything else — the alternatives, the leverage, the illusion that we ever had a choice about any of this. And the scariest part isn't that it's happening. It's that it's happening so quietly that most people won't realize what they lost until the first time a page refuses to load without its trackers, and there's no button left to press.
