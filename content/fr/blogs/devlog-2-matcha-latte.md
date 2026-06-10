---
title: "Kattruko — Devlog 2 : Design system et identité visuelle (Matcha Latte)"
description: "Pourquoi documenter une palette de couleurs et une charte typographique pour une app perso, et comment garder une cohérence visuelle clair/sombre."
date: 2026-06-10
translationKey: "devlog-2-kattruko"
image: "/images/kattruko-devlog-2.svg"
tags:
  - "devlog"
  - "kattruko"
  - "design"
  - "ui"
  - "palette"
  - "matcha"
mathjax: false
---

## Pourquoi un design system pour une app perso ?

> Dans le [Devlog 1](/blogs/devlog-1-kattruko-idee-conception/), j'ai raconté la genèse de Kattruko — un study companion connecté à Obsidian. Ici, on plonge dans l'identité visuelle : le design system Matcha Latte.

Pourquoi perdre du temps à documenter des couleurs, des espacements, des règles typographiques ? Est-ce que je ne ferais pas mieux de coder directement ?

La réponse est venue après quelques semaines de développement. Au début, je choisissais les couleurs au feeling. Un vert ici, un gris là-bas, une teinte chaude pour les boutons. Ça marchait, mais il y avait un problème : en passant d'un écran à l'autre, l'app n'avait pas d'identité. Chaque page avait l'air d'avoir été conçue séparément.

C'est là que j'ai compris qu'un design system n'est pas fait pour les autres. Il est fait pour toi, dans six mois, quand tu reviens sur un écran que tu n'as pas touché depuis trois semaines et que tu dois retrouver les bonnes valeurs. Sans documentation, tu devines. Avec, tu appliques.

## Le problème : les apps d'étude sont moches ou froides

Regardez Anki. C'est un outil incroyable, le standard de fait pour la mémorisation. Mais objectivement, l'interface est fonctionnelle au mieux. Les couleurs sont celles par défaut de Qt, la typographie est système, il n'y a pas de personnalité.

Du côté des apps plus modernes, c'est souvent l'inverse : design soigné mais ambiance froide. Des tons bleus, des angles droits, une ambiance de tableau de bord. Ça fonctionne pour une app de productivité, mais pas pour un outil qui veut donner envie d'apprendre.

Kattruko devait trouver une troisième voie : un design volontaire, identifiable, mais qui ne crie pas. Quelque chose de calme. Quelque chose qui ressemble à un carnet et à un café.

## La philosophie Matcha Latte

Le nom de la palette est arrivé tôt et ne m'a jamais quitté. Matcha comme le thé vert, latte comme le lait chaud. Un vert frais, des tons crème et pêche, une ambiance organique, pas technologique.

Le vert principal (#6B9F72) évoque la feuille, le naturel, la croissance. C'est une couleur qui ne surprend pas, qui repose. En face, le peach (#F0B27A) apporte de la chaleur sans agresser. Les neutres (crème, gris doux, blanc cassé) font le lien.

Pour la typographie, j'ai choisi [Inter](https://rsms.me/inter/). C'est une police qui a été pensée pour les écrans : les courbes sont légèrement ouvertes, la hauteur d'x est généreuse. Elle donne un rendu technique sans être froide. [Figma](https://www.figma.com/) et GitHub l'utilisent, et je trouve qu'elle capte bien ce que je veux pour Kattruko : un outil sérieux, pas une app de jeu.

Le vrai travail a été de créer deux thèmes, clair et sombre, qui partagent la même identité. Pas un simple invert des couleurs. Le mode clair est frais, aérien, inspiré du papier. Le mode sombre est profond, tamisé, comme un écran de lecture le soir. Les deux utilisent les mêmes couleurs de base, mais les proportions changent : le vert devient plus présent en sombre, le peach plus subtil.

## Les défis : cohérence cross-platform et documentation

Le plus gros défi technique a été de faire fonctionner ce design system sur mobile et desktop avec Flutter. Material 3 offre une base solide, mais la personnalisation fine demande de tout override : les couleurs des cartes, des boutons, des champs de texte, des dialogues. Et chaque override doit être testé dans les deux thèmes.

Un exemple précis : la carte de quiz en mode sombre. Sur mobile, un fond légèrement gris avec bord subtil fonctionne bien. Sur desktop (web Flutter), le rendu des ombres est différent, les bordures ressortent plus. J'ai dû ajuster les valeurs de surface et de border séparément pour chaque plateforme.

L'autre défi était plus philosophique : documenter sans sur-ingénierie. Un design system peut devenir une usine à gaz, des fichiers JSON, des générateurs de thèmes, des tokens partout. Pour un projet perso, j'ai choisi l'inverse : un seul fichier DESIGN.md, bien structuré, avec les couleurs, les espacements, les font-sizes et les règles d'utilisation. Assez pour que je puisse retrouver une valeur sans chercher, assez léger pour que j'ose le modifier.

## Ce que j'ai appris

Ce qui m'a le plus surpris, c'est à quel point un design system change la façon dont tu codes. Quand chaque couleur a un nom et une raison, tu hésites moins. Tu sais que le vert principal s'appelle "matcha" et qu'il est réservé aux actions principales. Tu ne te demandes pas "est-ce que je mets #5A8F62 ou #6B9F72" à chaque bouton.

Et ça crée de la cohérence sans effort. Pas de vert qui vire au bleu parce que tu as tapé la mauvaise valeur. Pas de gris qui change de page en page. L'app a une voix.

## La suite

Le prochain devlog parlera de l'import des notes Obsidian — le scanner multi-passes, les wikilinks, et comment extraire du sens de fichiers markdown sans tout casser.

En attendant, Kattruko continue en développement fermé. Le design system Matcha Latte est stable, documenté, et appliqué. Il me sert de fondation pour la suite.

*→ Intéressé par la philosophie d'apprentissage ? Jette un œil à la réflexion « [Mieux vaut un flat earther qui raisonne qu'un savant qui radote](/blogs/article-3/) ». Et si t'as loupé le début, le [Devlog 1](/blogs/devlog-1-kattruko-idee-conception/) raconte la genèse du projet.*
