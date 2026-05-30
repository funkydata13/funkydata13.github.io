---
title: "🖥️ Unreal Engine 5 : Tout savoir sur Nanite"
date: 2026-05-27 20:00:00 +0200
categories: [Tech, Graphisme]
tags: [computer-graphics, nanite, unreal-engine, optimisation, geometry]
image:
  path: /assets/img/covers/banner_tech.png
---

## UNREAL ENGINE 5 : Nanite et l'alternative SSDM

`#Dev` `#3D` `#Unreal` `#Analysis`

---

## 1. Nanite, c'est quoi ?

### 1.1 Description vulgarisée

Imagine que tu as une boîte de Lego magique. Jusqu'ici, pour que ton jeu tourne bien, tu devais construire tes châteaux avec de grosses briques grossières dès que le joueur s'éloignait, et ne sortir les petites briques détaillées que s'il collait son nez au mur. C’est le principe du LOD (*Level of Detail*).

Avec Nanite, tu jettes la boîte par terre, le moteur gère tout tout seul et tu gardes juste ton château hyper détaillé en micro-briques. Tu peux importer un modèle avec des millions de polygones (scanné dans la vraie vie ou sculpté en high-poly), et Nanite va adapter la complexité géométrique de l'objet en temps réel, selon sa distance à la caméra.

---

### 1.2 Pourquoi avoir développé Nanite ?

* **S'imposer au-delà du jeu vidéo (Cinéma et Production Virtuelle) :** Epic Games ne vise plus seulement le marché des gamers. Avec l'UE5, l'objectif est de faire du moteur la plateforme 3D universelle pour le cinéma, les séries (comme les tournages sur écrans LED *Volume* type *The Mandalorian*) et l'architecture. Pour séduire Hollywood, il fallait briser le plafond de verre des concessions graphiques propres au jeu vidéo. Nanite a été pensé pour offrir une qualité "cinéma en temps réel", permettant d'importer des assets bruts sans se soucier des contraintes de performance initiales.
* **Le pivot vers un moteur de production globale :** Cette stratégie engendre une ironie partagée par beaucoup de développeurs : l'UE5 est parfois perçu comme un logiciel de production cinématographique qui, par extension, est capable de sortir des jeux. Ce n'est plus "juste" le moteur 3D de jeu vidéo qu'il était. Nanite incarne cette philosophie en privilégiant le "zéro friction" artistique et réduction des coûts plutôt que le souci de la performance et de l'optimisation propre au domaine du jeu vidéo.
* **La promesse d'une facilité d'accès universelle :** L'industrie du JV passait une part immense de son temps et de son budget sur des tâches d'optimisation purement techniques (retopologie, création de LODs, baking). On parle de 30% environ. En automatisant tout cela via Nanite, Epic démocratise la création d'environnements ultra-réalistes rapidement et beaucoup moins cher : n'importe quel petit studio ou créateur solo peut "jeter" des assets Quixel high-poly ou des scans 3D dans sa scène et obtenir un visuel convaincant instantanément, sans avoir besoin d'une armée de graphistes ou d'ingénieurs pour le soutenir.
* **Le look "Next-Gen" standardisé :** Pour imposer son moteur comme la référence absolue de la génération PS5 / Xbox Series / PC modernes, Epic avait besoin d'une vitrine technologique qui marque une rupture visuelle nette avec l'UE4. Nanite s'inscrit avec Lumen dans cette logique de rupture, et force indirectement toute l'industrie à s'aligner sur les standards graphiques d'Epic et *in fine* à adopter son moteur devant l'énorme travail à fournir pour atteindre ce niveau d'exigence visuelle.

---

### 1.3 Description technique - Fonctionnement interne

Techniquement, Nanite est un système de **géométrie virtualisée**. Lors de l'import d'un mesh, le système va analyser le modèle et le découper en **clusters** (des petits groupes de triangles, généralement 128 triangles par cluster).

