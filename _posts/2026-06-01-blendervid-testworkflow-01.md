---
title: "📟 Test Workflow PAO pour des vidéos"
date: 2026-06-01 17:00:00 +0200
categories: [News]
tags: [pao, blender, ia, video]
description: "Test d'un pipeline de rendu de vidéos avec Blender et de l'IA."
image:
  path: /assets/img/pao/porsche911turbo_1.png
  alt: "Pipeline de rendu de vidéos avec Blender et de l'IA."
---

Test d'un pipeline de rendu et compositing avec Blender puis GIMM-VFI + IA Upscaling et enfin DaVinci Resolve :

{% include embed/youtube.html id="PdJSMsqR2Vg" %}

### Spécifications techniques
* **Rendu brut (Blender) :** 720p @ 10 FPS
* **Rendu final (Post-workflow) :** 2160p @ 30 FPS

Ce workflow permet de réduire le temps de rendu de manière drastique.

### ⏱️ Temps de rendu par frame (Blender brut)

Pousser les définitions dans Cycles augmente la charge de manière exponentielle. Voici les captures et les temps par image calculée :

![720p - 6.8 secondes](/assets/img/pao/wfia_01/blender_cycles_720p_6.8sec_0.05nt_comp.png){: .normal .left w="350" }
![1080p - 9.8 secondes](/assets/img/pao/wfia_01/blender_cycles_1080p_9.8sec_0.05nt_comp.png){: .normal .right w="350" }
![1440p - 16.5 secondes](/assets/img/pao/wfia_01/blender_cycles_1440p_16.5sec_0.05nt_comp.png){: .normal .left w="350" }
![2160p - 41.3 secondes](/assets/img/pao/wfia_01/blender_cycles_2160p_41.31sec_0.05nt_comp.png){: .normal .right w="350" }

<div style="clear: both;"></div>

La vidéo test dure 9 secondes, pour un total de 270 frames.

# ---

### 📊 Comparatif : Rendu Traditionnel (30 FPS) vs Workflow avec IA

Le VFI (Video Frame Interpolation) permet de diviser le temps par 3 en effectuant le rendu à seulement 10 FPS sous Blender pour en sortir 30 FPS en post-production. Au lieu de calculer 270 frames, la machine n'en traite plus que **90**.

### 📊 Comparatif : Rendu Traditionnel vs Workflow IA (Cible finale : 2160p @ 30 FPS)

Le VFI (Video Frame Interpolation) permet de diviser le temps par 3 en effectuant le rendu à seulement 10 FPS sous Blender pour en sortir 30 FPS en post-production. Au lieu de calculer 270 frames, la machine n'en traite plus que **90**.

Ici, le gain de temps et l'économie sont calculés par rapport au temps théorique d'un rendu classique en **2160p @ 30 FPS (3 h 05 min 51 s)** :

| Méthode de rendu (90 frames @ 10 FPS) | Temps total sous Blender | Temps économisé (vs 4K @ 30 FPS classique) | Chute du temps de calcul |
| :--- | :---: | :---: | :---: |
| **Rendu classique 2160p @ 30 FPS** | *3 h 05 min 51 s* | *Référence absolue* | *0 %* |
| **Workflow avec base 720p** | **10 min 12 s** | En moins : **2 h 55 min 39 s** | **-94,5 %** |
| **Workflow avec base 1080p** | **14 min 42 s** | En moins : **2 h 51 min 09 s** | **-92,1 %** |
| **Workflow avec base 1440p** | **24 min 45 s** | En moins : **2 h 41 min 06 s** | **-86,7 %** |
| **Workflow avec base 2160p** | **1 h 01 min 57 s** | En moins : **2 h 03 min 54 s** | **-66,6 %** |

---

### 🧠 La question en suspens : 720p ou 1080p ?

Le choix de la résolution de base à envoyer dans l'injecteur IA est crucial :
* **720p @ 10 FPS :** 10 minutes 12 secondes
* **1080p @ 10 FPS :** 14 minutes 42 secondes (**+44,1 %** de temps de calcul sous Blender)

L'impact n'est pas négligeable, car passer sur une base 1080p demande près de 44 % de temps de calcul supplémentaire par rapport au 720p. 

Néanmoins, la grosse faiblesse de ce workflow reste l'upscaling. L'IA a beaucoup de mal à reconstruire proprement et à upscaler les lignes parfaites ainsi que les détails géométriques de la PAO. Partir d'une base native plus riche en pixels (1080p) est donc une concession qui pourrait largement se justifier pour préserver la netteté, malgré le surcoût temporel.

---

### 🧠 L'autre question en suspens.

Convaincant ? Si oui, je pousserais plus loin en essayant de déplacer le compositing et en utilisant les *Tensor Cores* (ou Tensor Flow) du VFI. Ce sera bien plus complexe niveau workflow et ça ne changera pas foncièrement la donne, mais ce pourra sûrement être un peu mieux.

> **Note :** L'animation a été faite totalement à l'arrache, je souhaite juste valider le concept de Workflow avant de chercher à aller plus loin. J'ai déjà quelques avis mais tous les retours sont bons à prendre !