---
title: "Le fonds de dotation pour l'open source — un pari de $750K que le FOSS n'a pas besoin de VC"
description: "Le premier fonds de dotation dédié à l'open source vient d'être lancé avec $750K+ de 60 donateurs incluant les fondateurs de HashiCorp, Elastic, Supabase et cURL. C'est peut-être l'expérience de financement FOSS la plus importante de la décennie."
date: 2026-08-11
translationKey: "open-source-endowment"
image: "/images/open-source-endowment.svg"
tags:
  - "open-source"
  - "funding"
  - "sustainability"
  - "community"
  - "foss"
mathjax: false
---

## Le problème qu'on fait semblant de ne pas voir

L'open source a un problème de financement. Pas un petit. Un problème structurel.

Les chiffres sont absurdes. cURL — probablement le client HTTP le plus déployé de l'histoire de l'humanité, qui tourne sur des milliards d'appareils — était maintenu par une seule personne, Daniel Stenberg, qui jusqu'à récemment finançait son travail via des contrats de support commercial. OpenSSL, la bibliothèque qui sécurise environ 90% des connexions TLS d'internet, était maintenue par deux personnes en 2014 quand Heartbleed est arrivé. XZ Utils, une bibliothèque de compression utilisée par essentiellement toutes les distributions Linux, était maintenue par un seul développeur en burnout qui s'est fait social-engineer par un acteur étatique l'année dernière.

Le pattern est toujours le même : une infrastructure critique maintenue par des individus ou des toutes petites équipes sans filet de sécurité financier. Le logiciel fait tourner le monde. Les mainteneurs arrivent à peine à faire tourner leur propre vie.

