---
title: "Secret Hitler"
description: "Version en ligne du jeu de société Secret Hitler — jeu de déduction politique multijoueur en temps réel avec WebSocket, animations et timers"
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
  - "jeu"
  - "fullstack"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19/secret-hitler
  - icon: fas fa-globe
    url: https://secret-hitler.porus.dev
showInHome: true
---

## Résumé

Une version en ligne complète du jeu de société Secret Hitler, jouable à 5–10 joueurs dans un navigateur. Parties en temps réel, animations immersives, timers configurables, mode spectateur, et déploiement conteneurisé.

J'ai tellement aimé ce jeu de société que j'ai voulu pouvoir y jouer n'importe où, sans installer quoi que ce soit. Le résultat : un clone fidèle, mais pensé pour le web — avec tout ce que ça implique en termes de temps réel, de résilience réseau et d'UX.

## Objectifs

- Reproduire fidèlement les règles complètes de Secret Hitler (nomination, vote, législatif, veto, pouvoirs exécutifs, conditions de victoire, chaos)
- Offrir une expérience multijoueur fluide avec WebSocket et animations
- Supporter les reconnexions après refresh/perte réseau sans casser la partie
- Déployer en production avec un stack solide (HTTPS, base de données, cache Redis)

## Stack

- **Django + DRF + Channels** — API REST + WebSocket asynchrone avec Daphne
- **SvelteKit** — frontend réactif avec stores, animations, composants cérémoniels
- **PostgreSQL** — persistance des rooms, joueurs, état de jeu
- **Redis** — channel layer pour le broadcast WebSocket multi-process + cache de sessions
- **Docker + Nginx** — déploiement conteneurisé avec proxy inverse, Cloudflare Tunnel
- **Upstash Redis** — fallback serverless pour les environnements sans Redis local

## Architecture

```
Navigateur (SvelteKit) ──WebSocket──→ Daphne (ASGI)
                                         │
                                    Channels (Redis)
                                         │
                                    GameConsumer
                                   ┌──────────────┐
                                   │ connexion    │
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

Le moteur de jeu est du Python pur, sans aucune dépendance Django — testable isolément. Le consumer WebSocket orchestre les phases de jeu (nomination → vote → législatif → exécutif) et gère les timers avec des transactions atomiques PostgreSQL pour éviter les doubles-timeout. Les reconnexions sont gérées proprement : un refresh de page évince l'ancienne connexion et restaure l'état privé du joueur (rôle, cartes en main).

## Fonctionnalités notables

- **Reconnexion transparente** — le refresh de page ne fait pas perdre la partie ; l'état privé (rôle, cartes) est restauré automatiquement
- **Cérémonies et animations** — enveloppe de rôle, révélation des votes, couronne présidentielle, exécution, chaos, game over
- **Timers de phase configurables** — nomination, vote, législatif, exécutif : chaque phase peut avoir un timeout
- **Mode spectateur** — permet d'observer une partie sans y participer
- **Vote anonyme** — optionnel, configurable par room
- **Pouvoir de veto** — fidèle aux règles avancées
- **Lois customisables** — textes de lois libérales et fascistes personnalisables
- **Tutoriel intégré** — pour les nouveaux joueurs
- **401 tests** — couverture moteur, API, WebSocket, et flows end-to-end

## Leçons apprises

- **Le cross-origin c'est l'enfer.** Déployer un frontend SvelteKit sur Vercel et un backend Django sur Render m'a forcé à implémenter un système de session par header (`X-Session-Key`) parce que les cookies third-party sont bloqués. Le WebSocket a nécessité un paramètre `?sk=` dans l'URL pour la même raison. 27 commits rien que pour le déploiement.

- **Un moteur de jeu pur change tout.** Extraire les règles du jeu dans un module sans aucune dépendance Django a rendu les tests unitaires triviaux et le debug rapide. 401 tests, dont des flows de partie complets.

- **Redis est indispensable pour les WebSockets en production.** Sans channel layer Redis, Daphne ne peut pas broadcaster entre workers. Et sans Upstash comme fallback serverless, le déploiement sur Render devenait impossible.

- **Le refresh recovery est un must.** Dans un jeu de société en ligne, les joueurs vont forcément changer d'onglet, perdre le WiFi, fermer leur navigateur. Si chaque déconnexion casse la partie, le jeu est injouable. Le système d'éviction de socket et de `reconnect_private_state` a résolu ça.
