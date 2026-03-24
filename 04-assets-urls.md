# Prompt 4 — Export assets + remplacement URLs

## Ce que ça fait
Exporte toutes les images depuis Figma, construit une image map persistante (URL Figma → nom CDN), puis remplace toutes les URLs d'images dans le HTML par les URLs finales hébergées (Bunny.net).

> ⚠️ Ce prompt utilise à nouveau Figma MCP. Vérifier la connexion avec `/mcp`.  
> ⚠️ **Renseigner `[CDN-BASE-URL]` avant de lancer.** Si laissé en placeholder, le HTML contiendra des URLs d'images cassées.

## Variables à remplacer

| Variable | Quoi renseigner |
|---|---|
| `[PROJECT-SLUG]` | Préfixe nommage assets (ex. careme2026-entraide) |
| `[CDN-BASE-URL]` | URL complète du dossier CDN — ex. `https://adfinitas.b-cdn.net/careme2026-entraide/` |
| `[CAMPAIGN]` | Même slug que dans les prompts précédents |

## Prompt à copier-coller

```
CDN_BASE_URL: [CDN-BASE-URL]
PROJECT_SLUG: [PROJECT-SLUG]

Read figma-context-[CAMPAIGN].md to get:
- figma_file_key (under ## File)
- All image rows from ## Images table (layer name, Figma URL, dimensions, section)

Output file: email-[campaign]-[client].html

You have access to the Figma file via MCP.

STEP 1 — BUILD IMAGE MAP
Before downloading anything, write image-map.json:
{
  "figma_url_1": "[PROJECT-SLUG]-hero-background.jpg",
  "figma_url_2": "[PROJECT-SLUG]-card-80.jpg",
  ...
}
Apply naming convention:
[PROJECT-SLUG]-[section]-[description].[ext]

STEP 2 — EXPORT ASSETS
Export each image from the Figma file using the Figma URL from image-map.json.
Download sequentially (not in parallel — background jobs & are not reliable here).
Use sequential curl or xargs -P4 at most.

Check weight of each file:
- Photos: target < 200 KB, max 2 MB
- Logos/icons: target < 50 KB
Flag any file that exceeds the limit.

STEP 3 — REPLACE URLs IN HTML
Read the output HTML file.
For each entry in image-map.json, replace the Figma URL
with: [CDN-BASE-URL][filename]

Confirm:
"✓ image-map.json written — [number] entries"
"✓ [number] assets exported"
"✓ [number] URLs replaced in [output-filename]"
```

## Vérifications finales

- [ ] Images chargent — rafraîchir le HTML dans le navigateur, toutes les images s'affichent depuis le CDN
- [ ] `utm_content` — chaque bouton a une valeur distincte (ex. `header-cta`, `hero-cta`, `card-80`)
- [ ] **Test de rendu (obligatoire)** — aller sur [testi.at](https://testi.at/newprojet) et coller le HTML pour vérifier le rendu sur tous les clients email majeurs
- [ ] Envoi test — coller le HTML dans Mailchimp/Brevo et s'envoyer un email test avant l'envoi réel

> ⚠️ Ne pas sauter le test testi.at. Les emails peuvent sembler corrects dans le navigateur mais se casser dans Outlook ou sur mobile.

## Quick fixes courants

### Montants donation non centrés
```
In email-[campaign]-[client].html, fix section/donation-cards:
Add text-align: center to the amount <td>
on BOTH desktop AND mobile.
Check no inline style overrides the responsive class.
```

### Photos carte hauteur inégale
```
In email-[campaign]-[client].html, fix section/donation-cards:
Card photos: height: 200px, object-fit: cover,
width: 100%, display: block on all photos.
Mobile: height: 180px.
```

### Signatures désalignées
```
In email-[campaign]-[client].html, fix section/signature:
Two columns 50% each, vertical-align: top,
text-align: center, photo above name.
```

### Hero mobile pas pleine largeur
```
In email-[campaign]-[client].html, find all tables with
a hard-coded width:600px that are full-bleed sections.
Add width:100%!important to their mobile CSS class.
Do not modify desktop styles.
```

### Mobile ne rend pas correctement
```
Read figma-context-[CAMPAIGN].md section "Mobile Differences".
Fix all @media max-width: 620px rules in email-[campaign]-[client].html
to match the mobile specifications exactly.
```
