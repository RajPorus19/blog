---
title: "Bug Bounty — Recon Toolkit"
description: "Outils et scripts pour la reconnaissance en bug bounty"
date: 2026-04-10
image: "/images/project-placeholder.svg"
badges:
  - "Python"
  - "Bash"
  - "Sécurité"
tags:
  - "sécurité"
  - "bugbounty"
  - "dev"
links:
  - icon: fab fa-github
    url: https://github.com/RajPorus19
showInHome: true
---

## 📌 Résumé

Collection d'outils et de scripts pour la **reconnaissance en bug bounty** — collecte de sous-domaines, scan de ports, détection d'endpoints.

### 🔧 Outils inclus
- **Subdomain scraper** — collecte multi-sources (crt.sh, Certificate Transparency, etc.)
- **Endpoint extractor** — parse le JS pour trouver des endpoints cachés
- **Port scanner léger** — scan rapide des ports courants
- **Diff checker** — compare les réponses HTTP pour détecter des changements

### 🛡️ Stack
- Python + asyncio pour les scans parallélisés
- Bash pour le glue scripting
- curl/jq pour l'API parsing

### 🎯 Résultats
- Automatisation de la phase de reconnaissance
- Plus de vulnérabilités trouvées en moins de temps
- Workflow reproductible et documenté
