---
title: "Nine PBS a perdu 50 To et 70 ans d'histoire — et le cloud n'est pas le coupable que vous croyez"
description: "Une chaîne de télé publique a fait confiance à un fournisseur mourant, à un datacenter et à un contrat. Aujourd'hui, 70 ans d'archives dépendent d'un procès contre Iron Mountain. La leçon n'est pas « n'utilisez jamais le cloud » — c'est que la garde et la propriété ne sont pas la même chose."
date: 2026-08-14
translationKey: "2026-08-14-pbs-cloud-archive-loss"
image: "/images/2026-08-14-pbs-cloud-archive-loss.svg"
tags:
  - "cloud"
  - "backup"
  - "données"
  - "archivage"
  - "infrastructure"
mathjax: false
---

## Une histoire que Kafka aurait rejetée comme trop invraisemblable

Laissez-moi vous raconter ce qui est arrivé à Nine PBS, la chaîne de télévision publique de St. Louis, parce qu'à chaque fois que je relis la chronologie, j'y trouve une nouvelle couche d'absurdité.

La chaîne stockait **50 téraoctets** de matériel d'archive — environ **70 ans de programmes** — chez un fournisseur de stockage cloud appelé Open Source Storage (OSS), avec qui elle travaillait depuis 2019. En mars de cette année, le contrat arrivait à échéance. OSS n'a jamais répondu, puis a brutalement coupé l'accès de la chaîne à ses propres données, alors même qu'il restait 30 jours pour les récupérer.

Alors Nine PBS a creusé. Le site du fournisseur avait disparu. L'entreprise était listée comme *delinquente* auprès du secrétariat d'État du Colorado. Et il s'est avéré qu'OSS ne stockait même pas les données lui-même — elles reposaient sur l'infrastructure d'**Iron Mountain**.

Ce qui suit est un enchaînement qui ressemble à un scénario : une mise en demeure à Iron Mountain qui reste sans réponse. Un procès contre OSS, suspendu quand un certain James Tramel — se présentant comme managing partner du groupe qui avait « officiellement acquis » l'entreprise — prend contact, communique pendant un mois, puis disparaît, avant d'expliquer à la chaîne qu'il avait « été escroqué » en rachetant OSS et que la propriété était revenue aux anciens propriétaires. Un tribunal statue que Nine PBS a tous les droits sur ses données. Iron Mountain finit par admettre détenir les données, accepte de les restituer… puis fait marche arrière, en prétendant qu'OSS les possède. Aujourd'hui, Nine PBS poursuit aussi Iron Mountain, juste pour empêcher que les données soient supprimées.

Soixante-dix ans d'histoire d'une institution publique, pris en otage entre une entreprise morte et un datacenter qui refuse de les libérer sans ordonnance du tribunal, parce qu'il *a peur d'être poursuivi par l'entreprise morte.*

## La conclusion facile est fausse, et je veux le dire d'emblée

La réaction d'internet à cette histoire — et j'ai lu les fils Reddit — est un chœur satisfait de « le cloud, c'est l'ordinateur de quelqu'un d'autre », de « si tu ne possèdes pas le stockage, tu ne possèdes pas les données », et de « à quoi ils s'attendaient ? »

Cette conclusion est paresseuse, et elle est *fausse* d'une manière précise sur laquelle je veux être exact.

Ce n'est pas une histoire de défaillance du cloud. Les données ne sont pas corrompues. Elles ne sont pas chiffrées par un ransomware. Elles ne sont pas perdues à cause d'un bit-rot, d'une panne de disque ou d'une panne AWS us-east-1. Les données vont *bien*. Elles reposent sur bande dans une installation Iron Mountain en ce moment même, parfaitement intactes, et Nine PBS ne peut pas y accéder parce qu'une question **juridique** — qui a le droit de *diriger* le transfert — n'a pas de réponse propre quand l'entreprise du milieu meurt.