Au lieu de passer par le pipeline graphique traditionnel du GPU (qui galère quand les triangles deviennent plus petits qu'un pixel), Nanite utilise son propre **générateur de rendu logiciel (Software Rasterizer)** ultra-rapide via des *Compute Shaders*, et ne bascule sur le rendu matériel classique que pour les gros triangles proches. Il construit un arbre hiérarchique de ces clusters pour charger instantanément le niveau de détail parfait pour chaque pixel de l'écran.

Pour comprendre la rupture technologique que représente Nanite, il faut d'abord identifier le goulot d'étranglement (*bottleneck*) du pipeline graphique traditionnel. Classiquement, le processeur (CPU) doit envoyer des ordres de dessin (*Draw Calls*) à la carte graphique (GPU) pour chaque objet ou variations de l'objet (LOD). Si une scène contient trop d'objets ou des géométries extrêmement denses, le CPU sature et ne suit plus le rythme. De plus, les architectures GPU actuelles gèrent très mal les triangles trop petits et notamment ceux dont la taille est égale ou inférieure à celle d'un pixel à l'écran, ce qui engendre un gaspillage massif de ressources de calcul (*sub-pixel shading*).

Nanite contourne cette limite historique en appliquant à la géométrie le concept de **virtualisation**, une approche philosophiquement proche du fonctionnement des *voxels* ou des textures virtuelles (*MegaTextures*), où la géométrie est traitée comme un flux continu de données dynamiques plutôt que comme une liste d'objets statiques.

* **La structure en Clusters :** Lors de l'importation d'un *mesh*, Nanite segmente la géométrie en sous-ensembles rigides appelés **clusters**, contenant chacun un groupe fixe de **128 triangles**. Ces clusters sont ensuite organisés au sein d'une structure hiérarchique en arbre (un graphe orienté acyclique, ou *DAG*). C'est ce cluster, et non plus le triangle individuel ou l'objet complet, qui devient la primitive de base du moteur.
* **Le double pipeline de rendu (Software vs Hardware) :** Au lieu d'envoyer ces millions de triangles dans le pipeline de rastérisation classique du GPU, Unreal Engine 5 utilise un système hybride piloté par des **Compute Shaders** :
    * **Rastérisation Logicielle (Software Rasterizer) :** Lorsque les triangles d'un cluster sont plus petits qu'un pixel à l'écran (objets lointains ou micro-détails), Nanite court-circuite le pipeline matériel standard. Un *Compute Shader* optimisé prend le relais pour dessiner mathématiquement les clusters directement dans un tampon de visibilité (*Visibility Buffer*). Cela permet de traiter des milliers de clusters en un minimum d'appels, sans surcharger le GPU.
    * **Rastérisation Matérielle (Hardware Rasterizer) :** Dès que le joueur s'approche d'un objet et que ses triangles deviennent plus grands, Nanite bascule automatiquement et de manière transparente vers les unités de calcul matérielles classiques de la carte graphique, qui redeviennent alors plus efficaces.

> **En résumé :** Nanite s'affranchit du concept traditionnel de "un objet = un draw call". Le moteur analyse globalement la scène à l'écran, détermine précisément quels clusters de 128 triangles sont visibles à la granularité du pixel, et les distribue dynamiquement dans le pipeline de rendu le plus adapté. Le niveau de détail s'ajuste ainsi de manière continue, sans paliers visibles pour l'utilisateur.

---

### 1.4 Avantages pour le jeu, les graphistes et les devs

* **Pour les graphistes (Artistes 3D) :** C'est la liberté absolue. Plus besoin de passer des heures à faire de la "retopologie" (alléger un modèle lourd), plus besoin de générer 5 versions *LOD* différentes d'un même arbre, et fini le baking complexe de Normal Maps pour feinter le détail. Tu balances ton modèle High-Poly de ZBrush ou Quixel directement dans le moteur.
* **Pour les devs :** Gain de temps colossal sur l'optimisation de la géométrie et la gestion de la mémoire *streaming*. Fini les budgets de polygones stricts par scène.
* **Pour le jeu :** Une fidélité visuelle inégalée et une distance d'affichage théoriquement infinie. Nanite supprime par nature le phénomène de "pop-in" (qui, sur les systèmes classiques, découle souvent d'une mauvaise implémentation ou d'une machine à la peine) ou de clipping sur la distance puisque la géométrie s'adapte désormais de manière totalement continue et invisible au pixel près.

---

### 1.5 Inconvénients pour le jeu et les performances

* **Un gouffre à calcul :** Nanite n'est absolument pas gratuit. Faire tourner des *Compute Shaders* en continu pour trier et rastériser des millions de clusters à la volée est bien plus lourd pour le GPU qu'un système de LOD classique (où la carte graphique se contente de dessiner des meshes déjà simplifiés).
* **L'overdraw généralisé :** L'overdraw, c'est quand le GPU doit calculer plusieurs fois le même pixel à l'écran parce que des couches de géométrie se superposent. Avec des clusters de triangles très petits imbriqués, l'overdraw explose et le GPU sature complètement à essayer de deviner ce qui est visible ou non et doit écrire plusieurs fois le même pixel.
* **L'effet domino des dépendances :** Activer Nanite t'oblige à utiliser les autres technos "Next-Gen" de l'UE5. Le meilleur exemple, c'est les **VSM (Virtual Shadow Maps)**. Les ombres classiques ne fonctionnent pas avec la géométrie Nanite. Problème : les VSM coûtent une blinde en performance et viennent plomber encore le framerate, simplement parce que tu as activé Nanite. De même, Epic conseille fortement d'utiliser Lumen avec Nanite, ce qui ajoute encore une dépendance très coûteuse en ressources.
* **L'explosion du stockage et de la VRAM :** Même compressés, des modèles à plusieurs millions de polygones pèsent lourd sur le disque dur (les jeux dépassent vite les 100 Go) et demandent un streaming ultra-rapide depuis un SSD NVMe.
* **Pas adapté à tout le monde :** Nanite tolère encore assez mal la transparence, le feuillage dense (à cause de l'overdraw) et la déformation de géométrie (les personnages qui bougent). L'utiliser sur ce type d'assets demande des concessions ou des bidouilles d'optimisation complexes.
* **Des plateformes restreintes :** Nanite est gourmand, que ce soit intrinsèquement ou via ses dépendances. Utiliser Nanite sur son projet UE5, c'est limiter les plateformes supportées. Seuls le PC, la PlayStation 5 et les Xbox Series X/S sont supportés de manière stable. Les consoles de la génération précédente (comme la PS4) ont un support expérimental peu convaincant. Pour toutes les autres plateformes (mobiles, Android, casques VR, etc.), l'export est impossible et elles ne peuvent pas en profiter.

---

## 2. Plus en détails

### 2.1 La "maladie" de Nanite : l'overdraw

L'**overdraw** (la surimpression), c'est le fait que le GPU doive calculer et écrire plusieurs fois les données pour un même pixel à l'écran parce que plusieurs éléments se superposent dans l'espace 3D (par exemple, dix feuilles d'arbre transparentes ou des couches de feuillage les unes derrière les autres).

Avec Nanite, le problème change d'échelle et devient une vraie maladie à cause de la nature même du système :

* **La surmultiplication des micro-triangles :** Comme Nanite découpe tout en micro-clusters, tu te retrouves avec une grosse densité géométrique. Des micro-triangles s'imbriquent et se superposent, et le GPU passe un temps fou à recalculer et réécrire sans arrêt "par-dessus" les mêmes pixels.
* **Le cauchemar des masques d'opacité (Feuillage et Herbe) :** Quand tu utilises des masques d'opacité (des textures avec de la transparence pour découper la forme d'une feuille ou d'un brin d'herbe), Nanite perd son efficacité à partitionner l'espace. Le moteur doit tester la transparence pixel par pixel à travers des dizaines de couches de micro-géométrie.
* **Le verdict du *Quad Overdraw* :** Cette surcharge met instantanément à genoux le pipeline. Dans les outils de debug de l'UE5, la vue *Quad Overdraw* (qui colore la scène du bleu au rouge/blanc selon la gravité du problème) vire direct au rouge écarlate. Le coût de calcul explose, le framerate s'effondre, et cela ruine complètement les performances de tout le système.

#### Où est-ce que ça coince ?

À l'échelle des blocs de 128 triangles, Nanite est ultra-efficace. Avant même de dessiner quoi que ce soit, le moteur fait un **Two-Pass Occlusion Culling** (en gros, un double tri) sur le GPU. Il utilise les données de la frame précédente pour construire un Z-Buffer à basse résolution. Si un cluster est entièrement caché derrière un mur, **il est éjecté direct**. À ce niveau-là, le Z-Buffer fait parfaitement son boulot d'exclusion.

Le vrai problème se situe au niveau du *sub-pixel* et des quads de pixels. Une carte graphique ne calcule jamais les pixels un par un. Elle les traite par blocs de 2x2 pixels (un **Quad**). Pourquoi ? Pour pouvoir calculer les variations de textures (les dérivés) entre les pixels voisins.

C'est de là que vient le **drame du micro-triangle** : avec Nanite, si c'est loin et géométriquement dense, un triangle va être tellement minuscule qu'il ne va faire qu'**un seul pixel** à l'écran. Pour dessiner ce pauvre triangle d'un seul pixel, le GPU est obligé d'activer et de calculer le quad entier de **4 pixels** contenant ce fameux pixel. Les 3 autres pixels du bloc de calcul sont calculés puis, purement et simplement, jetés à la poubelle (*Helper Pixels*). Et *bis repetita* pour le pixel à côté. Pour calculer le quad complet (si on a des triangles de 1 pixel), on aura calculé 16 pixels en tout, soit 4 fois plus de pixels.

En plus, si on a par exemple 5 couches de feuilles transparentes ou des brins d'herbe les uns derrière les autres sur une zone dense, le Z-Buffer classique ne peut pas exclure les couches de derrière, car la zone est  transparente (masques d'opacité). Le GPU est obligé de lire la texture pour savoir si c'est transparent ou non et ne peut pas trier en amont. Chaque couche va forcer le GPU à lancer des calculs sur des Quads entiers (2x2) pour de simples micro-triangles sur des zones de jeu entières. Le GPU passe alors son temps à mettre à la poubelle 3 pixels calculés sur 4 et à devoir calculer 4 fois le même quad pour avoir les 4 pixels le composant correctement affichés.

---

### 2.2 Comment faire sans Nanite sans perdre en qualité visuelle ? Le Screen Space Displacement Mapping (SSDM) comme alternative

Pour les studios qui refusent de s'enfermer dans l'écosystème Unreal ou de subir les lourdes contreparties de Nanite (poids des jeux, surconsommation de VRAM, surcoût des Compute Shaders), il existe une autre voie. L'alternative moderne la plus crédible pour obtenir une fidélité graphique "Next-Gen" sans géométrie virtualisée, c'est le **SSDM**.

* **L'exemple *Crimson Desert* :** Le jeu de Pearl Abyss est une "masterclass" en la matière. Plutôt que de charger le moteur avec des milliards de polygones, les développeurs utilisent un pipeline de LODs classique mais appliquent du SSDM sur quasiment tous les objets et surfaces du monde (les rochers, les murs, les sols boueux, les troncs d'arbres). Le résultat visuel rivalise avec Nanite, tout en gardant une structure de mesh traditionnelle bien plus légère.
* **Le principe technique :** Contrairement à l'ancienne tessellation (qui subdivisait de vrais polygones dans le moteur et cassait les performances), le SSDM est une technique de rendu de surface basée sur l'écran (*Screen Space*). Le moteur utilise des textures de hauteur (*Heightmaps*) et calcule le relief et l'éclairage directement au moment du rendu des pixels.

#### Les limites et points négatifs du SSDM :

Malgré ses avantages en termes de stockage et de streaming, le SSDM traîne plusieurs casseroles techniques importantes :

* **La dépendance à l'espace écran (*Screen Space*) :** Comme son nom l'indique, le calcul se fait uniquement sur ce qui est visible à l'écran. Si une surface texturée sort du cadre ou est masquée par un premier plan, le moteur n'a plus les données nécessaires. Cela peut provoquer des coupures nettes du relief ou des déformations invisibles hors champ. De même, le relief a tendance à "baver" ou à s'étirer bizarrement sur les bords extrêmes de l'écran à cause de la distorsion de la caméra (perspective).
* **Contraintes artistiques et aspect "délavé" :** Le système demande des *Heightmaps* ultra-détaillées pour fonctionner proprement. En contrepartie, les artistes sont obligés d'utiliser des textures d'Albedo (la couleur de base) relativement neutres et peu contrastées. Si l'Albedo contient trop de fausses ombres ou de contrastes forts, cela court-circuite l'effet de relief calculé par le SSDM. C'est exactement ce qui donne cet aspect un peu "délavé" ou "terne" aux surfaces de *Crimson Desert* sous certains angles.
* **Une illusion purement visuelle :** Physiquement, la surface reste désespérément plate. Si un personnage marche sur un sol en pavés magnifiquement bombé en SSDM, ses pieds vont flotter ou s'enfoncer de manière linéaire. La physique du jeu (collisions, débris, interactions) traite l'objet comme un cube ou un plan lisse, ce qui peut briser l'immersion.
* **Le problème des silhouettes :** C'est le talon d'Achille de la technique. Si tu regardes un mur ou un rocher de face, le relief est bluffant. Si tu te décales pour aligner le bord de l'objet avec l'arrière-plan (vue de silhouette), le contour redevient parfaitement rectiligne et plat, trahissant immédiatement l'illusion.
* **Artefacts de mouvement (*Ghosting*) :** Lors des mouvements de caméra ultra-rapides, le calcul du *Screen Space* peut accuser un léger retard d'une image à l'autre, ce qui crée parfois du flou, du scintillement ou des traînées visuelles indésirables sur les micro-reliefs.

> #### Ce qui conclut cet article, en espérant que vous en savez un peu plus sur le sujet, je vous dis à la prochaine ! 😉
