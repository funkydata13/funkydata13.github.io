---
title: "💧 Modernisation de shader : le shader qui mouille !"
date: 2026-06-11 20:00:00 +0200
categories: [3D, Blender]
tags: [blender, shader]
description: "Shader eau."
image:
  path: /assets/img/pao/Dodge_Challenger_STR_Hellcat_Night_Shot_HD.png
  alt: "Dodge Challenger SRT Hellcat - Rendu Blender"
---

### 🛠️ La shader Wetness

C'est toujours pratique de pouvoir mouiller une surface facilement et rapidement. Qu'il s'agisse d'un sol, d'une carrosserie de voiture ou d'une vitre, toutes réagissent différemment à l'eau. Au lieu de réinventer la roue à chaque besoin, autant créer un nœud modulaire qui se branche directement sur le pipeline de shaders et applique automatiquement le bon effet.

J'ai donc mis à jour mon shader destiné à cette tâche.

Pour le tester, j'ai également remis au goût du jour un rendu datant de quelques années, parfait pour l'exercice et pour en faire la démonstration.

![Dodge Challenger SRT Hellcat](/assets/img/pao/Dodge_Challenger_STR_Hellcat_Night_Shot_HD.png){: .normal .text-center w="700" }  
[HD (1080p)](/assets/img/pao/Dodge_Challenger_STR_Hellcat_Night_Shot_HD.png){: .text-center }