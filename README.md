# claude-skills

A collection of custom [Claude Code skills](https://docs.claude.com/en/docs/claude-code/skills).

## Skills

### `actually-good-frontend-skill`

A lego-first frontend workflow for Claude Code. Instead of letting the model
hand-write UI from scratch (and reinvent a mediocre hero section every time),
it assembles pages from production-quality shadcn registry blocks:

- **[Watermelon UI](https://ui.watermelon.sh)** — 1,000+ items: complete
  dashboards, sidebars, animated components, heroes, footers
- **[Tailark](https://tailark.com)** — 460+ marketing blocks: heroes,
  features, pricing, testimonials, teams, auth pages, bentos

**How it works:**

1. **Theme first** — on activation it asks for a theme: not just colors, but
   a full design personality (radius, typography, depth, density, dark/light).
   The theme lives only in shadcn's `globals.css` variables, so it governs
   every block automatically and theme changes never touch components.
2. **Lego blocks always** — registry blocks are the first priority for any
   visual (landing pages, dashboards, sidebars, sections). Plain components
   (tables, cards, buttons) come from stock shadcn/ui. Hand-written UI is
   last resort, for glue and gaps only.
3. **Mix freely** — both libraries are shadcn-based, so a Watermelon hero +
   Tailark pricing on one page share the same primitives and tokens. A
   harmonization pass (radius, fonts, borders, rhythm) makes mixed blocks
   read as one designed source.
4. **Iterate by swapping** — "make the hero different" fetches a sibling
   variant from the registries instead of hand-restyling.

## Install

Copy a skill folder into your skills directory:

```bash
# personal (all projects)
cp -r actually-good-frontend-skill ~/.claude/skills/

# or per-project
cp -r actually-good-frontend-skill your-project/.claude/skills/
```

Claude Code picks it up automatically; invoke manually with
`/actually-good-frontend-skill` or let it trigger on any UI build request.
