# Prompt 3 — Ajouter le responsive mobile

## Ce que ça fait
Ajoute des classes CSS et un bloc `@media` au HTML existant pour gérer le rendu mobile. Lit les specs mobile depuis `figma-context-[CAMPAIGN].md`. Ne régénère pas le fichier.

> ⚠️ **Pas de `/clear` entre Prompt 2 et Prompt 3.**  
> Prompt 3 édite le fichier généré par Prompt 2 — il a besoin de l'historique de conversation pour connaître les classes et la structure utilisées.

## Prompt à copier-coller

```
Read email-[campaign]-[client].html.
Read figma-context-[CAMPAIGN].md, section "Mobile Differences".

TASK
Add responsive mobile support to the existing HTML.
Edit the file in place. Do NOT regenerate it.

CHANGES
1. Add mobile utility classes only where mobile behavior differs from desktop
   Examples: mobile-full, mobile-hide, mobile-stack, mobile-center, mobile-text-sm

2. Add one <style> block before </head> containing:
   .mobile-only{display:none;}
   .desktop-only{display:block;}
   @media screen and (max-width: 620px) {
     /* mobile overrides */
   }

3. Apply mobile rules from figma-context-[CAMPAIGN].md, including:
   - stacked cards where desktop uses columns
   - full-width CTA buttons
   - section font-size reductions
   - centered signature photo above name
   - image height adjustments
   - side padding reduced to 20px
   - width:100%!important for full-bleed 600px tables

4. For desktop/mobile image pairs (hero, CTA buttons), apply this pattern in @media:
   .desktop-only{display:none!important;}
   .mobile-only{display:block!important;width:100%!important;height:auto!important;}

   The desktop image is always present as a plain <img>.
   The mobile image must already be wrapped in <!--[if !mso]><!--> ... <!--<![endif]-->
   (applied in Prompt 2) so Outlook never renders it regardless of CSS.
   Do not remove those conditional comments — they are required for Outlook.

CONSTRAINTS
- Edit email-[campaign]-[client].html in place
- Do not create a new file
- Do not change content or desktop layout
- Do not rename existing classes
- Only add classes and the media-query block
- No comments
- No blank lines
- Never use border-radius on <table> elements — Outlook collapses it.
  border-radius is valid only on <td> and <a>.
- Never rely on CSS class rules alone to hide content from Outlook.
  Mobile-only content must be excluded via <!--[if !mso]><!--> conditional comments,
  not just via display:none in the <style> block.

RETURN
✓ responsive added — [number] rules
```

## Vérifier avant de passer au Prompt 4

Tester le rendu mobile dans le navigateur :
1. Ouvrir le fichier HTML dans Chrome ou Firefox
2. F12 > icône toggle device (ou Ctrl+Shift+M) > largeur 375px

Checklist width:100% à 375px :
- [ ] Tables hero — full-bleed avec `width:100%!important`
- [ ] Tables donation cards — chaque carte pleine largeur quand empilée
- [ ] Boutons CTA — pleine largeur
- [ ] Signature — photo centrée au-dessus du nom
- [ ] Side padding — 20px sur mobile
