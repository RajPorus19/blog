---
title: "Le Assembly Hall of Shame — ou pourquoi rendre les CPUs lents est un art"
description: "Plongée dans le projet qui trouve les instructions x86 les plus lentes. Le champion actuel : fxrstor64 en 62 secondes. C'est de la poésie hardware."
date: 2026-08-10
translationKey: "assembly-hall-of-shame"
image: "/images/assembly-hall-of-shame.svg"
tags:
  - "assembleur"
  - "cpu"
  - "performance"
  - "sécurité"
  - "hardware"
mathjax: false
---

## Le projet qui demande : à quel point une seule instruction peut-elle être lente ?

Il y a un repo GitHub qui tourne sur r/programming cette semaine : [asm-hall-of-shame](https://github.com/xoreaxeaxeax/asm-hall-of-shame), par le légendaire [xoreaxeaxeax](https://github.com/xoreaxeaxeax) (la même personne derrière le *Movfuscator* — un compilateur qui produit des binaires en utilisant uniquement des instructions `mov`). 476 upvotes et un thread rempli de nerds hardware en train de perdre la tête.

Le principe est magnifiquement simple : trouve la seule instruction x86 la plus lente que tu peux exécuter. Pas une séquence d'instructions. Pas un programme. **Une instruction.** Le champion actuel : `fxrstor64`, qui affiche **198 milliards de cycles** — soit **62 secondes** — sur un AMD Ryzen 7 5800H.

Pour comparaison : un `nop` normal prend 1 cycle.

## Comment faire ramper un CPU pendant 62 secondes

La stratégie derrière `fxrstor64` est un masterclass de sadisme hardware :

1. `fxrstor64` charge 512 octets d'état FPU/MMX/XMM depuis une adresse mémoire
2. Cette adresse pointe vers une **région MMIO à haute latence** dans le bus PCIe — pas de la RAM, mais une région I/O mappée en mémoire, délibérément lente
3. Pendant que le chargement est en vol, une flotte de « cœurs marteaux » martèle un autre registre MMIO à haute latence avec des lectures serrées de 4 octets, **saturant le root complex PCIe**
4. Le `fxrstor64` du CPU 0 doit faire la queue derrière tout ce trafic concurrent

Autrement dit : tu ne demandes pas juste au CPU de faire quelque chose de lent. Tu *fabriques un embouteillage* dans le bus PCIe, puis tu pousses un chargement de 512 octets à travers. C'est l'équivalent hardware de commander un seul café dans un Starbucks bondé à l'heure de pointe et de payer en pièces de 1 centime.

## Le classement raconte une histoire

Ce qui rend le classement fascinant, ce n'est pas juste les chiffres — c'est l'arc narratif du simple au dérangé :

| Rang | Instruction | Cycles |
|------|------------|--------|
| 27 | `nop` | 1 |
| 26 | `nop16` (nop long) | 20 |
| 25 | `rdtsc` (référence) | 49 |
| 24 | `idiv` | ... |
| ... | ... | ... |
| 🏆 1 | `fxrstor64` | 198 002 498 236 |

On peut tracer la progression : commencer par « rendons le rien plus long » (nops longs), passer à « instructions légitimes mais lentes » (idiv, rdtsc), puis escalader vers « instructions qui touchent du hardware externe », et finalement atterrir sur « instructions qui touchent du hardware externe pendant qu'on attaque activement le bus depuis d'autres cœurs. »

Il y a quelque chose de profondément satisfaisant à voir un classement où le but est d'être *dernier*.

## Pourquoi c'est important au-delà du meme

C'est facile de rejeter ça comme un projet fun mais inutile. Ce serait une erreur, pour au moins trois raisons :

**1. Ça t'apprend comment les CPUs modernes fonctionnent vraiment.** Lire des explications sur la contention PCIe et les régions MMIO, c'est abstrait jusqu'à ce que tu voies une instruction de 62 secondes. Les entrées du classement sont annotées avec des stratégies qui expliquent *pourquoi* chaque instruction est lente — et ce faisant, elles t'en apprennent plus sur la microarchitecture CPU que n'importe quel diagramme de manuel.

**2. C'est adjacent à de la recherche en sécurité sérieuse.** Le même auteur a utilisé un chargement `vmovdqu` non aligné violant la spec (Mention Honorable au classement) pour casser le System Management Mode — le contexte d'exécution le plus privilégié sur x86 — dans un projet appelé [smiiiiiiiiiiiiiiii](https://github.com/xoreaxeaxeax/smiiiiiiiiiiiiiiii). Comprendre comment faire se comporter le hardware de façon inattendue est la base de l'exploitation au niveau matériel.

**3. C'est un rappel que la performance, ce n'est pas juste la vitesse.** On passe tellement de temps à optimiser pour la latence *minimale* qu'on oublie que le hardware a des comportements fascinants à l'autre extrême. Parfois, comprendre le pire cas est plus important qu'optimiser le cas moyen — surtout si tu conçois des systèmes temps réel ou que tu durcis contre les attaques side-channel.

## Ce sont les règles qui rendent le truc intéressant

Le projet a quatre règles :

1. Une seule instruction est scorée — tu peux faire tout le setup que tu veux, mais une seule instruction compte
2. Les instructions trappées/émulées/virtualisées ne chronomètrent que le trap, pas le handler (pas de triche avec `int3`)
3. Les instructions ne doivent pas être interruptibles — `rep movs`, `pause`, etc. sont disqualifiées
4. Toutes les plateformes doivent être en configuration stock — pas de modifications hardware

Ces contraintes forcent la créativité. Tu peux pas juste déclencher un page fault et compter le temps du handler. Tu peux pas câbler du hardware externe pour bloquer le bus. Tu dois trouver des instructions qui sont *légitimement* lentes sur du hardware grand public non modifié. Le `fxrstor64` de 62 secondes fait exactement ça.

---

Si t'as jamais passé un après-midi à regarder les tables de latence d'Intel en te demandant « comment je pourrais abuser de ça ? », le Assembly Hall of Shame est ta drogue d'entrée. Le repo est bien documenté, les stratégies sont expliquées, et c'est authentiquement éducatif emballé dans la peau d'un shitpost.

C'est le meilleur type de projet.
