---
title: "Thermite SIMD — écrire son kernel une seule fois, et laisser Rust faire fondre tous vos CPU"
description: "Thermite est une bibliothèque SIMD portable qui compile un unique kernel Rust générique vers chaque jeu d'instructions et chaque largeur de vecteur, et calcule ses propres dérivées en bonus. Après six ans de réécriture, c'est peut-être le projet HPC mono-machine le plus intéressant de Rust."
date: 2026-08-13
translationKey: "2026-08-13-thermite-simd"
image: "/images/2026-08-13-thermite-simd.svg"
tags:
  - "rust"
  - "simd"
  - "hpc"
  - "performance"
  - "open-source"
mathjax: false
---

## « Melt your CPU » est une blague. La bibliothèque, non.

J'ai un faible pour les projets dont le slogan est légèrement dérangé. Thermite SIMD, une bibliothèque Rust qui a refait surface sur r/rust cette semaine, se décrit en quatre mots : *« Melt your CPU »*. Le README précise aussitôt que c'est « juste un slogan amusant, évidemment », et que le vrai but est de « tirer d'un CPU tout le travail utile qu'il peut physiquement produire ».

Cette tension — la blague d'un côté, l'ingénierie prise très au sérieux de l'autre — c'est exactement ce qu'est le projet. Et j'ai envie d'en parler, parce que je pense que Thermite fait quelque chose de vraiment rare : il construit de l'outillage de **HPC mono-machine**, dans un monde qui a passé la dernière décennie à se convaincre que seuls les clusters méritaient de l'intérêt.

Le pitch est d'une simplicité trompeuse. Vous écrivez un kernel numérique **une seule fois**, générique sur une petite hiérarchie de traits. Cette unique fonction compile en code haute performance pour chaque jeu d'instructions et chaque largeur de vecteur — AVX2, SSE2, NEON, WASM SIMD128, tout y passe. Sans la modifier, la *même fonction* calcule aussi ses propres dérivées, tourne en précision double-double, ou s'évalue sur le plan complexe.

Trois sens, un seul corps de fonction, zéro édition. Cette phrase à elle seule m'a fait dresser l'oreille.

---

## Le kernel qui ne sait pas ce qu'est un CPU

Voici l'exemple canonique du README, légèrement raccourci :

```rust
#[thermite::dispatch(V)]
fn gaussian<V: FloatVector + TranscendentalMath>(x: V) -> V {
    (-(x * x)).exp()
}
```

C'est tout le kernel. Regardez ce qu'il ne nomme *pas* : pas de jeu d'instructions, pas de nombre de lanes, pas de type d'élément, pas de `target_feature`, pas de `unsafe`. Il dit « moins x au carré, puis exponentielle », et rien d'autre.

Ce avec quoi vous l'instanciez décide de ce que vous obtenez. Passez-lui un vecteur SIMD classique et vous avez un `exp` vectorisé. Enveloppez-le dans le type multidual de `thermite_dual` et la même fonction produit la valeur *et* son gradient en une seule passe. Enveloppez-le dans `thermite_compensated` et il s'évalue avec une mantisse d'environ 106 bits. Donnez-lui des complexes et il tourne sur le plan complexe. Le kernel ne change jamais.

C'est le genre d'abstraction qui, normalement, coûte de la performance — et dans le monde SIMD, le coût est en général *catastrophique*. Un corps générique qui oublie d'être inliné, ou qui franchit une frontière d'appel sans ses target features, compile silencieusement en code scalaire de base. Correct, mais inutilement lent. La distance entre « générique et sûr » et « rapide » a toujours été un canyon.

La réponse de Thermite s'appelle `#[dispatch]`, une macro procédurale, et la comprendre, c'est comprendre tout le projet.

---

## `#[dispatch]` : réparer l'amnésie de `target_feature` en Rust

Rust a un piège bien connu autour de `#[target_feature(enable = "...")]`. Si une fonction porteuse de ces features n'est, un jour, *pas* inlinée, elle les « oublie ». L'optimisation s'évapore. Pour un programme compilé en SSE2 de base qui veut supporter le matériel moderne via du dispatch dynamique, c'est un désastre en attente : soit tout inline toujours (énorme explosion de code), soit tout se dégrade en désoptimisation dès que le compilateur fait un choix d'inlining différent.

`#[dispatch]` réécrit les fonctions pour que les target features se propagent automatiquement et **statiquement** à travers les frontières d'appel. Après élimination du code mort, c'est effectivement à coût nul. Elle peut s'appliquer à une fonction, un bloc `impl`, un trait, ou tout un `mod`, et elle est appliquée à tous les traits `*Math` pour qu'ils restent optimisés.

Le README est brutal sur ce qui arrive si on l'oublie : « Un corps SIMD générique sans `#[dispatch]` au-dessus compile sans features : chaque intrinsèque devient un appel hors-ligne, rien n'est optimisé entre eux. Ça compile, c'est correct, c'est catastrophiquement lent. »

