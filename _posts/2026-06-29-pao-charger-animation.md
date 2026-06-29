---
title: "🎞️ Animation : Charger '69 dans un tunnel"
date: 2026-06-29 16:00:00 +0200
categories: [PAO]
tags: [blender, cycles, rendering, animation, upscale, ia, reaper]
description: "Retour sur la mise à jour d'une ancienne animation de Charger '69, l'intégration d'une bande son maison sur Reaper et le constat technique face aux limites du frame-generation et de l'upscale."
image:
  path: /assets/img/covers/pao_anim_genlee.png
  alt: "Charger 69"
---

Aujourd'hui je vous propose une petite animation que j'avais déjà commencée il y a un paquet de temps. Je l'ai remise au gout du jour, enrichie et rallongée.

J'ai tenté de faire une bande son, je dis bien tenté, c'est la première fois que je fais ça et c'est pas simple du coup j'ai pas mal tatonné. C'est correct je pense, ça fait le boulot on va dire.

{% include embed/youtube.html id="Zz8WHT0V2Bs" %}

---

### Le crash-test de l'upscale IA et du VFI

J'ai tenté aussi de faire comme j'avais fait avec le test de la Porsche en moins agressif car je savais que le framerate x3 c'était mort direct. Je suis donc parti sur un x2 avec un rendu LD à 15 fps donc.

Ben soyons franc, ça a été une vraie catastrophe !

Si globalement le mouvement est fluide et pertinant, deux problèmes majeurs viennent pourrir le visuel surtout imputable à l'upscale :

* **Les arches chromées de la voiture bavent et se tordent :** Le gros contraste semble ingérable. Idem pour le sticker dont les bords font un peu des vagues.
* **Les reflets sur la carrosserie :** Le rythme est tel que l'IA est "perdue". Ça bave, c'est moche, et on perd tout le piqué sur la carrosserie.
* **Le motion blur devient erratique :** Les roues manquent cruellement de fluidité.

> **En résumé :** L'IA galère à mort sur cette animation. Si le VFI (Video Frame Interpolation) peut encore passer si on ne regarde pas de trop près, l'upscale, lui, est complètement aux fraises. Si j'ai le temps, je ferai un comparatif visuel de tout ça.

---

### Côté animation : De la triche classique mais efficace

Sinon, considérant l'animation, c'est ultra-classique. Les roues sont animées via un driver qui calcule la bonne vitesse radiale par rapport à la vitesse du véhicule. 

Enfin presque, car j'ai utilisé la technique assez courante aussi bien en PAO qu'au cinéma qui consiste à **faire bouger le décor mais en gardant le véhicule immobile**. C'est plus pratique à animer que ce soit la voiture ou la caméra, vu que la voiture est fixe. Ça permet aussi de faire "boucler" l'animation très facilement — ce qui n'a pas été nécessaire ici en l'occurrence.

Au final, on est donc sur un **pur rendu Blender à 25 FPS**.

Pour la partie audio, j'ai fait le son avec le logiciel **Reaper** et 3-4 "bons" samples de Charger '69, qui n'ont pas été faciles à dénicher.