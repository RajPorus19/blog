---
title: "Bad Apple dans un réseau de neurones de 3 Mo, et la crise de reproductibilité de NeurIPS"
description: "Quelqu'un a compressé tout le clip Bad Apple dans un LLaMA de 3,5M de paramètres. Pendant ce temps, les chercheurs ML à NeurIPS 2026 débattent si les papiers sans code devraient être desk-rejetés. Ces deux histoires sont la même histoire."
date: 2026-08-10
translationKey: "bad-apple-neurips-reproducibility"
image: "/images/bad-apple-neurips.svg"
tags:
  - "ia"
  - "machine-learning"
  - "reproductibilité"
  - "neurips"
  - "bad-apple"
mathjax: false
---

## Le réseau de neurones de 3 Mo qui joue Bad Apple

Deux choses se sont passées dans le monde du ML cette semaine qui semblent sans rapport mais ne le sont pas.

Premièrement : quelqu'un sur r/MachineLearning a posté un projet où ils ont compressé tout le clip Bad Apple!! — les 1 745 frames d'ombres chinoises — dans un **modèle LLaMA de 3,5 millions de paramètres pesant 13,4 Mo**. Donne-lui un numéro de frame, il recrache 58 lignes de `.` et `@` qui reproduisent parfaitement cette frame. 99,78% de précision moyenne. 53 frames sur 71 sont pixel-perfect.

Deuxièmement : un fil de discussion sur r/MachineLearning intitulé « Il est temps de desk-rejeter les papiers qui n'incluent pas de code reproduisant les résultats » a atteint 282 upvotes et 65 commentaires. Les reviewers de NeurIPS 2026 débattent si la soumission de code devrait être obligatoire, pas juste « fortement encouragée. »

C'est la même conversation.

## Pourquoi Bad Apple dans un réseau de neurones est important

Le projet Bad Apple fonctionne parce qu'il livre *tout*. Les poids du modèle. Le script d'entraînement. Le tokenizer. Les données de frames. Les hyperparamètres exacts (AdamW, lr=3e-4, CosineAnnealingWarmRestarts). Le hardware utilisé. Les benchmarks d'inférence (2 800 tok/s sur un 7950X avec GGUF).

N'importe qui avec un GPU peut cloner le repo et vérifier chaque affirmation du README. Il n'y a pas d'étapes manquantes, pas de « disponible sur demande raisonnable », pas de lien vers une page d'hébergement universitaire morte. Le projet est assez petit pour que **la reproductibilité ne soit pas une promesse — c'est une propriété de l'artefact lui-même.**

Compare ça au papier NeurIPS typique. Un PDF de 9 pages décrivant une architecture novatrice. Un lien GitHub qui 404. Les détails d'entraînement cachés dans l'Annexe C qui n'a pas été incluse à cause de la limite de pages. « On a utilisé 256 A100 pendant 3 semaines » comme phrase jetable qui rend la reproduction impossible pour quiconque n'a pas sept chiffres de crédit cloud.

Le projet Bad Apple serait probablement desk-rejeté de NeurIPS. Il n'a aucune revendication de nouveauté. Il ne résout aucun benchmark. Il n'avance aucun cadre théorique. Mais il a quelque chose que la plupart des papiers NeurIPS n'ont pas : **tu peux le lancer et il fait exactement ce qu'il dit.**

## Le débat sur la reproductibilité s'intensifie

Le fil r/MachineLearning a capturé quelque chose de réel. Voici le sentiment :

> « Si ton papier fait des affirmations empiriques et n'inclut pas de code, il devrait être desk-rejeté. Pas "fortement encouragé". Pas "les reviewers devraient en tenir compte". Rejeté. »

Ce n'est plus une position marginale. La résistance habituelle était « mais les papiers théoriques n'ont pas besoin de code » — et c'est juste. Mais le fil fait une distinction nette : les papiers qui font des **affirmations empiriques** (on bat SOTA sur le benchmark X, notre méthode est 3x plus rapide que Y) ont besoin de preuves reproductibles. Les papiers qui font des **affirmations théoriques** (on prouve la convergence sous ces hypothèses) n'en ont pas besoin.