La défaillance ici n'est pas technologique. Elle est **de garde** (custodiale). Le cloud n'est pas « l'ordinateur de quelqu'un d'autre » — c'est une formule creuse qui obscurcit plus qu'elle n'éclaire. Le cloud, c'est l'*entreprise* de quelqu'un d'autre, avec un bilan, un conseil d'administration, une inscription au secrétariat d'État et une durée de vie. Quand l'entreprise meurt, vos données deviennent un actif dans une succession que personne n'administre. Vos bits vont bien. Votre *titre* sur eux devient soudain contesté.

C'est ça, la vraie leçon, et elle est beaucoup plus difficile à tweeter : **on peut avoir une propriété parfaite de ses données et une garde nulle sur elles.** Ce sont deux choses différentes, et on les confond en permanence.

## La règle 3-2-1 n'est pas un conseil, c'est une forme de respect

Je vais dire la chose évidente que tout le monde dit, mais je vais la dire avec la colère qu'elle mérite : ça n'aurait jamais dû être possible.

50 téraoctets. Pour soixante-dix ans de production d'une chaîne de télé. Laissez-moi remettre ce chiffre en perspective, parce que quelques commentateurs ont remarqué quelque chose que j'ai remarqué aussi : 50 To, c'est *petit*. C'est un seul NAS six baies avec des disques haute capacité et un budget modeste — le genre de truc qu'un hobbyiste déterminé assemble en un week-end. Soixante-dix ans d'archives, et tout tenait dans une boîte qu'on peut acheter avec une carte de crédit.

La règle 3-2-1 — trois copies, deux supports différents, une hors site — n'est pas un « nice-to-have » pour une institution dont le travail entier consiste à *préserver la mémoire publique*. C'est la différence entre « on a eu un incident » et « on a perdu notre histoire ». Un incendie en Corée du Sud a détruit l'an dernier 858 To de données gouvernementales. Un projet allemand visant à préserver 60 000 jeux rétro s'est effondré cette année quand 1,8 M$ de financement se sont taris. La préservation ne cesse d'échouer, et elle échoue *toujours de la même façon* : l'archive a été traitée comme un passif au lieu d'un actif.

Mais — et c'est là que je défendrai la chaîne contre la schadenfreude — Nine PBS, c'est la télévision publique. Elle est financée comme l'est toujours la télévision publique : à contrecœur, avec ressentiment, et au bord de se faire couper les vivres. J'ai travaillé dans des endroits où « acheter un NAS de backup » était une demande qui mettait trois trimestres à passer. L'ingénieur qui a plaidé pour une deuxième copie de tout a probablement perdu l'argument face à une ligne budgétaire. Ce n'est pas une excuse ; c'est le contexte. Le *système* a échoué à valoriser l'archive, puis le fournisseur a échoué, et maintenant un juge doit décider qui a le droit de toucher les bandes.

## Garde, séquestre, et la correction ennuyeuse que personne ne veut financer

Alors quelle est la vraie correction ? Ce n'est pas « n'utilisez jamais le cloud », parce qu'héberger son propre datacenter a ses propres modes de défaillance — incendies, inondations, pannes de disque, le `rm -rf` accidentel, la personne qui part en emportant les clés. Les fournisseurs cloud font réellement la réplication et la durabilité mieux que vous. La correction, c'est d'arrêter de traiter *n'importe quelle* partie unique comme le gardien de dernier recours.

Trois choses, aucune n'étant sexy :

**Un, la règle 3-2-1 avec une vraie vérification.** Avoir trois copies ne vaut rien si vous n'avez jamais restauré depuis aucune d'elles. Un backup non testé est une rumeur de backup. Les institutions avec 70 ans d'histoire devraient faire des exercices de restauration comme les écoles font des exercices d'incendie.

