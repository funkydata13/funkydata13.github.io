---
title: "🎛️ Mon super-nœud de compositing sous Blender"
date: 2026-07-03 18:00:00 +0200
categories: [PAO]
tags: [blender, cycles, rendering]
description: "Présentation de la structure de mon arbre de compositing maison, divisé entre espace linéaire et espace colorimétrique AgX."
image:
  path: /assets/img/pao/Scifi_bedroom_with_character_01_HD.png
  alt: "Chambre Sci-Fi avec personnage - Rendu Blender avec Compositing"
---

Fin de la création de mon arbre de compositing ! 

Alors attention, on va rester honnête : ça reste du compositing relativement simple, rien de comparable à ce que certains pros font sur des monstres sacrés, ultra chers et spécialisés comme Nuke. Néanmoins, avec ce que permet Blender aujourd'hui, il y a clairement l'essentiel pour sortir faire un compositing simple mais tout à fait correct.

C'est d'ailleurs devenu super intéressant de se pencher sérieusement sur la question pour deux raisons majeures :
1. **Le compositor temps réel** : On peut désormais l'appliquer en direct directement sur le Viewport pendant qu'on bosse.
2. **L'accélération GPU** : Le compositor est maintenant traité par la carte graphique, ce qui booste énormément le traitement et rend le point numéro 1 d'autant plus agréable et fluide.

---

## 📐 Découpage du Workflow : Linéaire vs AgX

Par défaut dans Blender, on choisit un *View Transform* et celui-ci est appliqué tout à la fin de la chaîne de Compositing. Tout le traitement est donc fait en espace Linéaire avant la conversion finale. C'est bien, mais niveau modularité et contrôle, on a vu mieux. 

De mon côté, j'ai préféré opter pour une autre logique en coupant mon flux de travail en deux grosses étapes bien distinctes.

### Étape 1 : Les traitements en espace Linéaire
C'est la partie appliquée par défaut sur les données brutes du rendu. J'y ai regroupé différentes passes et effets optiques ou géométriques :
* **Denoiser tweaké** : Un petit ajustement maison pour tenter d'améliorer le nettoyage du bruit, notamment sur les passes de volumétrie.
* **Effets de Glare** : Éclats lumineux, halos et tout le toutim.
* **Exposition & Auto-Expo** : Réglage global et une tentative de module d'auto-exposition dynamique.
* **Atmosphère & Faux Volumes** : Un nœud pour simuler la profondeur atmosphérique. C'est un genre de *Mist Pass* amélioré, disons le brouillard du pauvre mais ultra efficace.
* **Contraste & Courbes de niveaux** de base.
* **Boost des couleurs** et balance des blancs.
* **Triptyque Linéaire** : L'inévitable balance des couleurs via les réglages *Offset*, *Power* et *Slope*.
* **Vignette** périphérique.
* **Passage en courbes Logarithmiques**.

### Étape 2 : Le passage dans l'espace AgX
Une fois le *View Transform* AgX activé, on continue la chaîne en appliquant des options directement adaptées à cet espace colorimétrique :
* **Correcteur AgX "Anti-Saumon"** : Un nœud custom qui tente de compenser la fâcheuse tendance de l'AgX à laisser une teinte rose saumon pas très esthétique sur les zones chaudes et sur-exposées ou émettants de la lumière 'chaude'.
* **Réglages fins** : Gestion précise des Noirs, des Ombres, des Lumières et des Blancs.
* **Courbes de niveaux** secondaires.
* **Filtres maison** : *Sharpness* (netteté), Clarté, Blend Négatif et Contraste semi-local (impossible de faire du vrai contraste local pur sous Blender pour le moment, malheureusement).
* **Corrections HSV** par teinte ciblée.
* **Ajustement colorimétrique avancé** : Gestion de la densité, de la saturation globale et de la saturation par rapport à la luminance.
* **Triptyque Non-Linéaire** : La balance des couleurs classique via le *Lift*, *Gamma* et *Gain*.
* **Split Tone** : Pour des effets de style ou d'étalonnage un peu typés ciné, pas indispensables tous les jours mais bien pratiques à avoir sous le coude.
* **Effets de lentille** : Distorsions, aberrations, bruit et grain argentique.

---

## 🪵 Un arbre un peu trop grand pour sa maison

Voilà en gros à quoi ressemble l'arbre, c'est un très gros arbre de nœuds qui s'étale sur plusieurs écrans. 

Pourquoi ne pas l'avoir encapsulé proprement dans un seul et unique *Node Group* me direz-vous ? Tout simplement parce que Blender ne permet pas encore de passer des nœuds de type "Courbes" en tant que paramètres d'entrée d'un groupe. On est donc obligés de tout laisser à plat pour garder la main sur les curseurs graphiques. Je ferais avec en attendant les futures versions !

## 📸 Finalisation sur une 'jeune-vieille' copine 😂

La dernière brique de ce système a été posée et testée sur une scène qui commence à dater un peu, mais que j'ai modernisée pour l'occasion. 

![Personnage Sci-fi](/assets/img/pao/Scifi_bedroom_with_character_01_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/Scifi_bedroom_with_character_01_HD.png) | [QHD (2560)](/assets/img/pao/Scifi_bedroom_with_character_01_QHD.png)
{: .text-center }

En théorie, il faudrait probablement reprendre pas mal de choses en profondeur si je voulais la mettre 'vraiment' au goût du jour, et certains assets trahissent un peu leur âge... Mais bon, elle présente encore très bien je trouve et il me fallait un visuel sympa pour illustrer le blog, donc ça fait parfaitement le job !