On a eu des solutions. GitHub Sponsors. Open Collective. Tidelift. Les entreprises open source financées par VC (qui pivotent inévitablement vers « l'open core » puis « en fait, les bonnes parties ne sont plus ouvertes »). Aucune n'a résolu le problème fondamental : **l'infrastructure open source est un bien public, et les biens publics sont systématiquement sous-financés dans les économies de marché.**

C'est pour ça que le fonds de dotation pour l'open source est important.

---

## C'est quoi ce fonds de dotation ?

Annoncé cette semaine sur r/opensource et lancé officiellement, le Open Source Endowment est le premier fonds de dotation dédié au logiciel libre et open source. Il est calqué sur les dotations universitaires : un capital qui est investi, dont les rendements servent à financer des projets FOSS à perpétuité.

Les chiffres pour l'instant : $750 000+ de capital engagé, 60+ donateurs incluant les fondateurs de HashiCorp, Elastic, Supabase, cURL, Vue.js et d'autres projets FOSS majeurs. Ce ne sont pas des dons corporate issus de budgets PR — ce sont des individus qui ont construit leur carrière sur l'open source, qui mettent leur propre argent dans une structure conçue pour leur survivre.

Le mécanisme est simple et radical : **on ne dépense pas le principal. On dépense les rendements. Pour toujours.**

Une dotation de 10 millions de dollars qui génère 5% par an produit $500 000 de financement durable par an. Une dotation de 100 millions produit 5 millions. Ce ne sont pas des montants à l'échelle du VC, mais ils sont *permanents*. Pas de cycles de fundraising. Pas de demandes de subvention. Pas de dépendance à un sponsor corporate unique qui pourrait changer d'avis le trimestre prochain.

---

## Pourquoi les dotations ont du sens pour le FOSS

J'ai passé des années à regarder les modèles de financement open source apparaître et disparaître. Chacun a le même défaut : **ils ne sont pas permanents.**

Le financement VC s'épuise. Les sponsors corporate se font acquérir ou changent de stratégie. Les dons individuels fluctuent avec l'économie. Même les modèles qui marchent comme Open Collective sont vulnérables — une mauvaise année, un donateur qui se retire, et le financement d'un projet disparaît.

Une dotation est différente. L'argent est légalement structuré pour exister à perpétuité. Le principal est investi de façon conservatrice. Les rendements sont distribués aux projets selon un processus transparent. C'est de la finance ennuyeuse appliquée à un problème qui a désespérément besoin de solutions ennuyeuses.

C'est important parce que le logiciel dont on dépend ne devrait pas être financé comme une startup. Les startups sont censées croître ou mourir. L'infrastructure critique est censée être stable, fiable, ennuyeuse. Le mécanisme de financement devrait correspondre à la mission.

Regarde l'alternative : tous les deux ou trois ans, on a une crise où on découvre qu'un morceau d'infrastructure critique est maintenu par une personne sans financement, et l'industrie tech panique collectivement pendant deux semaines, donne un peu d'argent, puis oublie jusqu'à la prochaine crise. Le Open Source Endowment est une tentative de briser ce cycle.

---

## Le piège du VC et pourquoi c'est différent

Laissez-moi dire quelque chose de controversé : **le capital-risque est incompatible avec la durabilité de l'open source.**

Le financement VC exige une croissance exponentielle — des retours 10x en 5-7 ans. Les projets open source, par nature, ne génèrent pas de revenus exponentiels. Tu ne peux pas faire 10x le revenu d'une bibliothèque qui est donnée gratuitement. Donc les entreprises open source financées par VC font inévitablement l'une de ces deux choses :

1. **Passer en open core** — garder la base ouverte, faire payer les bonnes parties. C'est le playbook HashiCorp (avant qu'ils passent en BUSL), le playbook Elastic (avant qu'ils passent en SSPL), le playbook GitLab (qui a marché, allez savoir comment).
2. **Pivoter vers le SaaS** — arrêter d'être une entreprise open source et devenir une entreprise cloud qui publie du code accessoirement. MongoDB, Confluent, Databricks.

Aucune de ces stratégies n'est diabolique. Les entreprises doivent gagner de l'argent. Mais ce ne sont pas des stratégies de *durabilité* pour les projets open source. Ce sont des stratégies de sortie pour les investisseurs.

Le Open Source Endowment n'a pas besoin de sorties. Il n'a pas besoin de croissance. Il a besoin que le S&P 500 monte sur 30 ans. C'est un pari avec d'assez bonnes chances.

---

## Les fondateurs qui mettent leur argent là où est leur code

La liste des donateurs est la partie la plus intéressante. Tu as Mitchell Hashimoto (co-fondateur de HashiCorp), qui a vu la controverse de licence de son entreprise de l'intérieur. Tu as Evan You (créateur de Vue.js), qui a construit l'un des frameworks frontend les plus populaires au monde tout en maintenant un modèle de financement remarquablement durable via les sponsors. Tu as Daniel Stenberg (cURL), qui sait exactement ce que c'est de maintenir une infrastructure essentielle tout seul.

Ce ne sont pas des philanthropes qui signent des chèques à distance. Ce sont des gens qui ont vécu le problème. Ils ont été le mainteneur à 2h du matin en train de corriger une vulnérabilité de sécurité qui affecte des millions d'utilisateurs. Ils ont eu la conversation gênante où une entreprise offre de « la visibilité » au lieu d'argent. Ils ont vu leurs projets être forkés, re-licenciés et commercialisés par des gens qui n'ont rien contribué.

Le fait qu'ils mettent leur propre argent dans une dotation — pas une fondation avec leur nom dessus, pas une initiative PR-friendly, mais un instrument financier ennuyeux — me dit qu'ils comprennent que l'open source a besoin de solutions permanentes, pas de pansements temporaires.

---

## Ce qui pourrait mal tourner

Je suis optimiste, mais je ne suis pas naïf. Les dotations ont leurs propres problèmes.

**La gouvernance.** Qui décide quels projets sont financés ? Comment empêcher la dotation de devenir un club de projets populaires en ignorant l'infrastructure sans glamour qui fait réellement tourner internet ? La structure de gouvernance initiale est un conseil de figures respectées du FOSS, mais les dynamiques de conseil peuvent mal tourner. J'ai vu trop de fondations open source devenir des champs de bataille politiques.

**L'échelle.** $750K, c'est un début. Ce n'est pas une solution. À un taux de rendement de 5%, ça fait $37 500 par an à distribuer — à peine assez pour financer un mainteneur à temps partiel. La dotation doit atteindre 10M, 50M, 100M pour devenir significative. Ça demande soit des dons individuels massifs, soit du soutien institutionnel. Les deux sont difficiles.

**Les effets de sélection.** Les dotations ont tendance à financer les projets établis et visibles — ceux qui ont de la notoriété. Mais les projets qui ont le plus besoin de financement sont les invisibles : les bibliothèques de compression, les implémentations TLS, les résolveurs DNS. Les trucs auxquels personne ne pense jusqu'à ce qu'ils cassent. L'impact de la dotation dépend entièrement de sa capacité à identifier et financer ces piliers invisibles.

**Le problème du « encore une fondation ».** On a déjà la Linux Foundation, l'Apache Foundation, la Python Software Foundation, l'Eclipse Foundation, l'OpenJS Foundation, et une douzaine d'autres. Ajouter une entité de plus au paysage de gouvernance FOSS crée des problèmes de coordination. Le Open Source Endowment est différent (c'est un véhicule de financement, pas un hébergeur de projets), mais la distinction sera perdue pour la plupart des gens.

---

## Pourquoi je reste optimiste

Malgré tout ça, je pense que c'est l'expérience de financement FOSS la plus importante depuis le lancement de GitHub Sponsors en 2019. Voici pourquoi :

D'abord, **ça change la conversation.** Pendant deux décennies, le financement open source a été formulé comme un problème de charité : « faites un don à votre projet préféré. » Les dotations le reformulent comme un problème d'infrastructure : « on a besoin de financement permanent pour une infrastructure permanente. » C'est une formulation plus honnête et plus soluble.

Ensuite, **ça crée un modèle.** Même si le Open Source Endowment reste petit, il prouve le concept. Les dotations universitaires n'ont pas commencé à 53 milliards (la dotation actuelle de Harvard). Elles ont commencé petit et ont grandi sur des siècles. La première dotation open source crée une structure légale, un modèle de gouvernance et un historique que d'autres peuvent reproduire.

Troisièmement, **ça ne demande pas de pitié.** Le narratif du « pauvre mainteneur triste » — le développeur en burnout qui supplie pour des dons — est émotionnellement efficace mais stratégiquement en faillite. Il présente l'open source comme un cas de charité plutôt que ce qu'il est vraiment : une infrastructure critique dont dépend l'économie mondiale. Le modèle de dotation dit : « c'est de l'infrastructure, financez-la comme telle. »

---

## Ce que tu devrais faire concrètement

Si tu travailles dans une entreprise qui dépend de l'open source (c'est-à-dire toutes les entreprises), transmets l'annonce du Open Source Endowment à ton CTO. Demande quelle est la stratégie de durabilité FOSS de ton entreprise. Si la réponse est « on n'en a pas », c'est un problème.

Si tu es développeur individuel, l'important n'est pas de donner 5€ par mois à un projet (même si c'est sympa). C'est de changer ta façon de penser l'open source. Le logiciel dont tu dépends est de l'infrastructure. Il a besoin de financement permanent. Le Open Source Endowment est une tentative. Soutiens-la, critique-la, mais ne l'ignore pas.

L'ère du « l'open source c'est gratuit » se termine. La question maintenant est de savoir si on construit des modèles de financement durables, ou si on attend le prochain Heartbleed.
