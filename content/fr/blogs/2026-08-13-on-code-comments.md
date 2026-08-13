---
title: "On comments — l'information que seul un commentaire peut porter, et pourquoi les agents IA en ont le plus besoin"
description: "Jon Gjengset (Helsing.ai) a écrit la taxonomie définitive des commentaires qui méritent vraiment leur place — TODOs, arguments de correction, choix porteurs, Y-Statements. À l'ère des LLM, le commentaire n'est plus un luxe : c'est la fenêtre de contexte que vos agents lisent."
date: 2026-08-13
translationKey: "2026-08-13-on-code-comments"
image: "/images/2026-08-13-on-code-comments.svg"
tags:
  - "code-comments"
  - "documentation"
  - "craft"
  - "llm"
  - "adr"
mathjax: false
---

## Orange vif, exprès

Il y a un essai qui circule sur r/programming cette semaine, et il s'ouvre sur le genre de détail personnel qui vous dit que l'auteur le pense vraiment. Jon Gjengset — mieux connu dans les milieux Rust sous le nom de jonhoo, la personne derrière des crates comme `haphazard`, `evmap` et `async-ssh` — configure depuis une décennie son colorateur syntaxique pour que **les commentaires de code soient orange vif** au lieu du gris délavé standard.

Pas un accident. Un mécanisme de contrainte. Il voulait s'améliorer dans la documentation du code, et la meilleure façon d'y arriver était de rendre les commentaires si criards que tout bruit superflu devienne *douloureusement visible*.

J'adore ça, parce que ça recadre tout le débat. L'industrie a passé des années à traiter le commentaire comme un embarras — quelque chose dont on est censé se débarrasser en grandissant, la preuve qu'on n'a pas su écrire du « code auto-documenté ». Gjengset les rend orange vif et fixe le bruit jusqu'à ce que ça fasse mal. C'est exactement l'énergie que je veux dans un billet sur la documentation.