J'adore cette formulation, parce qu'elle nomme un vrai mode de défaillance du modèle de features de Rust au lieu de le balayer d'un revers de main. Beaucoup de bibliothèques « rapides en Rust » font semblant que `target_feature` n'est pas un champ de mines. Thermite a construit le détecteur de mines.

---

## La hiérarchie de traits : une interface, pas un wrapper

En dessous, Thermite est une hiérarchie `GenericVector -> NumericVector -> FloatVector`. Un kernel est borné sur le trait le plus faible dont il a réellement besoin, et ne nomme rien d'autre. Le type `Vector` implémente cette hiérarchie pour chaque backend et chaque nombre de lanes — si bien que `f32x8` sur AVX2 et `f64x2` sur NEON présentent la même interface et la même sémantique.

L'astuce, c'est que la hiérarchie est *une interface, pas un wrapper sur les types d'une machine*. C'est pour ça que les duals autodiff, l'arithmétique compensée et les nombres complexes peuvent tous implémenter les mêmes traits et réutiliser chaque kernel, bibliothèque mathématique comprise. La composabilité découle du design au lieu d'être greffée après coup.

Et puis il y a le tableau des backends, qui mérite une lecture lente :

| Backend | ISA | Statut |
|---|---|---|
| `scalar` | aucun, 1 lane | Toujours dispo — les bouts irréguliers des boucles |
| `x86_v1` | SSE2 | Complet |
| `x86_v2` | SSE4.2 | Complet |
| `x86_v3` | AVX2 + FMA | Complet, cible d'optimisation principale |
| `x86_v4` | AVX-512 | Pas encore implémenté — tourne en AVX2 pour l'instant |
| `neon` | AArch64 AdvSIMD | Complet |
| `wasm` | SIMD128 | Complet |
| `spirv` | SPIR-V | Expérimental, une erreur de compilation en release |

Les trois backends x86 compilent sans condition dans un seul binaire, et `dispatch_dyn!` choisit le meilleur à l'exécution. Un seul binaire, tous les CPU Intel et AMD des quinze dernières années, sans recompilation.

Je vais être honnête sur ce qui saute aux yeux : **AVX-512 n'est pas terminé.** Le README réserve même les noms de features `avx512-tier1` à `tier4` pour que les noms restent stables quand le backend arrivera. C'est un projet honnête sur sa feuille de route au lieu de faire semblant que la partie difficile est finie. Je respecte ça plus qu'un README plein de coches.

---

## Une vraie bibliothèque mathématique vectorisée — et un système de politiques

La plupart des bibliothèques « SIMD math » vous donnent `exp`, `log` et la trigonométrie, et s'arrêtent là. Thermite va plus loin : `erf`, `gamma`, les intégrales elliptiques (les cinq primitives symétriques de Carlson), Lambert W (les deux branches réelles, entrelacées pour que la seconde soit quasi gratuite), les polynômes orthogonaux, l'intégrale exponentielle. Tout vectorisé plutôt que bouclé lane par lane.

Ce qui est vraiment inhabituel, c'est le système de **politiques**. Chaque fonction a une forme `_p` qui prend une politique au moment de la compilation, si bien que le même appel `exp` se règle de `UltraPerformance` à `Reference` sur le site d'appel, plutôt que via un second jeu de noms de fonctions. Vous ne payez pas pour une précision que vous n'avez pas demandée — et quand vous la demandez, vous l'avez entière.

Il y a aussi tout un tas de choses qui montrent que c'est construit par quelqu'un qui écrit vraiment des kernels : la compaction de flux, les prefix scans de lanes, la détection de conflits, le gather/scatter, les diviseurs sans branche, les formats de stockage compressés fp16/bf16/fp8 qui tiennent moitié moins d'octets pendant que l'arithmétique tourne à pleine précision. Un `SimdSlice` qui gère la danse prologue-épilogue aligné/non-aligné/streaming pour ne pas la réécrire à chaque boucle.

---

## La réécriture de dix ans dont personne ne veut parler

Voici la partie de l'histoire qui compte le plus pour moi, parce que c'est la plus facile à sauter.

La section « Histoire » du README est courte et inhabituellement honnête. Thermite a été conçu à l'origine en travaillant sur le renderer Raygon, quand l'auteur a décidé qu'il fallait une bibliothèque SIMD à la pointe pour les algorithmes SoA. Le premier prototype était raté — « trop d'abstractions qui fuient » — et en 2020, Rust lui-même était trop limité pour exprimer ce que le design exigeait.

Alors l'auteur l'a mis de côté. En 2025, avec des const generics matures, un meilleur résolveur de traits, l'edition 2024, et une couverture bien plus large des intrinsèques stables, il l'a redessiné de zéro. La phrase qui m'est restée : **« Rust est assez puissant pour exprimer tout ça en toute sécurité sur stable, mais il m'a fallu dix ans d'écriture (et un prototype raté) pour comprendre comment. »**

