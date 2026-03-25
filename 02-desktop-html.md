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
- Images: explicit width AND height attributes matching the actual CDN image dimensions, height:auto in style, display:block
- <body> must have both bgcolor attribute and style background-color: <body bgcolor="#XXXXXX" style="background-color:#XXXXXX;">
- Outer full-width <td> must have explicit bgcolor: <td align="center" bgcolor="#XXXXXX" style="background-color:#XXXXXX;">
- Head <style> block must begin with these exact rules (before any other rules):
  :root{color-scheme:light;}
  body,table,td,a{-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;}
  table,td{mso-table-lspace:0pt;mso-table-rspace:0pt;}
  img{-ms-interpolation-mode:bicubic;border:0;height:auto;line-height:100%;outline:none;text-decoration:none;}
- Add after X-UA-Compatible meta, before <title>:
  <meta name="color-scheme" content="light"/>
  <meta name="supported-color-schemes" content="light"/>

HERO — REQUIRED PATTERN (image tag, never VML background):
The hero must always be implemented as a plain <img> tag — never as a VML background image.
Use separate desktop and mobile image files. The desktop image is always present; the mobile image
is wrapped in MSO conditional comments so Outlook never sees it:

  <td align="center" style="padding:0;">
    <a href="[CTA-URL]..." target="_blank">
      <img src="[CDN-DESKTOP-HERO]" width="600" height="[H]" class="desktop-only"
           style="display:block;width:600px;height:auto;border:0;" alt="[ALT]"/>
      <!--[if !mso]><!-->
      <img src="[CDN-MOBILE-HERO]" width="[W]" height="[H]" class="mobile-only"
           style="display:none;width:100%;height:auto;border:0;" alt="[ALT]"/>
      <!--<![endif]-->
    </a>
  </td>

The base CSS must declare:
  .mobile-only{display:none;}
  .desktop-only{display:block;}

OUTLOOK CONSTRAINTS:
- Never put border-radius on <table> elements — Outlook collapses the border into a rectangle.
  border-radius is only valid on <td> and <a> elements.
- Never rely on CSS class-based display:none to hide content from Outlook.
  Outlook ignores <style> block rules. For any element that must be hidden in Outlook
  (mobile images, mobile-only layout), wrap it in <!--[if !mso]><!--> ... <!--<![endif]-->.
- When desktop and mobile variants of the same image coexist (hero, CTA buttons):
  desktop image → plain <img> with class="desktop-only"
  mobile image  → <img> with class="mobile-only" wrapped in <!--[if !mso]><!--> ... <!--<![endif]-->
  CORRECT:
    <img src="btn-desktop.png" width="259" height="52" class="desktop-only" style="display:block;..."/>
    <!--[if !mso]><!-->
    <img src="btn-mobile.png" width="188" height="39" class="mobile-only" style="display:none;..."/>
    <!--<![endif]-->
  INCORRECT (Outlook shows both):
    <img src="btn-desktop.png" class="desktop-only" style="display:block;..."/>
    <img src="btn-mobile.png" class="mobile-only" style="display:none;..."/>

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
