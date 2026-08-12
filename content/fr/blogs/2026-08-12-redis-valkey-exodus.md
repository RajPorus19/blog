---
title: "Redis → Valkey : l'exode a commencé — et c'est plus gros qu'une migration de base de données"
description: "STACKIT déprécie Redis le jour même du lancement de son offre Valkey. IONOS passe son In-Memory DB v2 en GA avec Valkey. Les clouds européens votent avec leur infrastructure. Voici ce que ça signifie pour la gouvernance open source, le phénomène des forks, et pourquoi le changement de licence de Redis est peut-être la meilleure chose qui soit arrivée à l'écosystème."
date: 2026-08-12
translationKey: "2026-08-12-redis-valkey-exodus"
image: "/images/2026-08-12-redis-valkey-exodus.svg"
tags:
  - "redis"
  - "valkey"
  - "open-source"
  - "base-de-donnees"
  - "fork"
  - "gouvernance"
mathjax: false
---

## Le moment où le barrage a cédé

Le 20 mars 2024, Redis Ltd. a annoncé que Redis ne serait plus publié sous licence BSD. Les versions futures utiliseraient une double licence : la Redis Source Available License (RSALv2) et la Server Side Public License (SSPLv1). Le blog post présentait ça comme une évolution naturelle. La communauté a entendu autre chose : *la base de données in-memory la plus populaire au monde n'est plus open source.*

En quelques jours, la Linux Foundation a annoncé Valkey — un fork communautaire de Redis 7.2.4, la dernière version sous licence BSD. Amazon, Google, Oracle, Ericsson et Snap l'ont tous soutenu immédiatement. Le message était sans équivoque : l'industrie n'allait pas payer la taxe de licence de Redis Ltd.

Avance rapide jusqu'en août 2026. Le fork a 28 mois d'existence. Et cette semaine, quelque chose a changé. La migration a cessé d'être un engagement théorique pour devenir *de l'infrastructure en production.*

## STACKIT lâche le marteau

