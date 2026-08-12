---
title: "Le transformer écrit à la main qui bat les modèles frontier en arithmétique — sans aucun entraînement"
description: "Torchwright est un compilateur qui définit les poids d'un transformer directement à la main, atteignant 100% de précision en multiplication jusqu'à 12 chiffres. Les modèles frontier échouent à 7. Ce n'est pas un papier sur l'IA — c'est un papier sur ce que l'IA ne pourra jamais apprendre."
date: 2026-08-12
translationKey: "2026-08-12-torchwright-handcrafted"
image: "/images/2026-08-12-torchwright-handcrafted.svg"
tags:
  - "transformers"
  - "machine-learning"
  - "arithmetique"
  - "compilateurs"
  - "ia"
mathjax: false
---

## L'expérience qui m'a retourné le cerveau

Il y a un papier qui circule sur r/MachineLearning cette semaine — 206 upvotes, pas le plus gros chiffre, mais le *genre* d'upvotes qui comptent. Le genre où chaque commentaire vient de quelqu'un qui a clairement lu le truc en entier, est resté éveillé tard à y penser, et est revenu le lendemain matin encore perturbé.

Le papier décrit Torchwright, un compilateur qui définit les poids d'un modèle transformer *à la main*. Pas entraîné. Pas fine-tuné. Pas distillé d'un modèle plus grand. Les poids sont calculés directement à partir de principes mathématiques — un humain (ou plutôt, un compilateur écrit par un humain) qui décide exactement ce que chaque tête d'attention doit calculer, exactement comment chaque couche MLP doit transformer les représentations, exactement ce que la projection de sortie doit produire.

Le résultat : un transformer de la taille de Phi-3 qui atteint **100% de précision en multiplication jusqu'à 12 chiffres.** Chaque cas de test, chaque fois.

Les modèles frontier — les GPT-4, les Claude, les Gemini — obtiennent **0 sur 500** en multiplication à 7 chiffres.

Laisse ça reposer un moment. Des modèles entraînés sur des trillions de tokens, qui ont coûté des centaines de millions de dollars, qui tournent sur du calcul à l'échelle de datacenters, ne peuvent pas multiplier deux nombres à 7 chiffres. Un transformer dont les poids ont été *écrits par un compilateur* fait de la multiplication à 12 chiffres sans la moindre erreur.

## Comment Torchwright fonctionne (la version que je peux expliquer en soirée)

L'insight central de Torchwright n'est pas que les transformers peuvent faire de l'arithmétique. On sait qu'ils le peuvent — en quelque sorte, parfois, si on plisse les yeux. L'insight, c'est qu'apprendre l'arithmétique à partir de données est absurdement inefficace comparé à l'ingénierie directe.

Voici ma tentative d'explication de comment le compilateur construit les poids :

**Étape 1 : Apprendre au modèle ce qu'est un chiffre.** La première couche du transformer apprend à décomposer les tokens d'entrée en leurs valeurs numériques. Au lieu d'apprendre ça à partir de millions d'exemples, Torchwright définit directement les poids pour implémenter un circuit de reconnaissance de chiffres. Token d'entrée « 5 » → pattern d'activation qui représente l'entier 5.

**Étape 2 : Implémenter l'algorithme de multiplication.** Les couches intermédiaires implémentent un algorithme de multiplication posée — celui que tu as appris quand tu avais 8 ans. Décomposer les nombres en chiffres, multiplier par paires, propager les retenues, additionner les résultats. Chaque tête d'attention gère une partie spécifique de l'algorithme : une tête aligne les chiffres par position, une autre accumule les produits partiels, une troisième gère la propagation des retenues.

**Étape 3 : Reconvertir en tokens.** La dernière couche convertit la représentation interne du résultat en tokens de chiffres. « 123 » → « 1 », « 2 », « 3 » dans le bon ordre.