NeurIPS 2026 va déjà dans cette direction. Le track Evaluations & Datasets a maintenant une soumission de code obligatoire pour les « artefacts exécutables réutilisables ». Le champ de justification pour les papiers sans code est scruté. Les reviewers sont explicitement informés : « Si la justification du code n'est pas convaincante, la soumission peut être sujette à rejet. »

Mais ce n'est pas suffisant. La politique traite toujours le code comme « fortement encouragé » pour les papiers réguliers, et l'échappatoire de la « justification convaincante » est assez large pour faire passer un camion. Chaque reviewer a vu « le code sera publié à l'acceptation » et puis... rien. Ou un repo apparaît avec un README, un requirements.txt, et zéro implémentation réelle.

## Ce que Bad Apple nous apprend sur la bonne recherche en ML

Le projet Bad Apple n'essaie pas d'être de la recherche. C'est un travail d'amour par quelqu'un qui voulait voir si un tout petit LLaMA pouvait mémoriser un clip musical. Mais il démontre accidentellement tout ce que la bonne recherche en ML devrait être :

1. **Complet.** Tout ce qui est nécessaire pour reproduire le résultat est dans un seul repo.
2. **Honnête.** Le README te dit la précision (99,78%), pas juste « état de l'art. » Il te dit quelles frames ont échoué (précision minimale : 94,19%).
3. **Accessible.** Ça tourne sur du hardware grand public. 2 800 tok/s sur un 7950X. Pas besoin de cluster.
4. **Bizarre.** Ça n'optimise pas pour un benchmark. Ça optimise pour quelque chose d'*intéressant.*

Ce dernier point compte plus qu'il n'y paraît. Tellement de recherche en ML consiste à gratter 0,3% de plus sur ImageNet ou MMLU. Le projet Bad Apple consiste à répondre à une question que quelqu'un s'est vraiment posée : « Est-ce que je peux faire tenir tout ce clip dans un tout petit transformer ? » La réponse s'est avérée être oui, et le processus pour y arriver nous a appris quelque chose sur la capacité de mémorisation, les stratégies de tokenization, et la relation entre la taille du modèle et la densité d'information.

Compare ça à un papier qui obtient 87,3% au lieu de 87,0% sur un benchmark en utilisant un schedule de learning rate légèrement différent et 10x plus de compute. Lequel tu préfères lire ?

## La voie à suivre

Je ne pense pas qu'on doive desk-rejeter chaque papier sans code. C'est trop brutal. Mais je pense qu'on doit relever la barre de ce que « preuve empirique » signifie.

Voici ma proposition, pour ce qu'elle vaut :

1. **Si tu prétends battre un benchmark, tu dois publier le code.** Aucune exception. Si tu ne peux pas publier le code (données propriétaires, restrictions légales), tu ne peux pas prétendre battre le benchmark. Soumets un papier théorique à la place.
2. **« Disponible sur demande » devrait être un motif de desk-rejet.** On est en 2026. Si ton code existe, mets-le sur GitHub. S'il n'existe pas, ne fais pas semblant.
3. **La reproductibilité devrait être pondérée dans les décisions d'acceptation.** Un papier avec du code complet et exécutable qui démontre chaque affirmation devrait recevoir un bonus significatif. Un papier sans code et avec des affirmations empiriques devrait recevoir une pénalité significative.

Le projet Bad Apple a obtenu 225 upvotes sur r/MachineLearning. Un truc de compression vidéo a généré plus d'engagement que la plupart des papiers NeurIPS. Peut-être que c'est parce que c'est fun. Mais je pense que c'est aussi parce que c'est **réel** — tu peux le toucher, le lancer, le vérifier. Dans un domaine noyé sous les affirmations irreproductibles, ça vaut plus qu'un autre chiffre SOTA.

---

*Le Bad Apple LLaMA : [nyuuzyou/BadApple-LLaMA-nano sur Hugging Face](https://huggingface.co/nyuuzyou/BadApple-LLaMA-nano)*
