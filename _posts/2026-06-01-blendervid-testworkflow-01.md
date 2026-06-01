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
* **Rendu final (Post-workflow) :** 4K @ 30 FPS

Convaincant ? Si oui, je pousserais plus loin en essayant de déplacer le compositing et en utilisant les *Tensor Cores* (ou Tensor Flow) du VFI. Ce sera bien plus complexe niveau workflow et ça ne changera pas foncièrement la donne, mais ce pourra sûrement être un peu mieux.

> **Note :** L'animation a été faite totalement à l'arrache, je souhaite juste valider le concept avant de chercher à aller plus loin. J'ai déjà quelques avis mais tous les retours sont bons à prendre !