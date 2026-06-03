---
title: "Codecrafters — Redis en Go"
description: "Reconstruire Redis from scratch en Go pour comprendre les systèmes distribués"
date: 2026-05-20
image: "/images/project-placeholder.svg"
badges:
  - "Go"
  - "Réseaux"
  - "Systèmes Distribués"
  - "Codecrafters"
tags:
  - "dev"
  - "backend"
  - "apprentissage"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19/codecrafters-redis
showInHome: true
---

## 📌 Résumé

Challenge **Codecrafters** : reconstruire Redis from scratch en Go. L'objectif est de maîtriser les protocoles réseau, la persistance des données et la réplication.

### 🎯 Challenges relevés
- ✅ Parsing du protocol **RESP** (Redis Serialization Protocol)
- ✅ Implémentation des commandes de base (GET, SET, DEL, etc.)
- ✅ Persistance RDB (Redis Database file format)
- ✅ Réplication master-slave
- ✅ Gestion de la concurrence avec goroutines

### 🧰 Stack
- **Go** — langage principal
- **TCP Sockets** — communication réseau
- **RESP Protocol** — parsing binaire
- **Goroutines & Channels** — concurrence

### 📖 Leçons apprises
- Architecture de Redis : comment un cache est conçu en profondeur
- Gestion des connexions concurrentes en Go
- Complexités de la réplication dans les systèmes distribués
