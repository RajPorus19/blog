---
title: "Secret Hitler — Devlog 1: Building an Online Board Game in 5 Days"
description: "87 commits, 401 tests, and a war against cross-origin cookies. How I turned a board game into a real-time web application."
date: 2026-06-10
translationKey: "devlog-secret-hitler"
image: "/images/secret-hitler-banner.svg"
tags:
  - "devlog"
  - "secret-hitler"
  - "game"
  - "fullstack"
  - "django"
  - "svelte"
  - "websocket"
mathjax: false
---

## Why Secret Hitler?

Secret Hitler is one of my favorite board games. Five to ten players, hidden roles, deduction, elections, laws to pass, powers to activate. A chaos mechanic that forces alliances and betrayals. But here's the thing: to play it, you need to be in the same room, with the physical board. And during a certain recent period, that wasn't always possible.

So I decided to build an online version. Not a quick prototype. A complete version, faithful to the rules, with animations, real-time play, and a proper deployment. All in five days.

This devlog covers how it went — the technical choices, the struggles, and what I learned.

## Day 1: The Foundation Sprint

The first day was the most intense. 22 commits. I laid all the foundations in a single session.

**The game engine.** The first decision, and probably the most important one, was to completely separate the game logic from the web framework. The engine is a pure Python module, with zero Django imports. It takes deterministic inputs (a shuffled deck, a list of players, votes) and returns results. No global state, no database access.

This choice changed everything going forward. Testing the game rules became trivial: call a function with parameters, check the return value. No need to mock an HTTP request or a WebSocket connection. Win conditions, role assignment, chancellor eligibility — all testable in isolation.

**The Django backend.** In parallel, I built the REST API (Django + DRF) and the WebSocket consumer (Django Channels). The consumer is the conductor: it receives player actions, calls the game engine, updates the database, and broadcasts results to the entire room via Redis.

**The SvelteKit frontend.** Landing page, lobby, game board. Nothing visually polished — just the functional skeleton. But the bases were there: WebSocket connection, board display, basic interaction.

By the end of day 1, you could create a room, join it, start a game, assign roles, nominate a chancellor, vote, and play through a full legislative session. The core flow was in place.

## Day 2: Identity and Advanced Rules

19 commits. Now that the skeleton worked, it needed flesh.

First, visual identity: customizable avatars (10 faces, 12 colors), lobby selection, persistence via session. It sounds cosmetic, but in a social deduction game, visually recognizing players is crucial — especially when a game lasts 45 minutes.

Then, advanced rules: presidential veto, configurable phase timers, anonymous voting, customizable laws. Each new rule touched the engine, the consumer, and the frontend simultaneously. The veto, for example, adds an entire step to the legislative flow — the chancellor can propose a veto, the president must accept or reject it, and if both agree, the law is discarded. This forced me to rethink the phase state machine.

And finally, social lobby features: ready check to prevent accidental starts, host kick, spectator mode for watching without playing.

## Day 3: The Ceremonies

16 commits. This is the day the game went from "functional" to "immersive."

Animations aren't luxury in a social game. They provide rhythm, tension, and communicate game state without anyone needing to read a message. I added:

- The role envelope (dramatic opening at game start)
- The ceremony wheel (phase transition)
- The government reveal (president + chancellor)
- The vote reveal (ballots dropping into the urn)
- The presidential crown flight
- The execution
- The chaos vignette (when three elections fail)
- Game over with full role reveal

Each ceremony has its own Svelte component, its own timings, its own sound effects. It's clockwork — the animation must finish before the next WebSocket message arrives, or the state desynchronizes.

I also implemented hover interactions: when the president mouses over a chancellor candidate, all players see that hover in real time. This adds a layer of social reading that doesn't exist in the physical game — you can see who the president is *considering* nominating, before they even click.

## Day 4: Deployment Hell

27 commits. The most frustrating day, and the one where I learned the most.

The deployment plan was simple on paper: SvelteKit frontend on Vercel, Django backend on Render, communication via REST API + WebSocket. In practice, I discovered that **third-party cookies are blocked by default** in most modern browsers. My Django session system, which used cookies to identify players, was broken in production.

The fix took 27 commits:

1. First, I tried configuring cookies for cross-origin: `SameSite=None`, `Secure`. It worked in theory, not in practice — Safari and Firefox still blocked them.
2. Then I switched to a header-based session system: the frontend stores the session key in `localStorage` and sends it in an `X-Session-Key` header. For WebSocket, which has no custom headers, I passed the key as a `?sk=` query parameter.
3. Then I had to deal with Redis. Render's free plan doesn't include Redis, so I configured Upstash (serverless Redis) as a fallback. Problem: the localhost Redis URL from docker-compose isn't accessible from Render, so I had to filter out localhost URLs.
4. Then Django sessions weren't surviving Render redeployments — fix: store sessions in Redis instead of the database.
5. Then WebSocket was rejecting connections because Daphne's routing didn't recognize the `/ws/` prefix added by Render's proxy.

Every time I thought I'd fixed the issue, a new symptom appeared. It was a hydra. But by the end of the day, the site was running on HTTPS, players could create rooms, and WebSockets were working between Vercel and Render.

## Day 5: Resilience

3 commits. The final day was dedicated to a problem I'd been putting off: **page refresh**.

In an online game, players switch tabs, lose WiFi, accidentally close their browser. If every disconnect kicks them out of the game, it's unplayable.

The solution: a socket eviction system. When a player reconnects after a refresh, their new WebSocket connection "steals" the slot from the old one. The old one receives a specific close code telling it to shut down cleanly (rather than triggering a disconnect notification). The new one sends a `reconnect_private_state` that restores the player's role, cards in hand, and any information they had (investigation results, deck peek).

I also finalized the production Docker configuration: PostgreSQL, Redis, Daphne behind Nginx, all behind a Cloudflare Tunnel. And disabled `/admin/` access at the Nginx level (returns a 404).

## What I Learned

**Separate the engine from the framework.** This was the best decision I made. The `engine.py` module (352 lines, zero Django dependencies) made unit tests trivial and rule bugs easy to reproduce and fix.

**Cross-origin is not a detail.** If I had hosted the frontend and backend on the same domain, I would have saved an entire day. Next time, I'm deploying everything behind a single Nginx from the start.

**Ceremonies are worth it.** In a social game, animations aren't decorative — they communicate state, create tension, and make the experience memorable. Players wait for the vote reveal like they'd wait for a physical ballot count.

**Redis is mandatory for production WebSockets.** Without a channel layer, Daphne can't broadcast between workers. And if you're using a managed service like Upstash, make sure your fallback URLs are clean — localhost has no place in a production config.

**401 tests change everything.** Every refactor, every new deployment, every bug fix — the tests were there to tell me if I'd broken something. In a project where a single misimplemented rule can ruin an entire game, this is essential.

## What's Next

The game is online and functional. Future devlogs will cover post-launch improvements: refactoring the consumer (2385 lines is too much), rate limiting, and maybe a ranked mode.

Until then, you can play at [secret-hitler.porus.dev](https://secret-hitler.porus.dev) — create a room, invite friends, and see if you can pass your fascist laws without getting caught.
