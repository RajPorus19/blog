---
title: "Stack Overflow a perdu 99 % de ses questions — et la raison est plus inconfortable que « l'IA l'a tué »"
description: "Le site est passé de 207 204 questions par mois en 2014 à 1 442 en juillet 2026. Tout le monde veut blâmer ChatGPT, mais le déclin a commencé en 2014, des années avant le premier LLM. La vraie histoire parle de ce qui arrive quand un commun de la connaissance aliène ses propres contributeurs — puis se fait dévorer par la machine qu'il a entraînée."
date: 2026-08-15
translationKey: "2026-08-15-stack-overflow-ai-collapse"
image: "/images/2026-08-15-stack-overflow-ai-collapse.svg"
tags:
  - "stack-overflow"
  - "ia"
  - "llm"
  - "communaute"
  - "connaissance"
  - "open-source"
mathjax: false
---

## Le chiffre qui m'a fait arrêter de scroller

Il y a un graphique qui circule sur r/programming cette semaine, et je l'ai maintenant regardé une bonne quarantaine de fois, et il me fait toujours quelque chose de désagréable à l'estomac, à chaque fois.

Le chiffre en tête de l'histoire est celui-ci : Stack Overflow a enregistré **1 442 nouvelles questions en juillet 2026**. Pas par jour. Par mois. Pour remettre en perspective, à son pic de mars 2014, le site voyait **207 204 questions en un seul mois**. C'est une baisse de 99,3 %. L'une des plus grandes et des plus importantes archives publiques de connaissance en programmation jamais créées reçoit désormais moins de questions qu'elle n'en recevait durant son tout premier mois d'existence, en 2008, quand ce n'était qu'un projet à moitié fini dont personne ne connaissait l'existence.

Le fil a 3 800 upvotes. Les commentaires sont la partie intéressante — ils ne fanfaronnent pas. Ils ne disent pas « bon débarras ». Ce sont beaucoup de gens qui pleurent en silence quelque chose qu'ils ne savaient pas aimer avant qu'il ne soit déjà parti, mêlés à un plus petit groupe qui insiste sur le fait que tout va bien, que c'est même une bonne chose, parce que « maintenant on a ChatGPT ».

J'ai envie d'en parler sérieusement, parce que je pense que la version de cette histoire que tout le monde raconte — « l'IA a tué Stack Overflow » — n'est pas seulement incomplète. Elle est *confortable*. Elle nous dédouane. Et la vraie version est beaucoup plus inconfortable.

---

## Le graphique qui raconte une autre histoire

Le graphique de référence a été ressorti il y a quelque temps par Gergely Orosz dans The Pragmatic Engineer, à partir des données du Stack Overflow Data Explorer, et popularisé par Marc Gravell — qui est, accessoirement, un contributeur du top 10 de tous les temps du site. Il trace les questions par mois de 2009 à aujourd'hui, et il ne ressemble pas du tout à ce qu'on attendrait si l'histoire se résumait à « ChatGPT est arrivé et tout le monde est parti ».

La courbe grimpe au début des années 2010, atteint son pic vers 2014, puis — *deux années entières avant que ChatGPT n'existe* — elle commence à décliner. Lentement d'abord, puis plus vite. Il y a un rebond pandémique début 2020 (tout le monde coincé chez soi, télétravail, Google au lieu de demander aux collègues). Puis, à partir de juin 2020, le déclin s'accélère encore. Quand ChatGPT sort en novembre 2022, le patient se vide déjà de son sang. ChatGPT ne tue pas Stack Overflow. Il arrive à l'hôpital, dépasse l'infirmière de tri, et débranche la machine.

Donc le premier fait inconfortable est celui-ci : **Stack Overflow était en train de mourir depuis huit ans avant l'arrivée des LLM.** La chose que l'on pleure était déjà en phase terminale, et la plupart d'entre nous — moi compris, utilisateur quotidien pendant toute cette période — n'avons jamais pris le temps de le remarquer.

---

## La partie qu'on n'aime pas admettre : il s'est fait ça tout seul

Laissez-moi dire la chose qui vous vaut des downvotes dans tous les fils sur le sujet, mais que je crois simplement vraie : le déclin de Stack Overflow n'était pas un accident de l'histoire. C'était, en grande partie, un *choix*.

J'ai commencé à utiliser le site vers 2012, et je me souviens de la texture exacte de la culture qui l'a construit : poser une question, obtenir une réponse, upvoter les bonnes, et une sorte de fierté collective à rendre l'ensemble meilleur pour la personne suivante. Quelque part en chemin — et les données disent que c'était justement en 2014, l'année où le déclin a commencé — cette culture a tourné. Le site a déployé des outils de modération plus rapides et plus agressifs, et le ton du lieu est passé de « laisse-nous t'aider » à « laisse-nous te faire la police ».

