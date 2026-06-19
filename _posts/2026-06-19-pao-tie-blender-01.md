---
title: "🚀 Dans la foulée du Destroyer : Modernisation du TIE Fighter sous Blender"
date: 2026-06-19 18:00:00 +0200
categories: [3D, Blender]
tags: [blender, hard-surface, star-wars, geometry-nodes, procedural]
description: "Après le chantier titanesque de l'ISD, place à la mise à niveau du TIE Fighter. Objectif : éradiquer l'ancien workflow de baking au profit du procédural."
image:
  path: /assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_03.png
  alt: "TIE Fighter - Rendu Blender"
---

Comme je l'évoquais en conclusion de l'article sur l'Imperial Star Destroyer, impossible de laisser mes autres assets Star Wars avec une technique de rendu à la traîne. Dans la foulée, j'ai donc ouvert le chantier de modernisation de mon **TIE Fighter**.

Rassurez-vous, l'échelle n'a absolument rien à voir avec le monstre de 1,6 km de la semaine dernière, mais la logique derrière cette mise à jour reste exactement la même.

### ⏳ Un modèle récent, mais une technique obsolète

Contrairement au Destroyer qui traînait ses premières versions depuis 20 ans, ce modèle de TIE Fighter est beaucoup plus récent et propre architecturalement parlant. Le maillage de base tenait permanence la route.

Le vrai problème se situait au niveau des textures. Ce modèle avait été conçu avec mon ancien workflow de **baking de textures**. À l'époque, c'était le passage obligatoire pour obtenir de beaux détails de salissure, d'usure des arêtes et de micro-détails de fonderie. Mais comme on l'a vu pour l'ISD, le baking est un piège : figé, lourd, et infernal à modifier si l'on veut ajuster la géométrie par la suite.

---

### 🛠️ Le même traitement de choc : Géométrie et Procédural

Le TIE Fighter a donc subi une mise à niveau technique complète pour s'aligner sur le pipeline moderne de Blender. Quelques mises à jour au niveau de la géométrie et des proportions, mais surtout : **adieu le Baking**. J'ai entièrement refait les Shaders. Tout est désormais généré à la volée de manière procédurale (effets d'usure, crasse dans les creux, texture du métal peint).

Le modèle est plus léger en VRAM, et peut être modifié à la volée sans avoir à recalculer la moindre texture.

---

### 📸 Galerie des rendus updated

Voici les premiers clichés du chasseur impérial avec son tout nouveau revêtement dynamique. Ce sont deux clichés en "situation", contrairement à l'ISD qui est présenté dans l'espace profond.

![TIE sur une plateforme de près](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_01.png){: .normal .text-center w="700" }
[HD (1920)](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_01_HD.png) \| [QHD (2560)](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_01.png){: .text-center }

---

![TIE sur une plateforme de près](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_03.png){: .normal .text-center w="700" }
[HD (1920)](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_03_HD.png) \| [QHD (2560)](/assets/img/pao/tie_fighter/TIE_Fighter_in_a_Canyon_Base_03.png){: .text-center }

---

### 🎬 Prochaine étape : La mise en scène

Maintenant que l'escorte (le TIE) et le navire amiral (l'ISD) partagent enfin le même pipeline graphique et les mêmes shaders, les assets sont officiellement prêts à être réunis dans le même espace.

Je vais enfin pouvoir me pencher sérieusement sur la création d'une ou deux séquences animées pour mettre en scène tout ce petit monde en orbite.

Affaire à suivre !