---
title: "Firefox est désormais le dernier grand navigateur à faire tourner uBlock Origin — et ça devrait faire peur à tout le monde"
description: "Chrome et Edge ont fini d'éliminer Manifest V2, ce qui signifie que le dernier navigateur capable de faire tourner nativement uBlock Origin est celui qui a 3 % de parts de marché, financé presque entièrement par Google. L'histoire la plus silencieuse de la semaine est en réalité l'avertissement le plus fort qu'on ait reçu sur qui possède le web."
date: 2026-08-15
translationKey: "2026-08-15-firefox-ublock-manifest-v2"
image: "/images/2026-08-15-firefox-ublock-manifest-v2.svg"
tags:
  - "firefox"
  - "chrome"
  - "navigateurs"
  - "vie-privee"
  - "adblock"
  - "manifest-v3"
  - "gouvernance"
mathjax: false
---

## Le post Bluesky qui a clos une époque

Quelque part au milieu de la semaine, Mozilla a posté sur Bluesky une phrase qui aurait été totalement anodine il y a dix ans et qui est devenue, on ne sait trop comment, une déclaration politique :

*« Notre soutien à uBlock Origin ne va nulle part. »*

Elle est tombée la même semaine où Chrome 150 a discrètement retiré les derniers drapeaux qui permettaient de conserver uBlock Origin, et où Chrome 151 a commencé à fermer les dernières portes de sortie. Microsoft, qui a passé des années à se présenter comme l'alternative à Chrome respectueuse de la vie privée, avait déjà rejoint Google en annonçant la fin du support de Manifest V2. Et c'est ainsi, avec l'efficacité feutrée d'un drapeau supprimé d'une base de code, que tout le public des navigateurs s'est retrouvé dans un monde nouveau et étrange : **il ne reste désormais qu'un seul grand navigateur capable de faire tourner le bloqueur de pub le plus populaire de la planète — et il a environ 3 % de parts de marché.**

Le fil r/technology sur le sujet a dépassé les dix mille cinq cents upvotes avant que j'arrête de le regarder grimper. Les commentaires les plus votés mêlaient le deuil, l'humour noir et des gens qui demandaient — sincèrement — s'il était temps de revenir à Firefox. Comme si Firefox était un pays qu'ils avaient quitté il y a des années et dont ils consultaient maintenant, à contrecœur, le prix des billets.

J'ai envie de prendre ça au sérieux, parce que je pense que c'est l'histoire la plus importante autour des navigateurs depuis une décennie, et que la plupart de la couverture la traite comme une querelle de niche entre geeks. Ce n'en est pas une. C'est le moment où le système immunitaire du web ouvert a été retiré, et presque personne ne l'a remarqué parce que le retrait était ennuyeux.

---

## Ce que Manifest V3 a réellement fait

Laissez-moi revenir en arrière, parce que toute cette histoire n'a de sens que si l'on comprend ce que « Manifest V3 » fait techniquement. La plupart de la presse le résume à « Google a changé le fonctionnement des extensions », ce qui est vrai au sens où « le médecin a changé le fonctionnement de vos organes » est vrai.

Les extensions de navigateur sont régies par un « manifest » — un fichier JSON qui déclare ce qu'une extension a le droit de faire. La version 2 de ce manifest, sur laquelle tout l'écosystème a tourné pendant des années, exposait une API appelée `webRequest`. Elle permettait à une extension d'*intercepter et d'inspecter* chaque requête réseau d'une page et — surtout — de **bloquer** ces requêtes avant qu'elles ne partent. C'est le mécanisme sur lequel repose uBlock Origin. Quand vous chargez une page, uBlock Origin voit la requête vers `tracker.js`, la compare à une liste de millions de règles et l'annule au niveau du réseau, avant qu'un seul octet du traceur n'atteigne votre machine.

Manifest V3 a supprimé ça. À la place, Google a livré ce qu'on appelle `declarativeNetRequest` — une API où c'est le navigateur lui-même, et non l'extension, qui fait le blocage, en utilisant des règles que l'extension doit déclarer *à l'avance*. Et il y a un plafond strict sur le nombre de règles déclarables. Les premières versions plafonnaient autour de 30 000 ; ça a grimpé depuis, mais le point demeure : un bloqueur de pub qui doit matcher des millions de règles de filtrage, faire du pattern matching dynamique et injecter des correctifs cosmétiques à la volée **ne rentre tout simplement pas** dans ce modèle.

Le résultat, c'est ce que Google appelle « uBlock Origin Lite » — une version conforme MV3, délibérément affaiblie, dont l'auteur, Raymond Hill, n'a jamais fait mystère. C'est uBlock Origin les mains attachées dans le dos. Il bloque moins, il ne peut pas mettre à jour ses listes de règles librement, et il fait confiance au navigateur — c'est-à-dire à la régie publicitaire — pour bloquer fidèlement à sa place.

