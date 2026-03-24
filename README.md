# adfinitas-workflow-figma

HTML email generation workflow — Figma → Claude Code  
adfinitas Belgium — internal use only

## What this is
4 prompts to run sequentially in Claude Code to generate a 
production-ready HTML email from a Figma design via MCP.

## How to use
1. Prepare your variables (file key, node IDs, CTA URL, ESP) — see cheat sheet in each prompt
2. Open Terminal in your campaign folder and launch `claude`
3. Run the 4 prompts in order, respecting the /clear rules below

## /clear rules
| Transition | /clear ? |
|---|---|
| After Prompt 1 | ✅ YES |
| Between Prompt 2 and 3 | ❌ NO — Prompt 3 needs the conversation history |
| After Prompt 3 | ✅ YES |
| After Prompt 4 | ✅ YES |

## Prompts
| File | What it does |
|---|---|
| 01-extract-figma.md | Reads Figma via MCP → saves local context file |
| 02-desktop-html.md | Generates desktop-only table-based HTML |
| 03-mobile-css.md | Adds @media mobile CSS to existing file |
| 04-assets-urls.md | Exports assets + replaces URLs with CDN paths |

## How to commit a change

When you update a prompt after a session learning, save the file then run these 3 commands in Terminal:

```bash
# 1. Navigate to the repo folder
#    Tip: type "cd " then drag the folder from Finder into Terminal
cd /path/to/workflow-prompts

# 2. Stage your changes
git add .

# 3. Commit with a clear message
git commit -m "fix 02-desktop-html — describe what changed"

# 4. Push to GitHub
git push
```

To get the latest changes from a colleague:
```bash
git pull
```

## Contributing
One commit per fix. Use clear messages:
`fix 02-desktop-html — describe what changed`
