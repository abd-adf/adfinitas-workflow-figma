# Brief designer — Intégration Figma → HTML email
**adfinitas Belgium · Workflow Claude Code · v1.0**

---

## Contexte

La maquette Figma est lue automatiquement par un script IA (Claude Code) qui génère le HTML email. Chaque règle ci-dessous a une raison technique directe. Un écart = correction manuelle ou rendu cassé.

---

## 1. Structure du fichier Figma

**Pages requises — dans cet ordre :**

| Page | Contenu |
|---|---|
| `_tokens` | Couleurs, typographies, espacements — toujours en première page |
| `email` | Frame desktop ET mobile côte à côte sur la même page |


⚠ Ne jamais séparer desktop et mobile sur deux pages différentes.

**Frames principales :**

| Frame | Largeur | Nom exact obligatoire |
|---|---|---|
| Desktop | 600px | `frame/desktop` |
| Mobile | 375px | `frame/mobile` |



---

## 2. Nommage des layers

**Sections — préfixe `section/` obligatoire :**

```
section/preheader
section/header
section/hero
section/body-text-1
section/body-text-2
section/donation-cards
section/signature
section/footer
section/legal
```

⚠ Un layer sans préfixe est ignoré ou mal interprété par le script.

---

## 3. Règles de design critiques pour le HTML

### Typographie
- Toutes les tailles en `px` — jamais en `%` ou `em`

### Couleurs
- Définir toutes les couleurs dans `_tokens` en valeurs hex exactes
- Pas de `rgba()` — Outlook ne supporte pas la transparence

### Espacements
- Définir les espacements dans `_tokens` : `section-gap`, `component-gap`, `horizontal-padding`
⚠ Toutes les hauteurs d'images doivent être fixées en px dans Figma — jamais "hug content" ou "fill". Sans valeur fixe, Outlook casse le layout.

### Coins arrondis
- Les coins arrondis sur les **boutons** sont supportés partout — Outlook utilise VML, les autres clients utilisent CSS. Les définir dans `_tokens`, le script gère le double mécanisme automatiquement
- Les coins arrondis sur les **images** (ex. hero) doivent être **intégrés à l'export** (baked in) — aucun équivalent VML n'existe pour arrondir une image, Outlook affichera toujours des coins carrés en CSS

### Hero
- Prévoir **deux exports** : desktop (600px) et mobile (375px)

### Boutons CTA
- Fond uni obligatoire — pas de dégradé

---

## 4. Grille mobile (frame/mobile)

Le script lit les dimensions et positions du frame mobile pour générer le CSS responsive. Ce qui n'est pas dans `frame/mobile` n'existe pas en mobile.

**À toujours mocker dans `frame/mobile` :**

| Élément | Comportement mobile attendu |
|---|---|
| Grille de dons | 3 lignes empilées — pas de colonnes |

| Padding horizontal | 20px (vs 40px desktop) |

⚠ Un élément présent uniquement en mobile (ex. bouton dans le header mobile) doit être explicitement visible dans `frame/mobile` — il ne sera pas déduit du desktop.

---

## 5. Assets — nommage et export

**Convention de nommage :**
```
[section]-[description].[ext]
```

**Exemples :**
```
✅ hero-desktop.jpg
✅ hero-mobile.jpg
✅ logo-header.png
✅ logo-footer.png
✅ icon-feestmaaltijd.png
✅ icon-facebook.png

❌ Image 1.png
❌ hero final v2.png
❌ Untitled.png
```

**Format d'export :**

⚠ **Aucun SVG dans le design** — Outlook ne supporte pas ce format. Utiliser uniquement des assets JPG ou PNG, y compris pour les icônes et logos.

| Type | Format |
|---|---|
| Photos, hero | JPG recommandé (poids) — PNG accepté |
| Logos, icônes, illustrations, transparence | PNG |

**Résolution et poids :**

| Asset | Largeur export | Poids cible |
|---|---|---|
| Hero desktop | 1200px (Retina) | < 200 KB |
| Hero mobile | 750px (Retina) | < 200 KB |
| Logo | dimensions réelles | < 50 KB |
| Icônes | dimensions réelles | < 50 KB |

---

## 6. Checklist avant livraison

```
☐ Page _tokens présente avec toutes les couleurs, polices, espacements
☐ frame/desktop nommé exactement frame/desktop — 600px
☐ frame/mobile nommé exactement frame/mobile — 375px
☐ Les deux frames sur la même page Figma
☐ Tous les layers préfixés section/
☐ Toutes les hauteurs d'images fixées en px (pas hug/fill)
☐ Icônes sociales en PNG (pas SVG)
☐ Nommage assets : [section]-[description].[ext]
☐ Pas de rgba(), pas de dégradés sur les boutons
```

---

*adfinitas Belgium — à jour mars 2026*