Voici la partie sur laquelle je veux que vous vous attardiez : l'entreprise qui tire l'écrasante majorité de ses revenus de la publicité a passé des années à concevoir une architecture technique dont l'effet explicite est de rendre structurellement impossible, pour un tiers, de bloquer la publicité à pleine puissance. Google n'allait jamais dire « on interdit les bloqueurs de pub » — ce serait un désastre d'image. Alors ils ont reconstruit la plateforme pour que les bloqueurs *existent encore techniquement* mais ne puissent plus faire leur travail. C'est le même manuel que « on ne retire pas la prise jack, on retire juste la raison pour laquelle vous en vouliez une. »

---

## Pourquoi « monopole Chromium » a cessé d'être un argument de tribune

Il y a une raison pour laquelle l'abandon de MV2 par Chrome se répercute sur tout. Le moteur de rendu et la plateforme d'extensions de Chrome — Chromium — propulsent désormais Chrome, Edge, Brave, Opera, Vivaldi, Arc et à peu près tous les navigateurs dont vous avez entendu parler, à l'exception de Firefox et Safari. À l'instant où Google décide de la direction de la plateforme, ce n'est plus une décision de Google ; c'est une décision *d'industrie* que Microsoft, Opera et les autres doivent ensuite implémenter, qu'ils le veuillent ou non.

C'est exactement ce qui s'est passé avec Edge. La position de Microsoft sur uBlock Origin a toujours été, historiquement, discrètement correcte. Mais Edge, c'est Chromium. Google a retiré MV2 de Chromium, et Microsoft — qui pourrait en théorie maintenir un fork — a choisi de ne pas le faire. Pourquoi le ferait-il ? Forker la plateforme de navigateur la plus complexe du monde pour préserver une API que sa propre régie publicitaire n'aime pas, c'est un marché perdant. Alors le « choix » offert par le marché des navigateurs s'est réduit d'une option, gratuitement, sans que personne ne vote.

C'est ça qui me fait vraiment peur, et ce n'est pas vraiment une histoire de pubs. C'est que la *neutralité* du web — cette propriété qui en a fait le web et non une collection de jardins clos — n'a jamais été garantie par la loi ni par la bonne volonté. Elle était garantie par le fait qu'il existait plusieurs moteurs de navigateur indépendants, et qu'aucune entreprise ne pouvait décider unilatéralement de ce qu'une page web avait le droit de faire. Nous sommes désormais réduits à, en gros, deux moteurs et demi : Chromium, WebKit (Safari) et Gecko (Firefox). Et le modèle d'extensions de WebKit est si différent et si contraint que, dans la pratique, **Firefox est le seul navigateur grand public où un développeur indépendant peut encore écrire un logiciel qui voit et contrôle ce que le navigateur fait de vos données.**

Ce n'est pas un classement de fonctionnalités. C'est un monopole structurel, et MV3 n'en est que le premier symptôme visible.

---

## L'astérisque Brave

Soyons justes, parce qu'il y a une objection qui circule dans tous les fils sur le sujet, et elle mérite une réponse honnête. Brave fait toujours tourner uBlock Origin. En quelque sorte.

Brave, c'est Chromium, mais Brave est aussi une entreprise dont toute la marque repose sur le blocage de pub — son propre bloqueur est intégré au navigateur plutôt que livré en extension. Brave a donc un intérêt puissant à garder MV2 en vie, et il le fait en patchant Chromium pour laisser les anciennes API en place. Le hic, c'est que c'est un *contournement*, pas une garantie. Ça dépend de la volonté de Brave de maintenir indéfiniment un fork de Chromium de plus en plus divergent, à contre-courant de chaque changement amont de Google. C'est le même calcul que Microsoft, avec un autre compte de résultat — et le jour où Brave décidera que le coût de maintenance n'en vaut pas la peine, le contournement s'évaporera et aucun utilisateur n'y pourra rien.

Comparez ça à Firefox, où le support d'uBlock Origin est une *politique affichée*, adossée à une vraie décision de faire coexister MV2 et MV3, et à l'identité même du produit Mozilla. L'une est un engagement. L'autre est un patch. Ce n'est pas la même chose, et les gens qui répondent négligemment « utilisez Brave » recommandent un pont qui pourrait être démonté dès le trimestre prochain, sans préavis.

---

## L'ironie qui devrait faire la une

Et maintenant la partie de l'histoire qui me donne toujours un léger haut-le-cœur, et que je n'ai jamais vue mentionnée dans un média grand public.

Mozilla — la fondation, le dernier navigateur indépendant, les gens qui se dressent désormais entre vous et un web où chaque page est une pub de surveillance — est financée, à hauteur d'environ **80 % de ses revenus**, par un accord qui fait de Google le moteur de recherche par défaut de Firefox. Google verse des centaines de millions de dollars par an à Mozilla pour ce privilège. L'entreprise qui *démantèle activement* l'infrastructure de blocage de pub du web ouvert est, simultanément, le plus gros donateur qui maintient en vie le dernier navigateur bloqueur de pub.

