---
title: "Linux Setup Generator — Comment un JSON devient un script bash en 3 clics"
description: "Une plongée dans l'architecture du générateur de scripts Linux : modèle data-driven, résolution de dépendances DFS, système CUSTOM_INSTALL, et comment 136 lignes de JS remplacent toutes les galères de post-install."
date: 2026-06-10
translationKey: "devlog-linux-setup-generator"
image: "/images/linux-setup-gen-banner.svg"
tags:
  - "devlog"
  - "linux"
  - "hugo"
  - "javascript"
  - "architecture"
  - "tool"
mathjax: false
---

## Le problème que je voulais résoudre

Je change de distro. Souvent. Trop souvent. Et chaque fois, c'est la même rengaine : je passe 2 heures à réinstaller mes programmes. Je cherche les noms de paquets. Je me souviens qu'il me manque VLC au moment où je veux regarder une vidéo. Je découvre que `chromium` s'appelle `chromium-browser` sur cette distro.

Le pire, c'est que je sais exactement quels programmes je veux. La liste est dans ma tête, mais la traduire en commandes shell c'est une corvée mécanique — le genre de tâche qu'un ordinateur devrait faire à ma place.

Alors j'ai construit un outil pour ça.

## Le concept : 3 étapes, zéro friction

Le **Linux Setup Generator** est un site statique. Tu arrives dessus, tu choisis ta distribution (7 dispos), tu cliques les programmes que tu veux parmi 291 disponibles, et tu obtiens un script bash prêt à exécuter. Copié-collé, ou téléchargé. C'est tout.

Pas de backend. Pas de base de données. Pas de compte utilisateur. Tout tourne dans le navigateur.

Ce qui m'intéresse ici, c'est pas le résultat final — c'est *comment* les 136 lignes de JavaScript derrière arrivent à transformer une poignée de clics en un script d'installation valide, avec résolution de dépendances, pour 7 distributions différentes.

## Le modèle de données : un fichier JSON par programme

Le cœur du projet, c'est pas le JS. C'est le modèle de données. Chaque programme est représenté par un dossier contenant un `program.json`. Voilà à quoi ça ressemble pour wpgtk :

```json
{
  "name": "wpgtk",
  "slug": "wpgtk",
  "description": "Colorscheme, wallpaper and template manager.",
  "categories": ["theming"],
  "dependencies": ["pip"],
  "package_names": {
    "arch": "python-wpgtk",
    "ubuntu": "CUSTOM_INSTALL",
    "void": "CUSTOM_INSTALL"
  }
}
```

Pas de base de données, pas d'API, pas de fichier de config centralisé. Juste un fichier JSON par programme.

Le site étant généré avec Hugo, ces fichiers sont lus au build et injectés directement dans la page HTML. Le template de la page générateur contient ces trois lignes :

```go
const DISTROS      = {{ $distros      | jsonify | safeJS }};
const PROGRAMS     = {{ $programs     | jsonify | safeJS }};
const DEPENDENCIES = {{ $dependencies | jsonify | safeJS }};
```

Résultat : quand la page se charge, le navigateur a déjà toutes les données — distros, programmes, catégories, dépendances — dans des variables JS. Aucun fetch, aucune latence. Le `ScriptGenerator` peut travailler directement.

## Le mécanisme `CUSTOM_INSTALL` : la killer feature

C'est là que le truc devient intéressant. Tous les programmes ne sont pas packagés dans les dépôts officiels. Certains s'installent via `cargo install`, d'autres via `npm install -g`, d'autres depuis un .deb tiers, d'autres en compilant depuis la source.

La solution naïve serait de faire une liste d'exceptions dans le code. Mais ça scale pas — chaque nouveau programme nécessite de modifier le JS. Et si un programme a des dépendances customs, bon courage.

La solution que j'ai choisie est plus élégante : le mot-clé `"CUSTOM_INSTALL"` comme valeur de `package_names`.

Quand le générateur voit `"ubuntu": "CUSTOM_INSTALL"`, il ne cherche pas un nom de paquet — il va fetch le fichier statique `/programs/wpgtk/custom_install/install.sh` et l'embarque dans le script final.

Ce qui est puissant, c'est que le `install.sh` peut faire n'importe quoi : un `pip install`, un `curl | bash`, un `git clone && make`, un `flatpak install`. Le générateur s'en fout — il concatène.

Ce mécanisme est même plus répandu que les installations classiques : **161 des 291 programmes** (soit 55%) utilisent un `CUSTOM_INSTALL`. C'est pas l'exception, c'est la norme.

## La résolution de dépendances : un DFS topologique

Les programmes ne sont pas des îles. wpgtk dépend de pip. Ungit dépend de npm, qui dépend de nodejs. Le générateur doit installer les dépendances *avant* les programmes, et sans les dupliquer.

Voici l'algorithme que j'ai écrit. Il fait un parcours en profondeur récursif (DFS) en post-order :

1. Pour chaque slug de programme, on regarde son tableau `dependencies`
2. Si la dépendance est un slug connu (programme ou dependency), on récupère son `package_names` pour la distro choisie
3. Si ce nom de paquet est `CUSTOM_INSTALL`, on **récurse** — on va chercher les dépendances de la dépendance
4. Un `Set` partagé (`visited`) empêche les boucles infinies et les installations en double
5. L'ordre post-order (ajouter le slug *après* avoir traité ses dépendances) garantit un ordre d'installation valide — une topological sort implicite

Prenons un exemple concret. Tu sélectionnes Ungit sur Ubuntu :

