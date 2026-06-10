---
title: "Secret Hitler — Devlog 1 : Construire un jeu de société en ligne en 5 jours"
description: "87 commits, 401 tests, et une guerre contre les cookies cross-origin. Comment j'ai transformé un jeu de plateau en application web temps réel."
date: 2026-06-10
translationKey: "devlog-secret-hitler"
image: "/images/secret-hitler-banner.svg"
tags:
  - "devlog"
  - "secret-hitler"
  - "jeu"
  - "fullstack"
  - "django"
  - "svelte"
  - "websocket"
mathjax: false
---

## Pourquoi Secret Hitler ?

Secret Hitler est un de mes jeux de société préférés. Cinq à dix joueurs, des rôles cachés, de la déduction, des élections, des lois à voter, des pouvoirs à activer. Une mécanique de chaos qui force les alliances et les trahisons. Mais voilà : pour y jouer, il faut être dans la même pièce, avec le plateau physique. Et pendant une certaine période récente, c'était pas toujours possible.

J'ai donc décidé d'en faire une version en ligne. Pas un prototype rapide. Une version complète, fidèle aux règles, avec des animations, du temps réel, et un déploiement propre. Le tout en cinq jours.

Ce devlog raconte comment ça s'est passé — les choix techniques, les galères, et ce que j'ai appris.

## Jour 1 : Le sprint fondateur

La première journée a été la plus intense. 22 commits. J'ai posé toutes les fondations en une seule session :

**Le moteur de jeu.** La première décision, et probablement la plus importante, a été de séparer complètement la logique du jeu du framework web. Le moteur est un module Python pur, sans aucune import Django. Il prend des inputs déterministes (un deck mélangé, une liste de joueurs, des votes) et retourne des résultats. Pas d'état global, pas d'accès à la base de données.

Ce choix a tout changé pour la suite. Tester les règles du jeu devenait trivial : on appelle une fonction avec des paramètres, on vérifie le retour. Pas besoin de mocker une requête HTTP ou une connexion WebSocket. Les win conditions, l'assignation des rôles, l'éligibilité du chancelier — tout ça est testable en isolation.

**Le backend Django.** En parallèle, j'ai construit l'API REST (Django + DRF) et le consumer WebSocket (Django Channels). Le consumer est le chef d'orchestre : il reçoit les actions des joueurs, appelle le moteur de jeu, met à jour la base de données, et broadcast les résultats à toute la room via Redis.

**Le frontend SvelteKit.** Landing page, lobby, game board. Rien de visuellement abouti — juste l'ossature fonctionnelle. Mais les bases étaient là : connexion WebSocket, affichage du plateau, interaction de base.

À la fin du jour 1, on pouvait créer une room, rejoindre, lancer une partie, assigner les rôles, nommer un chancelier, voter, et jouer une session législative complète. Le flow de base était en place.

## Jour 2 : L'identité et les règles avancées

19 commits. Maintenant que le squelette fonctionnait, il fallait lui donner de la chair.

D'abord, l'identité visuelle : avatars personnalisables (10 visages, 12 couleurs), sélection dans le lobby, persistance via la session. Ça paraît cosmétique mais dans un jeu de déduction sociale, pouvoir reconnaître les joueurs visuellement est crucial — surtout quand la partie dure 45 minutes.

Ensuite, les règles avancées : veto présidentiel, timers configurables par phase, vote anonyme, lois customisables. Chaque nouvelle règle touchait à la fois le moteur, le consumer, et le frontend. Le veto, par exemple, ajoute une étape entière au flow législatif — le chancelier peut proposer un veto, le président doit l'accepter ou le refuser, et si les deux sont d'accord, la loi est défaussée. Ça m'a forcé à repenser la machine d'état des phases.

Et enfin, les fonctionnalités sociales du lobby : ready check pour éviter les lancements accidentels, kick par le host, mode spectateur pour observer sans jouer.

## Jour 3 : Les cérémonies

16 commits. C'est le jour où le jeu est passé de "fonctionnel" à "immersif".

Les animations ne sont pas du luxe dans un jeu social. Elles donnent du rythme, de la tension, et elles communiquent l'état du jeu sans que personne n'ait besoin de lire un message. J'ai ajouté :