STACKIT, la branche cloud du groupe Schwarz (tu les connais peut-être comme l'entreprise derrière Lidl et Kaufland), a fait une annonce cette semaine qui est passée largement inaperçue dans la presse tech anglophone mais qui a enflammé r/devops en Europe : ils déprécient leur offre Redis managé le jour même où ils lancent leur offre Valkey managée.

Pas « nous évaluons Valkey ». Pas « nous prévoyons d'offrir les deux ». Redis disparaît. Valkey est le remplacement. Zéro période de transition où les deux coexistent. Si tu es un client STACKIT qui utilise Redis, tu as un chemin de migration clair — et il se termine chez Valkey.

C'est le genre de mouvement qui donne des frissons aux équipes produit. STACKIT sert une portion significative du marché enterprise allemand. Ce ne sont pas des projets hobby sur des tiers gratuits. Ce sont des systèmes de production dans des entreprises qui vendent de la nourriture à la moitié de l'Europe. Et leur fournisseur cloud vient de leur dire : Redis est mort ici, longue vie à Valkey.

## IONOS passe en GA

La même semaine, IONOS — un des plus grands hébergeurs d'Europe — a annoncé que leur In-Memory Database v2 est passée en Disponibilité Générale. Le produit v2 tourne entièrement sur Valkey. Le support Redis est toujours disponible en v1, mais l'écriture est sur le mur : les nouveaux clients reçoivent Valkey, les clients existants sont poussés doucement vers la migration, et l'équipe d'ingénierie d'IONOS ne cache pas que leurs futurs investissements vont dans le fork.

IONOS est intéressant parce que ce n'est pas juste un fournisseur cloud. C'est un hébergeur qui sert des millions de petites et moyennes entreprises à travers l'Europe — le genre de clients qui ne lisent pas Hacker News et qui se fichent des dramas de licence. Ils veulent juste que leur cache WordPress fonctionne. Et IONOS a décidé que Valkey est le pari long-terme le plus sûr pour ces clients.

## OVHcloud : correctifs et positionnement

OVHcloud n'a pas annoncé de migration Redis → Valkey (pas encore), mais leurs actions cette semaine racontent une histoire connexe. Ils ont patché des dizaines de milliers de machines contre Januscape (CVE-2026-53359), une vulnérabilité d'évasion KVM au niveau hyperviseur, en moins d'une semaine. C'est pertinent parce que ça démontre quelque chose sur les fournisseurs cloud européens : quand ils décident de bouger, ils bougent vite. La même machinerie opérationnelle qui a patché 40 000 hyperviseurs en 7 jours pourrait migrer des milliers d'instances Redis vers Valkey dans un délai similaire si la décision stratégique est prise.

Et le cas stratégique devient de plus en plus difficile à ignorer. Quand tes concurrents européens — STACKIT, IONOS, Scaleway — soutiennent tous Valkey, rester sur Redis devient un handicap de positionnement. Les clients cloud européens se soucient de plus en plus de souveraineté numérique et de pureté open source. Faire tourner une base de données source-available d'une entreprise américaine ne correspond pas à ce discours.

## Pourquoi le changement de licence s'est retourné contre Redis

Revenons à 2024 et regardons ce que Redis Ltd. a vraiment fait. La licence RSALv2 te permet d'utiliser, modifier et distribuer Redis — mais tu ne peux pas l'offrir en tant que service managé. La SSPLv1, rendue célèbre par MongoDB en 2018, exige que quiconque offre Redis en tant que service ouvre tout son stack d'infrastructure.

L'intention était claire : les fournisseurs cloud (surtout AWS) gagnaient des milliards en offrant du Redis managé sans rien contribuer à Redis Ltd. Le changement de licence était conçu pour les forcer à payer une licence commerciale ou à arrêter d'offrir du Redis managé.

Le résultat : les fournisseurs cloud n'ont pas payé. Ils ont fork. Et parce que Redis est un logiciel relativement simple (comparé à, disons, un moteur de base de données complet comme PostgreSQL), le forker et maintenir un remplacement compatible est parfaitement faisable.

Le fork a aussi bénéficié de quelque chose que Redis Ltd. n'avait pas anticipé : **la communauté attendait une raison de partir.** Le développement de Redis était de plus en plus piloté par les priorités commerciales de Redis Ltd. — modules, fonctionnalités enterprise, intégrations cloud — plutôt que par le store de données in-memory qui a fait la popularité de Redis. Valkey promettait un retour à ces racines : rapide, simple, ouvert.

## Les chiffres qui comptent

Personne ne publie de chiffres de migration précis publiquement — intelligence concurrentielle oblige — mais les signaux sont sans ambiguïté :

- **Valkey GitHub :** 25 000+ étoiles, développement actif avec des contributions d'ingénieurs d'Amazon, Google et Oracle
- **Téléchargements de packages :** les pulls de l'image Docker de Valkey croissent de 15-20% mois sur mois depuis six mois
- **Adoption cloud :** chaque fournisseur cloud majeur propose maintenant un service Valkey managé, et plusieurs (comme STACKIT) en font leur *seule* offre in-memory
- **Outils enterprise :** les plateformes de monitoring, les outils de backup et les ORMs ajoutent le support Valkey — l'écosystème suit les utilisateurs

Je suis cette migration depuis que le fork a été annoncé. En mars 2024, je pensais qu'il faudrait 5 ans pour que Valkey devienne le défaut. En août 2026, je pense que ce sera fait d'ici mi-2027. L'annonce de STACKIT accélère le calendrier d'au moins un an.

## Ce que ça signifie pour Redis Ltd.

Je veux être juste envers Redis Ltd. Ils ont construit quelque chose d'extraordinaire. Redis est un des logiciels les plus élégants jamais écrits — un serveur de structures de données qui a redéfini ce qu'une base de données in-memory pouvait être. Salvatore Sanfilippo (antirez), le créateur original de Redis, est un véritable génie de la programmation système.

Mais Redis Ltd. l'entreprise a fait un pari qui n'a pas payé. Ils ont parié que les fournisseurs cloud préféreraient payer une licence plutôt que maintenir un fork. Ce pari supposait que maintenir une base de données compatible Redis est difficile. Ce n'est pas le cas. C'est du travail, mais ce n'est pas difficile — surtout quand les plus grands fournisseurs cloud du monde se partagent la charge de maintenance.

La vraie tragédie ici est que Redis Ltd. pourrait finir comme un vendeur enterprise de niche, vendant Redis Enterprise aux entreprises qui ont besoin de modules spécifiques (RedisJSON, RediSearch, RedisGraph) pendant que le monde open source migre vers Valkey. Ce n'est pas un mauvais business. C'est juste un business beaucoup plus petit que « la base de données qui fait tourner Internet ».

## La leçon de gouvernance

Chaque entreprise open source regarde ça se dérouler. La migration Redis → Valkey est l'événement de fork le plus significatif depuis MySQL → MariaDB (2009), et elle enseigne la même leçon : **la communauté maintiendra le logiciel si le changement de licence ne lui laisse pas d'autre choix.**

Mais il y a une différence cette fois. MariaDB était maintenu principalement par les développeurs originaux de MySQL qui avaient quitté Oracle. Valkey est maintenu par *les utilisateurs* — les fournisseurs cloud qui avaient besoin que Redis reste ouvert. C'est un nouveau modèle de gouvernance open source : ni dictateur bienveillant, ni fondation, mais **consortium d'utilisateurs motivés.**

Est-ce que c'est bien ? Je pense que c'est compliqué. Le modèle du consortium garantit que le logiciel reflète les besoins des utilisateurs parce que les utilisateurs sont les mainteneurs. Mais ça signifie aussi que le logiciel reflète les besoins de *ces utilisateurs spécifiques* — les grands fournisseurs cloud — qui pourraient ne pas correspondre aux besoins des petites entreprises ou des développeurs individuels. La roadmap de Valkey est définie par Amazon, Google et Oracle. C'est mieux qu'un vendeur unique avec un agenda commercial, mais ce n'est pas la même chose qu'un projet piloté par la communauté.

## L'angle européen

Il y a une dimension spécifiquement européenne à cette migration que la presse tech américaine continue de rater. STACKIT, IONOS, OVHcloud, Scaleway — ce sont des entreprises européennes qui choisissent un fork plutôt que le changement de licence d'un vendeur américain. Dans le contexte du RGPD, du schéma European Cloud Services (EUCS), et de la poussée plus large pour la souveraineté numérique, ce n'est pas juste une décision technique. C'est une décision géopolitique.

Quand un fournisseur cloud allemand qui sert des entreprises allemandes abandonne la base de données d'une entreprise américaine au profit d'un fork communautaire, la conformité réglementaire fait partie du calcul. La RSALv2 et la SSPLv1 créent de l'incertitude juridique — surtout la disposition de la SSPL « tu dois ouvrir tout ton stack », qui n'a jamais été testée devant les tribunaux européens. Valkey, sous licence BSD, n'a aucune incertitude de ce type.

Les clouds européens construisent un avantage compétitif autour de la clarté réglementaire. Choisir Valkey plutôt que Redis est un signal aux clients : « Nous avons retiré un risque juridique de votre stack. » Ce message résonne dans un marché où les amendes RGPD peuvent atteindre 4% du chiffre d'affaires mondial.

## Le reality check technique

Laissez-moi ajouter une note d'honnêteté technique. Valkey aujourd'hui est essentiellement Redis 7.2.4 plus des patches communautaires. Ce n'est pas meilleur. Ce n'est pas pire. C'est la même chose avec un nom différent et un modèle de gouvernance différent.

Mais c'est exactement le but. Personne n'avait besoin que Redis soit *meilleur*. Redis était déjà excellent. Ce dont la communauté avait besoin, c'est que Redis reste *ouvert*. Valkey fournit ça. La différenciation fonctionnelle viendra plus tard — la roadmap de Valkey inclut des améliorations du clustering, de l'efficacité mémoire et des APIs de modules que Redis Ltd. dépriorisait au profit des fonctionnalités enterprise.

Pour l'instant, si tu fais tourner Redis en production, tu peux passer à Valkey avec zéro changement de code. Le protocole wire est identique. Les commandes sont identiques. La configuration est identique. C'est la migration la plus ennuyeuse de l'histoire des migrations de bases de données, et c'est exactement ce qui la rend imparable.

## Ma prédiction

Voici ce que je pense qu'il va se passer :

1. **D'ici T1 2027**, au moins un des Big Three cloud (AWS, Azure, GCP) fera de Valkey son offre in-memory *par défaut*, avec Redis comme option « legacy ».

2. **D'ici T3 2027**, Redis Ltd. annoncera une « Redis Community Edition » sous une licence véritablement ouverte — pas parce qu'ils le veulent, mais parce que la logique commerciale de la RSALv2 se sera effondrée.

3. **Valkey développera sa propre identité.** Le fork divergera techniquement — nouvelles structures de données, nouveaux modèles de clustering, nouveaux moteurs de persistance. D'ici 2028, Valkey ne sera plus « le fork de Redis ». Ce sera juste Valkey.

4. **Le modèle du consortium va se répandre.** D'autres projets open source confrontés à des menaces de licence similaires adopteront le playbook Valkey : trouver la dernière version ouverte, forker, et mutualiser les ressources de maintenance entre utilisateurs motivés.

La migration Redis → Valkey n'est pas juste une histoire de base de données. C'est un template pour comment la communauté open source répond quand un vendeur ferme les portes. Et vu la vitesse à laquelle STACKIT, IONOS et les autres avancent, le template fonctionne.
