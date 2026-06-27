---
title: "🎮 The Funky Breakout : Expérimentation visuelle et dilemme de moteur"
date: 2026-06-16 16:30:00 +0200
categories: [Gamedev]
tags: [the-funky-breakout, godot, unity, devlog]
description: "Test d'un proto en 2.5D. Entre la logique de jeu sur Godot et un rendu visuel sur Unity, l'heure est à la réflexion."
image:
  path: /assets/img/covers/funky_breakout_visualdemo_1.png
  alt: "The Funky Breakout - Prototype Visuel"
---

Je me suis un peu laissé embarquer sur mon projet de Breakout (baptisé pour le moment **The Funky Breakout**). Quitte à y passer du temps, je me suis dit que ce serait une bonne idée de tenter un visuel moderne en 2.5D. C'est nettement plus long à mettre en place, mais j'ai commencé à produire pas mal d'assets pour concevoir un premier prototype graphique. Il y a encore une masse de boulot pharaonique, mais l'intention est là.

### 📸 Le proto actuel

![Proto 1](/assets/img/covers/funky_breakout_visualdemo_1.png){: .normal .text-center w="700" }

---

### 🛠️ Le choix d'Unity pour le prototype visuel

La petite subtilité, c'est que j'ai monté ce prototype visuel sous **Unity**. Pour le moment on est dans un subtil mélange entre dynamisme et baking au niveau des assets.

Toute la logique et le code du jeu sont actuellement développés sous **Godot**. Cependant, n'ayant jamais poussé Godot dans ses retranchements sur de la 2.5D avancée, je n'étais pas certain de sa capacité à afficher ce que j'avais en tête. Pour éviter de me retrouver bloqué en cours de route, j'ai préféré partir en terrain connu avec un moteur dont je maîtrise le pipeline graphique et sur lequel je ne partais pas de zéro.

L'idée est de caler proprement le visuel sur Unity, puis de tenter de le transposer sous Godot pour voir comment il s'en sort.

---

### 🎨 Trouver l'équilibre visuel

Je suis parti dans pas mal de directions et j'ai poussé le curseur assez loin - trop surement - pour arriver à ce style. Les grandes questions résidaient dans le look des briques, de la balle, et surtout dans la manière d'occuper l'immense zone vide sur la droite de l'écran.

Ce premier jet apporte des réponses, même s'il y aura d'évidents ajustements à faire :
* **La lisibilité avant tout :** C'est un jeu d'arcade. Le rendu actuel est peut-être un peu *too much* ou trop imposant. Si c'est joli mais que le jeu devient illisible à cause du style visuel ou des effets, ça ne sert à rien. Tout devra certainement être affiné pour que le jeu soit aussi agréable à jouer.
* **Les assets animés :** Il reste encore à créer tous les feedbacks visuels indispensables pour tester un proto vraiment jouable (effets de collision, destruction des briques, modélisation des bonus, déclinaisons des raquettes...).

En espérant que la gestion de l'éclairage ne pose pas trop de problèmes techniques lors des phases dynamiques.

---

### 🧭 Le dilemme de la plateforme

À terme, la question du moteur va se poser concrètement. Deux scénarios sont envisageables :

1. **Le plan A :** Godot encaisse le pipeline visuel sans broncher. Le rendu sera peut-être légèrement différent d'Unity, mais s'il reste suffisamment propre et fidèle au prototype, on valide.
2. **Le plan B :** Si Godot montre ses limites sur la partie 3D, je devrai convertir l'intégralité du code du jeu en C# pour tout basculer sous Unity.

Bien que le projet ne soit pas titanesque, il y a déjà une quantité de code non négligeable derrière. Une telle conversion serait tout sauf anodine et rallongerait pas mal le développement. J'espère donc que Godot saura se montrer à la hauteur du visuel cible !

La suite au prochain numéro.