Les deux parties sont piégées dans cet arrangement. Google continue de payer parce que, ironie du sort, il a *besoin* que Firefox existe — un navigateur à 3 % de parts de marché est une feuille de vigne bon marché pour tous les arguments antitrust qui prétendent que Google est un monopole. Et Mozilla continue d'accepter l'argent parce que, sans lui, Firefox ne survit pas. Donc le navigateur qui défend le web ouvert dépend financièrement de l'entreprise qui attaque le web ouvert, et l'entreprise qui attaque le web ouvert dépend financièrement du navigateur qui le défend, pour pouvoir plaider devant un tribunal que le web ouvert se porte très bien.

Si vous pouvez lire ce paragraphe sans que votre foi dans le « le marché va régler ça » en prenne un coup, je ne sais honnêtement pas quoi vous dire. C'est le genre de nœud qu'aucun techno-optimisme ne défait. Ce n'est pas un bug du système. C'est *le* système.

---

## Ce qu'on perd vraiment

Je reviens sans cesse à une question qu'il est facile d'écarter comme du mélodrame mais qui est, je crois, le vrai cœur du sujet : qu'est-ce que ça veut dire, concrètement, de perdre la capacité de dire non à une page web ?

Un bloqueur de pub, ce n'est pas vraiment une histoire de pubs. C'est une histoire de **consentement**. Le web moderne est une machine qui, par défaut, exécute du code arbitraire sur votre machine, envoie votre comportement à des dizaines de tiers dont vous n'avez jamais entendu parler, et affiche du contenu dont le but premier est de manipuler votre attention. uBlock Origin est le seul outil qui vous permet de dire « non » à tout ça, en bloc, avec une seule installation, gratuitement, pour toujours. Ce n'est pas un accessoire. C'est la dernière pédale de frein restante sur un véhicule qui préférerait nettement que vous n'en ayez pas.

Et voilà la chose que personne ne veut admettre : **les gens qui ont le plus besoin de ce frein ne sont pas ceux qui lisent r/technology.** Ce sont mes parents. C'est la personne qui clique sur le bouton « télécharger » qui est en réalité trois boutons de téléchargement. Ce sont tous ceux qui ne savent pas ce qu'est un « manifest » et qui ne devraient pas avoir à le savoir. Les utilisateurs techniques qui migreront vers Firefox et s'en sortiront très bien sont une minorité. La majorité vient d'être silencieusement déplacée, par une dépréciation d'API dont ils n'entendront jamais parler, vers un web où les pubs et les traceurs sont désormais *hors de portée de tout blocage*. Ils n'ont pas consenti à ça. On ne leur a simplement jamais demandé.

---

## Ce que j'en pense vraiment

Je crois que le bilan honnête est inconfortable, alors je vais le dire simplement : **le web ouvert est en train d'être démantelé par son propre gardien, et sa dernière ligne de défense est un navigateur maintenu en vie par l'entreprise qui fait le démantèlement.** Tout le reste — les posts Bluesky, les drapeaux Chrome, les contournements de Brave — n'est que du bruit par-dessus cette unique phrase.

Que faire, concrètement ? La seule chose qui ait jamais réellement compté dans ce combat : faire un choix qui a un effet *structurel*, pas cosmétique. Passer à Firefox, ce n'est pas obtenir un meilleur navigateur — sur certains benchmarks et certains sites, il sera plus lent, et oui, certains sites vous reprocheront un « navigateur non supporté ». C'est se faire compter comme utilisateur du seul moteur qui n'appartient pas à une régie publicitaire. La part de marché est le seul langage que tout ça respecte, et les 3 % de Firefox sont l'unique raison pour laquelle cette histoire a un méchant au lieu d'une conclusion écrite d'avance.

Mais je veux être lucide sur la limite de ce conseil. Vous dire de passer à Firefox, c'est un pansement sur un problème structurel, et je mentirais en le présentant autrement. La vraie solution — celle dont personne dans la tech ne veut parler — c'est que nous avons laissé l'infrastructure fondatrice de la communication humaine devenir la propriété privée d'une seule entreprise publicitaire, et aucune fidélité à un navigateur ne va défaire ce qui est fait. MV3 n'a pas créé ce problème. Il l'a juste rendu impossible à ignorer.

Le post Bluesky dit vrai. Le soutien de Firefox à uBlock Origin ne va nulle part. Ce qui s'en va, c'est tout le reste — les alternatives, le rapport de force, l'illusion que nous avions un jour eu le choix dans tout ça. Et le plus effrayant n'est pas que ça arrive. C'est que ça arrive si doucement que la plupart des gens ne comprendront ce qu'ils ont perdu que le jour où une page refusera de se charger sans ses traceurs, et qu'il n'y aura plus aucun bouton à presser.