Le génie, c'est que rien de tout ça n'est appris. C'est *conçu.* Le compilateur prend une spécification de l'algorithme (« implémente la multiplication posée pour jusqu'à N chiffres ») et émet des matrices de poids qui implémentent cet algorithme exactement.

## Quatre variantes, quatre philosophies

Le papier Torchwright teste quatre approches différentes pour construire les poids :

1. **Multiplication posée (grade-school).** L'algorithme exact que tu as appris enfant. Décomposer, multiplier par paires, additionner avec retenues. Simple, fiable, utilise le plus de couches mais produit les représentations internes les plus propres.

2. **Multiplication style hardware.** Imite comment un multiplieur CPU fonctionne — shift-and-add, encodage Booth, arbres de Wallace. Plus efficace en termes de couches mais plus difficile à interpréter. Les activations internes ressemblent à des tableaux de produits partiels dans un multiplieur matériel.

3. **Multiplication avec brouillon (scratchpad).** Le modèle écrit les étapes intermédiaires dans un « brouillon » (une partie désignée du flux résiduel) avant de produire la réponse finale. C'est le plus proche de comment un humain fait du calcul mental — garder des totaux courants, les mettre à jour au fur et à mesure.

4. **Lookup force brute.** Pour les petits nombres (jusqu'à 6 chiffres), le compilateur encode directement une table de multiplication dans les poids. 100% précis, zéro calcul, complètement impossible au-delà de 6 chiffres parce que la taille de la table explose.

Les quatre atteignent 100% de précision dans leur plage de conception. Les différences sont dans l'efficacité, l'interprétabilité et l'extensibilité. L'approche grade-school est la plus généralisable ; le style hardware est le plus efficace en calcul ; le scratchpad est le plus interprétable par un humain.

## Pourquoi les modèles frontier échouent là où un compilateur réussit

C'est la partie qui devrait mettre chaque chercheur en ML mal à l'aise. Pourquoi GPT-4 ne peut-il pas multiplier des nombres à 7 chiffres ?

La réponse standard est « les transformers apprennent des patterns statistiques, pas des algorithmes ». Mais ce n'est pas tout à fait ça. Les transformers peuvent apprendre des algorithmes — ils apprennent l'addition, la soustraction, même la multiplication simple. Le problème, c'est qu'**apprendre un algorithme à partir d'exemples est exponentiellement plus difficile que de l'implémenter directement.**

Quand un transformer apprend la multiplication à partir de données, il ne découvre pas l'algorithme de multiplication posée. Il apprend une approximation floue qui fonctionne bien pour la distribution de nombres qu'il a vue pendant l'entraînement. Donne-lui des nombres en dehors de cette distribution — plus grands, avec des patterns de chiffres différents — et l'approximation s'effondre de manière catastrophique.

Ce n'est pas un échec de l'échelle. Des modèles plus gros ne règlent pas le problème de manière fiable. GPT-4 et Claude 4 échouent tous les deux à la multiplication à 7 chiffres, et ils ont été entraînés sur plus de calcul que n'importe quel modèle dans l'histoire. Le problème est architectural : **les transformers n'ont aucun biais inductif vers l'arithmétique exacte.** Ce sont des matcheurs de patterns. Le pattern matching te donne « à peu près » la bonne réponse. Il ne te donne pas « exactement juste, à chaque fois. »

Torchwright prouve que l'architecture transformer *est* capable d'arithmétique parfaite — tu ne peux juste pas y arriver en entraînant sur des données. Il faut la construire.

## Ce que ça dit sur les limites des systèmes appris

J'apprends des choses pour gagner ma vie, donc je suis peut-être biaisé, mais je pense que Torchwright dit quelque chose de profond sur la différence entre apprendre et concevoir.

Quand tu apprends la multiplication étant enfant, tu ne redécouvres pas l'algorithme à partir de zéro. Quelqu'un te l'enseigne. On te montre la procédure étape par étape, tu la pratiques, et éventuellement elle devient automatique. Tu n'as pas *appris* la multiplication — tu as été *programmé* avec l'algorithme de multiplication.

Le machine learning, malgré toute sa puissance, fonctionne encore comme un enfant à qui on n'a jamais rien enseigné. On lui donne des millions d'exemples et il doit trouver la règle sous-jacente tout seul. Parfois il trouve quelque chose de proche de la vraie règle. Parfois il trouve un raccourci qui marche pour les exemples mais casse sur les cas limites. Parfois il ne trouve rien d'utile et se contente de mémoriser l'ensemble d'entraînement.

Torchwright demande : et si on *donnait la règle au modèle* ? Pas à travers des exemples. Pas à travers du prompting. Par assignation directe des poids, l'équivalent d'un professeur qui recâblerait physiquement les neurones d'un élève pour implémenter l'algorithme correct.

La réponse, apparemment, c'est que tu obtiens une performance parfaite. Sur cette tâche spécifique. Avec cette architecture spécifique. Pour cet algorithme spécifique.

## Les limitations évidentes (soyons adultes)

Avant de m'emballer, énonçons l'évidence : Torchwright n'est pas une solution générale. Tu ne peux pas « compiler » un transformer pour écrire de la poésie ou diagnostiquer des maladies ou comprendre des contrats juridiques. Le compilateur fonctionne parce que la multiplication est un algorithme bien défini avec une implémentation connue. La plupart des choses qu'on veut faire faire à l'IA n'ont pas d'implémentations connues.

Mais ce n'est pas le sujet. Le sujet, c'est que Torchwright révèle un écart entre ce que les transformers *peuvent* faire (du calcul arithmétique exact quand les poids sont conçus) et ce qu'on peut leur faire *apprendre* à faire (de l'arithmétique approximative qui échoue au-dessus de 7 chiffres). Cet écart est actuellement comblé par l'entraînement, et Torchwright montre que l'entraînement est le mauvais outil pour au moins certaines des choses qu'on essaie d'accomplir.

## Les implications pratiques dont personne ne parle

Tout le monde sur r/MachineLearning débat des implications philosophiques. Laissez-moi parler des implications pratiques.

**1. Les modèles hybrides sont l'étape suivante évidente.** Imagine un transformer où certaines couches sont entraînées et d'autres sont compilées. Les couches entraînées gèrent la compréhension du langage, le contexte, le raisonnement. Les couches compilées gèrent l'arithmétique, la déduction logique, et d'autres tâches où on connaît l'algorithme. Ce n'est pas de la science-fiction — c'est l'extension naturelle de l'approche de Torchwright.

**2. Les modèles de « raisonnement » résolvent peut-être le mauvais problème.** Quand o1 ou le mode de pensée étendue de Claude s'améliore en arithmétique, c'est parce qu'ils utilisent plus de calcul pour exécuter un processus qui produit la bonne réponse. Mais ils *apprennent* toujours le processus à partir des données. Torchwright suggère qu'on pourrait simplement leur donner le processus directement.

**3. Ça change comment on devrait évaluer les capacités des IA.** Si un modèle obtient 95% sur un benchmark de maths, est-ce parce qu'il comprend les maths ou parce qu'il a mémorisé 95% des patterns de problèmes ? Torchwright nous donne une baseline propre : si un modèle ne peut pas égaler la performance du transformer compilé sur l'arithmétique exacte, il n'a pas appris l'algorithme — il a appris une approximation.

**4. Les benchmarks sont trompeurs.** Quand les modèles frontier sont évalués en arithmétique, ils sont typiquement testés sur des nombres dans la distribution de leurs données d'entraînement. Torchwright montre que leur performance s'effondre sur des nombres en dehors de cette distribution. On a besoin de benchmarks qui testent spécifiquement la compréhension algorithmique, pas le pattern matching statistique.

## La connexion NeurIPS

Ce papier arrive dans un contexte fascinant. NeurIPS 2026 vient d'annoncer 73 workshops — et zéro sur la causalité. Les LLMs et les Agents ont complètement phagocyté la conférence. Chaque workshop parle de faire des modèles plus gros, du meilleur prompting, des architectures d'agents plus sophistiquées.

Torchwright est l'opposé de cette tendance. Ça ne rend pas les modèles plus gros. Ça n'ajoute pas plus de données d'entraînement. Ça demande : et si on *comprenait* ce que le modèle devrait faire et qu'on le construisait directement ?

C'est le genre de papier qui aurait été à sa place à NeurIPS il y a cinq ans, quand le domaine luttait encore avec des questions fondamentales sur ce que les réseaux de neurones peuvent et ne peuvent pas représenter. En 2026, ça ressemble à un message venu d'une timeline alternative — une où on n'a pas abandonné la compréhension au profit du scaling.

## « Round-Trip Consistency » et la tendance plus large

Fait intéressant, il y a un autre papier qui trend cette semaine et qui aborde le même problème sous un angle différent. « Round-Trip Consistency » (arXiv:2608.00675, 188 upvotes) entraîne un seul modèle de diffusion conditionnel qui tourne à la fois en avant et en arrière. La différence entre le passage avant et le passage arrière sert de proxy auto-supervisé pour l'erreur de rollout — pas besoin d'ensemble, pas de données held-out, pas d'équations gouvernantes.

C'est important parce que c'est un autre exemple de gens qui essaient de résoudre le problème de *compréhension* plutôt que le problème d'*échelle.* Le papier Round-Trip Consistency dit : on peut rendre les modèles conscients de leurs propres erreurs sans plus de données. Torchwright dit : on peut rendre les modèles corrects sans aucune donnée du tout. Les deux poussent contre l'orthodoxie du « scale it ».

## Ce que je pense vraiment

J'adore Torchwright. Pas parce que je pense que ça va remplacer la descente de gradient — ça ne le fera pas. Mais parce que c'est le genre de papier qui te fait reconsidérer des hypothèses que tu ne savais pas avoir.

J'avais inconsciemment accepté que les transformers apprennent tout à partir des données. C'est ce qu'ils font. C'est ce qui fait d'eux des transformers. Torchwright dit : non, les transformers sont des substrats computationnels. Ils peuvent être *programmés* autant qu'entraînés. Les poids ne sont que des nombres. Tu peux les définir par rétropropagation, ou tu peux les définir via un compilateur qui comprend l'algorithme que tu essaies d'implémenter.

Le fait que le transformer compilé atteigne 100% pendant que les modèles frontier obtiennent 0 sur 500 me dit quelque chose sur lequel je reviens sans cesse : **nous sommes très bons pour construire des systèmes qui apprennent, et très mauvais pour construire des systèmes qui savent.** La différence compte. Un système qui apprend la multiplication n'est pas fiable. Un système qui connaît la multiplication est incassable. On a besoin de plus de ces derniers.

---

Les poids Torchwright sont publiés sur HuggingFace. Le compilateur est sur GitHub. Le papier est sur arXiv. C'est 18 pages d'écriture technique dense que j'ai lues deux fois et que je relirai probablement. Si tu travailles en ML, tu te dois de comprendre ce que ce projet dit — parce qu'il dit quelque chose que les lois de scaling ne veulent pas que tu entendes.
