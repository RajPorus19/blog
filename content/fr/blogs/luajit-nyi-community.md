---
title: "Le bug LuaJIT qui empoisonnait silencieusement les boucles — et la communauté Lua qui refuse d'avancer"
description: "Enquête approfondie sur le bug NYI de LuaJIT qui causait un ralentissement 20x dans du code non lié, combinée au débat sur pourquoi l'écosystème Lua est bloqué. Ces deux histoires n'en font qu'une."
date: 2026-08-11
translationKey: "luajit-nyi-community"
image: "/images/luajit-nyi-community.svg"
tags:
  - "lua"
  - "luajit"
  - "performance"
  - "debugging"
  - "community"
mathjax: false
---

## Le bug

Voici une phrase qui devrait terrifier quiconque écrit du code sensible à la performance : **une fonction qui ne s'exécute jamais peut ralentir une boucle chaude complètement indépendante par un facteur 20.**

Ce n'est pas une hypothèse. C'est ce qui a été documenté cette semaine dans une enquête sur r/programming — 159 upvotes, 95% de ratio positif, parce que les ingénieurs performance reconnaissent leurs cauchemars dans cette histoire.

Le contexte : un programme LuaJIT avec une boucle de calcul serrée. La boucle est chaude. Elle s'exécute des millions de fois. C'est le genre de code qu'on écrit quand on a besoin que Lua aille vite. Et elle est lente. Pas « un peu lente ». Vingt fois plus lente qu'elle devrait l'être.

La cause ? Une fonction complètement indépendante ailleurs dans le codebase que LuaJIT avait classée comme NYI — « Not Yet Implemented » — ce qui signifie que le compilateur JIT ne pouvait pas l'optimiser et devait revenir à l'interpréteur. Cette fonction n'est jamais appelée. Elle n'a aucun impact sur la correction du programme. Mais sa simple *existence* dans la trace a empoisonné les décisions d'optimisation du JIT pour la boucle chaude.

Prenons le temps de digérer ça. La présence de code mort — du code qui s'exécute zéro fois — a causé une régression de performance de 20x dans du code qui s'exécute des millions de fois.

---

## NYI : la face obscure des JIT à tracing

Pour comprendre pourquoi ça arrive, il faut comprendre comment LuaJIT fonctionne. C'est un JIT à tracing, pas un JIT par méthode.

Un JIT par méthode (comme le compilateur C2 de la JVM ou TurboFan de V8) compile des fonctions entières. Il regarde une fonction, l'analyse, l'optimise, et génère du code machine pour l'ensemble. Si une fonction en appelle une autre, c'est une unité de compilation séparée.

Un JIT à tracing (comme LuaJIT) fonctionne différemment. Il observe le programme s'exécuter, identifie les *chemins* chauds — des séquences d'instructions qui s'exécutent fréquemment — et compile ces traces en code machine linéaire. Le mot clé est « linéaire ». Une trace est une ligne droite à travers le programme. Pas de branches. Pas d'appels de fonction (ils sont inlinés). Juste un chemin chaud.

C'est génial pour la performance quand ça marche. Les traces éliminent le surcoût des appels de fonction, les erreurs de prédiction de branche, et tous les autres coûts de l'exécution de bytecode interprété. LuaJIT peut produire du code qui rivalise avec le C en vitesse.

Le problème : les traces sont fragiles. Si la trace rencontre quelque chose qu'elle ne peut pas inliner — comme un appel à une fonction NYI — plusieurs mauvaises choses peuvent arriver :

1. **La trace avorte.** La trace est coupée court. Le code compilé revient à l'interpréteur au site d'appel. Si ça arrive dans ta boucle chaude, chaque itération paie le coût de passer du code compilé à l'interpréteur.

2. **Les traces latérales.** LuaJIT peut générer des traces latérales pour gérer le cas NYI, mais les traces latérales ont un surcoût. Trop de traces latérales, et le JIT passe plus de temps à compiler qu'à exécuter.

