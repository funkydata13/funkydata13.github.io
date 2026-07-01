---
title: "🍺 Auberge médiévale : Low-Poly, Texture Caching et le nouveau super-nœud"
date: 2026-07-01 18:00:00 +0200
categories: [PAO]
tags: [blender, cycles, rendering, ia, upscale]
description: "Une scène d'auberge médiévale/fantasy pour crash-tester plusieurs briques techniques d'un coup : peinture UV sur low-poly, upscale IA via chaiNNer, le nouveau Texture Caching de Blender, l'instancing de collections et un éclairage volumétrique bien piégeux."
image:
  path: /assets/img/pao/medieval_inn/Medieval_Inn_002A_HD.png
  alt: "Auberge médiévale - Rendu Blender"
---

Nouvelle scène de PAO, et cette fois-ci ce n'est pas un seul truc à tester mais carrément une pile de six. Le thème : une auberge médiévale/fantasy, avec ses poutres, ses bougies et son ambiance tavernes-de-jeu-de-rôle. Le prétexte visuel est sympa, mais l'objectif réel était clairement technique.

Voici le menu du jour.

---

### 🧱 Low-Poly + peinture UV, retour aux sources

Toute la scène est en low-poly. Pas de sculpt ou de détail géométrique brut : tout repose sur le bon vieux **modificateur de Subdivision**. Le vrai exercice ici, c'était de retravailler la peinture de texture directement sur ces maillages légers, avec des UV proprement dépliés pour que la subdivision ne vienne pas tout casser au niveau des jointures et des angles. Simplement voir si c'était possible car la subdivision subdivise aussi les UVs ce qui peut occasionner nombre de problèmes visuels qu'il faut anticiper.

---

### 🖼️ Upscale x2 via chaiNNer

Considérant la quantité d'objets la définition des textures n'était pas très grande afin de rentrer dans 16 Go de VRAM. Mais pour ce test (et surtout à cause du point suivant) j'ai laché la bride sur la résolution. Je suis passé par **chaiNNer** pour un upscale IA x2.

Le résultat est propre sur ce type de textures : contrairement à l'upscale vidéo (galère absolue, voir mon article sur le Charger '69), l'IA s'en sort beaucoup mieux sur une texture statique, fixe, sans mouvement à reconstruire. Les détails de peinture restent cohérents, pas de bavure ni d'artefact flagrant.

---

### 💾 Le vrai morceau : le Texture Caching de Blender

Voilà le point le plus intéressant de cet article. Avec la densité d'objets de cette scène, la variété d'assets (mobilier, vaisselle, décorations, structure...) et les textures fraîchement upscalées, la VRAM a forcément explosé direct. Ça ne rentrait pas sur 16 Go, pas même sur 24 Go.

C'est là qu'intervient la toute dernière fonctionnalité de Blender : le **Texture Caching**.

Le principe est simple sur le papier : au lieu de charger la totalité de chaque texture en pleine résolution dans la VRAM (même les zones de l'image qui ne sont jamais visibles à l'écran), Blender convertit désormais les textures en **.tx**, un format tuilé et mipmapé (à la OpenImageIO). Concrètement :

* **Le découpage en tuiles (tiles) :** chaque texture est débitée en petits blocs indépendants au lieu d'être une seule grosse image monolithique.
* **Les niveaux de LOD (mipmaps) :** chaque texture existe en plusieurs résolutions empilées (comme une pyramide), de la pleine définition jusqu'à des versions minuscules.
* **Le streaming à la demande :** le moteur ne charge en VRAM que les tuiles et le niveau de LOD réellement nécessaires selon la distance caméra, l'angle et la taille à l'écran. Un pot en arrière-plan flou n'a aucune raison de bouffer sa texture en 4K native.

Résultat : la scène qui ne passait pas sur 24 Go tourne désormais avec **environ 8 Go de VRAM**. C'est une bascule complète de philosophie, dans le même esprit que ce qui se fait dans le jeux vidéos avec le .DDS mais appliqué et adapté à la PAO. Ça change clairement la donne pour ce genre de scène chargée en assets uniques.