Dix ans, un prototype raté, et un langage qui a dû grandir entre-temps. C'est le *vrai* coût des abstractions que Thermite fournit. Personne n'écrit un README comme ça sans l'avoir réellement payé.

Ça explique aussi les contraintes que le projet s'impose, qui se lisent comme un manifeste : `no_std` par défaut, un arbre de dépendances minuscule, un toolchain stable, pas de scripts de build, pas d'étape de codegen, pas de FFI dans le crate cœur. L'auteur le formule comme la quête de « la bibliothèque que j'ai toujours voulue pour le HPC mono-machine et que je n'ai jamais trouvée ».

---

## Le HPC mono-machine est une vraie chose, et on l'a négligée

Cette dernière phrase, c'est la thèse, et elle mérite qu'on s'y attarde. Le monde du calcul numérique, dit l'auteur, « se divise entre des frameworks à l'échelle du cluster d'un côté, et de fines surcouches SIMD de l'autre, avec un marécage de bloat logiciel entre les deux ».

Il a raison. On a passé une décennie à rendre facile la location de mille GPU, et vraiment difficile le fait de presser une seule grosse station de travail jusqu'à sa dernière goutte. Tout le monde se jette sur le cluster parce que l'outillage de la machine unique est un bazar : les intrinsèques sont `unsafe` et non portables, les surcouches sont fines, et les abstractions fuient.

Le pari de Thermite, c'est que si vous rendez la machine unique *aussi maniable* que le cluster — écrivez une fois, dispatchez à l'exécution, obtenez dérivées et précision gratuitement — alors beaucoup de gens qui n'avaient jamais besoin du cluster arrêteront de le payer. Je ne sais pas si ce pari paiera, mais c'est un pari qui vaut la peine, et je n'ai vu personne d'autre le faire aussi proprement dans l'écosystème Rust.

Il y a aussi quelque chose de discrètement subversif là-dedans sur le rapport entre **sécurité et vitesse.** Le récit Rust par défaut, c'est qu'on peut avoir la sécurité mémoire, et qu'on peut avoir la performance, mais que cette dernière exige de descendre dans `unsafe` et de bidouiller les intrinsèques à la main. Toute la prémisse de Thermite, c'est qu'on peut avoir un kernel *sûr, générique, `no_std`* qui fait quand même fondre le CPU — parce que le compilateur fait la plomberie `unsafe` une seule fois, correctement, derrière une frontière de trait.

---

## Ce que j'en pense vraiment

Je ne vais pas prétendre que Thermite est fini. AVX-512 manque, l'ARM 32 bits n'est pas supporté, RISC-V V n'est pas commencé, et le backend SPIR-V est littéralement une erreur de compilation en release. Le projet a 222 étoiles, trois forks, zéro issue ouverte — ce qui veut dire que c'est le travail d'amour d'une seule personne, pas un écosystème.

Et je l'aime précisément pour ça. C'est le genre de projet qui n'existe que parce que quelqu'un avait besoin d'un outil, a passé une décennie à apprendre à le construire, et puis l'a… construit. Pas d'entreprise derrière, pas de levée de fonds, pas de landing page « nous construisons le futur du calcul ». Un slogan qui dit *fais fondre ton CPU*, et une base de code capable de le soutenir.

La chose sur laquelle je reviens sans cesse, c'est cette réécriture de dix ans. On parle sans fin de « réécrire en Rust » comme d'un mème, mais Thermite est le projet rare où la réécriture s'est faite *vers* un langage qui a dû devenir capable du projet d'abord. Les const generics, le résolveur de traits, l'edition 2024, les intrinsèques stables — ce ne sont pas des features accessoires. Ce sont les murs porteurs qui ont rendu l'abstraction possible. Thermite est, en un sens bien réel, un reçu de la maturité de Rust.

Alors non, je ne pense pas que vous devriez réécrire vos kernels de production en Thermite demain. Mais je pense que vous devriez lire le README, lire le guide, et faire attention à ce que ce projet affirme : que l'écart entre « générique et sûr » et « aussi rapide que le silicium le permet » est plus petit qu'on nous l'a dit, et que le refermer est un problème de design, pas un problème de matériel.

Si vous avez une seule machine et un problème numériquement lourd, Thermite est l'argument qui dit que vous méritez mieux qu'un marteau en forme de cluster. Et au pire, le slogan est excellent.

---

*Thermite SIMD vit sur [github.com/raygon-renderer/thermite](https://github.com/raygon-renderer/thermite), avec le tour complet dans le module `thermite::guide`. La branche rewrite a été poussée cette semaine — ce qui est, je soupçonne, exactement la raison pour laquelle elle a resurgi sur r/rust.*
