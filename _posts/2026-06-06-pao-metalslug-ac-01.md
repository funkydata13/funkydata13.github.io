---
title: "🕹️ Projet Borne d'Arcade Metal Slug : Modélisation & Animation"
date: 2026-06-06 22:00:00 +0200
categories: [3D, Blender]
tags: [blender, arcade, metal-slug, hard-surface, animation]
description: "Modélisation 'from scratch' et animation d'une borne d'arcade thématique Metal Slug autour d'un CRT 32\", avant sa future construction réelle."
image:
  path: /assets/img/covers/metal_slug_ac_image.png
  alt: "Borne d'Arcade Metal Slug - Rendu Blender"
---

Pour préparer un futur projet de construction réelle, je me suis lancé dans la modélisation complète, puis l'animation d'une borne d'arcade dédiée à l'univers de **Metal Slug** sous Blender.

### 📐 Spécifications et conception du meuble

Le modèle a été entièrement pensé et dimensionné autour d'un écran cathodique **CRT de 32 pouces**. La borne fait **2 mètres de haut**.

* **Panel de contrôle :** La version finale sera équipée d'un double stick avec 6 boutons par joueur. Pour la vidéo de présentation, je n'en ai intégré qu'un seul.
* **Teinte et esthétique :** J'hésite encore sur la nuance exacte du support. Je souhaite rester fidèle aux tons militaro-cartoon de la licence *Metal Slug*, mais j'aimerais essayer un max de coloris.
* **Système Audio :** Deux haut-parleurs de 6,5 pouces sont intégrés juste au-dessus de l'écran. Suivant les retours des habitués de l'arcade ou des audiophiles, j'ajouterai peut-être des tweeters à côté.
* **Le cœur du système :** C'est très probablement un **Raspberry Pi 5 couplé à MAME** qui fera tourner le tout. Ma Neo-Geo AES d'origine a aujourd'hui bien trop de valeur pour que je me risque à la bidouiller directement pour ce projet !

---

### 🎬 Animation et mise en situation

L'animation ci-dessous est un petit plaisir personnel pour mettre le modèle en situation. Le rendu reste brut et volontairement simple, sans post-production poussée. À regarder en 4K (réalisé sans IA), et pardon pour l'ignoble compression YouTube ! Le rendu est un peu plus propre dans cette résolution, mais ça reste une énorme boucherie sur ce genre de vidéos.

{% include embed/youtube.html id="_JJLtd6yaDU" %}

---

### 🤖 Prochaine étape : Le crash-test de l'IA (VFI + Upscaling)

Le workflow risque de devenir intéressant puisque je prévois de passer cette animation à la moulinette IA. 

Le défi sera de taille car la scène accumule les cas complexes :
1. Un ratio d'image et un format inhabituels.
2. Une composition visuelle très éloignée de ce sur quoi les IA sont entraînées.
3. La simulation (exagérée) du rendu du tube CRT.
4. La capture du jeu en *Pixel Art* affichée sur ce même CRT.

On verra comment l'IA va s'en dortir. **Un article technique dédié à ce sujet arrivera donc prochainement, stay tuned !**

---

### 💡 La note bonus : Le saviez-vous ?

Pour l'anecdote historique, **Metal Slug n'est pas une création originale de SNK !** Le jeu a initialement été développé par **Nazca**, un studio fondé par des anciens de chez *Irem* (les génies derrière *In the Hunt* et *GunForce II*, dont on ressent d'ailleurs immédiatement la filiation artistique). 

Face à l'immense succès critique, à la qualité d'animation ahurissante du titre et à son statut de véritable "machine-seller", SNK a rapidement racheté l'intégralité du studio Nazca. C'est ainsi que *Metal Slug* a été intégré à la marque, pour devenir la licence la plus emblématique de SNK en dehors de ses célèbres jeux de combat.