---

### 🎛️ Le super-nœud de compositing, version quasi-finale

J'utilise et je fais évoluer depuis un moment mon "super-nœud" de compositing maison sous Blender. Il a encore pas mal grandi depuis la dernière fois que j'en ai parlé, et je le sens sérieusement approcher de sa version finale. Cette scène m'a servi de nouveau terrain de test grandeur nature pour vérifier sa robustesse sur un rendu complexe (volumétrie, multiples sources de lumière, matériaux variés).

Je referai probablement un article dédié à ce nœud une fois qu'il sera scellé.

---

### 🪑 Instancing de collections

Avec autant de mobilier et de petits objets répétés (chaises, chopes, tonneaux, bougies...), c'était l'occasion parfaite de pousser l'**instancing de collections**. Au lieu de dupliquer bêtement la géométrie, chaque groupe d'objets est instancié depuis une collection source unique, ce qui garde la scène légère en mémoire tout en permettant des variations rapides (position, rotation, échelle) sans exploser le nombre de vrais meshes.

---

### 🕯️ L'éclairage : le pire scénario possible pour un path-tracer

Dernier test, et pas des moindres : l'éclairage. J'ai volontairement cumulé tout ce qui fait souffrir un moteur en path-tracing :

* Une scène **sombre**, avec très peu de lumière ambiante.
* Une multitude de **petites sources ponctuelles** (les bougies), chacune générant son propre bruit à nettoyer.
* De la **volumétrie** partout pour donner cette ambiance poussiéreuse/enfumée de taverne.

Le combo lumière faible + multi-sources + volumes est probablement l'un des pires cas de figure pour un path-tracer : chaque rayon a statistiquement moins de chances de croiser une source de lumière, ce qui fait exploser le bruit et le temps de convergence. Il a fallu pas mal de samples, beaucoup d'ajustements, et un peu de triche pour arriver à un résultat propre sans perdre l'ambiance tamisée voulue.

---

### 📸 Galerie des rendus

![Auberge médiévale - vue 2](/assets/img/pao/medieval_inn/Medieval_Inn_003A_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/medieval_inn/Medieval_Inn_003A_HD.png) | [QHD (2560)](/assets/img/pao/medieval_inn/Medieval_Inn_003A.png)
{: .text-center }

---

![Auberge médiévale - vue 3](/assets/img/pao/medieval_inn/Medieval_Inn_004A_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/medieval_inn/Medieval_Inn_004A_HD.png) | [QHD (2560)](/assets/img/pao/medieval_inn/Medieval_Inn_004A.png)
{: .text-center }

---

![Auberge médiévale - vue 4](/assets/img/pao/medieval_inn/Medieval_Inn_005A_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/medieval_inn/Medieval_Inn_005A_HD.png) | [QHD (2560)](/assets/img/pao/medieval_inn/Medieval_Inn_005A.png)
{: .text-center }

---

![Auberge médiévale - vue 5](/assets/img/pao/medieval_inn/Medieval_Inn_006A_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/medieval_inn/Medieval_Inn_006A_HD.png) | [QHD (2560)](/assets/img/pao/medieval_inn/Medieval_Inn_006A.png)
{: .text-center }

---

![Auberge médiévale - vue 6](/assets/img/pao/medieval_inn/Medieval_Inn_009_HD.png){: .normal .text-center w="700" }

[HD (1920)](/assets/img/pao/medieval_inn/Medieval_Inn_009_HD.png) | [QHD (2560)](/assets/img/pao/medieval_inn/Medieval_Inn_009.png)
{: .text-center }

---

Beaucoup de cases cochées d'un coup sur cette scène, mais c'était clairement le but : la faire suffisamment dense et complexe pour mettre tout mon pipeline actuel sous pression en même temps. Le Texture Caching à lui seul justifie l'article, ça ouvre clairement la porte à des scènes bien plus chargées sans avoir à sacrifier la VRAM.
