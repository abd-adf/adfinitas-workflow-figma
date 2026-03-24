# Prompt 2 — Générer le HTML desktop

## Ce que ça fait
Génère l'email HTML table-based complet en version desktop uniquement, en lisant le fichier `figma-context-[CAMPAIGN].md` local. Zéro appel Figma MCP.

> ⚠️ Nommer le fichier de sortie explicitement dans le prompt. Un placeholder a causé des confusions de nom de fichier par le passé (`TEMPLATE_email-[wrong-name].html`).

## Variables à remplacer

| Variable | Quoi renseigner |
|---|---|
| `[CTA-URL]` | URL de la page de don principale |
| `[CAMPAIGN-NAME]` | Slug UTM (ex. careme2026) |
| `email-[campaign]-[client].html` | Nom de fichier exact — décider une fois, utiliser partout |
| `[ESP]` | Plateforme email : `mailchimp`, `brevo`, ou autre |
| `[CAMPAIGN]` | Même slug que dans Prompt 1 |

> ℹ️ Le champ `[ESP]` détermine les merge tags.  
> Mailchimp : `*|ARCHIVE|*` et `*|UNSUB|*`  
> Brevo : `{{ mirror }}` et `{{ unsubscribe }}`

## Prompt à copier-coller

```
Generate a production HTML email using table-based markup
for Outlook 2016+, Gmail, and Apple Mail.

INPUT
- Read figma-context-[CAMPAIGN].md
- Do NOT use Figma MCP

OPTIONAL TEMPLATE
- If template.html exists, use it as the structural base
- Preserve its HTML architecture, classes, and MSO patterns
- Update content and tokens to match figma-context-[CAMPAIGN].md

LINKS
- Main CTA base URL: [CTA-URL]
- UTM: ?utm_source=email&utm_medium=email&utm_campaign=[CAMPAIGN-NAME]&utm_content=[button-name-slug]
- Preserve existing query params

ESP
- Platform: [ESP]
- Use correct merge tags for mirror/archive link and unsubscribe link
- If unknown, use: [[MIRROR_LINK]] and [[UNSUBSCRIBE_LINK]]

CONSTRAINTS
- Table-based, inline styles, no comments, no blank lines
- Desktop only; no media queries
- MSO conditional buttons required
- Hidden preheader with zero-width non-joiner
- Images: explicit width, height:auto, display:block

OUTPUT
- Open email-[campaign]-[client].html immediately
- Write each section to disk as you generate it
- Do not buffer full HTML in memory
- Do not print HTML in terminal
- Generate the complete file without truncation — do not stop mid-HTML
- If any text content is missing from figma-context-[CAMPAIGN].md,
  insert [MISSING — description] and continue. Never fabricate copy.
- Return only: ✓ desktop email generated (or list any [MISSING] items found)
```

## Vérifier avant de passer au Prompt 3

- [ ] Layout — sections, espacement, couleurs correspondent au design Figma desktop
- [ ] Boutons — liens CTA pointent vers la bonne URL avec paramètres UTM
- [ ] Merge tags — `*|ARCHIVE|*` et `*|UNSUB|*` (ou équivalents) présents
- [ ] Rechercher `[MISSING]` dans le HTML — résoudre chaque occurrence avant Prompt 3

> ⚠️ Corriger tous les problèmes desktop maintenant. C'est beaucoup plus simple sans le CSS responsive.
