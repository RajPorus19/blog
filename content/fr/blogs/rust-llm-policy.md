---
title: "Rust a banni le code généré par LLM — et c'est la bonne décision"
description: "Pourquoi la nouvelle politique LLM du projet Rust est importante : le code est la plus petite partie de l'open source. Le vrai travail, c'est la compréhension, la communauté et la confiance."
date: 2026-08-10
translationKey: "rust-llm-policy"
image: "/images/project-placeholder.svg"
tags:
  - "rust"
  - "open-source"
  - "gouvernance"
  - "llm"
  - "ia"
mathjax: false
---

## La nouvelle

Le 5 août 2026, cinq équipes du projet Rust ont adopté une politique encadrant l'utilisation des Large Language Models pour contribuer à `rust-lang/rust`. Le résumé : **les LLM, c'est bien pour poser des questions, analyser, distiller, relire. Mais pas pour créer.**

La politique n'interdit pas complètement les LLM. Elle trace une ligne : tu peux les utiliser pour *réfléchir*, mais pas pour *générer* à ta place. Si tu postes du code généré par LLM, tu dois le déclarer. Si tu copie-colles des commentaires de review dans ChatGPT et que tu recolles la réponse, ta PR est fermée. Si tu soumets des changements critiques du compilateur générés par une IA, tu dois être un expert du domaine — et même là, c'est fortement déconseillé.

Internet, comme prévu, a explosé. 610 upvotes sur r/programming. Des centaines de commentaires. La moitié saluant une défense nécessaire de la communauté humaine, l'autre moitié criant au gatekeeping luddite.

## Pourquoi ils ont raison

Je contribue à l'open source depuis quelques années. Pas à l'échelle de Rust, évidemment — mais assez pour comprendre la dynamique. Et voilà le truc dont personne ne parle dans le débat « faut-il accepter le code LLM dans l'open source ? »

**Le code est la plus petite partie de l'open source.**

Quand tu soumets une PR à un projet comme Rust, le code lui-même représente peut-être 20% de ta contribution. Les 80% restants, c'est :
- Comprendre pourquoi ce changement est important
- Anticiper comment il interagit avec tout le reste
- Être disponible pour maintenir ce code quand il cassera inévitablement
- Rejoindre la communauté et construire une relation de confiance avec les reviewers

Un LLM peut simuler les 20%. Il ne peut pas simuler les 80%. Et quand un reviewer passe 45 minutes à éplucher une PR polie et bien testée pour découvrir que l'auteur n'a aucune idée de ce que fait le code — ce n'est pas juste du temps perdu. C'est **de la confiance détruite.**

La politique Rust a un excellent cadre pour ça. Elle distingue l'utilisation des LLM comme *outil de réflexion* (répondre à des questions, analyser des RFCs, vérifier des cas limites) de leur utilisation comme *remplacement de la réflexion* (générer du code, écrire des commentaires, répondre aux reviews). Le premier usage est autorisé, souvent sans déclaration. Le second est lourdement restreint.

## Le vrai problème : la capacité de review

Il y a actuellement 1 281 PRs ouvertes sur `rust-lang/rust`. Le projet n'a jamais eu assez de reviewers. Rendre le code *plus facile* à produire sans rendre la review *plus facile* à faire, c'est une recette pour l'effondrement.

La politique l'aborde frontalement : les reviewers ne sont pas obligés de review les PRs générées par LLM. Ils peuvent les fermer à vue. Ce n'est pas de l'élitisme — c'est du triage. Quand t'as 1 281 PRs et une attention de reviewer limitée, tu *dois* prioriser les PRs où l'auteur peut réellement participer à la conversation de review.

## L'approche Zig vs l'approche Linux

Ce qui rend ce sujet fascinant, c'est comment différents projets gèrent le même problème :

- **Zig** a une interdiction totale : « No LLM-generated content, whether it be code or prose. » Point. Andrew Kelley a tracé une ligne dure.
- **Linux** dit « AI is a tool, just like other tools we use. » Linus Torvalds est pragmatique — si le code est bon, qui se soucie de comment il a été fait ?
- **Rust** essaie de trouver un terrain d'entente. Ni interdiction ni laisser-faire. Une politique qui reconnaît les deux côtés du débat et crée des règles applicables.

Je respecte les trois positions, mais l'approche de Rust est la plus intéressante à mes yeux. Elle est explicitement conçue pour être modifiée plus tard — la politique contient des dispositions qui la rendent plus facile à changer qu'à adopter. C'est un document vivant pour un problème qui évolue plus vite qu'aucune structure de gouvernance ne peut suivre.

## Ce que ça signifie pour nous

La plupart d'entre nous ne contribuons pas à Rust. Mais les dynamiques que cette politique adresse affectent chaque projet, chaque entreprise, chaque équipe.

La tension centrale est réelle : **les LLM rendent plus facile la production d'artefacts, mais l'open source n'est pas une usine à artefacts.** C'est une communauté de personnes qui construisent des choses ensemble. Si tu optimises pour la production d'artefacts, tu détruis la communauté. Si tu optimises pour la communauté, tu ralentis peut-être la production d'artefacts. Il n'y a pas de réponse facile.

Ce que j'apprécie dans la politique Rust, c'est qu'elle est honnête sur cette tension. Elle ne fait pas semblant que les LLM n'existent pas. Elle ne fait pas semblant que ce sont des outils neutres. Elle dit : voilà comment on gère ça maintenant, on sait que c'est imparfait, et on revisitera.

C'est ça, la gouvernance bien faite.

---

*Pour les curieux : [la politique complète est ici](https://forge.rust-lang.org/policies/llm-usage.html), et [le blog post d'annonce](https://blog.rust-lang.org/inside-rust/2026/08/05/rust-langrust-is-adopting-an-llm-policy/) mérite d'être lu en entier.*
