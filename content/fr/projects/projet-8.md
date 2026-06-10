---
title: "Linux Setup Generator"
description: "Générateur de scripts d'installation Linux — choisis ta distro, clique les logiciels, et obtiens un script bash prêt à exécuter. 291 programmes, 7 distros, zéro backend."
date: 2026-03-10
image: "/images/linux-setup-gen-banner.svg"
badges:
  - "Hugo"
  - "Vanilla JS"
  - "GitHub Pages"
  - "Bash"
tags:
  - "dev"
  - "frontend"
  - "linux"
  - "tool"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19/linux-setup-generator
  - icon: fas fa-globe
    url: https://linux-setup-generator.online/
showInHome: true
---

## Résumé

Installer Linux, c'est facile. Réinstaller tous tes programmes après un wipe, c'est l'enfer. Tu cherches les noms de paquets, tu copy-paste des commandes, et tu oublies toujours la moitié. **Linux Setup Generator** remplace tout ça par un wizard en 3 étapes :

1. Choisis ta distribution
2. Clique les programmes que tu veux
3. Copie ou télécharge ton script d'installation

Le tout dans le navigateur. Pas de compte, pas de backend, pas de collecte de données.

## Pourquoi c'est utile

- **291 programmes** couvrant 69 catégories — du terminal au window manager, en passant par les IDE, navigateurs, et outils audio
- **7 distributions** supportées : Alpine, Arch, Debian, Fedora, openSUSE, Ubuntu, Void
- **Noms de paquets distro-aware** — chaque programme sait quel nom de paquet utiliser selon ta distro (ex: `chromium` sur Arch → `chromium-browser` sur Ubuntu)
- **161 scripts d'installation customs** pour les programmes absents des dépôts officiels — npm global, cargo, AppImage, builds from source…
- **Résolution automatique des dépendances** — sélectionne un programme, ses dépendances sont incluses sans que tu aies à y penser

## Stack technique

- **Hugo** — site statique, build en < 5 secondes
- **Vanilla JS** — 136 lignes, zéro dépendance npm
- **GitHub Pages** — déploiement automatique via GitHub Actions
- **Aucun backend** — tout le script est généré côté client par une classe `ScriptGenerator`

## Le truc malin

L'architecture est purement data-driven. Ajouter un programme, c'est créer un dossier avec un `program.json` (et optionnellement un `install.sh`). Pas de base de données, pas d'API, pas de config centralisée. Le `ScriptGenerator` lit les JSON injectés dans la page par Hugo, résout les dépendances en DFS topologique, et génère un script bash adapté à ta distro.

---

[Lire l'article détaillé sur l'architecture →](/blogs/devlog-linux-setup-generator/)
