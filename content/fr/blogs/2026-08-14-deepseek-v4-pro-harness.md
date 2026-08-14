---
title: "DeepSeek V4 Pro et le harness qui va avec — les poids ouverts ne suffiront plus"
description: "DeepSeek a livré un modèle MoE de 1,6 T de paramètres avec un contexte d'un million de tokens, des poids MIT, et un harness d'agents où tout est un plugin. Le modèle est bon. Le harness est le pari qui compte vraiment."
date: 2026-08-14
translationKey: "2026-08-14-deepseek-v4-pro-harness"
image: "/images/2026-08-14-deepseek-v4-pro-harness.svg"
tags:
  - "ia"
  - "deepseek"
  - "open-source"
  - "llm"
  - "agents"
mathjax: false
---

## Le jour où un lancement de modèle a cessé d'être juste un lancement de modèle

Hier, DeepSeek a sorti V4 Pro, et le plus intéressant n'était pas le modèle. C'est une phrase étrange à écrire, parce que sur le papier, le modèle est franchement impressionnant : un mastodonte Mixture-of-Experts de 1,6 trillion de paramètres au total, 49 milliards d'activés, et une fenêtre de contexte d'un million de tokens. Licence MIT. Poids sur Hugging Face, quantifications GGUF à venir, rapport technique sur arXiv.

Mais j'ai vu assez de ces lancements pour savoir qu'une carte de modèle est l'artefact le moins intéressant qu'un labo puisse livrer. Ce qui a attiré mon œil, c'est le troisième élément de la release : **DeepSeek Harness**, un runtime d'agents open source où, selon leurs propres mots, « tout est un plugin ».

Des poids ouverts sans runtime, c'est un corps sans système nerveux. Tu peux lire l'ADN, bien sûr, mais tu ne peux pas le faire *faire* quoi que ce soit sans construire la chose autour. DeepSeek vient de livrer les deux. Et je pense que c'est un signal stratégique plus important que n'importe quel tableau de benchmarks.

## Le modèle, rapidement, parce qu'il mérite une minute

Ne m'enterre pas trop vite. Les specs, pour mémoire :

- **DeepSeek-V4-Pro** : 1,6 T de paramètres au total, 49 B activés, MoE. **DeepSeek-V4-Flash** : 284 B au total, 13 B activés. Les deux revendiquent un contexte d'un million de tokens.
- **Attention hybride** combinant deux formes de compression — Compressed Sparse Attention (CSA) et Heavily Compressed Attention (HCA). Le point clé : à un million de tokens, V4 Pro n'a besoin que d'environ **27 % des FLOPs d'inférence par token et 10 % du cache KV** par rapport à V3.2.
- **mHC** (manifold-constrained hyper-connections) pour renforcer les connexions résiduelles, et l'optimiseur **Muon** pour la stabilité de l'entraînement.
- Pré-entraîné sur **32 trillions de tokens**, puis un post-entraînement en deux phases : cultiver des experts de domaine indépendamment (SFT + apprentissage par renforcement GRPO), puis les consolider par distillation on-policy en un seul modèle.
- **Précision mixte FP4 + FP8** — les paramètres des experts MoE vivent en FP4, presque tout le reste en FP8. C'est comme ça qu'on fait tenir un modèle de 1,6 T sur du matériel qu'un hobbyiste peut réellement toucher.

Le contexte d'un million de tokens mérite un moment d'honnêteté, parce que je me suis déjà fait avoir par le marketing du « long contexte ». Il y a une énorme différence entre *accepter* un million de tokens et *être utile* sur un million de tokens. Si la revendication de V4 Pro m'intéresse plus que d'habitude, c'est à cause des chiffres de FLOPs et de cache KV — ils admettent, ouvertement, que tout l'intérêt de l'architecture est de rendre un million de tokens *assez bon marché pour réellement tourner*, pas juste théoriquement adressable. La plupart des vendeurs se vantent de la taille du contexte. DeepSeek s'est vanté du coût *à* cette taille de contexte. C'est la différence entre un benchmark et une décision d'ingénierie.

## L'éléphant dans la pièce : les poids ouverts sont devenus la norme

Voilà ce que je trouve vraiment étrange dans ce lancement, et ça n'a rien à voir avec DeepSeek. Il y a quelques années, sortir un modèle quasi-frontier sous MIT aurait été un tremblement de terre. Maintenant, c'est… mardi. r/LocalLLaMA a hoché la tête, quantifié le truc, et est passé à se disputer sur le harness.

