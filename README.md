# Femmes en Colère (Abrégé) - Le Président

Application de révision pour acteur interprétant **Le Président** dans la pièce "Femmes en Colère". 
Fichier HTML unique (React 18 + Tailwind CDN), zéro build step.

---

## 📋 Table des matières

1. [Navigation générale](#navigation-générale)
2. [Modes de lecture](#modes-de-lecture)
3. [Mode Filage (⏯ Filage)](#mode-filage--filage)
4. [Mode Turbo (⚡)](#mode-turbo-)
5. [Mode Enregistrement (🎤)](#mode-enregistrement-)
6. [Mode Lecture (🎧)](#mode-lecture-)
7. [Souffleur/Prompteur (👁)](#souffleurprompeur-)
8. [Paramètres des voix (🔊)](#paramètres-des-voix-)
9. [Marque-pages (📌)](#marque-pages-)
10. [Gestes tactiles](#gestes-tactiles)
11. [Persistance des données](#persistance-des-données)
12. [Architecture technique](#architecture-technique)

---

## Navigation générale

### Sélection de scène
- Boutons **A1, A2, B1, B2, B3, C1, C2, D1, D2, D3, D4, D5, D6, FIN** en haut de l'écran
- Chaque scène regroupe plusieurs répliques (5 à 30 lignes par scène)
- Un clic réinitialise la lecture à la ligne 1 de la scène

### Boutons de navigation
- **↺ Recommencer** : retour à A1 ligne 1
- **⏮ Précédent** : ligne précédente (ou dernière ligne de la scène précédente)
- **⏭ Suivant** (gros bouton rouge) : ligne suivante (ou première ligne de la scène suivante)

### Compteur et barre de progression
- Affiche : `Ligne X / Y lignes` (ex: "5 / 28 lignes")
- Barre verte : progression dans la scène actuelle
- Mise à jour en temps réel en mode Filage

### Gestes tactiles (mobile/tablette)
- **Swipe gauche** → Suivant
- **Swipe droite** → Précédent
- Un indice s'affiche si c'est la première visite

---

## Modes de lecture

Quatre modes distincts :

### Mode Normal (défaut)
- Toutes les répliques sont lues **en entier**
- Passage à la réplique suivante quand la lecture précédente est terminée
- Texte des autres personnages affiché normalement
- Votre texte (Le Président) : 1er tap = révèle, 2e tap = lit à voix haute

### Mode Turbo (⚡)
- **Saute automatiquement** aux répliques juste avant le vôtre ("cue lines")
- Les autres répliques sont sautées
- Cue line : les **2 dernières phrases seulement** sont lues
- Incomparable pour un filage ultra-rapide sans surprises
- **Compatible avec Mode Filage** : combine avance auto + cue lines

### Mode Filage (⏯)
- Simule un **vrai filage debout**
- Avance **automatiquement** réplique par réplique (pas de clic "Suivant")
- Répliques des autres → lues à voix haute (temps normal)
- **Vos répliques (Le Président)** → silence chronométré **identique** à la durée vocale
- Les vôtres affichent : **"🎭 À vous de jouer !"** + barre de progression (%)
- Auto-avance désactivable (bouton ▶ Auto ON / ⏸ Auto OFF)
- **🎤 Enregistrer** : active l'enregistrement auto de vos répliques

**Mode Filage + Turbo** : combine répliques de cue line (2 phrases lues) + silence de toute votre réplique.

### Mode Lecture (🎧)
- Lit le texte complet comme un filage, **en remplaçant vos silences par vos enregistrements**
- Répliques des autres → TTS normal
- Vos répliques → joue votre enregistrement MediaRecorder
- Si pas d'enregistrement pour une réplique → passage automatique (0.8s)
- Disponible uniquement quand au moins un enregistrement existe

---

## Mode Filage (⏯ Filage)

### Activation
- Bouton **⏯ Mode Filage** (orange) à côté du compteur sous les scènes
- Affiche un panneau orange paramètres quand actif

### Affichage des répliques

**Autres personnages** :
- Texte visible, lu à voix haute à la vitesse sélectionnée
- Exemple : "20 ans ! L'avocat..." (texte complet)

**Le Président (vous)** :
- Écran spécial :
  ```
  🎭 VOTRE RÉPLIQUE
  🎭
  À vous de jouer !
  [████░░░░░░░] 45%
  ```
- Barre de progression : 0% → 100% au fil du décompte silencieux
- Pourcentage affiché sous la barre
- Avancée automatique à 100% (si Auto ON)

### Paramètres du Mode Filage

Visible quand Mode Filage est **actif** :

1. **Vitesse** (curseur)
   - Plage : 0.5× à 2.0× (par 0.1)
   - Multiplicateur sur la vitesse générale
   - Exemple : générale 1.15× + filage 1.5× = 1.725× effectif

2. **Auto-avancer** (bouton toggle)
   - ▶ **Auto ON** (vert) : avance auto à chaque réplique terminée
   - ⏸ **Auto OFF** (gris) : attend un clic manuel "Suivant"

### Avance automatique
- Délai de 300ms entre fin lecture et changement de ligne
- Fonctionne même en changeant de scène
- Les changements de section sont transparents

---

## Mode Enregistrement (🎤)

### Activation
- Bouton **🎤 Enregistrer** dans le panneau orange Mode Filage (visible uniquement quand Filage actif)
- S'active/désactive en cours de filage sans l'interrompre

### Comportement
- Dès qu'une **réplique du Président** arrive en filage → enregistrement **démarre automatiquement**
- Dès que la ligne suivante commence → enregistrement **s'arrête et se sauvegarde**
- Badge **● REC** rouge clignotant pendant l'enregistrement
- Chaque réplique = une prise distincte (clé `section_ligne`)
- **Nouvelle prise écrase l'ancienne** pour la même réplique

### Contrôles manuels (sur l'écran "À vous de jouer !")
- **🎤 Rec** / **⏹ Stop** : démarrer/arrêter manuellement si besoin
- **▶ Écouter** : réécouter la prise actuelle (visible dès qu'un enregistrement existe)
- **🗑** : effacer l'enregistrement de cette réplique

### Réécoute individuelle
- Naviguer sur une réplique du Président → révéler le texte
- **🎙 Enreg. : ▶ Écouter** apparaît sous le texte
- Visible aussi sur l'écran "Cliquez pour révéler" (sans avoir à révéler)

### Limitation
- Enregistrements stockés en mémoire (perdus au rechargement)
- Nécessite autorisation microphone navigateur

---

## Mode Lecture (🎧)

### Activation
- Bouton **🎧 Mode Lecture** (bleu) dans la barre des modes
- **Disponible uniquement** quand au moins un enregistrement existe
- Désactive automatiquement le Mode Filage

### Comportement
- Avance automatiquement réplique par réplique (comme le Mode Filage)
- **Répliques des autres** → lues par TTS (voix normale)
- **Vos répliques (Le Président)** → joue votre enregistrement
  - Enregistrement disponible → lecture audio + icône 🔊
  - Pas d'enregistrement → ⚠️ "Pas d'enregistrement — passage" (0.8s)
- Arrêt automatique à la fin de la pièce

### Cas d'usage typique
1. Mode Filage + 🎤 Enregistrer → faire un filage complet (ou partiel)
2. Arrêter le filage
3. Activer Mode Lecture → s'entendre dans le contexte des autres répliques

---

## Mode Turbo (⚡)

### Activation
- Bouton **⚡ Mode Turbo** (jaune) ou **⚡ Mode Normal** selon l'état
- À droite du bouton Mode Filage

### Comportement

**Mode Normal activé (⚡ Mode Turbo disponible)** :
- Toutes les répliques sont lues en entier (défaut)

**Mode Turbo activé (⚡ Mode Turbo)** :
- Saute automatiquement les répliques non-essentielles
- S'arrête à la "cue line" (réplique juste avant Le Président)
- Cue line : ne lit que les **2 dernières phrases**
- Passe directement à votre réplique sans interruption
- Fonctionne même entre sections (cherche cue line dans sections suivantes)

### Logique de recherche
- Cherche la prochaine réplique du Président depuis la ligne courante
- Si pas de Président dans la section : cherche dans les sections suivantes
- Fin du texte : arrête la lecture

### Compatibilité
- **Mode Normal + Mode Normal** = lecture complète
- **Mode Normal + Mode Turbo** = lecture cue lines (2 dernières phrases)
- **Mode Filage + Mode Normal** = avance auto, répliques complètes
- **Mode Filage + Mode Turbo** = avance auto, cue lines (2 phrases seulement)

---

## Souffleur/Prompteur (👁)

### Activation
- Bouton **👁** (oeil) dans la barre de contrôle
- Vert quand actif, gris quand inactif
- Ne fonctionne que sur **vos répliques (Le Président)**

### États du Souffleur

Le texte de votre réplique passe par **4 états** :

1. **Hidden (👁 Off)** : "..." masqué
   - Chronomètre compte à rebours (X s avant révélation)
   - Délai configuré par défaut : 3s

2. **Hint (👁 Clignote)** : 2 premiers mots + "..."
   - Exemple : "Ah bien" pour "Ah bien, c'est pas..."
   - Automatique après délai, clic pour voir plus

3. **Full (👁 yeux ouverts)** : texte complet visible
   - Clic pour passer à l'état Audio

4. **Audio (🔊 parlant)** : texte complet + lecture à voix haute
   - Clic pour revenir à Full

### Transitions (clic)
- Hidden → Hint (auto après délai, ou clic)
- Hint → Full (clic)
- Full → Audio (clic)
- Audio → Full (clic)

### Configuration
- Paramètre : **⏱️ Délai du souffleur** (1-9s)
- Accessible dans le panneau Réglages (🔊)

---

## Paramètres des voix (🔊)

### Accès
- Bouton **⚙️** (engrenage) dans la barre de contrôle
- Ouvre un panneau gris "Réglages des voix"

### Paramètres globaux

1. **Vitesse générale**
   - Plage : 0.90× à 1.40×
   - Pas : 0.05×
   - Affichage : "Vitesse: 1.15x"
   - S'applique à toutes les lectures

2. **Pause virgules (,:;)**
   - Plage : 0 à 0.5s
   - Pas : 0.01s
   - Pause après une virgule, point-virgule ou deux-points

3. **Pause phrases (.!?)**
   - Plage : 0 à 1.5s
   - Pas : 0.01s
   - Pause après un point, point d'exclamation ou point d'interrogation

4. **👩 Voix FEMMES**
   - Dropdown avec voix FR disponibles
   - S'applique à Mathilde Collignon, avocates, jurées, etc.

5. **👨 Voix HOMMES**
   - Dropdown avec voix FR disponibles
   - S'applique à L'Avocat Général, Juré, Vous (Le Président), etc.

6. **⏱️ Délai du souffleur**
   - Plage : 1 à 9s
   - Temps avant révélation automatique (si souffleur actif)

### Comment les voix sont chargées
- Récupère voix système au chargement
- Essaie de détecter voix premium (Google, Microsoft)
- Fallback sur voix prononcées "Female/Male"
- Fallback sur première voix FR

### Persistance
- Tous les paramètres sont sauvegardés automatiquement au changement
- Restaurés à chaque rechargement de la page

---

## Marque-pages (📌)

### Ajouter/Retirer
- Bouton **📌** (vide) dans la barre de contrôle
- Change en **📌 rempli** si ligne actuelle marquée
- Clic pour basculer

### Voir les marque-pages
- Bouton **📋** (liste) dans la barre de contrôle
- Affiche panneau violet "Marque-pages"
- Liste toutes les lignes marquées avec :
  - Personnage
  - Section et numéro de ligne
  - Extrait du texte (50 premiers caractères)
  - Bouton "Aller à cette réplique" (bleu)
  - Bouton "×" pour retirer le marque-page

### Persistance
- Marque-pages sauvegardés en localStorage
- Restaurés à chaque rechargement

---

## Gestes tactiles

Fonctionnalité mobile/tablette :

- **Swipe gauche** (glissement vers la gauche) → Bouton "Suivant" (ligne suivante)
- **Swipe droite** (glissement vers la droite) → Bouton "Précédent" (ligne précédente)
- Nécessite au minimum 80px horizontal + angle dominé (ratio 2:1)
- Un indice s'affiche au premier swipe : "Swipe gauche/droite pour naviguer"
- L'indice disparaît après premier usage

---

## Persistance des données

La plupart des données sont sauvegardées en `localStorage` navigateur :

1. **femmesTRAE_settings**
   - Vitesse générale, pauses, voix sélectionnées, souffleur activé, délai souffleur
   - Sauvegardé lors d'une modification en réglages

2. **femmesTRAE_bookmarks**
   - Tous les marque-pages (section, ligne, personnage, extrait)
   - Mis à jour à chaque ajout/retrait

3. **femmesTRAE_prompter**
   - Délai du souffleur

4. **femmesTRAE_swipeHintSeen**
   - Booléen : indice de swipe a-t-il été vu ?

**Pas de sauvegarde** :
- Position actuelle (section/ligne)
- État du Mode Filage/Turbo
- État du souffleur (position dans révélation)

---

## Architecture technique

### Stack
- **HTML5** avec une seule div `#root`
- **React 18** (UMD CDN)
- **Babel Standalone** (JSX → JS en navigateur)
- **Tailwind CSS** (CDN)
- **Web Speech API** (TTS native navigateur)

### Composant principal
- `FemmesRehearsal` : composant monolithique
- ~1800 lignes de React JSX
- Utilise Hooks : `useState`, `useEffect`, `useRef`

### État global
- **Lecture** : currentSection, currentLine, isPlaying
- **Voix** : speechRate, commaPause, sentencePause, selectedVoices, voices
- **Modes** : turboMode, filageMode, filageSpeed, filageAutoAdvance, filageProgress, lectureMode, recordingMode, isRecording, recordings, playingRecording
- **Souffleur** : showPrompter, prompterState, prompterDelay, prompterTimer
- **Interaction** : showSettings, showPrompter, showBookmarks, revealedLine
- **Marque-pages** : bookmarks (objet clé-valeur)
- **Mobile** : touchStartX, showSwipeHint

### Fonctions clés

- `speakWithPunctuationPauses()` : lit texte avec pauses contextuelles + callback `onProgress`
- `speakPresidentFromChunk()` : découpe réplique en chunks, gère pause/reprendre
- `speak()` : wrapper lecture + avance automatique
- `getLastSentence()` : extrait 2 dernières phrases (pour turbo)
- `findCueLineIndex()` : cherche prochaine réplique du Président
- `startRecording()` / `stopRecordingNow()` : MediaRecorder API, clé section_ligne
- `playRecordingForKey()` / `deleteRecordingForKey()` : lecture/suppression prise
- Navigation : `handleNext()`, `handlePrevious()`, `handleReset()`
- Marque-pages : `toggleBookmark()`, `goToBookmark()`, `removeBookmark()`
- Gestes : `handleTouchStart()`, `handleTouchEnd()`

### Logique filage
- `useEffect` dédié sur `filageMode`, `currentSection`, `currentLine`, `turboMode`
- Intègre logique Turbo (sauter non-cue, ne lire que 2 phrases)
- Utilise `filageGenRef` pour éviter race conditions
- Callback `onProgress` met à jour `filageProgress` en %
- Auto-démarre enregistrement si `recordingModeRef.current` + réplique Président
- Cleanup : `cancel()` + `clearTimeout` + arrêt MediaRecorder

### Logique lecture
- `useEffect` dédié sur `lectureMode`, `currentSection`, `currentLine`
- Utilise `lectureGenRef` + `lectureAudioRef` pour éviter race conditions
- Réplique Président avec enregistrement → `new Audio(url).play()`
- Réplique Président sans enregistrement → timeout 800ms puis avance
- Répliques autres → `speakWithPunctuationPauses()` avec callback `advanceNext`

### Rendering
- Utilise `React.createElement` (pas JSX direct)
- Rendu optimisé avec dépendances d'effets
- Responsive : Tailwind breakpoints (mobile 768px)

---

## Notes importantes

### Web Speech API limitations
- Navigateur dépendant (meilleur support sur Chrome)
- `speechSynthesis.pause()` peu fiable → utilise `cancel()` + restart
- `onend` déclenché par chunk, pas par utterance
- Timeout de 30s par utterance (standard navigateur)

### Didascalies
- Texte entre parenthèses est filtré avant la lecture
- Exemple : "Ah bien (elle regarde) oui" → lira "Ah bien oui"
- Utile pour éviter qu'indications scéniques soient énoncées

### Raccourcissement en mode Turbo
- `getLastSentence()` découpe par phrase (`.!?`)
- Retourne les 2 dernières ou texte entier si ≤2 phrases
- Exemple : "Ah oui. C'est bien. Donc voilà." → "C'est bien. Donc voilà."

---

## À venir (To Do)

### 🔴 **Haute priorité** (à tester en conditions réelles)

#### 1. **Minuteur/Chronomètre de Filage** ⏱️
- **Description** : Affiche durée totale du filage en cours (HH:MM:SS)
- **Placement** : Panneau orange Mode Filage (à côté des paramètres)
- **Utilité** : vérifier durée scène, comparer versions précédentes, ajuster tempo
- **Technique** : useEffect + setInterval (update chaque seconde)
- **Optionnel** : son/vibration de fin filage

#### 2. **Mode Lecture Rapide** ⚡📖
- **Description** : Bouton pour lire tout texte d'une scène sans pauses (vitesse ≥1.5×)
- **Placement** : Panneau réglages (🔊) ou bouton dédié barre contrôle
- **Utilité** : preview complet rapide, révision pré-filage
- **Technique** : toggle bool + override pauses (0ms) + vitesse minimale en `speakWithPunctuationPauses()`

#### 3. **Enregistrement Audio Personnel** 🎤 ✅ Implémenté
- Voir section [Mode Enregistrement](#mode-enregistrement-) et [Mode Lecture](#mode-lecture-)
- **Bonus futur** : export WAV/MP3, persistance localStorage (IndexedDB), rejouer filage avec sync TTS+audio

### 📋 **Moyen terme**

- Sauvegarde position actuelle (Section/Ligne restaurée au rechargement)
- Raccourcis clavier (Space=Suivant, Shift+Space=Précédent, R=Reset, T=Turbo, F=Filage, P=Souffleur)
- Mode révision (texte blanc sur blanc, self-test mémoire, compteur révélations)
- Recherche/Jump to (chercher texte, aller ligne X)
- Notes personnelles par réplique (📝 mini-éditeur, localStorage)
- Presets params (configs nommées sauvegardées : "Travail rapide", "Intense", "Loisir")

### 💭 **Idées créatives**
- Historique filages (stats durée/date, progression sur graphique)
- Souffleur inversé (vous dites autres répliques, app dit les vôtres)
- Thèmes dark/light (☀️/🌙 toggle + Tailwind dark classes)
- Comparaison versions script (deux versions côte à côte, diffs surlighs)

#### **IDÉE FOLLE: Vérification d'Accuracy Vocale** 🎤✅

**Concept** : L'app écoute votre voix lors du filage et vérifie l'accuracy (exactitude) de votre réplique.

**Workflow**:
1. Pendant filage, quand c'est à vous (ligne Président) : au lieu de silence, un bouton 🎤 Record apparaît
2. Vous cliquez, vous dites la réplique
3. L'app utilise Web Speech API (SpeechRecognition) pour transcrire votre voix
4. Compare texte reconnu vs texte exact du script (distance Levenshtein)
5. Affiche % accuracy + highlight des mots mal dits (diff visuel)

**Cas d'usage** :
- Auto-critique : enregistrer, voir où on perd des mots
- Progression : tracker % accuracy par session (localStorage)
- Mémorisation : identifier phrases problématiques, les réviser

**Variantes** :
- Mode strict (100% ou rien) vs progressive (% tolerance)
- Word-by-word scoring vs phrase globale
- Phonetic similarity (ignorer accents régionaux)

**Challenges techniques** :
- SpeechRecognition (TTS vs. STT différent) nécessite internet
- Homophones français (tout/tu, ce/se, où/ou) → faux positifs
- Accents régionaux → besoin calibration
- Solution : télécharger modèle ML offline (avancé)

**Impact** : Très haut — game-changer pour coaching/évaluation
