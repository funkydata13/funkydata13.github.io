---
title: "🚀 Fin d'un chantier de 20 ans : Mon Imperial Star Destroyer sous Blender"
date: 2026-06-09 20:00:00 +0200
categories: [PAO]
tags: [blender, cycles, rendering, star-wars, geometry-nodes, kitbash]
description: "Après presque deux décennies d'allers-retours, de 3ds Max à Blender, le Destroyer Stellaire de 1,6 km est enfin prêt pour la production."
image:
  path: /assets/img/pao/isd/Imperial_Star_Destroyer_02_HD.png
  alt: "Imperial Star Destroyer - Rendu Blender"
---

Après avoir traîné ce projet dans mes archives pendant près de deux décennies, je peux enfin le dire : mon **Imperial Star Destroyer** est terminé. Le chantier a été long, parfois laborieux, mais c'est fait.

Pour l'anecdote, les toutes premières ébauches du modèle datent de 2004, sous 3D Studio Max à l'époque. Mais soyons franc : la construction a véritablement démarré en 2020 avec mon passage du projet sous  Blender, et ce pour plusieurs raisons.

### 📐 Le problème de l'échelle : 1,6 km et 4 000 Meshes

Ce genre de vaisseau pose un défi paradoxal. Géométriquement, la forme globale est plutôt simple. En revanche, le modèle est gigantesque (1,6 km à l'échelle réelle) et composé de presque **4 000 meshes**. C'est le royaume absolu du *kitbashing*. 

Sur 3ds Max, l'interface et la gestion des raccourcis rendaient l'exercice pénible. De plus, le résultat visuel ne me convenait pas. Pour faire les choses dans les règles de l'art sans outils adaptés, il aurait fallu une patience infinie pour coller des petits cubes et des cylindres pendant des semaines. Je fais de la 3D pour le plaisir, pas pour y passer des journées entières de manière industrielle. Le projet est donc resté très longtemps en sommeil, avec de rares sessions de modélisation de temps en temps.

---

### 🛠️ Le déclic : Geometry Nodes et Shaders procéduraux

Après un gros coup de collier en 2022 et une première vidéo courte, je me suis à nouveau retrouvé bloqué par des contraintes techniques majeures : la densité des détails de la coque, le manque de flexibilité des premiers Geometry Nodes, et surtout l'enfer du **baking de textures**.

Dans sa première mouture, le modèle nécessitait pas moins de **125 textures bakées** pour couvrir sa surface. Le moindre déplacement d'un élément impliquait de refaire le baking de la zone, de retoucher les UVs et de mettre à jour les Shaders. Un workflow lourd et impossible à maintenir.

Le projet s'est débloqué récemment grâce à deux évolutions majeures de Blender :
1. **La puissance actuelle des Geometry Nodes :** Beaucoup plus matures, modulables et pertinents visuellement pour générer de la micro-détention de surface à cette échelle.
2. **Les matériaux complexes à la volée :** La possibilité de s'affranchir totalement du baking en générant des textures procédurales avancées en temps réel.

---

### 📸 Galerie des rendus (Cyclorama)

![Modèle vu de face](/assets/img/pao/isd/Imperial_Star_Destroyer_01_HD.png){: .normal .text-center w="700" }  
[HD (1080p)](/assets/img/pao/isd/Imperial_Star_Destroyer_01_HD.png) \| [UHD (4K)](/assets/img/pao/isd/Imperial_Star_Destroyer_01_UHD.png){: .text-center }

---

![Modèle vu de 3/4 face](/assets/img/pao/isd/Imperial_Star_Destroyer_02_HD.png){: .normal .text-center w="700" }  
[HD (1080p)](/assets/img/pao/isd/Imperial_Star_Destroyer_02_HD.png) \| [UHD (4K)](/assets/img/pao/isd/Imperial_Star_Destroyer_02_UHD.png){: .text-center }

---

![Modèle vu de côté](/assets/img/pao/isd/Imperial_Star_Destroyer_03_HD.png){: .normal .text-center w="700" }  
[HD (1080p)](/assets/img/pao/isd/Imperial_Star_Destroyer_03_HD.png) \| [UHD (4K)](/assets/img/pao/isd/Imperial_Star_Destroyer_03_UHD.png){: .text-center }

---

![Modèle vu de 3/4 arrière](/assets/img/pao/isd/Imperial_Star_Destroyer_04_HD.png){: .normal .text-center w="700" }  
[HD (1080p)](/assets/img/pao/isd/Imperial_Star_Destroyer_04_HD.png) \| [UHD (4K)](/assets/img/pao/isd/Imperial_Star_Destroyer_04_UHD.png){: .text-center }

---

### 🎬 Prêt pour la production

Évidemment, "finir" un modèle de cette taille est un bien grand mot. On pourrait y passer sa vie, ajuster un équilibre ici, peaufiner un matériau là. Je reste d'ailleurs en totale admiration devant les maquettistes de l'époque qui géraient ça physiquement.

Cependant, le niveau de détail et la flexibilité du modèle sont aujourd'hui largement suffisants pour que le vaisseau soit exploitable en production. C'est ce qui compte.

Je vous laisse avec ces quelques rendus du bestiau. Je compte maintenant le mettre en scène dans une ou deux vidéos de présentation. Et pour accompagner tout ça, je vais en profiter pour moderniser mon **TIE Fighter** qui utilise encore l'ancien workflow de baking. Heureusement, l'échelle n'a rien à voir, ça devrait aller beaucoup plus vite !

Je referais peut-être quelques shootings de ce modèle si j'ai le temps et que je trouve des angles intéressants.