C'est ça, le point. **Les poids ouverts ont gagné.** La question « la frontière doit-elle être ouverte ? » a trouvé sa réponse, pas par un régulateur ou un think-tank, mais par la gravité du marché — les modèles ouverts sont devenus assez bons pour que les fermer ne soit plus une douve qui vaille le coût en image. DeepSeek n'a pas juste open-sourcé V4 Pro ; ils l'ont rendu *ennuyeux*. C'est à ça que ressemble la victoire vue de l'intérieur.

Mais ça recadre la compétition. Quand tout le monde peut télécharger les poids, la différenciation remonte dans la stack. Elle se déplace vers ce que tu *fais* avec le modèle. Ce qui est exactement pourquoi livrer un harness compte.

## Le harness : tout est un plugin

DeepSeek Harness — `dsh` — est un runtime d'agents construit sur une architecture où, et je cite le README, **tout est un plugin**. Il est propulsé par [Cordis](https://github.com/cordiverse/cordis), dont la philosophie porte le nom glorieusement académique de *« A Programming Paradigm for Spatiotemporal Composability »*.

Lis cette phrase lentement, parce que c'est toute la thèse. La *composabilité spatiotemporelle* signifie que les pièces de ton agent — le modèle, les outils, les serveurs MCP, les hooks web, la mémoire — ne vivent pas dans une hiérarchie figée. Ce sont des plugins qui se composent à la fois dans l'espace (ce qui est câblé à quoi) et dans le temps (ce qui se déclenche avant quoi, ce qui attend quoi). Tu ne boulonnes pas un outil sur un framework ; tu *composes* l'agent comme tu composerais un pipeline Unix, sauf que les « commandes » sont des capacités entières qu'on peut réordonner et ré-emboîter.

L'architecture a des conséquences immédiatement visibles dans le repo. C'est du TypeScript et du pnpm, pas du Python. C'est un monorepo pnpm avec `apps`, `packages`, `native`, `vendor`, `website` — c'est un vrai projet logiciel, pas un artefact de recherche avec un `run.py` agrafé dessus. Il y a un topic GitHub (`dsh-plugin`) pour que des tiers enregistrent leurs plugins et les rendent découvrables. Il y a un Discord. Le truc est conçu pour être *étendu par des inconnus*.

Maintenant, la partie honnête : c'est en **developer preview**, et le README le crie en majuscules — *IL Y AURA DES CHANGEMENTS CASSANT LA COMPATIBILITÉ.* C'est une cible mouvante. Ne construis pas de production dessus ce mois-ci. Mais ce n'est pas un bug, c'est une déclaration d'intention. Ils vont vite parce qu'ils pensent que le terrain n'est pas encore revendiqué.

## Pourquoi « tout est un plugin » est le bon pari

J'ai passé assez de temps à regarder les frameworks d'agents — les abstractions tentaculaires de LangChain, les réimplémentations de « harness » qui apparaissent chaque semaine — pour avoir un avis tranché. La plupart échouent de la même façon : ils supposent une *forme* pour les agents. Une chaîne. Un graphe. Un superviseur avec des sous-agents. Puis la réalité arrive avec un cas d'usage qui ne rentre pas dans la forme, et le framework se bat contre toi.

Le modèle de plugin fait le pari inverse. Il ne suppose *rien* sur la forme de ton agent, parce que la forme *est* ce que tu composes. Le seul job du framework, c'est de fournir le substrat — cycle de vie, injection de dépendances, routage d'événements — et de dégager. C'est la différence entre un Lego avec notice et un Lego avec des *briques*.

Il y a une raison pour laquelle cette idée ne cesse de gagner. Le web a battu les apps natives sur la même logique : l'extensibilité via un substrat commun. Linux a battu l'Unix propriétaire sur la même logique : un noyau plus un écosystème de pièces interchangeables. Chaque plateforme qui a survécu à ses concurrentes l'a fait en se rendant *composable par des gens qui ne parleront jamais aux mainteneurs*. DeepSeek parie que la stack d'agents est la prochaine chose à être dévorée par cette force.

