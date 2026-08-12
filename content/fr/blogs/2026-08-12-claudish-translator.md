---
title: "Arrête de parler Claudish, Claude — un plugin qui traduit la verbosité des IA en langage clair"
description: "Un plugin open-source pour Claude Code utilise Gemma 4 et Ollama en local pour transformer les pavés 'Je serais ravi de vous assister' en 'Voilà la solution'. L'ironie est délicieuse, et la philosophie derrière l'outil est plus importante que l'outil lui-même."
date: 2026-08-12
translationKey: "2026-08-12-claudish-translator"
image: "/images/2026-08-12-claudish-translator.svg"
tags:
  - "claude"
  - "ia"
  - "llm"
  - "open-source"
  - "outils"
mathjax: false
---

## Le problème que tout le monde ressent mais que personne ne nomme

Il y a un moment dans chaque session Claude Code où j'ai envie de jeter mon laptop par la fenêtre. Ce n'est pas quand Claude se trompe — les erreurs, ça se corrige. C'est quand Claude a *raison* mais prend trois paragraphes de politesse performative pour le dire.

« Je serais ravi de vous assister avec cette requête. Permettez-moi d'abord de clarifier quelques points importants concernant votre codebase afin de vous fournir la réponse la plus utile possible. Je constate que vous travaillez avec un script Python qui... »

Frère. Je t'ai demandé d'ajouter un try-catch. Ajoute. Le. Try-catch.

Cette semaine, la communauté r/ClaudeAI a explosé autour d'un plugin qui résout exactement ce problème. Et la solution est si magnifiquement ironique que je dois en parler.

## Le plugin : une deuxième IA pour corriger la première

Voici le principe. Quelqu'un (le post original crédite un développeur sur r/ClaudeAI dont j'ai passé une heure à scruter le profil GitHub) a construit un plugin open-source pour Claude Code qui fait une seule chose : il intercepte la sortie de Claude, la passe à travers Gemma 4 qui tourne en local via Ollama, et renvoie le même contenu — mais en langage clair, serré, sans bullshit.

Le plugin ne modifie pas le comportement de Claude. Il ne change pas le prompt. Il n'altère pas le modèle sous-jacent. C'est un filtre de post-traitement. Claude écrit ce que Claude écrit. Gemma 4 le réécrit. Tu vois la réécriture.

Le fil a atteint 1 981 upvotes avec un ratio de 97%. Ça, ce n'est pas « les gens aiment bien ». C'est « ça comble un vide que personne n'a correctement reconnu ».

## Pourquoi le « Claudish » est un vrai problème

J'utilise Claude depuis les débuts de Claude 2. La verbosité a toujours été là, mais elle est devenue *plus* prononcée à chaque release, pas moins. Claude 4 est le pire. Ce n'est pas que le modèle est mauvais — c'est sans doute le meilleur assistant de code disponible. C'est qu'Anthropic semble constitutionnellement incapable de laisser Claude parler comme un être humain normal.

À quoi ressemble le Claudish ? Voici des exemples authentiques de mes propres sessions :

**Ce que je demande :** « corrige la race condition dans le cache »

**Ce que Claude répond :** « Je vous remercie de porter cela à mon attention. Les conditions de concurrence dans les couches de cache sont un sujet fascinant et important en ingénierie des systèmes distribués. Je vais aborder cela systématiquement en examinant d'abord l'implémentation actuelle du cache pour comprendre les patterns d'accès concurrents, puis je proposerai une solution qui maintient la sécurité des threads tout en préservant les caractéristiques de performance que vous avez soigneusement conçues. Permettez-moi de vous guider étape par étape... »

**Ce dont j'avais besoin :** « Voici un mutex. Ligne 47. »

Ce n'est pas juste agaçant. Ça a des conséquences réelles :

1. **Ça détruit la lisibilité rapide.** Quand Claude lâche 400 mots avant d'arriver au code, je ne peux pas évaluer rapidement si la solution est correcte. Je dois lire tout l'essai.

2. **Ça gonfle la fenêtre de contexte.** Dans une longue session de code, 40% des tokens de la conversation sont de la politesse. Des tokens que tu payes. Des tokens qui grignotent ton budget de contexte.