3. **L'effet d'empoisonnement (le bug).** Dans certains cas, l'enregistreur de trace voit une fonction NYI *n'importe où* dans le chemin de code qu'il explore — même si cette fonction n'est pas réellement appelée sur le chemin chaud — et prend des décisions d'optimisation pessimistes qui affectent toute la trace. C'est ce qui s'est passé dans le cas documenté.

Le développeur a trouvé le coupable en bissectant systématiquement son code : supprimer des fonctions une par une jusqu'à ce que la boucle chaude devienne soudainement 20x plus rapide. La fonction coupable ? Un utilitaire de logging qui utilisait une opération de chaîne NYI. Elle n'était jamais appelée. La supprimer a restauré la performance complète.

---

## Pourquoi c'est un cauchemar magnifique

J'adore ce bug. Pas parce que j'ai envie qu'il m'arrive — j'ai passé assez de nuits à débugger des bizarreries de JIT pour savoir que je ne veux jamais débugger ce truc spécifique — mais parce qu'il révèle quelque chose de profond sur le logiciel.

On construit des abstractions pour cacher la complexité. On suppose qu'ajouter du code mort est inoffensif. On suppose que le compilateur va se débrouiller. On suppose que la performance est locale — qu'une fonction lente n'affecte que le code qui l'appelle.

L'architecture de tracing de LuaJIT pulvérise toutes ces suppositions. Dans un JIT à tracing, chaque morceau de code dans le chemin de trace est connecté à tous les autres. Une décision que le JIT prend à propos de la Fonction A peut affecter l'optimisation de la Fonction Z, même si A et Z n'interagissent jamais au runtime.

Ce n'est pas un bug au sens traditionnel. C'est une propriété émergente de l'architecture du JIT à tracing. Le JIT fait exactement ce pour quoi il a été conçu — optimiser agressivement les chemins chauds — et l'empoisonnement NYI est un effet secondaire de cette agressivité. Le corriger demanderait des changements architecturaux dans la façon dont les traces sont formées et comment les fonctions NYI sont gérées pendant l'enregistrement de trace.

Les développeurs de LuaJIT sont conscients de cette classe de problèmes. Mike Pall, le créateur de LuaJIT, a écrit abondamment sur les compromis des JIT à tracing. Mais la conscience ne signifie pas qu'un correctif est facile ou arrive bientôt.

---

## L'autre histoire : le problème communautaire de Lua

La même semaine où cette enquête est devenue virale sur r/programming, un autre post lié à Lua grimpait dans les classements : « The Lua community needs to learn to move on » de Hisham Muhammad — 548 upvotes, 92% de ratio.

La thèse : Lua est bloqué. Pas techniquement — LuaJIT reste l'un des morceaux de génie logiciel les plus impressionnants qui existent, et Lua-le-langage est élégant d'une façon que Rust et Go ne seront jamais. Mais la communauté est coincée dans une boucle de disputes internes, de fragmentation, et d'une incapacité à faire évoluer l'écosystème.

Les griefs spécifiques : le split Lua/LuaJIT (deux implémentations incompatibles, toutes deux appelées « Lua »), la réticence à adopter des outils modernes (gestionnaires de paquets, linting, formatage), la culture « not invented here » qui produit cinq bibliothèques OOP différentes et zéro consensus sur laquelle utiliser, et l'ambiance générale qui veut que suggérer des améliorations à Lua soit un acte d'agression.

Je l'ai ressenti personnellement. Lua a été mon premier « vrai » langage de script après bash. Je l'adorais. Il était rapide, simple, embarquable. Le manuel tenait dans ma tête. Mais chaque fois que j'essayais de faire quelque chose au-delà d'un script mono-fichier — utiliser un gestionnaire de paquets, configurer un framework de test, trouver de la documentation pour une bibliothèque — je me prenais un mur. Les outils existaient, mais ils étaient maintenus par une personne, documentés dans un README de 2018, et incompatibles avec les autres outils dont j'avais besoin.

Ce n'est pas un problème de langage. C'est un problème de gouvernance communautaire.

---

## C'est la même histoire

Le bug NYI et la stagnation communautaire sont connectés d'une façon qu'aucun des deux auteurs n'a probablement anticipée.

