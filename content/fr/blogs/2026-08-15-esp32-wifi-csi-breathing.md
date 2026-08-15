---
title: "Un ESP32 à 4 $ peut désormais vous voir respirer à travers les murs — et c'est à la fois un miracle et un problème"
description: "Un nouveau projet open source transforme n'importe quel routeur WiFi en moniteur de respiration de bébé sans contact, grâce au Channel State Information et à un ESP32 à 4 $. Le DSP est élégant, la cause est noble — et les implications pour la vie privée sont exactement aussi inquiétantes qu'elles en ont l'air. Vos murs n'ont jamais été aussi solides que vous le croyez."
date: 2026-08-15
translationKey: "2026-08-15-esp32-wifi-csi-breathing"
image: "/images/2026-08-15-esp32-wifi-csi-breathing.svg"
tags:
  - "esp32"
  - "hardware"
  - "dsp"
  - "wifi"
  - "vie-privee"
  - "open-source"
  - "securite"
mathjax: false
---

## La phrase du README qui m'a fait reposer mon café

Il y a un projet GitHub qui a refait surface sur r/github cette semaine, et il s'ouvre sur une phrase qui donne l'impression d'avoir été écrite par quelqu'un qui a pleinement saisi les implications de ce qu'il a construit et qui a décidé d'aller droit au but :

*« Transformez n'importe quel routeur WiFi en moniteur de respiration de bébé sans contact. Juste un ESP32 (4 $), votre routeur existant, et la physique. »*

Le projet s'appelle BabyGuard, il est sous licence MIT, et son postulat est le suivant : chaque signal WiFi qui rebondit dans votre maison est subtilement, mesurablement déformé par la montée et la descente de votre poitrine. Ces déformations sont encodées dans ce qu'on appelle le Channel State Information de chaque paquet — et un microcontrôleur à quatre dollars, si l'on sait écouter, peut en extraire votre rythme respiratoire. À travers les murs. Sans caméra. Sans capteur porté. Sans vous toucher.

Je lis sur la détection WiFi depuis des années — c'est tout un sous-domaine de la recherche, avec des papiers comme Wi-Sleep et BreathJunior — mais il y a quelque chose dans le fait de voir ça emballé comme *un appareil à 4 $ avec un backend Python et 32 tests unitaires qui passent* qui change la température de la chose. C'est le moment où une technologie cesse d'être une curiosité de recherche pour devenir quelque chose que vous pouvez construire en un après-midi. Et ça coupe dans les deux sens à la fois.

---

## Comment ça marche vraiment : vos poumons sont une station radio

Laissez-moi expliquer la physique, parce qu'elle est réellement magnifique et que presque personne en dehors d'une niche n'a l'occasion de l'apprécier.

Quand votre routeur parle à un appareil, les ondes radio ne voyagent pas en ligne droite et propre. Elles rebondissent sur les murs, le sol, les meubles et — surtout — sur *vous*. Un corps humain est en grande partie de l'eau, et l'eau fait des choses très précises et très prévisibles à un signal à 2,4 GHz : elle le réfléchit, l'absorbe et change légèrement sa phase. Quand vous respirez, votre poitrine bouge de quelques centimètres. Ce minuscule mouvement suffit à modifier de façon mesurable la manière dont les signaux WiFi de la pièce rebondissent.

Le hic, c'est que « mesurable » ne veut pas dire ce qu'on croit. Le changement est microscopique — une fraction de fraction de longueur d'onde. Mais le WiFi est un protocole assez moderne pour que la puce puisse vous dire, pour chaque paquet, exactement comment le signal est arrivé : l'amplitude et la phase sur **64 sous-porteuses** du canal. C'est le Channel State Information, et l'ESP32 peut le lire gratuitement, sur du matériel que vous possédez déjà.

