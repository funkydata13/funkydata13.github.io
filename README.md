# LE WORKBLOG DE FUNKYDATA
Bienvenue sur ce workblog !
---

<p align="left">
   🌐 <b>Navigation rapide :</b> 
   <a href="https://github.com/funkydata13">GitHub</a> |
   <a href="#3-funky-tetris-v102">FUNKY TETRIS</a>
</p>

---

## <a id="test-llm1"></a>[29/05/2026] - Retours d'expérience : LLM Locaux (Gemma 4 26B vs Qwen 3 Coder 30B)

`#Dev` `#AI` `#Hardware` `#LocalLLM`

Après plusieurs jours/semaines en local sur **Gemma 4 (26B A4B)** et **Qwen 3 Coder (30B A3B)** voici mon verdict. On est face à deux modèles qui boxent dans la même catégorie mais qui se comportent assez différemment. 

Voici un récap complet jusqu'à ma conclusion (sans langue de bois) sur l'utilité des LLM locaux pour du dév aujourd'hui. (selon moi)

---

### ⚙️ 1. Le Guide de Survie Technique

Faire tourner ce genre de modèle chez soi demande un bon paramétrage à la mimine sous peine de voir sa machine et/ou les performances exploser en vol. Si vous voulez tester, voici quelques règles qui n'ont bien été utiles :

#### 🌍 Les règles communes (Valables pour les deux modèles)
*   **Gestion de la VRAM :** Ne saturez jamais votre carte graphique. Laissez au moins 1 Go de VRAM de libre pour le système, mais remplissez-la au maximum pour trouver le point d'offload GPU optimal. 
*   **Stockage du contexte :** Le contexte doit impérativement être stocké dans la VRAM, sinon les performances s'effondrent instantanément.
*   **Quantification du KV Cache :** Par défaut, il est en FP16. Si vous manquez de VRAM, quantifiez le KV Cache en **Q8** (et pas moins). Cette compression du contexte permet de gratter 2, 3 ou 4 couches supplémentaires dans la VRAM.
*   **Profils d'inférence :** Chaque modèle vient avec des spécifications et des paramètres d'inférence précis. Veillez à bien reporter les bonnes valeurs de température, top_p et pénalités pour chacun d'eux pour obtenir des résultats cohérents.

---

#### ☯️ Spécificités : Qwen 3 Coder
*   **L'appétit des tokens :** Ce modèle bouffe les tokens comme un goinfre. Adaptez la taille du contexte à la taille de votre projet.
    *   *Petits projets (ex: Tetris) :* Bloquez à 15-25k tokens pour économiser la VRAM, laisser de la place pour les couches et accélérer l'exécution.
    *   *Gros projets :* On peut monter à plus de 100k, mais attention à la baisse drastique de vitesse. Un contexte trop bas tronquera la réponse ou bridera la réflexion du modèle.
*   **Limite de quantification :** Ne descendez jamais sous le format **Q4**. Pour la version Coder, une quantification inférieure détruit complètement la logique et la pertinence du code généré.

---

