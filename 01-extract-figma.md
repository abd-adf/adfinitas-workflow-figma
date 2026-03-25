# Prompt 1 — Extract Figma → fichier context local

## Ce que ça fait
Lit le fichier Figma une seule fois via MCP et sauvegarde toutes les informations de design dans un fichier Markdown local. Ce fichier remplace Figma pour les prompts 2 et 3 — plus aucun appel MCP.

## Variables à remplacer

| Variable | Quoi renseigner |
|---|---|
| `[FILE-KEY]` | File key Figma (voir cheat sheet) |
| `[DESKTOP-NODE]` | Node ID du frame/desktop (ex. 3:314) |
| `[MOBILE-NODE]` | Node ID du frame/mobile (ex. 3:336) |
| `[CAMPAIGN]` | Slug campagne court (ex. careme2026-entraide) |

## Prompt à copier-coller

```
FILE_KEY: [FILE-KEY]
DESKTOP_NODE: [DESKTOP-NODE]
MOBILE_NODE: [MOBILE-NODE]

TASK
Extract design context into figma-context-[CAMPAIGN].md.

Constraints
- Do NOT generate HTML.
- Do NOT take screenshots.
- Ignore browser selection errors — continue to next step.
- Use the provided node IDs only.
- Run tool calls in the exact order below.
- Do not inspect unrelated nodes.

TOOL ORDER
1. get_metadata nodeId="[DESKTOP-NODE]"
   Identify the top-level section hierarchy.
2. get_design_context nodeId="[DESKTOP-NODE]"
   Extract layout, elements, images, typography, and all text strings.
3. get_metadata nodeId="[MOBILE-NODE]"
   Detect responsive overrides relative to desktop.
   Attributes to capture: padding, gap, fills, dimensions,
   visibility changes, element reordering.

OUTPUT
Write figma-context-[CAMPAIGN].md:

# Design Context — [CAMPAIGN]

## File
figma_file_key: [FILE-KEY]

## Global Tokens
Unique values only (from get_design_context styles):
- colors
- fonts and font sizes
- spacing
- radius if present
Format: token-name: value

## Desktop Sections
Sequential list. For each section:
- name and node ID
- layout
- dimensions: width, height, padding, gap
- background / fills
- typography: family, size, weight, color, line-height
- elements: text, image, button, spacer
- raw copy: every string exactly as written in Figma

Never summarise. Example:
✅ "[exact text as it appears in Figma — copied in full]"
❌ "paragraph about children in poverty"

Capture merge tags exactly as written (e.g. <perso>, *|FNAME|*).
Do not interpret or replace them.

## Mobile Overrides
List ONLY differences vs desktop. For each section:
- layout changes
- dimension changes
- padding changes
- element reordering
- visibility changes
- typography: if font size changes, write new value.
If no change, write [SAME AS DESKTOP] explicitly.

## Images
Sequential table of every image asset, in document order.
One row per unique URL — deduplicate shared assets.
If the same URL appears in multiple sections, keep one row
and add a note: (also used in: [section]).

| # | Layer name | Figma URL | Width | Height | Section | Variant |
|---|---|---|---|---|---|---|
| 1 | component/logo | https://... | 150px | 150px | header | — |
| 2 | section/hero-desktop | https://... | 600px | 609px | hero | desktop |
| 3 | section/hero-mobile | https://... | 375px | 455px | hero | mobile |

Notes:
- Record exact rendered dimensions, not intrinsic component size.
- Hero images: capture desktop and mobile as two separate rows with explicit pixel dimensions.
  These will be implemented as plain <img> tags (never VML background) — see Prompt 2.
- CTA button images: if the design uses distinct desktop and mobile button images,
  capture both as separate rows and mark variant accordingly.
- All Figma URLs expire in 7 days — replace via Prompt 4.

RULES
- Record 100% of text strings exactly as written. Never summarise.
- If a text node returns empty or truncated,
  call get_design_context again on that node before writing.
- This markdown is the only copy source for Prompt 2.
  Missing text here = fabricated text there.
- Output only the Markdown file. No commentary.
```

## Vérifier avant de passer au Prompt 2

- [ ] File key écrit sous `## File`
- [ ] `## Global Tokens` liste toutes les couleurs, fonts et spacing
- [ ] `## Desktop Sections` contient le copy brut — pas des descriptions
- [ ] `## Mobile Overrides` a `[SAME AS DESKTOP]` explicitement pour les typos inchangées
- [ ] `## Images` liste toutes les URLs Figma avec dimensions
- [ ] Merge tags (ex. `<perso>`) capturés tels quels, non interprétés