Vous connaissez le rituel. Vous posez une question que vous ne parvenez sincèrement pas à résoudre après une heure de recherche, et en moins de quatre-vingt-dix secondes elle est **« marquée comme doublon »** — d'une question de 2011 tangente, dans une autre langue, sur une version du framework qui n'existe plus, dont la réponse acceptée est fausse. Ou elle est **« fermée comme hors-sujet »** parce qu'elle est « basée sur l'opinion », comme si l'immense majorité de ce dont les développeurs seniors ont réellement besoin n'était pas, à un certain niveau, un jugement de valeur. Ou vous recevez un commentaire de quelqu'un qui n'a manifestement pas lu la question, vous expliquant pourquoi votre question est mauvaise, sur le ton d'un employé de préfecture qui vous déteste.

Gergely Orosz l'a résumé en une phrase que je n'ai jamais réussi à me sortir de la tête : *« J'ai arrêté de poser des questions à cette époque parce que le site était devenu inhospitalier. »*

C'est tout, exactement ça. Un commun de la connaissance vit ou meurt selon que les gens qui *créent* la connaissance se sentent bien de le faire. Et Stack Overflow a passé une décennie à enseigner méthodiquement à ses contributeurs les plus précieux — ceux qui posent les questions difficiles et écrivent les bonnes réponses — que le prix de la participation était de se faire rabrouer par un inconnu. Ce n'est pas une « communauté ». C'est un bizutage avec une barre de recherche.

Je ne dis pas que toute la modération était mauvaise — une bonne partie a réellement empêché l'endroit de devenir une décharge à ciel ouvert. Mais on peut avoir raison sur chaque décision de modération individuelle et se tromper catastrophiquement sur la *culture* que ces décisions finissent par composer. Stack Overflow a optimisé pour la propreté et a perdu son âme en chemin.

---

## Puis la machine est arrivée, et elle était polie

Dans ce commun à moitié mort, en novembre 2022, ChatGPT a fait son entrée. Et voici le détail le plus cruel de toute l'histoire : **ChatGPT a été entraîné, en grande partie, sur les données de Stack Overflow.** Le corpus même de questions et de réponses que la communauté a passé quinze ans à construire — souvent gratuitement, souvent en s'entendant dire que sa question était un doublon — est devenu le matériau d'entraînement de la chose qui allait rendre cette communauté inutile.

Et ChatGPT a fait la seule chose que Stack Overflow avait cessé de faire : il répondait à *toutes* les questions, immédiatement, sans jugement, avec une patience infinie. Votre question est bête ? Il s'en fiche. C'est un doublon d'un truc de 2011 ? Il s'en fiche. Vous lui posez douze variantes d'affilée parce que les onze premières réponses ne fonctionnaient pas tout à fait ? Il ne vous dit jamais que vous lui faites perdre son temps.

Ce n'est pas que les réponses soient toujours justes. Elles ne le sont souvent pas. C'est que l'*expérience* est l'exact opposé de ce que Stack Overflow était devenu. Orosz encore : ChatGPT « est poli et répond à toutes les questions, contrairement aux modérateurs de Stack Overflow ». On ne bat pas une institution sociale avec de meilleures réponses. On la bat avec un meilleur *sentiment*. Et le sentiment de demander de l'aide à une IA, c'est « soulagement ». Le sentiment de demander de l'aide à Stack Overflow, à la fin des années 2010, c'était « appréhension ».

Quand ce sont vos deux options, le déclin cesse d'être un mystère.

---

## Le parasite qui a mangé son hôte

Voici la partie de tout ça qui, je crois, comptera plus que tous les compteurs d'upvotes, et que je ne vois presque jamais personne articuler clairement.

La valeur de Stack Overflow n'a jamais été seulement les *réponses*. C'était la **boucle**. Quelqu'un rencontre un problème, demande en public, un inconnu le corrige, il corrige à son tour, un expert ajoute une nuance que personne d'autre ne connaissait, et le résultat — la réponse finale, éditée, validée par la communauté — est *meilleur que ce qu'aucune des personnes impliquées n'aurait pu produire seule*. Cette boucle, répétée des dizaines de millions de fois, c'est ce qui a construit le corpus. Les réponses sont le pot d'échappement. La boucle, c'est le moteur.

ChatGPT a consommé le pot d'échappement et l'a reproduit à la demande. Et ce faisant, il a arrêté le moteur. Plus personne ne pose de nouvelles questions, donc plus aucune nouvelle réponse ne s'écrit, donc plus aucune nouvelle nuance ne se capture, donc le corpus — les données d'entraînement — cesse de croître. La machine tourne désormais sur un instantané figé d'un commun qui ne produit plus. C'est le parasite parfait : il a dévoré l'hôte, et il l'a fait en utilisant la générosité de l'hôte, tout en étant, à chaque instant, *plus agréable à fréquenter que l'hôte ne l'avait jamais été.*