- L'enveloppe de rôle (ouverture dramatique au début de partie)
- La roue de cérémonie (transition de phase)
- La révélation du gouvernement (président + chancelier)
- La révélation des votes (les bulletins qui tombent dans l'urne)
- Le vol de la couronne présidentielle
- L'exécution
- La vignette de chaos (quand trois élections échouent)
- Le game over avec révélation complète des rôles

Chaque cérémonie a son propre composant Svelte, ses propres timings, ses propres effets sonores. C'est un travail d'horlogerie — il faut que l'animation se termine avant que le prochain message WebSocket n'arrive, sinon l'état se désynchronise.

J'ai aussi implémenté les interactions de survol : quand le président passe sa souris sur un candidat au poste de chancelier, tous les joueurs voient ce survol en temps réel. Ça ajoute une couche de lecture sociale qui n'existe pas dans le jeu physique — tu peux voir qui le président *envisage* de nommer, avant même qu'il ne clique.

## Jour 4 : L'enfer du déploiement

27 commits. La journée la plus frustrante, et celle où j'ai le plus appris.

Le plan de déploiement était simple sur le papier : frontend SvelteKit sur Vercel, backend Django sur Render, communication via API REST + WebSocket. Dans la pratique, j'ai découvert que **les cookies third-party sont bloqués par défaut** dans la plupart des navigateurs modernes. Mon système de session Django, qui utilisait des cookies pour identifier les joueurs, était cassé en production.

La solution a pris 27 commits :

1. D'abord, j'ai essayé de configurer les cookies pour le cross-origin : `SameSite=None`, `Secure`. Ça marchait en théorie, pas en pratique — Safari et Firefox bloquaient quand même.
2. Ensuite, j'ai basculé sur un système de session par header : le frontend stocke la clé de session dans `localStorage` et l'envoie dans un header `X-Session-Key`. Pour le WebSocket, qui n'a pas de headers custom, j'ai passé la clé en query param `?sk=`.
3. Puis il a fallu gérer Redis. Le plan gratuit de Render n'inclut pas Redis, donc j'ai configuré Upstash (Redis serverless) en fallback. Problème : l'URL Redis locale de docker-compose n'est pas accessible depuis Render, donc j'ai dû filtrer les URLs localhost.
4. Ensuite les sessions Django ne survivaient pas aux redéploiements Render — fix : stocker les sessions dans Redis plutôt qu'en base.
5. Puis le WebSocket refusait les connexions parce que le routing Daphne ne reconnaissait pas le préfixe `/ws/` ajouté par le proxy Render.

À chaque fois que je croyais avoir réglé le problème, un nouveau symptôme apparaissait. C'était une hydre. Mais à la fin de la journée, le site tournait en HTTPS, les joueurs pouvaient créer des rooms, et les WebSocket fonctionnaient entre Vercel et Render.

## Jour 5 : La résilience

3 commits. La dernière journée a été consacrée à un problème que j'avais repoussé : **le refresh de page**.

Dans un jeu en ligne, les joueurs changent d'onglet, perdent le WiFi, ferment leur navigateur par erreur. Si chaque déconnexion les exclut de la partie, le jeu devient injouable.

La solution : un système d'éviction de socket. Quand un joueur se reconnecte après un refresh, sa nouvelle connexion WebSocket "vole" le slot de l'ancienne. L'ancienne reçoit un code de fermeture spécifique qui lui dit de s'éteindre proprement (plutôt que de déclencher une notification de déconnexion). La nouvelle envoie un `reconnect_private_state` qui restaure le rôle du joueur, ses cartes en main, et les informations qu'il avait (résultats d'investigation, peek de deck).

J'ai aussi finalisé la configuration Docker de production : PostgreSQL, Redis, Daphne derrière Nginx, le tout derrière un tunnel Cloudflare. Et désactivé l'accès à `/admin/` côté Nginx (retourne un 404).

## Ce que j'ai retenu

**Séparer le moteur du framework.** C'est la meilleure décision que j'ai prise. Le module `engine.py` (352 lignes, zéro dépendance Django) a rendu les tests unitaires triviaux et les bugs de règles faciles à reproduire et corriger.

**Le cross-origin, c'est pas un détail.** Si j'avais hébergé le frontend et le backend sur le même domaine, j'aurais économisé une journée entière. La prochaine fois, je déploie tout derrière un seul Nginx dès le départ.

**Les cérémonies valent le coup.** Dans un jeu social, les animations ne sont pas décoratives — elles communiquent l'état, créent de la tension, et rendent l'expérience mémorable. Les joueurs attendent la révélation des votes comme ils attendraient le dépouillement physique.

**Redis est obligatoire pour les WebSocket en production.** Sans channel layer, Daphne ne peut pas broadcaster entre workers. Et si tu utilises un service managé comme Upstash, assure-toi que tes URLs de fallback sont propres — localhost n'a rien à faire dans une config de prod.

**401 tests, ça change tout.** Chaque refactor, chaque nouveau déploiement, chaque correction de bug — les tests étaient là pour me dire si j'avais cassé quelque chose. Dans un projet où une seule règle mal implémentée peut ruiner une partie entière, c'est indispensable.

## La suite

Le jeu est en ligne et fonctionnel. Les prochains devlogs parleront des améliorations post-lancement : refactor du consumer (2385 lignes, c'est trop), rate limiting, et peut-être un mode ranked.

D'ici là, tu peux jouer sur [secret-hitler.porus.dev](https://secret-hitler.porus.dev) — crée une room, invite des amis, et vois si tu peux faire passer tes lois fascistes sans te faire démasquer.

---

[Voir la fiche projet →](/projects/projet-7/)
