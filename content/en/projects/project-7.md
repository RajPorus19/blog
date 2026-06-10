---
title: "Secret Hitler"
description: "Online version of the Secret Hitler board game — real-time multiplayer political deduction with WebSockets, animations, and configurable timers"
date: 2026-04-04
image: "/images/secret-hitler-banner.svg"
badges:
  - "Django"
  - "Svelte"
  - "WebSocket"
  - "Docker"
  - "PostgreSQL"
tags:
  - "dev"
  - "backend"
  - "frontend"
  - "game"
  - "fullstack"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19/secret-hitler
  - icon: fas fa-globe
    url: https://secret-hitler.porus.dev
showInHome: true
---

## Summary

A complete online version of the Secret Hitler board game, playable by 5–10 players in a browser. Real-time matches, immersive animations, configurable timers, spectator mode, and containerized deployment.

I loved this board game so much I wanted to play it anywhere, without installing anything. The result: a faithful clone, designed for the web — with everything that implies for real-time networking, resilience, and UX.

## Goals

- Faithfully reproduce the full Secret Hitler ruleset (nomination, voting, legislative, veto, executive powers, win conditions, chaos)
- Deliver a smooth multiplayer experience with WebSocket and animations
- Support reconnection after page refresh/network loss without breaking the game
- Deploy to production with a solid stack (HTTPS, database, Redis cache)

## Stack

- **Django + DRF + Channels** — REST API + async WebSocket via Daphne
- **SvelteKit** — reactive frontend with stores, animations, ceremonial components
- **PostgreSQL** — room, player, and game state persistence
- **Redis** — channel layer for multi-process WebSocket broadcast + session cache
- **Docker + Nginx** — containerized deployment with reverse proxy, Cloudflare Tunnel
- **Upstash Redis** — serverless fallback for environments without local Redis

## Architecture

```
Browser (SvelteKit) ──WebSocket──→ Daphne (ASGI)
                                       │
                                  Channels (Redis)
                                       │
                                  GameConsumer
                                 ┌──────────────┐
                                 │ connection   │
                                 │ dispatch     │
                                 │ game actions │
                                 │ timers       │
                                 └──────────────┘
                                       │
                                  PostgreSQL
                                 ┌──────────────┐
                                 │ Room         │
                                 │ Player       │
                                 │ GameState    │
                                 │ PlayerRole   │
                                 │ Vote         │
                                 └──────────────┘

                              Nginx (reverse proxy)
                             ┌──────────────────┐
                             │ /       → SPA    │
                             │ /api/   → Django │
                             │ /ws/    → Daphne │
                             │ /admin/ → 404    │
                             └──────────────────┘

                            Cloudflare Tunnel → HTTPS
```

The game engine is pure Python with zero Django dependencies — fully testable in isolation. The WebSocket consumer orchestrates game phases (nomination → voting → legislative → executive) and manages timers with atomic PostgreSQL transactions to prevent duplicate timeouts. Reconnections are handled cleanly: a page refresh evicts the old connection and restores the player's private state (role, cards in hand).

## Notable Features

- **Seamless reconnection** — page refresh doesn't lose your game; private state (role, cards) is restored automatically
- **Ceremonies and animations** — role envelope, vote reveal, presidential crown, execution, chaos, game over
- **Configurable phase timers** — nomination, voting, legislative, executive: each phase can have a timeout
- **Spectator mode** — watch a game without participating
- **Anonymous voting** — optional, configurable per room
- **Veto power** — faithful to the advanced rules
- **Custom laws** — customizable liberal and fascist law texts
- **Built-in tutorial** — for first-time players
- **401 tests** — covering engine, API, WebSocket, and end-to-end flows

## Lessons Learned

- **Cross-origin is hell.** Deploying a SvelteKit frontend on Vercel and a Django backend on Render forced me to implement a header-based session system (`X-Session-Key`) because third-party cookies are blocked. WebSocket required a `?sk=` query parameter for the same reason. 27 commits just for deployment.

- **A pure game engine changes everything.** Extracting the game rules into a module with zero Django dependencies made unit tests trivial and debugging fast. 401 tests, including full game flows.

- **Redis is non-negotiable for production WebSockets.** Without a Redis channel layer, Daphne can't broadcast between workers. And without Upstash as a serverless fallback, deploying on Render became impossible.

- **Refresh recovery is a must.** In an online board game, players will inevitably switch tabs, lose WiFi, or close their browser. If every disconnect breaks the game, it's unplayable. The socket eviction and `reconnect_private_state` system solved this.
