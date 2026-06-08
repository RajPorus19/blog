---
title: "Discord Forbidden API"
description: "Toolchain de synchronisation automatisée de messages Discord avec API REST, watchdog et stockage local"
date: 2026-06-07
image: "/blog/images/project-placeholder.svg"
badges:
  - "Python"
  - "Flask"
  - "API"
  - "DevOps"
tags:
  - "dev"
  - "backend"
  - "api"
  - "devops"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19
showInHome: true
---

## 📌 Résumé

Une toolchain auto-hébergée qui synchronise les salons Discord en local, expose une API REST pour lire et envoyer des messages, et t'avertit quand quelque chose de nouveau arrive — le tout automatisé sur un schedule cron.

Parti d'un besoin simple : **ne pas rater de messages sur 12+ salons, même quand t'es pas sur Discord.** Ça tourne 24/7 dans un conteneur Docker, synchronise toutes les 5 minutes, et ne te ping que quand il y a du nouveau.

### 🎯 Objectifs

- ✅ Synchroniser automatiquement les messages de tous les salons Discord en fichiers JSONL
- ✅ Exposer une API REST pour lire, chercher et envoyer des messages
- ✅ Notifier uniquement sur les messages *nouveaux* — zéro spam, zéro bruit
- ✅ Tourner 24/7 sans intervention manuelle (Docker + cron)

### 🧰 Stack

- **Python + Flask** — API REST (health, salons, messages CRUD)
- **JSONL** — stockage append-only léger (un fichier par salon)
- **Discord API (self-bot)** — lecture et envoi de messages
- **Docker** — déploiement containerisé avec docker-compose
- **Hermes Agent cron** — watchdog toutes les 5 min pour détecter les nouveaux messages

### 🔧 Comment ça marche

```
Discord API  ──→  main.py (sync loop)  ──→  chatlogs/messages_in_*.jsonl
                                                    │
                                                    ▼
                                              app.py (Flask API)
                                           ┌─────────────────────┐
                                           │ GET  /health        │
                                           │ GET  /channels      │
                                           │ GET  /channels/X/   │
                                           │      messages       │
                                           │ POST /channels/X/   │
                                           │      send           │
                                           └─────────────────────┘
                                                    │
                                           watchdog (cron/5min)
                                           ─→ notifie sur les nouveaux messages
```

1. **Sync loop** (`main.py`) interroge Discord toutes les 5 min et ajoute les nouveaux messages dans `messages_in_{salon}.jsonl`
2. **Flask API** (`app.py`) sert ces fichiers en HTTP — tu peux curl pour les derniers messages, ou envoyer des réponses via POST
3. **Watchdog** (cron job) compare les derniers IDs de messages avec l'état sauvegardé ; si un truc est nouveau, il t'alerte direct — sinon il reste silencieux (zéro coût token)

### 📖 Leçons apprises

- **Le stockage append-only c'est sous-côté.** JSONL te donne une historique tolérante aux pannes, greppable, sans base de données. Si le container crash, tu perds rien.
- **Le silence du watchdog compte autant que ses alertes.** Un cron job qui parle seulement quand y'a du nouveau est plus utile qu'un qui dit "rien de nouveau" toutes les 5 min. Le pattern `no_agent: True` (le script tourne, stdout décide si le message est livré) économise des tokens et de l'attention.
- **Les apostrophes sont l'ennemi du curl inline.** Appris à mes dépens : toujours écrire le payload JSON dans un fichier temporaire et utiliser `-d @file` au lieu de `-d '...'`. Le shell bouffe les quotes plus vite que tu crois.
- **Stateful vs stateless, ça compte même dans des outils simples.** Le watchdog stocke les derniers IDs de messages vus par salon dans un fichier d'état — sans ça, chaque exécution re-notifierait les vieux messages.