Le montage de BabyGuard est donc presque insultant de simplicité. L'ESP32 pingue votre routeur cinquante fois par seconde. Chaque réponse transporte ces 64 lectures de sous-porteuses, chacune une image légèrement différente de la façon dont le signal a été perturbé. La montée et la descente de la poitrine s'impriment sur ces lectures comme une oscillation faible et répétitive — de la même façon qu'un sismographe capte des pas. Le signal a toujours été là. Le routeur diffuse le fait de votre respiration depuis des années. Il fallait juste une puce à 4 $ pour l'entendre.

---

## Le pipeline DSP est là où vit le vrai métier

Voici la partie dont je veux réellement parler, parce que c'est ce qui sépare un gadget d'un projet d'ingénierie, et c'est la raison pour laquelle je me retrouve sincèrement impressionné.

Le signal CSI brut est un chaos. Il est enfoui sous le bruit, la dérive thermique, le contrôle de gain automatique de l'ESP32 lui-même (qui provoque des sauts d'amplitude soudains qui ressemblent exactement à de la respiration si l'on n'y prend pas garde), et les interférences de tout le reste de la pièce. Transformer ça en un nombre fiable de respirations par minute est un problème classique de traitement du signal numérique, et le README déroule le pipeline comme quelqu'un qui comprend réellement pourquoi chaque étage existe :

1. **Suppression des valeurs aberrantes de Hampel** — éliminer les glitches sans lisser le signal.
2. **Filtrage passe-bande à 0,3–1,2 Hz** — isoler exactement la bande de fréquence où vit la respiration d'un nourrisson (un bébé respire 30 à 60 fois par minute ; un adulte, 12 à 20). Tout le reste est du bruit.
3. **FFT plus autocorrélation** — estimer le rythme respiratoire par deux méthodes indépendantes, pour que l'une rattrape ce que l'autre rate.
4. **Fusion de sous-porteuses par PCA** — au lieu de choisir la meilleure des 64 sous-porteuses, prendre la composante principale, qui capture le mouvement *cohérent* (la respiration) et rejette le bruit non corrélé.
5. **Détection du premier pic** — parce que l'autocorrélation d'un signal périodique a des harmoniques, et qu'un `argmax` naïf s'accrochera à l'une d'elles et rapportera *la moitié* du vrai rythme. L'auteur l'a repéré. Ce n'est pas de la chance ; c'est l'avoir déjà fait.