3. **Ça érode la confiance.** Quand chaque réponse sonne comme écrite par un consultant en relations publiques, tu commences à te demander : est-ce que le modèle est vraiment confiant dans cette solution, ou est-ce qu'il emballe juste son incertitude dans de la chaleur corporate ?

4. **C'est épuisant.** Après 6 heures de code, lire « Je serais ravi de » pour la 47ème fois, ça ne ressemble plus à de la politesse. Ça ressemble à du gaslighting.

## L'architecture technique qui rend ça possible

L'architecture du plugin est astucieuse dans son minimalisme :

```
Claude Code → [intercepter la sortie] → Gemma 4 (Ollama, local) → [réécrire] → terminal
```

C'est tout. Pas d'API cloud. Pas d'abonnement. Aucune donnée ne quitte ta machine. Gemma 4 tourne entièrement en local via Ollama, ce qui signifie que l'étape de traduction est privée, rapide (moins d'une seconde sur un GPU décent), et gratuite.

Pourquoi Gemma 4 spécifiquement ? Le développeur l'a choisi pour trois raisons : c'est assez léger pour tourner à côté de Claude sans bouffer toute ta VRAM, c'est bon en résumé sans halluciner du nouveau contenu, et c'est un des rares modèles capables de faire du « transfert de style » de manière fiable — préserver la précision technique tout en changeant le ton. Tu pourrais le remplacer par Llama ou Mistral ou autre chose, mais Gemma 4 tape dans le sweet spot vitesse/qualité.

Le plugin est open-source, évidemment. Licence MIT. Le repo a des instructions pour le configurer avec le système de plugins de Claude Code, qui est rafraîchissant de simplicité : tu poses un fichier, tu config un hook, c'est fait.

## La couche philosophique : pourquoi ça compte au-delà de l'outil

Je n'arrête pas de penser à ce que ce plugin *signifie*. Ce n'est pas juste un utilitaire. C'est une critique de toute l'industrie des assistants IA, implémentée en code qui marche.

Pense à la chaîne : Anthropic dépense des millions pour entraîner Claude à être « utile, inoffensif et honnête ». Ce processus de RLHF intègre un ton qu'Anthropic juge approprié — poli, déférent, exhaustif. Les utilisateurs détestent ça. Alors la communauté construit un outil qui utilise le modèle d'une *autre entreprise* (Gemma de Google) pour défaire les choix de ton soigneusement élaborés par Anthropic.

C'est de l'alignement IA à l'échelle micro, et c'est complètement inversé par rapport à ce que les labos avaient prévu. Anthropic a aligné Claude pour qu'il soit poli. Les utilisateurs l'ont réaligné pour qu'il soit direct, en utilisant une autre IA comme outil.

Si j'étais product manager chez Anthropic, ça m'empêcherait de dormir. Pas parce que le plugin existe — parce que le ratio d'approbation de 97% me dit que j'ai fondamentalement mal compris ce que veulent mes utilisateurs.

## L'angle local-first

Le plugin fait tourner Gemma 4 en local. Ça compte plus que tu ne le penses.

En 2026, l'industrie de l'IA pousse à fond vers le tout-cloud. Claude est cloud-only. ChatGPT est cloud-only. Même le tier « gratuit » de Mistral téléphone à la maison. Le postulat est clair : l'inférence IA est trop lourde pour le matériel local, donc tu utiliseras nos serveurs, et on respectera (probablement) ta vie privée.

Le plugin traducteur de Claudish rejette ce postulat. Il dit : j'ai déjà un GPU. Je peux faire tourner un petit modèle en local. L'étape de traduction — la partie qui nettoie la sortie — n'a pas besoin d'un modèle frontier. Un modèle de 9 milliards de paramètres qui tourne sur ma propre machine est largement suffisant pour transformer « Je serais ravi de vous assister » en « Fait ».

C'est la même philosophie derrière Unsloth Desktop, qui a lancé cette même semaine sur r/selfhosted. Les gens en ont marre d'envoyer tout dans le cloud. Ils veulent de l'IA qui tourne selon leurs conditions, sur leur matériel.

## Les objections « mais pensez aux enfants »

Le fil a eu son lot de critiques prévisibles. Abordons-les honnêtement.

**« Tu ajoutes de la latence à chaque réponse. »** Vrai, mais exagéré. Gemma 4 en local sur un GPU moderne traite quelques centaines de tokens en moins de 300ms. Claude prend déjà 2 à 10 secondes pour générer une réponse. Les 300ms supplémentaires sont invisibles.

**« Et si Gemma lâche des détails techniques dans la réécriture ? »** Inquiétude légitime. Le plugin utilise un prompt soigneusement calibré qui insiste sur la préservation du code, des termes techniques et des détails spécifiques. Le développeur affirme n'avoir jamais perdu d'information critique dans ses tests, mais je voudrais vérifier ça sur plusieurs semaines d'usage quotidien avant de faire confiance aveuglément.

**« C'est juste un exemple de plus de bloat IA — pourquoi ne pas utiliser un autre modèle ? »** Parce que Claude est vraiment le meilleur pour coder, et je ne veux pas sacrifier la qualité du code juste pour éviter la verbosité. Le plugin me permet d'avoir les deux : la qualité de raisonnement de Claude avec la qualité d'écriture de Gemma.

**« Anthropic devrait juste ajouter un curseur de concision. »** Ils devraient. Mais ils ne l'ont pas fait. La communauté a construit la solution parce que le vendeur ne l'a pas fournie.

## Ce que je pense vraiment

J'ai installé ce plugin hier. Je l'ai utilisé pendant environ 4 heures de code. Voici mon évaluation honnête :

Ce n'est pas parfait. Parfois Gemma 4 sur-compresse et je perds une nuance — un « mais considère le cas limite où... » que Claude avait glissé entre deux couches de politesse. J'ai appris à garder la sortie originale de Claude visible dans un buffer en arrière-plan, au cas où.

Mais ces cas limites arrivent peut-être 10% du temps. Les 90% restants, c'est un soulagement pur. Claude dit « Je serais ravi de vous assister » pour la 48ème fois, et mon terminal affiche « Voici le correctif : » suivi immédiatement d'un bloc de code. C'est comme si quelqu'un avait retiré le coton de mon écran.

La vraie valeur n'est pas technique. Elle est émotionnelle. Après 4 heures de code, je suis moins fatigué. Moins irrité. Moins susceptible de fermer mon laptop et d'aller faire autre chose. Le plugin retire une couche de friction cognitive dont je n'avais pas réalisé à quel point elle me drainait, jusqu'à ce qu'elle disparaisse.

## Ce que ça dit sur le futur de l'UX des IA

Le traducteur de Claudish est un canari dans la mine de charbon. Il signale que les utilisateurs commencent à traiter les sorties d'IA comme de la matière première, pas comme des produits finis. Claude génère. Gemma raffine. Je consomme. L'IA n'est plus le point final — c'est une étape dans un pipeline.

Cette approche en pipeline va devenir la norme. On le voit déjà avec les workflows agentiques : un modèle planifie, un autre exécute, un troisième révise. Le traducteur de Claudish ajoute une nouvelle étape : le transfert de style. Et le transfert de style compte parce que *comment* une IA dit quelque chose affecte ce que tu en fais.

Une IA verbeuse fait de toi un lecteur passif. Une IA directe fait de toi un constructeur actif. Je sais lequel je veux être.

---

Le GitHub du plugin est encore petit — quelques centaines d'étoiles — mais je parierais de l'argent réel qu'il atteindra 5 000 dans un mois. Pas parce que le code est révolutionnaire. Parce qu'il résout un problème que chaque utilisateur de Claude ressent dans ses tripes, et il le résout avec une élégance qui te fait te demander pourquoi Anthropic ne l'a pas construit eux-mêmes.

Ou peut-être qu'ils ont essayé. Peut-être que la politesse est une fonctionnalité, pas un bug. Peut-être que les gens de la sécurité chez Anthropic croient que le langage déférent réduit le risque que les utilisateurs fassent confiance aveuglément aux sorties de l'IA. Si c'est le cas, la communauté vient de voter avec 1 981 upvotes et un ratio de 97% que le calcul de sécurité d'Anthropic est faux.

Dans tous les cas, je garde le plugin. Ma tension artérielle me remercie.
