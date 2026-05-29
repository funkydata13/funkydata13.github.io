---
title: "🎮 The Funky Breakout - Présentation"
date: 2026-05-29 16:00:00 +0200
categories: [Dev, Gaming]
tags: [dev, gaming]
image:
  path: /assets/img/covers/funky_breakout.png
---

## The Funky Breakout - Présentation 1

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