Il y a plus — une étape de **rejet du mouvement** (un détecteur à ratio de variance qui met en pause l'alarme d'apnée quand quelqu'un traverse la pièce, pour qu'une personne qui bouge ne masque pas un bébé qui *s'arrête*), une **compensation de gain** dans le firmware pour normaliser les sauts d'AGC, et un **watchdog** qui alerte les parents si l'ESP32 lui-même meurt, parce que, selon les mots de l'auteur, *« l'échec silencieux est le mode de défaillance le plus dangereux. »*

Cette dernière phrase est celle qui m'a convaincu que ce projet est réel. La personne qui écrit « l'échec silencieux est le mode de défaillance le plus dangereux » dans le README d'un moniteur pour bébé n'est pas quelqu'un qui joue à l'ingénieur. Elle a pensé à ce qui se passe à 3 heures du matin quand la chose à qui vous confiez la vie de votre enfant s'arrête, tout simplement.

---

## La raison noble pour laquelle ça existe

Et c'est ici que l'histoire prend son cœur, parce qu'il est facile d'être désinvolte à propos d'un gadget à 4 $ et de passer à côté de la raison pour laquelle quelqu'un l'a construit.

Environ **3 400 nourrissons meurent de mort subite inattendue du nourrisson aux États-Unis chaque année** — et une part significative de ces décès est du genre qu'un dispositif de surveillance pourrait détecter à temps. Les options existantes sont toutes mauvaises d'une manière ou d'une autre : les capteurs portés qu'on attache à un nouveau-né (inconfortables, et précisément le genre de chose que les consignes de sommeil sécurisé disent de ne pas mettre dans le berceau), les caméras (la surveillance de votre propre enfant, et inutiles dans le noir), et les moniteurs médicaux propriétaires (des milliers de dollars, des abonnements, des boîtes noires).

L'argument de BabyGuard, c'est que la capacité fondamentale — détecter si une poitrine monte et descend encore — ne devrait exiger rien de tout ça. Elle devrait coûter 4 $, tourner sur du matériel que vous possédez déjà, et vous rendre ses données sous une forme que vous contrôlez. Le détecteur d'apnée surveille la forme d'onde respiratoire, et si l'énergie tombe sous un seuil pendant plus de douze secondes — une alerte précoce, en avance sur la définition clinique de vingt secondes — il déclenche une alarme sonore et pousse une notification sur votre téléphone.

Il y a une phrase en bas du README, sous la licence, qui, je l'avoue, m'a touché : *« MIT — utilisez-le comme bon vous semble. Si ça sauve ne serait-ce qu'un bébé, ça valait la peine d'être construit. »* C'est le genre de phrase qui serait mièvre si l'ingénierie n'était pas là pour la soutenir, et terriblement émouvante parce qu'elle l'est.

---

## Maintenant la partie qui devrait vous empêcher de dormir

J'ai passé cinq cents mots à vous dire que c'est un moniteur pour bébé magnifique, humain, open source. Ça l'est. Maintenant laissez-moi vous dire pourquoi c'est aussi l'avertissement le plus net en matière de vie privée que j'aie lu cette année.

Tout ce que je viens de décrire — la lecture du CSI, les perturbations des sous-porteuses, l'extraction de la respiration — fonctionne sur **n'importe quel** signal WiFi, y compris celui qui traverse le mur de votre voisin pour arriver dans votre appartement. Ça ne demande pas de caméra. Pas de microphone. Pas de consentement, pas de mandat, pas même une ligne de vue. Les ondes radio traversent la cloison sèche comme si elle n'existait pas, et la fonctionnalité même qui fait de BabyGuard un meilleur moniteur — pas de caméra, pas de capteur, rien à pointer vers l'enfant — est exactement la même fonctionnalité qui en fait un outil de surveillance parfait.

Laissez-moi être très concret sur ce que ça signifie. Un ESP32 à 4 $, un script Python et un ordinateur portable constituent désormais la liste complète des matériaux d'un appareil capable de dire, depuis l'extérieur de votre domicile, si quelqu'un se trouve dans une pièce, s'il bouge, à peu près où il est, et — avec le bon traitement — s'il respire, et à quel rythme. Il y a dix ans, la détection humaine à travers les murs était le domaine des radars militaires et des équipements des forces de l'ordre avec une étiquette de prix finissant par plusieurs zéros. Aujourd'hui, c'est un projet d'un après-midi avec une nomenclature de composants moins chère que le café que j'ai bu en lisant le README.

La recherche pertinente, d'ailleurs, va bien au-delà de la respiration. La même technique CSI est décrite dans la section « au-delà du monitoring bébé » du README pour la **détection de l'apnée du sommeil, la détection de chute des personnes âgées, la détection de présence pièce par pièce, le suivi du stress et le monitoring d'animaux domestiques**. Autrement dit : qui est chez soi, où dans la maison, s'il n'a pas bougé depuis trois heures, et quel est son niveau d'anxiété de base. Tout ça, passivement, à partir du signal que votre routeur émet depuis le début.

---

## La vie privée par l'obscurité est morte — elle n'a jamais été vivante

Il y a une tentation de répondre à tout ça par « mes murs sont assez solides » ou « on s'en fiche, je ne suis pas intéressant ». J'ai envie de pousser contre les deux, parce que ce sont la même erreur sous deux costumes différents.

La première n'est que de l'ignorance physique. Vos murs ne sont pas une barrière au 2,4 GHz — ils sont à peine un ralentisseur. Le signal que votre routeur produit quitte, en ce moment même, votre domicile, traverse le salon de votre voisin, et transporte avec lui un enregistrement ténu mais parfaitement lisible du fait que vous êtes assis là, à respirer, à lire ceci. Vous n'avez pas opté pour ça. C'est simplement ce que le WiFi *est*.

La seconde est la plus dangereuse. « Je ne suis pas intéressant » est l'argument que les gens avancent quand une technologie est rare, chère et dirigée vers des cibles de grande valeur. Il cesse de fonctionner au moment où la technologie coûte quatre dollars et tient dans un tiroir. Le problème de la détection passive à travers les murs et bon marché, ce n'est pas que quelqu'un va la pointer vers *vous* en particulier. C'est que la capacité existe désormais, en masse, à un prix où *tout le monde* peut se l'offrir — les propriétaires, les harceleurs, les partenaires jaloux, les bâtiments « intelligents », et oui, les entreprises dont tout le modèle économique consiste à savoir où vous êtes et ce que vous faites. Pas besoin d'être intéressant. Il suffit d'être à portée.

C'est ce que je veux dire quand je dis que la vie privée par l'obscurité n'a jamais été vraiment vivante. Pendant la majeure partie de l'histoire d'internet, une bonne part de la « vie privée » n'était en réalité que de l'*impraticabilité* — la surveillance était possible en théorie mais trop chère pour s'appliquer à tout le monde. L'histoire des vingt dernières années, c'est l'histoire de l'effondrement de cette impraticabilité, une puce à 4 $ à la fois. La détection CSI du WiFi n'en est que l'exemple le plus récent, et à certains égards le plus saisissant : le monde physique, l'intérieur de votre maison, le rythme de votre propre respiration, est devenu lisible pour du matériel de grande consommation. Il n'y a pas de retour en arrière possible.

---

## Ce que j'en pense vraiment

Je veux tenir les deux choses à la fois sans m'effondrer dans l'une ou l'autre, parce que je crois que c'est la seule façon honnête de penser ce projet.

BabyGuard est une chose réellement bonne. C'est exactement le genre de projet pour lequel je tiens ce blog : une personne, un vrai problème, un morceau de matériel à quatre dollars, un pipeline DSP que l'on peut lire, comprendre et auditer, et une cause difficile à contester. Le fait qu'un parent puisse désormais construire un moniteur d'apnée sans contact pour le prix d'un café, en open source, sans abonnement ni jardin clos, c'est l'éthique open source qui fonctionne exactement comme annoncé.

Et c'est aussi, simultanément, un avertissement que le monde que nous construisons rend trivialement facile pour n'importe qui de regarder n'importe qui à travers les murs. Ces deux faits ne sont pas en tension. C'est le même fait. La propriété exacte qui rend la technologie *bonne* — passive, invisible, bon marché, traverse les murs — est la propriété qui la rend *dangereuse*. On ne peut pas avoir le moniteur pour bébé sans l'outil de surveillance. Ce sont les mêmes ondes radio, lues par la même puce, traitées par la même FFT.

Alors non, je ne vais pas vous dire de paniquer, et je ne vais pas vous dire que tout va bien. Ce que je vais vous dire, c'est de remarquer le schéma. Tous les quelques mois désormais, un élégant projet open source démontre qu'une capacité qui exigeait autrefois un État-nation tient maintenant dans une plaque d'essai, et nous faisons tous la même danse : admirer l'ingéniosité, ressentir brièvement le frisson des implications, puis retourner à l'hypothèse que nos murs sont solides. Ils ne le sont pas. Ils ne l'ont jamais été. Le signal quitte votre maison depuis toujours. La seule chose qui a changé cette semaine, c'est le prix auquel on peut le lire.

*BabyGuard vit sur [github.com/mohosy/baby-monitor-wifi-csi](https://github.com/mohosy/baby-monitor-wifi-csi). Il s'appuie sur des travaux dont Wi-Sleep, BreathJunior et le framework ESP-CSI d'Espressif — tout ce champ mérite un terrier de lapin, si vous voulez comprendre à quel point votre salon est déjà lisible.*
