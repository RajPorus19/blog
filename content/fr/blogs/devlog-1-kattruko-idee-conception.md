---
title: "Kattruko — Devlog 1 : Idée et conception du produit"
description: "Pourquoi j'ai construit un study companion connecté à mon Obsidian vault, et les choix qui ont guidé sa conception."
date: 2026-06-09
image: "/images/kattruko-devlog-1.svg"
tags:
  - "devlog"
  - "kattruko"
  - "conception"
  - "spaced-repetition"
mathjax: false
---

## Le problème : des notes qu'on ne revoit jamais

J'ai un Obsidian vault qui grossit depuis des années. Des centaines de notes sur la biologie, la chronobiologie, la philosophie bouddhiste, l'histoire — tout ce qui me passionne. Mais il y a un problème silencieux avec ce genre de vault : **on écrit, on archive, on oublie.**

Une note sur la régulation de la leptine et de la ghréline ? Très intéressant le jour où tu l'écris. Deux mois plus tard, tu te souviens que ça existe, mais tu serais incapable de dire comment les deux hormones interagissent. Dans la vraie vie, ce que tu n'utilises pas, tu le perds.

Les apps de flashcards existent — Anki, Quizlet, etc. Mais elles ont un défaut fondamental : **tu dois tout recréer.** Copier le contenu de tes notes vers un autre format, un autre outil, une autre base. C'est une friction insurmontable sur le long terme. Et quand t'as 500 notes, c'est trop tard.

L'idée de Kattruko est née de là : **et si l'app qui te fait réviser était directement branchée sur ton vault ?**

### L'origine du nom

Kattruko (கற்றுக்கொ) signifie « apprends » en tamoul — une langue dravidienne du sud de l'Inde et du Sri Lanka. C'est une contraction de *katrukol* (apprendre, étudier). Le nom est venu naturellement : l'app existe pour ça, apprendre. Pas pour gérer des tâches, pas pour faire des statistiques — apprendre. Le mot est court, il sonne bien, et il dit exactement ce que le projet fait.

---

## Pourquoi pas une solution existante ?

J'ai passé pas mal de temps à regarder ce qui existe. Anki est le standard, mais :

- **Obsidian to Anki** — des plugins existent, mais ils sont fragiles, dépendent de AnkiConnect, et cassent à chaque mise à jour d'un côté ou de l'autre
- **RemNote** — tout-en-un mais fermé, pas de vault local, pas de contrôle sur tes données
- **Notion** — pas de spaced repetition natif, pas de quiz généré automatiquement

Le vrai besoin, c'est une app qui **lit directement tes notes**, les comprend, et les transforme en matériel d'étude sans que tu aies à faire de travail manuel. Et ça, aucune solution clé en main ne le fait bien.

---

## Le concept : un "study companion" pas un dashboard de productivité

Très tôt, j'ai pris une décision de design qui a tout influencé après : **Kattruko n'est pas un outil de productivité.** Pas de compteurs de temps d'étude, pas de classement, pas de gamification agressive, pas de « streaks » qui te font sentir coupable si tu rates un jour.

C'est un compagnon d'étude. Calme, apaisant, qui te propose ce qu'il y a à voir aujourd'hui sans pression.

Ça semble anodin, mais ça change tout dans la conception :

- **Pas de push notifications agressives** — l'app te parle quand tu l'ouvres, pas quand elle décide
- **Les suggestions sont des invitations, pas des injonctions** — « voilà ce qui serait bien de revoir » pas « TU AS 47 CARTES EN RETARD »
- **Le design doit donner envie de rester** — pas juste être fonctionnel

Ce dernier point m'a amené à la direction artistique du projet.

---

## La philosophie Matcha Latte

Le nom de la palette dit tout : un vert frais, des tons chauds, une ambiance qui évoque un café calme et un carnet de notes. Je voulais que l'app ait une **personnalité** — pas juste un thème Material 3 par défaut.

Les choix qui en découlent :

- **Typo Inter** — lisible, technique sans être froide, utilisée par Figma, GitHub. Une police qui dit « outil sérieux » sans dire « hôpital »
- **Palette duale (clair/sombre)** — pas un simple invert des couleurs. Les deux modes ont leur propre personnalité : le clair frais et aéré comme une feuille de papier, le sombre profond comme une lampe de lecture le soir
- **Design system documenté** — chaque couleur, chaque spacing, chaque font-size a une raison d'être

Le défi technique : faire en sorte que ce design system s'incarne aussi bien sur mobile (Flutter natif) que sur desktop (web Flutter). Material 3 aide, mais la personnalisation fine demande de tout override correctement.

---

## Pourquoi Flutter + Django DRF ?

Le choix technique le plus important après le concept lui-même.

**Flutter pour le frontend :**
- Cross-platform sans sacrifier la qualité
- Hot reload pour itérer vite sur le design
- Material 3 natif — la spec de design voulue est littéralement dans le framework

**Django DRF pour le backend :**
- Python, l'écosystème que je maîtrise le mieux
- DRF mature et documenté — pas de surprises
- Modèle relationnel parfait pour le domaine (notes, flashcards, quiz, SM-2)
- Celery pour les tâches asynchrones sans réinventer la file d'attente

Le vrai défi : **ne pas tomber dans le piège du "microlith"** — une app simple qui devient complexe parce qu'on a trop voulu découper. J'ai gardé le backend monolithique (une seule app Django) avec des services bien séparés en modules.

---

## Le plus gros challenge : l'import des notes

Le problème qui a pris le plus de réflexion : comment scanner un vault Obsidian et en extraire du sens ?

Obsidian c'est juste des fichiers markdown dans un dossier. Simple en apparence. Mais concrètement :

- **Frontmatter YAML** — certaines notes ont des métadonnées (tags, dates, statut), d'autres non
- **Wikilinks** — `[[Note liée]]` partout, qu'il faut parser et résoudre
- **Tags inline** — `#tag` dans le texte, différents des tags en frontmatter
- **Flashcards embarquées** — on peut écrire `Q::question` / `R::réponse` directement dans une note, mais comment les détecter sans casser le parsing ?
- **Fichiers binaires** — images, PDFs, à filtrer
- **Notes orphelines** — fichiers non liés mais avec du contenu utile

La solution : un scanner en plusieurs passes. D'abord une passe pour lister les fichiers valides et extraire les métadonnées. Ensuite une passe pour parser le contenu avec une vraie machine à états — pas juste des regex — pour distinguer les flashcards inline du texte normal. Enfin, une passe de résolution des wikilinks pour construire le graphe de notes.

Ce système a été réécrit deux fois. La V1 était trop optimiste et cassait sur les notes complexes. La V2 est plus modulaire, avec des tests unitaires sur chaque étape.

---

## La suite

Le Devlog 2 parlera du **design system et de l'identité visuelle** — les choix de palette, la typographie, et comment tout ça s'intègre dans une charte complète.

Entre-temps, Kattruko reste en développement fermé. Pas de repo public, pas de beta ouverte — c'est un outil perso, construit pour résoudre *mon* problème. Si ça peut aider d'autres personnes un jour, tant mieux, mais ce n'est pas l'objectif aujourd'hui.
