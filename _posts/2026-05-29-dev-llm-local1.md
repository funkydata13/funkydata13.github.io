---
title: "🤖 LLM Locaux : Gemma 4 vs Qwen3Coder"
date: 2026-05-29 19:00:00 +0200
categories: [Dev, AI, Tests]
tags: [dev, ai, tests]
image:
  path: /assets/img/covers/llm_qwen_gemma.png
---

## LLM Locaux - Gemma 4 26B vs Qwen 3 Coder 30B - Retour d'expérience

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