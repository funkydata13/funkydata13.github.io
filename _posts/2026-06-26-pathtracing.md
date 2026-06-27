---
title: "⚙️ Ray Tracing et Path Tracing dans les jeux vidéo"
date: 2026-06-26 13:00:00 +0200
categories: [Tech]
tags: [3d-engine, render-engine, computer-graphics, ray-tracing, path-tracing, ia]
description: "Analyse technique et historique de l'évolution du Ray Tracing vers le Path Tracing dans le jeu vidéo, face aux réalités industrielles et à l'avènement du rendu neuronal."
image:
  path: /assets/img/covers/banner_tech_pt_rt.png
  alt: "Ray Tracing et Path Tracing"
---

## 1. Le Ray Tracing (Les Fondations)

Aujourd'hui, le "Ray Tracing" est sur toutes les boîtes de consoles et toutes les fiches techniques de cartes graphiques. Mais avant d'être un argument de vente, c'est une technique de calcul optique qui n'est pas toute jeune.

Pour comprendre pourquoi elle a bousculé le jeu vidéo, il faut d'abord comprendre comment on affichait de la 3D avant elle : la rasterisation. C'est la méthode traditionnelle. Pour faire simple, le processeur prend les triangles de tes objets 3D, les projette sur ton écran plat, et le GPU colorie les pixels à l'intérieur. C'est ultra rapide, mais pour les ombres, les reflets et la lumière, on triche. On utilise des textures précalculées, des projections de fausses ombres (shadow maps) et des approximations. Ça marche bien, mais ça a des limites physiques évidentes.

Le Ray Tracing, lui, prend le problème à l'envers.

### Le concept : Copier la physique (mais à l'envers)

Dans la vraie vie, la lumière part du Soleil ou d'une ampoule, rebondit sur les objets, et finit par atteindre tes yeux. Si on essayait de simuler ça sur un ordinateur, la machine exploserait : la source de lumière émet des milliards de rayons qui partent dans le vide et que tu ne verras jamais.

En 1979, un ingénieur nommé Turner Whitted a une idée logique : et si on faisait le chemin inverse ?