Le bug NYI existe parce que LuaJIT est maintenu par un tout petit nombre de personnes. Mike Pall est un génie — vraiment, sans hyperbole, l'un des meilleurs ingénieurs compilateur vivants — mais il est essentiellement une seule personne. La liste « Not Yet Implemented » dans LuaJIT n'est pas une roadmap. C'est une liste de choses qu'une personne n'a pas eu le temps de faire.

Si LuaJIT avait la base de contributeurs de, disons, V8 (des centaines d'ingénieurs chez Google), les fonctions NYI seraient implémentées. Les bugs de tracing seraient corrigés. La documentation serait complète. Mais LuaJIT n'a pas ça. Il a un petit groupe dédié de contributeurs qui font un travail héroïque avec des ressources limitées.

Et pourquoi n'a-t-il pas plus de contributeurs ? En partie parce que la communauté Lua est fragmentée et résistante au changement. En partie parce que contribuer à LuaJIT demande une connaissance profonde d'un codebase célèbre pour sa complexité. En partie parce que l'écosystème n'attire pas le genre de sponsorisation corporate que JavaScript et Python obtiennent.

Les problèmes techniques (bugs NYI, empoisonnement de trace) et les problèmes sociaux (fragmentation communautaire, burnout des mainteneurs) sont deux symptômes de la même maladie : **le sous-investissement dans l'infrastructure partagée.**

---

## Ce que « avancer » voudrait vraiment dire

L'appel de Hisham à la communauté Lua pour « avancer » est provocateur mais vague. Avancer, ça veut dire quoi concrètement ?

Pour moi, ça veut dire trois choses :

**1. Reconnaître que Lua et LuaJIT sont des projets différents avec des cas d'usage différents.** Lua est un petit langage de script embarquable. LuaJIT est un compilateur JIT haute performance qui se trouve implémenter le langage Lua. Les confondre nuit aux deux. Lua-standard pourrait être l'option « écris une fois, embarque partout ». LuaJIT pourrait être l'option « j'ai besoin de performance niveau C depuis un langage de script ». Les deux sont valides. Aucun ne remplace l'autre.

**2. Investir dans l'outillage partagé.** L'écosystème Lua a besoin d'un vrai gestionnaire de paquets (LuaRocks est correct mais semble abandonné), d'un vrai formateur, d'un vrai serveur de langage. Ce sont des problèmes ennuyeux qui demandent un investissement soutenu, pas des projets de week-end. Ils ont besoin de financement, pas de bénévoles.

**3. Rendre la contribution plus facile.** La barrière pour contribuer au core de Lua/LuaJIT est astronomique. Les codebases sont complexes, la documentation est éparse, et la culture n'est pas accueillante pour les nouveaux. Baisser cette barrière — via des programmes de mentorat, une meilleure documentation, des chemins de contribution plus clairs — élargirait la base de contributeurs et réduirait le bus factor.

---

## L'enquête de performance comme thérapie communautaire

Il y a quelque chose de poétique dans le fait que l'enquête NYI de LuaJIT soit devenue virale la même semaine que l'article « Lua doit avancer ». L'enquête est un chef-d'œuvre du genre de travail technique profond qui rend la communauté Lua spéciale. C'est détaillé, rigoureux, et ça révèle quelque chose de vraiment surprenant sur le fonctionnement des JIT à tracing.

C'est aussi exactement le genre de travail qui est insoutenable sans une communauté saine. La personne qui a écrit cette enquête a passé des heures — probablement des jours — à bissecter son codebase pour trouver une seule régression de performance causée par une fonction qui n'était jamais appelée. Ce n'est pas quelque chose qu'on fait par hasard. C'est quelque chose qu'on fait parce qu'on tient profondément à comprendre ses outils.

La communauté Lua a des gens qui tiennent aussi profondément. Elle en a toujours eu. La question est de savoir si la communauté peut canaliser cette passion dans la construction d'une infrastructure durable — pas seulement des enquêtes brillantes ponctuelles, mais des améliorations permanentes aux outils eux-mêmes.

J'espère que oui. Lua mérite mieux que d'être le langage que tout le monde utilise mais que personne ne finance.