L'essai s'appelle [« On comments »](https://blog.helsing.ai/posts/on-comments/), il est sur le blog de Helsing.ai, et je pense que c'est la chose la plus utile écrite sur les commentaires de code depuis des années. Pas parce que c'est polémique, mais parce que c'est *précis* — il refuse les deux dogmes à la fois.

---

## Les deux dogmes qui ratent tous les deux la cible

Il y a deux camps sur les commentaires, et Gjengset les balaie dans le même souffle.

Le premier, c'est celui qu'on a tous vécu : le commentaire comme bruit. Le `// incrémente i` au-dessus de `i++`, le paragraphe qui explique l'évidence, le commentaire périmé de trois refactors qui ment désormais activement et vous fait douter de votre propre lecture du code. On en a tous écrit, on s'est tous brûlés avec. La réaction était prévisible : *écrivez moins de commentaires, écrivez du code plus lisible.*

Le second camp est plus subtil, et c'est celui vers lequel dérivent les ingénieurs seniors. Rendre le code lisible en le découpant, en utilisant des noms descriptifs, en s'appuyant sur le système de types. Le point de Gjengset, c'est que ces techniques sont toutes bonnes — et qu'elles ne *remplacent pas* les commentaires. Elles rendent le **comment** plus clair. Elles ne font presque rien pour le **pourquoi**.

C'est la phrase que je veux encadrer au mur : **« L'information qui n'a jamais été écrite ne peut pas être retrouvée en lisant plus fort. »**

Le code lisible vous dit ce qu'il fait et comment. Il ne peut pas vous dire *pourquoi l'alternative évidente ne marche pas*, ni *pourquoi cette constante vaut 1492 et pas 1500*, ni *pourquoi cette fonction ne doit jamais être exportée.* Cette information n'existe que dans un commentaire, ou elle n'existe pas du tout.

---

## La documentation est pour les consommateurs. Les commentaires pour les collaborateurs.

Tôt dans l'essai, il y a une distinction qui recadre tout : Gjengset parle des *commentaires de code*, pas de la documentation embarquée comme les doc-comments `///` de Rust. « La documentation est écrite pour vos consommateurs. Les commentaires sont écrits pour vos collaborateurs. »

C'est la séparation la plus propre que j'aie jamais vue pour cet argument. Les `///` ont un public défini — quelqu'un qui utilise votre API de l'extérieur, et qui ne lira jamais votre source. Les `//` classiques ont un autre public : la personne qui doit *modifier* le code. Leurs besoins sont entièrement différents. Les confondre, c'est comme ça qu'on se retrouve avec un crate plein de doc-comments léchées et pas une seule note sur pourquoi le bout compliqué est compliqué.

Ensuite il fait la chose qui rend l'essai réellement utile : il donne une taxonomie. Pas « écrivez de bons commentaires » en tant que vibe, mais des *catégories précises* de commentaires qui valent presque toujours la peine. Laissez-moi les parcourir, parce que chacune correspond à un échec réel que j'ai personnellement commis.

---

## La taxonomie : neuf commentaires qui méritent leur place

**Les TODOs.** Vous savez que le code n'est pas fini et c'est ok. La règle n'est pas « écrivez des TODOs » — c'est *écrivez assez d'information pour que le TODO soit utilisable six mois plus tard.* Quand tout est frais, vous griffonnez deux mots-clés. Quand un ingénieur junior le trouve après votre départ, ces deux mots ne servent à rien. Les TODOs multi-lignes sont très bien. Les ébauches de résolution aussi. Le seul TODO impardonnable, c'est celui qui est un mystère pour son futur lecteur.

**Les références.** Le code qui a un lien fort avec une source externe — une copie légèrement modifiée d'un code ailleurs, un encodage d'un algorithme issu d'un papier ou d'un livre — mérite que ce lien soit écrit. Et ça doit être un **permalink**, pas un lien vers `main` qui pourrira. Appuyez sur `y` sur GitHub avant de copier l'URL. Documentez aussi toute divergence par rapport à la référence, et *pourquoi* vous avez divergé.

**Les arguments de correction.** Des preuves, informelles ou formelles, qu'un code non trivial fait bien la bonne chose. Les tests vérifient le *quoi*, le code montre le *comment*, mais le raisonnement sur *pourquoi ces étapes mènent de façon fiable à ce résultat* ne vit que dans un commentaire. Gjengset couple ça avec des assertions comme `unreachable!` quand c'est possible. Et il lâche un pro-tip glorieusement honnête : *ne vous arrêtez pas au milieu d'un argument de correction quand vous réalisez qu'il ne tient pas, pour commiter la preuve partielle.* Il renvoie vers un vrai commit où il a fait exactement ça.

**Les leçons durement apprises.** La règle est d'une simplicité magnifique : si vous avez passé plus de trente minutes à faire marcher quelque chose, et que le correctif était une incantation brève et peu intuitive, *commentez-la.* Vous n'aviez pas réalisé qu'elle était nécessaire il y a trente minutes — donc la personne suivante ne le réalisera pas non plus. Même si vous ne savez pas *pourquoi* elle est nécessaire, documentez comment vous y êtes arrivé et ce qui casse sans elle.

**La raison des constantes.** On a tous vu `max_packet_size = 1492` sans savoir si ce nombre était porteur ou arbitraire. Un commentaire devrait dire ce que la constante représente, comment elle a été choisie, et les conséquences de la changer. Et — c'est ma phrase préférée de tout l'essai — *ce n'est pas grave de dire qu'une valeur a été choisie à peu près au hasard.* C'est quand même une information dont le prochain a désespérément besoin.

**Les choix porteurs.** Quand la correction dépend d'un détail apparemment anodin ailleurs — « on doit collecter dans un BTreeSet ici car le code en dessous suppose que l'itération est ordonnée » — ce détail a besoin d'un commentaire, sinon un innocent refactor futur le remplacera par un HashSet et cassera silencieusement la prod.

**Les plans d'algorithme.** Quand un algorithme simple se perd dans la syntaxe, un plan de haut niveau — en tête ou entremêlé — dit au lecteur dans quelle « partie » il se trouve. Une forme légère de programmation lettrée.

**Les « pourquoi pas ».** Quand le code *viole délibérément* la convention — délaisse le type ou le helper évident — écrivez pourquoi. Sinon tout le monde réapprend à la dure. C'est le corollaire des leçons durement apprises : pas « pourquoi cette ligne est nécessaire », mais « pourquoi tu ne l'as pas fait de la façon évidente ».

**Les compromis intentionnels.** Quand vous évaluez des options et en choisissez une, documentez la décision. Sinon la discussion se rejoue tous les quelques années. C'est là que Gjengset introduit quelque chose de vraiment nouveau.

---

## Les Y-Statements : l'ADR qui vit à côté du code

Les compromis sont traditionnellement capturés dans des Architecture Decision Records — ce qui est très bien, sauf que les ADR vivent *séparément du code*, se font oublier, et deviennent obsolètes. La tentative de Gjengset pour concilier les deux est un format plus strict et plus concis, écrit *directement dans des commentaires à côté du code qu'ils justifient*, en utilisant des Y-Statements :

> Dans le contexte de `<cas d'usage>`, confrontés à `<préoccupation>` nous avons décidé `<option>` pour atteindre `<qualité>`, en acceptant `<inconvénient>`.

Le format est délibérément strict pour que chaque partie soit considérée à chaque fois. On ne peut pas sauter un champ, et on est censé écrire des *fragments concis*, pas des paragraphes.

Et voici la partie qui m'a fait sourire : Helsing a publié en open source un petit outil pour aller avec. [`yadr`](https://github.com/helsing-ai/yadr) parcourt un arbre source et liste chaque décision, vous laisse en imprimer une en entier, et — en CI — *vérifie que tous les Y-Statements suivent la convention, jusqu'à la ponctuation.* Un linter pour le raisonnement architectural. Je trouve ça absurde et merveilleux à parts égales.

---

## Ce que les LLM ont changé : tout

C'est ici que l'essai devient une lecture essentielle pour 2026, plutôt que simplement un bon conseil.

L'argument final de Gjengset, c'est que le développement assisté par LLM ne réduit pas l'importance des commentaires — il l'*inverse*. Un agent de codage arrive sur votre fichier avec zéro connaissance de la raison pour laquelle cette boucle de retry est là, de ce qui s'est passé dans la discussion de design, ni des choix délibérés par opposition aux choix arbitraires. C'est « un nouvel ingénieur rapide et confiant (avec une énergie infinie), mais qui ne sait rien de pourquoi votre base de code est ce qu'elle est. » Ce qui en fait *exactement* le public cible des commentaires qui transmettent de l'information au-delà du code.

Il y a deux raisons pour lesquelles ça compte. La première, c'est que les commentaires sont injectés directement dans la fenêtre de contexte de l'agent — il n'a pas besoin de se rendre compte qu'il existe un ADR ailleurs, ni de tomber sur la bonne description de MR ou le bon ticket Jira. Le commentaire est *juste là*, dans le fichier qu'il édite.

La seconde, c'est l'heuristique qui clôt l'essai, et je pense qu'elle va devenir une pratique standard : **repérez les corrections que vous devez répéter sans cesse à vos agents, et intégrez-les comme des commentaires stratégiquement placés au lieu de strophes supplémentaires dans votre `AGENTS.md`.** Les corrections que vous répétez en boucle sont précisément la connaissance du « pourquoi » que le code ne peut pas exprimer. L'écrire une fois, dans le fichier, c'est la version durable de ce que vous n'arrêtez pas de devoir dire.

---

## Trois règles d'écriture pour les commentaires

Gjengset termine la partie artisanale par trois règles pour écrire les commentaires *comme de la rédaction technique*, et elles méritent d'être répétées quasi textuellement, parce que c'est la différence entre un commentaire qui aide et un qui ne sert à rien :

**Les octets ne coûtent rien.** Ne soyez pas laconique. Utilisez des phrases complètes et la ponctuation. La version fatiguée, sous-caféinée et pressée de vous-même d'il y a deux ans vous remerciera.

**Pensez au lecteur.** Supprimez vos propres connaissances et relisez le commentaire. Les mots « évidemment », « trivialement », « bien sûr » et « il suffit de » sont des drapeaux rouges que vous supposez un contexte que le lecteur n'a pas.

**La précision compte.** Les fautes de frappe, la ponctuation ambiguë et les références invalides à des variables ou des fonctions peuvent mener le lecteur à de mauvaises conclusions. Relisez vos commentaires une fois de plus.

Et puis la note de clôture, réellement mature, que la plupart des essais sur ce sujet ne prendraient jamais la peine d'inclure : **n'exigez pas la perfection des commentaires.** Les développeurs ont un nombre limité de cuillères. Parfois, livrer du bon code avec des commentaires moyens est la bonne décision, et améliorer les commentaires est une tâche séparée — idéalement faite par quelqu'un d'autre, qui n'a pas la connaissance implicite de l'auteur et écrira donc plus clair. Il n'y a pas de ratio correct de commentaires par rapport au code. Bien commenter, c'est du jugement, de l'empathie et de la prévoyance — pas une tâche mécanique.

---

## Ce que j'en pense vraiment

J'ai été la personne qui n'écrivait pas de commentaires parce que le code était « évident ». J'ai été la personne qui écrivait un `// TODO: réparer ça` sans pouvoir se souvenir, un mois plus tard, de ce que « ça » était. J'ai été la personne qui supprimait un commentaire « périmé » pour redécouvrir, douloureusement, qu'il était porteur. L'essai se lit comme écrit par quelqu'un qui a été ces trois personnes aussi, et c'est pour ça qu'il touche juste.

Mais la raison pour laquelle je pense que cet essai compte *maintenant*, précisément, c'est la section sur les LLM. On est tous en train de comprendre comment travailler avec des agents qui ont une énergie infinie et zéro contexte. On est tous en train de découvrir que le goulot d'étranglement n'est pas l'intelligence du modèle — c'est le *contexte qu'on lui passe*. Et la façon la moins chère et la plus durable de passer du contexte à un agent, c'est la chose qu'on a négligée pendant une décennie : un commentaire, dans le fichier, qui explique le pourquoi.

L'industrie a passé des années à essayer de rendre les commentaires obsolètes par la lisibilité, le nommage et les types. Il s'avère qu'ils n'ont jamais été optionnels. Ils attendaient juste un lecteur qui les apprécierait vraiment — et ce lecteur s'avère être une machine infatigable, sans contexte et un peu trop confiante, qui supprimera volontiers votre `BTreeSet` porteur à moins que vous n'ayez écrit pourquoi il était là.

L'orange vif, en fin de compte, était en avance sur son temps.

---

*« On comments » est de Jon Gjengset, publié sur le [blog de Helsing.ai](https://blog.helsing.ai/posts/on-comments/). L'outil compagnon, `yadr`, est open source sur [github.com/helsing-ai/yadr](https://github.com/helsing-ai/yadr).*