**Deux, l'indépendance de garde.** La copie hors site ne doit pas se trouver derrière la même relation fournisseur que la copie primaire. Nine PBS avait *un* fournisseur (OSS) qui avait *un* fournisseur (Iron Mountain). Ce n'est pas de la redondance — c'est un point de défaillance juridique unique déguisé en chaîne d'approvisionnement. Si votre « backup » n'est qu'un autre SKU de la même entreprise mourante, vous n'avez pas de backup.

**Trois, un séquestre de données et des clauses de sortie qui fonctionnent réellement.** C'est la partie dont personne ne parle. Un contrat qui dit « vous pouvez récupérer vos données dans les 30 jours suivant la résiliation » ne vaut que ce que vaut l'entreprise qui l'honore — et comme l'a appris Nine PBS, l'entreprise peut ne plus *exister* à ce moment-là. Une vraie protection, c'est le séquestre : un tiers neutre détenant la capacité de libérer les données, ou au minimum un droit contractuel qui survit à la faillite. La plupart des acheteurs n'ont jamais entendu le mot « séquestre » dans ce contexte. Ils devraient.

Rien de tout ça n'est glamour. C'est la plomberie peu glamour de la mémoire institutionnelle, et elle continue de perdre la bataille du financement face à des choses avec des dashboards et des logos.

## Pourquoi cette histoire compte au-delà d'une seule chaîne de télé

Je ne cesse de penser à ce que « 70 ans de l'histoire de notre organisation » signifie en termes concrets. Ce n'est pas juste de vieux épisodes d'une émission d'affaires publiques régionale. C'est l'ère des droits civiques enregistrée en temps réel, des élections locales, les voix de gens aujourd'hui morts, l'enregistrement non monté d'une ville qui se parle à elle-même depuis sept décennies. Certaines de ces bandes n'existent nulle part ailleurs sur terre.

Quand la préservation échoue, elle échoue *en silence*. Vous ne recevez pas d'alerte pager. Vous recevez un lundi où quelqu'un vérifie enfin et où les données ont disparu, ou sont verrouillées, ou — comme ici — juridiquement mises en quarantaine. Le coût de cette perte n'est pas payé par le fournisseur, ni par le datacenter, ni par la victime de fraude de l'entreprise acquéreuse. Il est payé par tous ceux qui, dans vingt ans, iront chercher un morceau de leur propre passé et trouveront un lien mort.

C'est ça, le truc avec les archives : personne ne les finance parce que personne ne peut mesurer la valeur de ce qu'elles empêchent. On peut chiffrer le coût d'une panne de datacenter. On ne peut pas chiffrer la valeur de *toujours avoir* l'enregistrement d'une assemblée municipale de 1965. Alors on la réduit ligne par ligne jusqu'à ce qu'elle disparaisse.

## Où j'atterris

Je ne suis pas en colère contre Iron Mountain, exactement — un datacenter qui refuse de libérer des données sans ordonnance du tribunal, parce qu'il ne veut pas être poursuivi par une entreprise morte peut-être frauduleuse, est *raisonnable*. Je suis en colère contre la forme du système qui a rendu ce geste raisonnable.

L'histoire de Nine PBS n'est pas un conte moral sur le cloud. C'est un conte moral sur la garde. Sur la différence entre posséder des données et pouvoir les atteindre. Sur la façon dont une archive — la chose la plus importante qu'un diffuseur public *possède* — s'est retrouvée juridiquement enchevêtrée avec un fournisseur disparu, une victime de fraude et un datacenter qui veut juste éviter un procès.

Le cloud n'a pas perdu 70 ans de télévision. Une *chaîne de garde* s'est brisée, au moment exact où la chaîne incluait une entreprise qui n'existait plus. Et tant que nous ne traiterons pas les archives comme ce qu'elles sont — l'infrastructure de la mémoire du futur — nous continuerons à lire des versions de cette histoire, avec d'autres noms, jusqu'à ce que les bandes aient disparu pour de bon.
