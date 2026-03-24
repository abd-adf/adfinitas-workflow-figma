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
- MSO conditional buttons required only OUTSIDE VML context (header, footer)
- Hidden preheader with zero-width non-joiner
- Images: explicit width, height:auto, display:block
- <body> must have both bgcolor attribute and style background-color: <body bgcolor="#XXXXXX" style="background-color:#XXXXXX;">
- Head <style> block must begin with these exact rules (before any other rules):
  :root{color-scheme:light;}
  body,table,td,a{-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;}
  table,td{mso-table-lspace:0pt;mso-table-rspace:0pt;}
  img{-ms-interpolation-mode:bicubic;border:0;height:auto;line-height:100%;outline:none;text-decoration:none;}
- Add after X-UA-Compatible meta, before <title>:
  <meta name="color-scheme" content="light"/>
  <meta name="supported-color-schemes" content="light"/>

VML HERO BACKGROUND (if email has a hero with background image):
- VML opening: <v:textbox inset="0,0,0,0"> — no style attribute, no inner <div> wrapper
  CORRECT:   <v:textbox inset="0,0,0,0"><![endif]-->
  INCORRECT: <v:textbox style="mso-fit-shape-to-text:false" inset="0,0,0,0"><div style="..."><![endif]-->
- VML closing: </v:textbox></v:rect> — no </div>
  CORRECT:   <!--[if gte mso 9]></v:textbox></v:rect><![endif]-->
  INCORRECT: <!--[if gte mso 9]></div></v:textbox></v:rect><![endif]-->
- Hero CTA button inside VML: use plain unconditional table button (NO <!--[if mso]> inside VML)
  Nested conditional comments inside <!--[if gte mso 9]> cause blank emails at 120 DPI in Outlook 2021+
  CORRECT inside VML hero:
    <table role="presentation" border="0" cellpadding="0" cellspacing="0" align="center"><tr>
    <td align="center" bgcolor="#COLOR" style="background-color:#COLOR;border-radius:4px;mso-padding-alt:0;">
    <a href="[URL]" style="...;mso-padding-alt:12px 24px;">BUTTON TEXT</a></td></tr></table>
- Add bgcolor="#XXXXXX" to the <td> wrapping the VML hero to prevent image bleed below VML rect

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