#### 🪙 Spécificités : Gemma 4
*   **Gestion des tokens :** Bonne nouvelle, il est beaucoup moins gourmand en tokens que son rival. Pour du dev standard, partez sur une base de **8192 tokens** et ne montez au-dessus que si vous ressentez un réel besoin d'étendre la mémoire du projet.
*   **Le casse-tête du protocole :** Gemma 4 utilise un protocole très récent et moderne. Problème : son mode **"Tool"** (l'appel de fonctions / outils externes) est particulièrement pénible à configurer avec les plugins et extensions d'IDE actuels (comme VS Code), qui ne le supportent pas encore correctement. Attendez-vous à devoir mettre les mains dans le cambouis pour stabiliser l'environnement de travail.

---

### 🧠 2. Match de comportement : Gemma 4 vs Qwen 3 Coder

Les deux modèles partagent un énorme défaut : une approche **"Brute-Force" / Straight-Forward**. Ils ne cherchent pas à écrire du code propre ou scalable, ils cherchent juste à répondre au prompt immédiatement, quitte à générer un bordel monumental, impossible à maintenir.

*   **Gemma 4 :** C'est le modèle récent, très moderne dans son protocole... un peu trop d'ailleurs. Actuellement, beaucoup d'extensions d'IDE (comme VS Code) ne le supportent pas encore nativement. Ses modes de réponse font bugger les plugins existants, ce qui demande un effort de configuration pénible. 
    *   *Comportement :* Il est imprévisible. Il peut être foudroyant de pertinence sur un truc complexe là où on ne l'attend pas, et se gaufrer lamentablement la minute d'après sur une broutille.
*   **Qwen 3 Coder :** Beaucoup plus constant et mature. Il fait de grosses boulettes beaucoup moins souvent, mais il n'est jamais impressionnant non plus. C'est le modèle 30B prédictif de base par excellence.

> 💡 **Le conseil :** Face à leurs grosses boulettes (ou moins grosses d'ailleurs), n'insistez pas. Ne vous lancez pas dans une cascade de corrections ("corrige ça", "ah non en fait remets comme avant"). Effacez simplement sa dernière réponse, repensez votre prompt initial et relancez proprement.

---

### 📉 3. Le problème de la dégradation des performances

Au début d'un projet, c'est fluide. Les deux modèles tournent à environ **20-25 tokens par seconde (t/s)**. C'est tout à fait acceptable.

Le drame arrive à mesure que les heures passent : le contexte augmente et la charge de calcul explose de manière exponentielle. 
*   Après quelques heures de dev, la vitesse est divisée par deux (**~10 t/s**).
*   Au bout de deux jours de dev, on tombe à **2 ou 3 tokens par seconde**. 

Le moindre script un peu lourd met 5 bonnes minutes à générer. Si le code déconne et qu'il faut refaire une passe, c'est reparti pour 5 minutes d'attente. Travailler dans ces conditions est tout simplement invraisemblable.

---

### ⚖️ 4. Verdict : Le grand paradoxe de l'assistant local

Ces modèles souffrent d'un paradoxe majeur. Pour obtenir de bons résultats, il faut être extrêmement directif et guider l'IA en citant explicitement les classes et les fonctions. Si vous la laissez libre, elle part dans le décor. 

> **Le Paradoxe :** Pour diriger efficacement l'IA, il faut être soi-même un excellent analyste et parfaitement connaître son sujet. Un débutant total se fera submerger par les erreurs du modèle et passera son temps à demander à l'IA de se corriger elle-même en priant. Quant au professionnel, s'il doit concevoir toute l'architecture lui-même et passer son temps à fliquer la machine, ça ira plus vite à coder tout seul.

De plus, faire tourner un 30B demande une bonne machine moyen de gamme dédiée. Si vous l'utilisez sur votre PC principal, le navigation web et les applications font vite se retrouver à court de RAM et tout faire ramer.

---

### 🖥️ 5. La Machine de Test (Hardware dédié)

Pour que ces retours d'expérience soient pertinents, il faut garder en tête que tous ces tests ont été réalisés sur une machine de milieu de gamme entièrement dédiée à cette tâche (laissant ainsi le PC principal 100% libre pour le code). 

Voici la fiche technique de la bécane :

```text
[ CONFIGURATION DE TEST ]
-----------------------------------------------------------------
• CPU     : AMD Ryzen 7 5700X
• RAM     : 32 Go DDR4 @ 3600 MHz
• GPU     : Nvidia RTX 5070
• STORAGE : NVMe M.2 4 To
-----------------------------------------------------------------
```

> ⚠️ **Constat d'allocation :** Sous LM Studio, quasi 100% des ressources mémoire étaient utilisées (RAM + VRAM). Si la configuration dispose d'une belle marge en computing pur, on se retrouve face au grand classique des LLM : le fameux bottleneck sur la bande passante mémoire.

## 🛑 Conclusion : Uninstall direct

Pour moi, la conclusion est implacable. À moins d'avoir un besoin ultra-spécifique (aucun accès internet, données ultra-secrètes), **le jeu n'en vaut pas la chandelle**. 

Ces modèles 30B locaux ne sont que des produits d'appel, des apéritifs pour donner envie d'utiliser les gros modèles en ligne. Ils ne font pas assez bien le travail pour justifier le blocage d'une machine dédiée chez soi. Sur tous mes projets récents, coder seul aurait été plus rapide.

**Résultat : suppression des fichiers de ma machine.** Je préfère largement payer des tokens à l'utilisation ou prendre un abonnement pour un gros modèle Cloud performant. C'est nettement plus rentable, infiniment plus rapide, et ça libère mes cartes graphiques pour autre chose.

---

> 🏁 **Fin de transmission...**

---

## [29/05/2026] - Présentation du projet : The Funky Breakout

`#Dev` `#Gaming` `#Godot` `#PixelArt`

*The Funky Breakout* est un projet de casse-briques très inspiré d'arcade (*Arkanoid*), mais revisité avec des mécaniques modernes. Je m'appuie largement sur les sensations des versions Arcade, Amstrad CPC et Amiga, tout en y injectant ma sauce.

<img width="960" height="540" alt="funky_arkanoid1" src="https://github.com/user-attachments/assets/97009f80-dbe7-4ee8-b3b9-e059c22fa460" />
Screenshot non contractuel pour sûr 😂

---

### 🕹️ Spécifications techniques & Features

*   **Visuels :** Pixel-art rétro sur une grille stricte de $13 \times 20$.
*   **Contrôle de la balle :** Il est possible de déplacer une balle collée à la raquette pour ajuster précisément son angle de départ.
*   **Système de niveaux persistants (Danger Level - DL) :**
    *   Chaque brique détruite rapporte un certain nombre de points de **DL**.
    *   À chaque palier de DL franchi : la balle accélère, le scoring augmente et le taux de loot des bonus est décuplé.
    *   *Contrepartie :* Un niveau de DL est perdu à chaque fois qu'une vie est consommée.
    *   *Objectif :* Réussir à "câper" son DL au maximum et y survivre le plus longtemps possible.
*   **Pipeline de Level Design "Zéro Friction" :**
    *   Les niveaux sont de simples images **PNG** à glisser dans un dossier.
    *   Toutes les données du niveau sont stockées dans les pixels de l'image.
    *   Le jeu applique automatiquement le type de brique dont la couleur est la plus proche du pixel analysé.
    *   La table de loot est stockée directement sur les deux lignes du bas de l'image.
    *   Les bonus associés sont dissimulés dans la couche alpha (transparence) des pixels.

---

### 🧱 Le Bestiaire des Briques (Data & Équilibrage)

Chaque type de brique possède ses propres caractéristiques mécaniques et mathématiques :

#### Tiers Basique (Remplissage & Couleurs Froides)
*   ⚪ **Blanche :** Le bloc de base par excellence. 
    *   `1 HP` `30 Pts` `+1 DL`
    *   Multiplicateur de drop : `0.8` (malus)
*   🟢 **Verte :** Un bloc standard léger. 
    *   `1 HP` `50 Pts` `+1 DL`
    *   Multiplicateur de drop : `0.9`
*   🔵 **Cyan :** Le point d'ancrage neutre du jeu. 
    *   `1 HP` `70 Pts` `+1 DL`
    *   Multiplicateur de drop : `1.0` (neutre)
*   🔷 **Bleue :** Le haut du panier des briques froides. 
    *   `1 HP` `90 Pts` `+1 DL`
    *   Multiplicateur de drop : `1.1` (avantageux)

#### Tiers Intermédiaire (Transition & Couleurs Chaudes)
*   🟣 **Violette :** L'entrée dans le second tiers. 
    *   `1 HP` `110 Pts` `+2 DL`
    *   Multiplicateur de drop : `1.3` 
*   💗 **Rose :** Un bloc intermédiaire supérieur. 
    *   `1 HP` `130 Pts` `+2 DL`
    *   Multiplicateur de drop : `1.4` 
*   🔴 **Rouge :** Le sommet des briques standards à un seul point de vie. 
    *   `1 HP` `150 Pts` `+2 DL`
    *   Multiplicateur de drop : `1.5`

#### Tiers "Heavy" & Spéciaux (Sacs à PV & Objectifs)
*   🟡 **Jaune :** Un bloc à haute valeur de score. 
    *   `2 HP` `300 Pts` `+3 DL`
    *   Multiplicateur de drop : `2.0` (double les chances)
*   🟠 **Orange :** Le premier vrai rempart du niveau. Demande 3 coups de bille ou un laser. 
    *   `3 HP` `400 Pts` `+4 DL`
    *    Multiplicateur de drop : `2.5`
*   👑 **Dorée :** Le "Mini-Boss" statique de la grille. Un monstre de résistance qui demande 10 coups de bille ou le Laser . 
    *   `10 HP` `800 Pts` `+10 DL` (pic massif)
    *   Multiplicateur de drop : `3.0` (max) 
*   ⛔ **Grise (INDESTRUCTIBLE) :** Obstacle structurel et élément d'architecture. Elle n'offre aucun point et stoppe les balles. *Seule exception :* elle est sensible aux tirs lasers et peut être pulvérisée avec quelques coups bien placés si le joueur possède le bonus adéquat.

---

### 💊 Les Modules de Bonus (Capsules)

Lorsque les briques se brisent, elles libèrent des capsules à attraper à la volée. Les bonus sont soit stockés en dur par brique, soit générés aléatoirement via la table de loot globale du niveau :

*   **`[E]` EXPAND :** Augmente de 50% la taille de la raquette pendant 10 secondes *(temps cumulable)*.
*   **`[S]` SLOW :** Ralentit toutes les balles à l'écran pendant 10 secondes *(temps cumulable)*.
*   **`[C]` CATCH :** Permet d'aimanter et d'attraper la balle *(système à 3 charges cumulables)*.
*   **`[D]` DISRUPTION :** Crée instantanément deux clones de chaque balle en jeu (génération de multiballe).
*   **`[P]` PLAYER :** Octroie une vie supplémentaire (1UP).
*   **`[L]` LASER :** Permet d'équiper la raquette de canons et de tirer pendant 5 secondes *(temps cumulable)*.
*   **`[B]` BREAK :** Ouvre une porte latérale sur la grille pour terminer automatiquement le niveau en cours.

---

### 🛠️ Note Générale (État d'avancement)

> Tout ce qui est présenté ici est évidemment sur sujet à modifications et ne représente que la base. De nombreux autres bonus et mécaniques sont à l'étude ou en test et seront abordés ultérieurement.

### 🛠️ Note de Dev (État d'avancement)

> **Post-mortem temporaire :** On ne va pas se mentir, cela prendra plus de temps que prévu pour sortir une première version de test. En dehors de quelques imprévus hors projet, j'ai largement sous-estimé le temps requis pour avoir un bon feeling en jeu et un gameplay solide.
> 
> Associer tous ces réglages ET la conception des niveaux s'avère être un vrai casse-tête de game design. C'est une phase intense de réglages en tout genre et de recherche pour trouver la meilleure solution technique. Mais bon, ça va le faire ! 🤣

---

> 🏁 **Fin de transmission...**

---

### La musique de FUNKY TETRIS
`#MAO` `#Gaming` `#Chiptune`

Vous voulez faire du chiptune facilement et de manière ludique ? Foncez sur [UltraBox](https://ultraabox.github.io/).

<img width="358" height="410" alt="ultrabox" src="https://github.com/user-attachments/assets/096004b0-3c40-4782-8fa7-e1ee91bf67d9" />

Pour l'occasion, je vous partage le thème de **Funky Tetris** que j'ai composé directement dessus :

🎶
👉 [FUNKY TETRIS sur UltraBox](https://ultraabox.github.io/#u5N0vTETRIS%20THEME%20by%20funkydatan210s0k02l00e0nt2ma3g0nj0jr3O_U000000i0o32T9v0pu1cf010s500q040O0d030w20h0M00001111222233334444555566667777MMMMLLLLKKKKJJJJIIIIHHHHGGGGFFFFE0c0T1v0ku01f150q040O0d030A1F0B0Q644rPb793R0000E3c061962a639T2v0uu15f180q040O0d030w0h0E0c0b28p2Cew8xAaoW4GmNaCK28p2CexaBIqVY8OzecEQ288My2c8wz288y5cs8wEp2bGI8Lx260oh74Fg5C4higba0AoRi4A4xp7CBaJvil06hyAhOyhtbtMFrMz7CBdqx82gS5Aw93Kldx5QHhF0icrjOcqhvb064nOQjmABlfN1DUl4og4x58ihi5A6k18sPP6flq-YY4CnRIFYhzhx2icm4b8OSeJshzPqSTAA18r2ig4xN6lf6PJP6dcz78QLAoX2uHdP3omVwAnOhHm4qYV80MzmykHi0AoE0Iz42hBOcqkp684DaOw92d2hHi032bV2xtqg4z6t32bF6852Pa0ArOidqh-lYzn0FAumh3hEQqap5EIc3gyChq2o6lV20ChUwEQqd78YgzAe63A870Z6zlGV7y4sxNnA875qd6zhNny5YxM3O43xkzE4qoKwbBp790aF5gEMpvHwd58YwhSyeVI0buhVx789B-yn-e0aB6zhSf48V3oWg2DhEQszI8V3oXE2HhERsHI8V3oWE2HhEQszIbV3trE2WhQ2dcjgXw9JrKW0GAt2zNTo5TrwbF7g8Xh5sS05KzhWyns9w4dhEQu8ZgzAdzBw56zhEV7ohO6NQg56zhdrh0p7ohS338J29HOdAOyG08GcECaMphehjIpOSlcnoO5AuND8npyXehjIpO5SgKMyb8ZzagKP5SsyDoPAkVa8IzOe95dakyCBahjiB8mhQFiaqkF5dakJObgOsVyOeBapjiB8FFiAkXamOAuOBb9AujDczOujDcn9yVesJD6hVeAxtGbIV6W00)

---

> 🏁 **Fin de transmission...**

---

## [28/05/2026] - Analyse Tech : Tout comprendre sur Nanite (UE5) et l'alternative SSDM

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

---

> 🏁 **Fin de transmission...**
> 
> #### Ce qui conclut cet article, en espérant que vous en savez un peu plus sur le sujet, je vous dis à la prochaine ! 😉

---

## [27/05/2026] - Projets en cours & Mises à jour

### 1. Test de LLM locaux
`#Dev` `#AI`

*   **Gemma 4 (26B A4B) :** En plein test actuellement.
*   **Qwen 3 Coder (30B A3B) :** Comparaison à venir très prochainement.
*   *Verdict temporaire :* Pour le moment, c'est très serré ! 🙂

---

### 2. Remake d'Arkanoid (Version revisitée)
`#Dev` `#Gaming`

Je bosse sur un remake du célèbre jeu d'arcade. J'utilise Gemma 4 pour m'épauler dessus. Malheureusement, le projet est beaucoup trop complexe pour une génération totale : j'ai dû me résoudre à redonner à l'IA un "simple" rôle d'assistant.

<img width="960" height="540" alt="funky_arkanoid" src="https://github.com/user-attachments/assets/1e9ef5be-82ca-4380-9686-8913fd5c23b1" />

Il sera assez évolué : 

*   **Contenu fidèle & nouveautés :** Toutes les raquettes et tous les bonus des versions d'arcade seront présents, accompagnés de quelques nouveautés.
*   **Mécaniques exotiques au programme :**
    *   Briques "Mine"
    *   Portails de téléportation
    *   ... et d'autres surprises !
*   **Gestion des niveaux :** Système de chargement et de création de niveaux via des images BITMAP.

Bref, ce n'est pas un petit morceau et il y aura pas mal de mécaniques sympas au rendez-vous.

*   **Statut :** Déploiement d'une pré-version bientôt disponible.

---

### 3. Funky Tetris v1.02
`#Dev` `#Release` `#Gaming`

Le **["FUNKY TETRIS"](https://github.com/funkydata13/Qwen3Tetris-)** est officiellement passé en version 1.02 ! 
**[Il est jouable ici](https://funkydata13.github.io/Qwen3Tetris-/index.html)** ou récupérable sur le dépot pour les plus aventureux.

<img width="642" height="361" alt="funky_tetris" src="https://github.com/user-attachments/assets/7a415344-1747-4789-8f72-1167fe93c81a" />

*   Le système de scoring est ouvert -comme les sources 😂-.
*   Appuyez sur Alt+M en jeu pour la surprise 😉 -désolé-
*   *À venir :* Une passe d'embellissement de rigueur dès que j'aurai un créneau.
