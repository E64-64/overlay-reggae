# 🎡🍃 Guide d'installation — Overlay reggae + Roue de la fortune

Setup complet pour OBS + Twitch. Suis les étapes dans l'ordre.

---

## 1. Les fichiers

| Fichier | Rôle | Taille OBS conseillée |
|---|---|---|
| `index.html` | Roue de la fortune (déclenchée par les points de chaîne) | 1280 × 720 |
| `overlay.html` | Panneaux events : sub / dons (gifts + bits) / follow — **en direct** | 1920 × 1080 |
| `chat.html` | Chat (ressource indépendante) | ~420 × 620 |
| `avatar.html` | Webcam + avatar lip-sync (bascule auto quand la cam est coupée) | taille de la cam |

> Garde tout le dossier **en local** sur le PC du streamer. Ne le mets pas en ligne / sur GitHub (à cause du token, voir §3).

---

## 2. Prérequis

- **OBS Studio** installé et lancé.
- Le streamer **connecté à son compte Twitch** dans son navigateur.
- Aucun autre logiciel requis (pas de Streamer.bot : on branche Twitch en direct).

---

## 3. Générer le token Twitch ⚠️ (à faire sur le compte DU STREAMER)

1. Le streamer va sur **https://twitchtokengenerator.com** (connecté à SON compte Twitch).
2. Onglet **Scopes** → coche **exactement** ces 4 droits :
   - `channel:read:subscriptions`  → subs / resubs
   - `moderator:read:followers`    → follows
   - `bits:read`                   → dons (bits / cheers)
   - `channel:read:redemptions`    → roue (points de chaîne)
3. Clique **Generate Token!** → autorise → **copie l'ACCESS TOKEN**.

### 🔒 Sécurité (important)
- Le token = un accès au compte. **Ne jamais le partager**, ne pas le montrer à l'écran/en stream, ne pas le mettre en ligne.
- Garder les fichiers `.html` **en local** uniquement.
- En cas de doute → régénérer un token (ça invalide l'ancien) sur le même site.

---

## 4. Coller le token dans les fichiers

Ouvre les fichiers avec le **Bloc-notes** (clic droit → Ouvrir avec).

### Dans `index.html` (la roue)
Cherche le bloc `twitch:` et mets :
```js
  twitch: {
    enabled: true,
    token: 'COLLE_LE_TOKEN_ICI',
    rewardName: 'Roue du Chaos',   // doit correspondre EXACTEMENT au nom de la récompense (§6)
  },
```

### Dans `overlay.html` (les events)
Cherche le bloc `CONFIG` / `twitch:` et mets :
```js
const CONFIG = {
  twitch: {
    enabled: true,
    token: 'COLLE_LE_MEME_TOKEN_ICI',
  },
};
```

> Le **même token** marche pour les deux fichiers (il a tous les scopes).

Enregistre les deux fichiers (Ctrl+S).

---

## 5. (Pour la roue) Créer la récompense de points de chaîne

1. Le streamer va sur **https://dashboard.twitch.tv** → **Viewer Rewards → Channel Points → Manage Rewards**.
2. **Add New Custom Reward** → Nom : `Roue du Chaos` (le même que dans `index.html`).
3. Choisis un coût en points → **Save**.

---

## 6. Ajouter les sources dans OBS

Pour chaque fichier : **Sources → + → Navigateur**, coche **Fichier local**, sélectionne le fichier, règle la taille (tableau §1), **OK**.

- ⚠️ **Ne pas** ajouter `?debug=1` dans l'URL → sinon des infos de réglage seraient visibles à l'antenne. URL = juste le fichier.
- Ordre conseillé (de haut en bas dans OBS) : `chat` / `overlay` / `avatar` au-dessus du jeu, etc.

### Avatar (cam + lip-sync)
- Cette source **remplace** la source caméra classique (elle gère la cam ET l'avatar).
- 1re fois : **clic droit sur la source avatar → Interagir** → clique « Activer caméra + micro » → autorise.
  - Si rien ne s'affiche : remets temporairement l'URL en `avatar.html?setup=1`, Interagir, autorise, puis **enlève `?setup=1`**.
- Mettre la **bonne caméra et le bon micro en périphériques par défaut Windows** (l'avatar prend les défauts).
- Pour basculer cam/avatar en live : couper la caméra (ou touche **C** en mode Interagir).

---

## 7. Tester

Ouvre la **console** d'une source (clic droit → Interagir, puis F12 si dispo) ou teste les fichiers dans un navigateur :

- **overlay** : console doit afficher `[Twitch] token OK pour <login>` puis `abonné : channel.subscribe`, etc.
  - `échec channel.follow 403` → scope `moderator:read:followers` manquant.
- **roue** : `[Twitch] abonné aux points de chaîne ✅`. Un viewer dépense ses points → la roue tourne.
- **roue (test manuel)** : dans la source, touche **Espace** = tour aléatoire.

---

## 8. Aperçu vs Antenne

- En ouvrant un fichier **dans un navigateur** (hors OBS), un fond sombre + des démos s'affichent : c'est normal, c'est l'**aperçu**.
- Dans **OBS**, le fond est transparent et les démos disparaissent automatiquement.
- `?debug=1` = panneaux de réglage (à n'utiliser QUE pour régler, jamais à l'antenne).

---

## 9. Personnalisation rapide

- **Gages de la roue** : tableau `ITEMS` en haut de `index.html` (`short` = texte sur la roue, `full` = texte du résultat).
- **Couleurs / vitesse / sons** : bloc `CONFIG` en haut de `index.html`.
- **Case VIP** : `weight: 0.154` ≈ 0,5 % de chance (formule expliquée dans le code).
- **Chat** : pour du vrai chat, superposer un widget tiers (StreamElements / KapChat) par-dessus `chat.html`, ou pousser des messages via `addChatMessage()`.

---

Bon stream ! 🎶🍃