Au lieu de partir de la lumière, on part de la caméra (l'œil du joueur).
1. Pour chaque pixel de ton écran, l'algorithme lance un rayon virtuel "dans" le jeu.
2. Ce rayon voyage en ligne droite jusqu'à ce qu'il percute un polygone (un mur, une bagnole, un personnage).
3. À l'endroit précis de l'impact, le programme jette un deuxième rayon (un "rayon d'ombre") directement vers les sources de lumière de la scène.

Si ce second rayon atteint la lumière sans encombre, le point est éclairé. S'il croise un autre objet en chemin, le point est dans l'ombre.

[Caméra] ---> (Rayon primaire) ---> [Objet touché]
                                         │
                                   (Rayon d'ombre)
                                         ▼
                               [Source de lumière]

### Pourquoi c'est une révolution (et pourquoi c'est lourd)

Avec cette méthode, plus besoin de tricher. Les ombres portées sont parfaites au pixel près, et si l'objet touché est un miroir, le rayon rebondit selon le bon angle pour aller voir ce qui s'y reflète.

Le problème, c'est que c'est un gouffre à puissance. Pour une simple résolution en 1080p, il y a plus de 2 millions de pixels. Ça veut dire qu'il faut calculer l'intersection de millions de rayons avec des millions de triangles géométriques qui bougent, et ce, 60 fois par seconde. Pendant des décennies, le Ray Tracing est donc resté coincé dans les labos de recherche ou le cinéma d'animation, qui pouvait se permettre de passer des heures à calculer une seule image.

Mais le plus important à retenir ici, c'est que le Ray Tracing "classique" (celui de Whitted) est déterministe et direct. Un rayon tape un objet, on regarde s'il voit la lumière, et s'il rebondit, il le fait de manière fixe (comme un reflet net). Il ne gère pas naturellement la lumière qui ricoche partout pour éclairer une pièce sombre.

Et c'est exactement là que le Path Tracing va entrer en scène.

## 2. Le Path Tracing (L'Évolution)

Si le Ray Tracing classique a posé les bases en calculant la lumière directe, il laissait un énorme problème de côté : dans la vraie vie, la lumière ne s'arrête pas après avoir tapé un mur. Elle ricoche, s'atténue, se colore et éclaire indirectement tout ce qui l'entoure. C'est ce qu'on appelle l'illumination globale.

Pour résoudre ça, un chercheur nommé James Kajiya publie en 1986 un papier scientifique qui va poser l'équation fondamentale du rendu 3D. Sa solution ? Le Path Tracing (ou "tracé de chemin").

### Le concept : Le rebond aléatoire

Là où le Ray Tracing classique est très carré et direct (un rayon touche un objet, regarde la lampe, et c'est fini), le Path Tracing est beaucoup plus chaotique.

On part toujours de la caméra, mais dès que le rayon percute une surface, il ne se contente pas de regarder les sources de lumière. Il rebondit. Et il le fait de manière totalement aléatoire (stochastique) dans l'espace, comme une bille de flipper folle.

1. Le rayon part du pixel, tape un mur rouge.
2. Il rebondit au pif vers le plafond blanc, en emportant avec lui un peu de la couleur rouge du mur.
3. Il rebondit à nouveau vers un meuble à l'ombre, qui se retrouve alors légèrement éclairé... par un reflet diffus et teinté de rouge.
4. Il continue de ricocher jusqu'à ce qu'il croise enfin une vraie source de lumière, ou que l'algorithme décide d'arrêter les frais après un certain nombre de rebonds.

[Caméra] ---> [Mur Rouge] ---> [Plafond Blanc] ---> [Zone d'ombre] ---> [Lampe]

En faisant ça, le Path Tracing simule TOUS les phénomènes lumineux d'un seul coup : la lumière indirecte, les reflets flous sur le métal, la translucidité et la façon dont la couleur d'un objet "bave" sur son voisin (le color bleeding). C'est le Saint Graal du réalisme.

### Le revers de la médaille : Le "Bruit"

Si cette méthode est mathématiquement parfaite, elle a un immense défaut graphique : le bruit numérique.

Comme le rayon rebondit au hasard, deux rayons lancés à partir de deux pixels côte à côte peuvent prendre des chemins complètement différents. L'un va trouver une lampe en deux rebonds (le pixel sera brillant), l'autre va se perdre dans un coin sombre (le pixel sera noir). Résultat en sortie : une image brute ressemble à de la neige texturée, un nuage de points blancs et noirs hyper agressif.

Pour obtenir une image propre, il n'y a pas de secret : il faut de la statistique. Il faut lancer non pas un seul rayon par pixel, mais des dizaines, des centaines, voire des milliers (les fameux "samples"). En faisant la moyenne de tous ces chemins, le bruit disparaît et laisse place à une image parfaitement lisse et réaliste.

Autant dire qu'en 1986, faire tourner ça en temps réel relevait de la science-fiction pure. Le Path Tracing était l'outil exclusif du offline et des fermes de calcul.

Mais alors, comment s'est-on retrouvé avec ces termes techniques collés sur nos jeux d'action aujourd'hui ? C'est toute l'histoire du braquage sémantique opéré par le marketing.

## 3. Les nouveaux sens

Si tu as bien suivi les deux premiers chapitres, tu as compris que le Path Tracing est, techniquement, une sous-famille ultra-poussée du Ray Tracing. Sauf qu'entre la théorie des labos des années 80 et les rayons commercialisés dans nos cartes graphiques de nos jours, les départements marketing sont passés par là. Et ils ont joyeusement tordu le sens de ces mots pour en faire des arguments de vente.

### Le Ray Tracing : Le grand écart avec l'origine

C'est sans doute le terme qui a le plus souffert. Dans sa définition d'origine (1979), le Ray Tracing est un système global : il est censé calculer toute la scène, de la caméra jusqu'aux sources de lumière.

Dans le jeu vidéo moderne, on en est très loin. Pour que les jeux restent fluides, les développeurs ont créé un modèle hybride : 90% de l'image est calculée à l'ancienne via la rasterisation (les polygones, la géométrie, la base des textures). On injecte ensuite une micro-dose de Ray Tracing uniquement là où la rasterisation galère le plus : souvent juste pour afficher des reflets nets sur les carrosseries, ou pour lisser les ombres sous les pieds des personnages.

Le "Ray Tracing" est donc devenu l'effet cosmétique optionnel, l'effet de luxe "Waouh" qui rend toutes les surfaces bien trop réfléchissantes mais qui justifie le prix exorbitant des GPU. C'est l'artifice visuel parfait pour proposer un gap graphique que l'on n'est plus capable de franchir d'une génération de carte à l'autre par les méthodes traditionnelles.

### Le Path Tracing : Une techno souvent galvaudée

Pour vendre les générations de GPU supérieures, les constructeurs avaient besoin d'un nouveau mot fort : le "Path Tracing" (ou "Full Ray Tracing"). Sauf qu'entre la théorie et la pratique, le terme est souvent galvaudé et utilisé à toutes les sauces.

Dans l'esprit collectif, le Path Tracing est devenu un synonyme marketing d'une "Illumination Globale (GI) parfaite". Le problème, c'est que beaucoup de titres qui se vantent d'intégrer du Path Tracing ne le font que de manière très partielle. Il n'est pas rare qu'un jeu l'utilise uniquement pour gérer l'éclairage indirect (la lumière qui ricoche) et les réflexions complexes, tout en laissant la rasterisation traditionnelle s'occuper de l'éclairage direct, des ombres principales ou de la diffusion atmosphérique.

Il est aussi certain que beaucoup de titres, même estampillés "Full Path Tracing", ne gèrent ni la réfraction (le comportement de la lumière à travers du verre ou de l'eau) et encore moins les volumes (le brouillard, la fumée réactive). Dire qu'il est difficile de savoir de quoi on parle est un euphémisme. Par définition, le Path Tracing est un algorithme unifié. Il n'est pas censé cohabiter avec la rasterisation pour décider si un pixel est éclairé ou non. Actuellement, on décrète plus qu'on ne fait réellement du Path Tracing.

Le marketing a donc recréé une frontière artificielle et illisible car mouvante. Mais même quand un jeu pousse les curseurs à fond et utilise un Path Tracing beaucoup plus global (comme Cyberpunk 2077 en mode Overdrive), il triche quand même massivement (en faisant l'impasse sur la réfraction, les caustiques, les volumes, etc.) par rapport au monde des path-traceurs "académiques". C'est ce qui sépare le monde du jeu vidéo du rendu dit "offline".

## 4. Path-tracing de jeu vidéo vs Path-tracing offline

Maintenant que le tri est fait entre la théorie et le marketing, posons la question qui fâche : pourquoi un jeu vidéo en "Path Tracing" n'approchera-t-il jamais la fidélité visuelle d'un moteur de rendu offline comme Blender Cycles, V-Ray ou Arnold ?

Tout est une question de temps, de mathématiques et de concessions.

### Le nerf de la guerre : Le temps par image

La différence fondamentale entre le temps réel et le offline se résume à une bête division.

- **En jeu vidéo (Temps réel) :** Pour afficher un jeu à 60 images par seconde, ton GPU dispose d'environ **16,6 millisecondes** pour fabriquer l'image. Pas une de plus. Dans ce laps de temps ridicule, il doit gérer la physique du jeu, l'intelligence artificielle, l'interface, la géométrie, et enfin, lancer ses rayons de lumière.
- **Dans Blender Cycles (Offline) :** L'artiste 3D s'en fout du temps réel. S'il faut passer **3 minutes, 20 minutes ou 2 heures** pour calculer une seule image finale, ce n'est pas un problème. Le GPU a tout le temps de respirer, de chauffer et de calculer les trajectoires jusqu'au bout.

### Les "Samples" : La pluie de rayons vs le compte-gouttes

Comme on l'a vu au Chapitre 2, le Path Tracing est un algorithme stochastique (aléatoire) qui produit du bruit numérique. Pour éliminer ce bruit et obtenir une image nette, il faut balancer un maximum de rayons par pixel, ce qu'on appelle les échantillons ou **Samples per Pixel (spp)**.

Dans un logiciel comme Blender, pour une scène complexe en intérieur, un artiste va configurer son rendu à **1024, 2048 voire 4096 samples par pixel**. Chaque pixel de l'écran va ainsi voir défiler des milliers de rayons qui vont explorer la pièce dans les moindres recoins, capturer les micro-détails des ombres, traverser le verre (réfraction) et rebondir 12 fois de suite.

Dans un jeu vidéo à 60 FPS, ces calculs sont terrifiants. Si on voulait lancer ne serait-ce que 100 samples par pixel en 4K (environ 8 millions de pixels), le GPU devrait calculer près d'un milliard de rayons en 16 millisecondes. C'est impossible. En réalité, un jeu vidéo moderne en Path Tracing ne lance souvent qu'**un seul et unique rayon par pixel (1 spp)**, parfois deux.

### La solution du JV : L'accumulation temporelle et le pansement IA

Lancer 1 ou 2 spp, c'est obtenir une méga bouillasse de bruit complètement illisible. L'image brute ressemble à un écran de télévision cathodique qui n'a plus de signal. Alors, comment font les développeurs pour sortir un rendu propre ? Ils trichent avec le temps.

Puisqu'on ne peut pas lancer 1000 rayons sur une seule image (frame), le moteur de jeu va **accumuler les spp sur plusieurs dizaines de frames consécutives** pour en faire une moyenne. En gros, le GPU fusionne les informations lumineuses des images précédentes avec celle en cours pour fabriquer, petit à petit, une approximation propre de la scène. Par-dessus ça, on colle un "denoiser" (un débruiteur) dopé à l'IA et hyper agressif, chargé de lisser les trous restants.

Le problème majeur, c'est que cette méthode d'accumulation temporelle hérite de tous les pires travers des technologies comme le TAA (Temporal Anti-Aliasing). Dès que la caméra ou les objets bougent rapidement, l'illusion se fissure :
- **Le Ghosting :** Des traînées fantômes apparaissent derrière les personnages ou les bagnoles parce que le moteur utilise des données lumineuses qui appartiennent aux frames passées.
- **Le Smearing :** L'image devient floue en mouvement, perdant tout son piqué, le temps que l'accumulation temporelle se "recharge" et se stabilise sur la nouvelle position de la caméra.

Le comble de l'histoire, c'est que ces défauts visuels sont d'autant plus marqués et longs à disparaître que le taux de rafraîchissement (les FPS) est bas... ce qui est précisément le cas lorsqu'on active une technologie ultra gourmande comme le Path Tracing. Un cercle vicieux technique assez ironique.

### Les autres coupes de la version "temps réel"

Pour tenir le contrat des 16,6 ms malgré cette gymnastique temporelle, les moteurs de jeu doivent couper où c'est possible  :

1. **La limite des rebonds :** Là où Cycles va laisser un rayon ricocher 8, 12, 24 fois pour illuminer une pièce, un jeu vidéo arrête après 1 ou 2 rebonds maximum.
2. **L'impasse sur la physique complexe :** On oublie les caustiques (les reflets de lumière à travers du verre ou de l'eau) et la vraie diffusion de la lumière dans les volumes denses (brouillard, fumée).
3. **Une géométrie simplifiée :** Les rayons du jeu vidéo ne testent pas la vraie géométrie 3D complexe, mais une structure simplifiée et invisible (la BVH) pour accélérer les calculs, quitte à générer de légères approximations.

## 5. Pourquoi ?

Quand on voit l'usine à gaz technologique que représente le Path Tracing en temps réel, les sacrifices géométriques qu'il impose et la bouillie visuelle qu'il faut nettoyer à grands coups d'IA, on est en droit de se poser une question toute simple : pourquoi ? Pourquoi les constructeurs de GPU et les studios se sont-ils lancés dans cette course à la technologie ultra-gourmande et complexe, au point d'imposer des unités dédiées (les RT Cores) sur nos cartes graphiques ?

La réponse ne tient pas seulement de l'amour de la science. C'est avant tout une histoire de business et de mur technologique.

### Le mur de la rasterisation et la stagnation visuelle

Pendant près de vingt ans, le jeu vidéo a progressé par vagues massives. On passait de la SD à la HD, puis de la HD au 4K, tout en augmentant le nombre de polygones, d'effets et de lumières à l'écran. On se prenait régulièrement des "claques". Sauf que depuis quelques générations de cartes graphiques, on est arrivé au bout de ce modèle. Fini les claques. Aujourd'hui, le seul effet qu'on ressent en changeant de GPU, c'est de se dire que ça rame moins qu'avant ou qu'enfin, le jeu tourne correctement, ou qu'on va enfin pouvoir lâcher son 1080p.

Le problème, c'est que la méthode traditionnelle (la rasterisation) a atteint ses limites industrielles. Bien qu'il reste encore des axes d'amélioration, la technologie est tellement mature que tout développement majeur supplémentaire coûterait une fortune sans pour autant offrir un véritable saut visuel. Ce n'est tout simplement plus viable économiquement parlant. Même développer ou maintenir son propre moteur de jeu maison est devenu extrêmement difficile. Pourtant, l'industrie a toujours besoin de sortir des jeux plus beaux pour vendre, et si possible moins chers à produire. Pour résoudre cette équation, elle s'en est largement remise à Epic Games et Nvidia, qui l'ont bien compris.

### Justifier le prix exorbitant des GPU

C'est là que le business entre en scène. Pour un constructeur comme Nvidia, il est impossible de vendre une nouvelle génération de cartes graphiques à 1000 ou 1500 € en disant simplement : *"Elle fait la même chose que la précédente, mais avec 15% de pixels en plus"*. Sans la fameuse "claque" visuelle, le consommateur ne suit plus.

Il fallait que ce soit plus beau, mais comme l'évolution visuelle classique allait stagner, il a fallu créer une rupture technologique majeure, un ajout graphique inaccessible aux anciennes cartes pour justifier le renouvellement du matériel. Le Ray Tracing, puis le Path Tracing, étaient les candidats parfaits, main dans la main avec l'IA et les outils de mise à l'échelle (upscaleurs).

En intégrant des "RT Cores" (des unités de calcul matériellement dédiées à tester l'intersection des rayons), les constructeurs --- surtout Nvidia, en réalité --- ont créé un nouveau besoin. Ils ont sciemment orienté le marché vers des technologies dotées d'une énorme marge de progression et d'évolution, de quoi justifier à elles seules la sortie et l'achat de très nombreuses futures générations de GPU.

C'était aussi un excellent moyen de relancer une dynamique visuelle "Waouh" sans faire exploser les coûts de développement pour les studios, tout en justifiant des tarifs de cartes toujours plus élevés. Quitte à rendre les jeux tellement lourds au passage qu'ils devenaient injouables sans une autre technologie miracle : l'intelligence artificielle.

## 6. Avenir

Si l'on regarde froidement la situation actuelle, le Path Tracing en temps réel est une technologie bien trop lourde pour les GPU actuels et futurs, mais qu'on a poussée quand même pour toutes les raisons évoquées précédemment. On demande à des cartes graphiques de calculer des rayons de lumière de manière ultra-précise, pour ensuite se retrouver avec une bouillie numérique infâme que l'on doit camoufler à l'aide d'algorithmes temporels ingénieux mais bancals.

En tout cas, l'avenir de cette technologie ne passera pas par une hausse miraculeuse de la puissance brute des GPU : le gap est bien trop gigantesque. Les lois de la physique et du silicium ont leurs limites. L'avenir du Path Tracing appartient presque exclusivement à l'accumulation temporelle poussée à l'extrême et à la reconstruction neuronale par l'Intelligence Artificielle.

### De la correction de pixels à la génération d'images

Jusqu'ici, l'IA dans nos jeux vidéo servait de "pansement". Avec des technologies comme le DLSS 2 (Nvidia) ou l'FSR (AMD), l'IA prenait une image calculée dans une résolution inférieure (par exemple en 1080p) et se chargeait de boucher les trous pour l'afficher proprement sur un écran 4K.

Avec le Path Tracing, on a franchi une étape bien plus radicale. On ne demande plus à l'IA de simplement lisser une image pixelisée, on lui demande de la réinventer. C'est ce que fait Nvidia avec le DLSS Ray Reconstruction (DLSS 3.5). Au lieu d'utiliser un débruiteur classique codé à la main qui détruit les détails et crée du flou en mouvement, le moteur confie la bouillie de bruit à un réseau de neurones. L'IA hallucine les formes, sait à quoi est censé ressembler un reflet sur de l'eau ou l'ombre d'une grille, et repeint littéralement les pixels manquants en temps réel.

### Vers un rendu totalement neuronal ?

À quoi ressemblera le jeu vidéo dans quelques années ? Très probablement à un immense trompe-l'œil géré par l'IA.

Le calcul de rayons physiques purs (le Path Tracing académique) va rester l'apanage des professionnels du cinéma ou de la 3D fixe sur les logiciels de PAO. En jeu vidéo, le GPU ne lancera qu'une quantité ridicule de rayons --- le strict minimum mathématique pour donner une cohérence globale aux ombres et aux sources lumineuses. Tout le reste du travail sera délégué à des modèles neuronaux.

On se dirige vers un futur où le moteur graphique ne calculera plus vraiment la lumière, mais donnera simplement une "intention" géométrique et lumineuse à une IA. C'est elle qui se chargera de générer le rendu visuel final, fluide, net et sans bruit, à 120 ou 240 FPS.

Mais si on se réfère aux récentes annonces autour du DLSS 5 qui réinterprète toute l'image, éclairage compris, quel est vraiment l'avenir du Path Tracing dans les jeux ? Est-ce bien la peine de tenter de calculer un éclairage à coup de Path Tracing hyper lourd pour seulement s'en servir de base pour un filtre IA qui va tout réinterpréter à sa sauce de toute façon ? On est en droit de se demander si cette technologie a un réel avenir, considérant le virage "full IA" pris par Nvidia, qui est le constructeur guidant le marché.

En tout cas, le business des constructeurs de GPU a réussi son pari : en imposant une technologie nativement trop lourde pour nos machines, ils ont rendu l'usage de l'IA totalement indispensable. La course aux gigahertz et au nombre de cœurs est bel et bien terminée ; la guerre s'est déplacée sur le terrain des algorithmes de reconstruction, des upscalers et des technologies propriétaires. Et pour le joueur, la fameuse "claque" est surtout maintenant dans le portefeuille, plus que sur son vieil écran qu'il garde précieusement pour ne pas que ça rame trop.
