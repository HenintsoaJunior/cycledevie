# Mini-TP 3 — « Observer le cycle de vie »
**Module M1 · Développement mobile Kotlin — Séance 3**  
*ITUniversity — Travail individuel*

---

## Étape 1 — Lire et prédire (avant tout lancement)

| Scénario | Séquence EXACTE prédite (dans l'ordre) | Réponse à la question « + » (une phrase) |
|---|---|---|
| **Scénario A** — Rotation de l'écran | `onPause` → `onStop` → `onDestroy` → `onCreate` → `onStart` → `onResume` | Le compteur est perdu et réinitialisé, car l'instance de l'Activity est détruite puis une nouvelle instance est créée. |
| **Scénario B** — Accueil, puis retour | `onPause` → `onStop` → `onRestart` → `onStart` → `onResume` | L'Activity n'est ni détruite (`onDestroy` non appelé) ni recréée (`onCreate` non appelé) : son instance et ses données restent conservées en mémoire en arrière-plan. |

---

## Étape 2 — Observer au Logcat (`tag:CYCLE`)

| Scénario | Séquence observée | Écart avec ma prédiction + explication |
|---|---|---|
| **Rotation de l'écran** | `onPause` → `onStop` → `onDestroy` → `onCreate` → `onStart` → `onResume` | **Aucun écart.** Lors de la rotation, Android déclenche un changement de configuration (*configuration change*) qui détruit entièrement l'instance existante pour en recréer une nouvelle adaptée à la nouvelle orientation. |
| **Accueil, puis retour** | Départ : `onPause` → `onStop`<br>Retour : `onRestart` → `onStart` → `onResume` | **Aucun écart.** L'Activity n'est plus visible et passe à l'état stoppé dans la tâche de fond sans être détruite. À la réouverture, elle redémarre directement via `onRestart`. |

---

## Question d'observation

> **À la rotation, le numéro d'instance affiché par `onCreate` change. Qu'est-ce que cela prouve — et qu'arriverait-il à un compteur stocké dans l'Activity ?**

**Réponse :**  
Cela prouve que l'ancien objet `MainActivity` a été détruit par le système et qu'une nouvelle instance en mémoire (identifiée par un `hashCode` différent) a été allouée. En conséquence, un compteur stocké comme variable membre de l'Activity perdrait sa valeur actuelle et reviendrait à sa valeur initiale par défaut (0).

---

## Bonus — Entrelacement des activités (`CYCLE` et `CYCLE-2`)

> **Ouvrez le second écran (bouton) et observez l'entrelacement des étiquettes `CYCLE` et `CYCLE-2` : qui se met en pause avant que qui ne se crée ?**

**Réponse :**  
C'est `MainActivity` (`CYCLE`) qui se met en pause (`onPause`) **avant** que `SecondActivity` (`CYCLE-2`) ne soit créée (`onCreate`).

**Séquence complète observée au Logcat :**
1. `CYCLE` : `onPause` *(MainActivity cède le premier plan)*
2. `CYCLE-2` : `onCreate` *(SecondActivity est créée)*
3. `CYCLE-2` : `onStart` *(SecondActivity devient visible)*
4. `CYCLE-2` : `onResume` *(SecondActivity passe au premier plan et devient interactive)*
5. `CYCLE` : `onStop` *(MainActivity n'est plus visible)*