1. Ungit → `CUSTOM_INSTALL`, dépend de `npm` → on récuse dans npm
2. npm → `CUSTOM_INSTALL`, dépend de `nodejs` → on récuse dans nodejs
3. nodejs → `nodejs` (paquet standard) → ajouté aux packages
4. Puis npm → son `install.sh` est ajouté (post-order)
5. Puis Ungit → son `install.sh` est ajouté

Le script généré ressemble à :

```bash
#!/bin/bash
# Script for Ubuntu
# Generated by Linux Setup Generator

sudo apt-get install -y nodejs
# ... custom install script for npm ...
# ... custom install script for ungit ...
```

La beauté c'est que le `Set` partagé est global à toute la session de génération : si deux programmes différents partagent une dépendance custom, elle n'est installée qu'une seule fois.

## Le `ScriptGenerator` : 136 lignes, 4 méthodes

La classe `ScriptGenerator` fait quatre choses :

1. **`_retrievePackageNames()`** — pour chaque programme sélectionné, regarde dans `package_names` et catégorise en `packageNames` (standard) ou `customSlugs` (CUSTOM_INSTALL)

2. **`_buildPackageInstallCmd()`** — prend tous les noms de paquets et les concatène en une seule commande : `sudo pacman -S git neovim docker tmux` (une commande, pas 5 appels séparés)

3. **`_custom_installs()`** — pour chaque slug custom, appelle `_get_custom_install()` qui résout les dépendances (DFS) puis va `fetch()` les `install.sh` correspondants

4. **`_script_header()`** — ajoute le shebang et un commentaire avec le nom de la distro

Le résultat est assemblé dans `_buildScript()` :

```javascript
async _buildScript() {
  const { packageNames, customSlugs } = this._retrievePackageNames();
  const installCmd = this._buildPackageInstallCmd(packageNames);
  const customCmd = await this._custom_installs(customSlugs);
  return this._script_header() + installCmd + "\n" + customCmd + "\n";
}
```

C'est tout. Pas de state machine, pas de pipeline complexe. Une classe, quatre méthodes, et le travail est fait.

## Pourquoi ça marche aussi bien

Plusieurs principes rendent ce design robuste :

### 1. Séparation données / logique

L'ajout d'un programme ne touche jamais au code. Tu crées un dossier, tu mets un fichier JSON, optionnellement un `install.sh`, et c'est tout. Hugo fait le reste au build. Cette séparation signifie que le catalogue peut grandir indéfiniment sans que la complexité du JS n'augmente.

### 2. Aucune dépendance externe

Le site n'a pas de backend, pas de CDN, pas d'API tierce. Une fois le HTML chargé, tout fonctionne offline. Le seul appel réseau optionnel, ce sont les `fetch()` vers les `install.sh` — et même ça pourrait être préchargé dans le HTML.

### 3. Distro-agnostique par design

Le `package_names` map des slugs de distro vers des noms de paquets, avec un fallback `"default"`. Ça veut dire qu'un programme peut avoir des noms complètement différents selon la distro sans que le code ne fasse de branchement conditionnel. Le JS ne fait que `package_names[distro.slug] || package_names["default"]`.

### 4. Le navigateur comme moteur de génération

En générant le script côté client plutôt que côté serveur, j'ai évité d'avoir à maintenir un backend, un worker, ou une lambda. Le site est littéralement des fichiers statiques servis par GitHub Pages. Zéro coût, zéro maintenance.

## Ce qui aurait pu être amélioré

Avec le recul, deux choses me trottent dans la tête :

**Le `visited Set` global est un peu magique.** Si deux programmes customs partagent une dépendance, elle n'est installée qu'une fois — ce qui est correct pour des packages systèmes, mais pourrait causer des problèmes si deux `install.sh` customs font des choses différentes avec le même slug. En pratique, comme les slugs sont uniques à un programme et que les dépendances pointent vers le même slug, ça ne pose pas de problème. Mais l'hypothèse implicite ("même slug = même installation") mériterait d'être documentée.

**Pas de gestion des erreurs.** Si un `install.sh` fail (code de retour != 0), le script continue. Dans une V2, j'aimerais ajouter `set -e` ou un mécanisme de `trap` pour arrêter en cas d'erreur. Là encore, c'est un choix conscient — un `install.sh` custom qui fail ne devrait pas empêcher le reste du script de tourner. Par exemple, une AppImage qui 404 ne devrait pas bloquer l'installation de ton window manager.

## La vraie valeur du projet

Honnêtement, la partie la plus satisfaisante de ce projet, c'est pas le site — c'est le modèle de contribution. N'importe qui peut ajouter un programme. Pas besoin de savoir coder, pas besoin de comprendre Hugo ou le JS. Tu regardes un exemple de `program.json`, tu copies le pattern, et tu fais une PR. Le fichier JSON fait 10 lignes. Le `install.sh` fait souvent 2-3 lignes.

C'est ce qui a permis de passer de 0 à 291 programmes. Chaque ajout prend littéralement 2 minutes.

## Et maintenant ?

Le site tourne à [linux-setup-generator.online](https://linux-setup-generator.online/). Le repo est sur [GitHub](https://github.com/RajPorus19/linux-setup-generator). La roadmap inclut plus de programmes (évidemment), le support de plus de distros (NixOS, Gentoo ?), et un mode "flatpak-only" pour les transfuges de Windows qui veulent un maximum de GUI sans terminal.

Mais soyons honnêtes : le plus probable, c'est que je continue d'ajouter des programmes au fur et à mesure que j'en découvre de nouveaux. C'est ça qui est beau avec un catalogue data-driven — il n'y a jamais de "fini".

---

[Voir la fiche projet →](/projects/projet-8/)