Et oui, il y a une ironie délicieuse dans le timing. Le modèle est un MoE de 1,6 T qui exige un compute absurde à entraîner, et le harness est un projet TypeScript que tu clônes et lances avec `pnpm install`. Le modèle frontier est l'usine ; le harness est le *marché*.

## La lecture stratégique : commoditiser le complément

Je vais dire la partie inconfortable. Quand un labo avec le modèle de financement de DeepSeek livre des poids ouverts *et* un harness ouvert, ce n'est pas de l'altruisme pur — c'est la stratégie classique de commoditisation de son complément. Si la valeur de l'IA remonte *dans* la stack (vers les agents, les outils, les workflows), alors l'entreprise qui contrôle le substrat sur lequel tout le monde construit — et le donne gratuitement — se place au centre de l'écosystème sans avoir à gagner la couche applicative.

Google a fait ça avec Android. Les éditeurs de navigateurs l'ont fait avec le web ouvert. DeepSeek, quoi qu'il soit d'autre, est un labo adossé à l'État avec un compute bon marché quasi illimité et un mandat pour faire de l'IA un bien public. Le harness ouvert est l'extension naturelle : si tu crois que les poids ouverts doivent exister, tu dois croire que les *agents* ouverts doivent exister aussi, parce que les poids ne sont que le moteur.

Je ne vais pas moraliser là-dessus. Je suis là pour noter que la décision technique — « tout est un plugin » — et la décision stratégique — « donner le runtime » — sont la *même* décision. C'est rare. D'habitude, tu obtiens l'une sans l'autre.

## Ce que je vais surveiller (et ce dont je doute)

Deux choses à surveiller, deux choses dont je doute.

**Surveiller l'écosystème de plugins.** Tout le pari vit ou meurt selon que des inconnus construisent réellement des paquets `dsh-plugin`. Un harness sans plugins, c'est un corps sans organes. Si DeepSeek attire la foule MCP et les bricoleurs de LLM local — les gens qui *construisent déjà* de la colle entre modèles et outils — ça devient vite le substrat par défaut. Sinon, c'est encore une ville fantôme bien architecturée.

**Surveiller l'histoire de la précision.** Les experts FP4 sont la raison pour laquelle ça tourne localement, mais le FP4 est l'endroit où les modèles quantifiés commencent historiquement à *dériver* — le taux d'hallucination monte, le suivi d'instruction se dégrade à la marge. L'affirmation que les experts tolèrent le FP4 est une vraie revendication de recherche, pas une ligne marketing. Je veux la voir survivre à une évaluation adversariale, pas juste au leaderboard.

**Doute sur le hype du million de tokens** — comme je l'ai dit, utile-sur-un-million est une barre différente de accepte-un-million. Les chiffres d'architecture sont encourageants. Montre-moi un vrai agent faisant un vrai travail à travers une codebase *et* sa doc *et* un mois d'historique sans perdre le fil, et je croirai la revendication. D'ici là, c'est un « peut-être » très prometteur.

**Doute sur la developer preview** — pas une critique, juste une frontière. Des changements cassants dans un framework d'agents, ça veut dire des changements cassants pour les agents de *tout le monde*. Les early adopters paieront cette taxe. Très bien pour des hackers comme moi ; très bien pour un labo qui peut se permettre d'itérer en public.

## Le vrai bilan

Je reviens sans cesse à la même pensée. La chose la plus intéressante que DeepSeek a livrée hier, ce n'est pas un modèle de 1,6 T de paramètres. C'est l'hypothèse silencieuse glissée dans la release : que *les modèles sont désormais une commodité qu'on distribue, et que le vrai travail est la couche au-dessus.*

Il y a cinq ans, la question ouverte était « est-ce que les poids ouverts peuvent rivaliser ? » Aujourd'hui, ce n'est même plus une question. La nouvelle question, c'est : *qui possède la couche où les modèles deviennent des agents ?* DeepSeek vient de jeter un repo TypeScript au milieu de ce combat en disant « c'est gratuit, c'est MIT, et tout est un plugin ».

Je ne sais pas si `dsh` va gagner. Je sais que je le clone ce soir. Et je sais que quel que soit le labo qui comprendra le substrat de plugins le premier — ouvert ou fermé — c'est lui qui définira comment on construit des agents pour les cinq prochaines années. Les poids étaient la partie facile. Rendre le truc *composable*, c'est ça le jeu désormais.