Et c'est le détail qui devrait réellement inquiéter tout le monde, pas seulement les nostalgiques d'un site web : **les réponses existent encore, mais la chose qui les créait a disparu.** Nous vivons sur le capital d'une civilisation du partage de connaissance à laquelle nous avons déjà cessé de contribuer. Le jour où l'instantané n'aura plus de réponse à un problème — un problème réellement nouveau, dans un framework réellement nouveau, avec des modes de défaillance réellement nouveaux — il n'y aura plus aucun mécanisme public pour générer la réponse. Il n'y aura plus qu'un modèle qui hallucine avec assurance quelque chose qui sonne juste.

---

## Ce qu'on a vraiment perdu

Je continue d'essayer de mettre le doigt sur ce qui fait que ça ressemble à une perte plutôt qu'à une simple migration, et je crois que c'est ça : Stack Overflow était l'un des derniers endroits d'internet où un parfait inconnu vous aidait à résoudre un problème difficile *pour le bien du dossier public.*

Pas pour l'argent. Pas pour la gloire (enfin, un peu). Pas parce que vous étiez son collègue, son ami ou son client. Juste parce que vous demandiez, en public, et que vous répondre rendait internet légèrement meilleur pour la prochaine personne qui aurait le même problème. C'est une chose stupéfiante quand on y pense vraiment — des millions de personnes, donnant leur expertise à une ressource partagée que n'importe qui pouvait utiliser. C'était un vrai commun. L'un des rares que l'industrie du logiciel ait jamais construit.

Ce qui le remplace, dans la pratique, est pire sur presque tous les axes qui comptent à long terme. La connaissance migre vers des endroits qui ne sont **ni publics, ni indexables, ni permanents** : des serveurs Discord privés, des groupes WhatsApp et Telegram, des canaux Slack d'entreprise, et la mémoire d'un modèle dont on ne peut pas inspecter les poids ni auditer les données d'entraînement. Un développeur dans dix ans ne pourra plus chercher « pourquoi cette erreur Kubernetes » et trouver la réponse, librement, sur une page qui renvoie à la discussion qui l'a produite. Il obtiendra une réponse d'une boîte noire, sans aucun moyen de savoir si elle est juste, sans voir le raisonnement, et sans pouvoir y apporter sa propre correction durement acquise.

Ce n'est pas du progrès. C'est l'enclosure du commun, et nous nous la faisons à nous-mêmes, joyeusement, parce que la boîte noire est plus gentille avec nous que le commun ne l'a jamais été.

---

## Ce que j'en pense vraiment

Je vais être honnête sur ma position, parce que j'ai lu quarante commentaires cette semaine qui soutiennent que c'est une non-histoire, et je pense qu'ils ont tort d'une manière précise et révélatrice.

Le déclin de Stack Overflow n'est pas une tragédie parce qu'un site web est devenu moins populaire. Les sites meurent tout le temps. C'est une tragédie parce que c'est le signal le plus clair que nous ayons reçu sur ce que l'ère des LLM fait *réellement* à l'économie de la connaissance partagée : elle convertit un bien public en produit privé, et elle le fait si doucement que les gens convertis ne remarquent pas qu'ils fournissent la matière première.

La vérité inconfortable autour de laquelle je tourne sans cesse, c'est que les deux parties sont responsables, et prétendre le contraire est le mensonge confortable que j'évoquais au début. Stack Overflow a fait fuir les gens qui le rendaient précieux — ça, c'est la faute du site, et ça a commencé des années avant la moindre IA. Et l'IA a ensuite mangé l'archive que ces gens avaient déjà construite — ça, c'est la faute du reste d'entre nous, qui avons supposé que l'archive serait toujours là pour nourrir une machine qui ne rend rien.

Je ne sais pas quelle est la solution. Peut-être qu'il n'y en a pas — peut-être que l'ère du commun de connaissance public et indexable est tout simplement terminée, et que le futur, ce sont des silos privés et des hallucinations confiantes, et qu'on s'en sortira tous parce que les *réponses* fonctionnent à peu près. Mais je sais une chose : quand je tombe sur un problème réellement dur aujourd'hui, je ne demande plus à Stack Overflow, et je ne crois pas que vous le fassiez non plus. Je le colle dans une fenêtre de chat, j'obtiens quelque chose de presque correct, et je ne me demande jamais une seule seconde que la chose à qui je parle a appris tout ce qu'elle sait de gens qui me répondaient autrefois gratuitement, en public, pour le dossier.

C'est cette partie que je n'arrête pas de ruminer. Pas que le site soit en train de mourir. C'est qu'on regarde le commun se faire démonter, et que le seul sentiment que la plupart d'entre nous en tire, c'est le soulagement.
