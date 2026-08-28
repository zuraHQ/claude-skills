---
name: actually-good-frontend-skill
description: Use for any UI build request — landing page, dashboard, sidebar, component, auth screen, marketing site, or design iteration. Lego-first workflow - ask the user for a theme, then ALWAYS build from production blocks in the Watermelon UI and Tailark shadcn registries as first priority, hand-writing UI only as last resort.
---

# Actually good frontend

**Core principle: lego blocks first, always.** Registry blocks are the
default way to build ANY visual — not something to reach for when asked, and
not a fallback. Every build starts from "which existing blocks cover this?"
and hand-written UI is the last resort, used only for glue and for what no
block covers. The blocks are legos: fetch them, snap them together, customize
content and theme — that IS the workflow.

This works because both source libraries are shadcn registries: every block
renders off the shadcn CSS variables, so one customized `globals.css` themes
all of them consistently — and blocks from the two libraries can be freely
mixed on the same page (e.g. a Watermelon hero + Tailark features + Tailark
pricing + Watermelon footer).

## Step 1 — Always ask for the theme first

When this skill activates, before touching any code, ask the user for the
theme with AskUserQuestion. Offer roughly:

- **Keep existing theme** — project's current shadcn `globals.css` as-is
- **Describe it** — user gives brand color / vibe (e.g. "violet, dark,
  rounded, techy") and you translate it into shadcn variables
- **Neutral default** — stock zinc/neutral shadcn theme

A theme is NOT just colors. To keep every project from converging on the
same-looking page, a theme is a full design personality across these axes —
vary them deliberately per project, matched to the brand:

- **Color**: palette + which role is loud (vivid primary vs near-monochrome)
- **Radius**: sharp (`--radius: 0`) / subtle (0.375rem) / soft (0.75rem) /
  pill — a sharp-cornered page and a soft one feel like different products
- **Typography**: font pairing and weight attitude (grotesk/techy, serif
  editorial headings, mono accents, tight vs airy tracking)
- **Depth**: flat borders-only vs layered shadows vs gradient-tinted surfaces
- **Density**: compact and information-dense vs spacious marketing-airy
- **Mode**: dark-first, light-first, or both

Two products can even share blocks yet look unrelated if these axes differ.
So: a fintech might get sharp corners + serif headings + flat borders; a
consumer AI app soft radius + grotesk + layered glows. Pick a combination
that fits the startup, don't default to "zinc, 0.5rem, Inter" every time.

Then apply it by editing the shadcn CSS variables in `globals.css`
(`app/globals.css` or `src/index.css` etc.) — both `:root` and `.dark`:
`--background --foreground --card --popover --primary --secondary --muted
--accent --destructive --border --input --ring --radius --chart-1..5
--sidebar-*` — plus the font setup in the layout/CSS. Because both libraries
consume these tokens, the theme propagates to every fetched block
automatically.

If the project has no shadcn setup (`components.json` missing), run
`npx shadcn@latest init` first.

## Step 2 — Find blocks in the registries

Two sources, both free, no auth:

| Source | Index | Item install |
|---|---|---|
| Watermelon UI | `https://registry.watermelon.sh/r/registry.json` | `npx shadcn@latest add https://registry.watermelon.sh/r/<name>.json` |
| Tailark | `https://tailark.com/r/registry.json` | `npx shadcn@latest add https://tailark.com/r/<name>.json` |

Browse the index (names + titles + descriptions), filter by category:

```bash
curl -s https://registry.watermelon.sh/r/registry.json | jq -r '.items[] | "\(.name)\t\(.title // "")"' | grep -i hero
curl -s https://tailark.com/r/registry.json | jq -r '.items[] | "\(.name)\t\(.title // "")"' | grep -i pricing
```

To evaluate a candidate before installing, fetch its item JSON — it contains
the full source code, npm `dependencies`, and `registryDependencies`:

```bash
curl -s https://tailark.com/r/hero-section-3.json | jq -r '.files[0].content'
```

### What lives where (verified catalog map)

**Watermelon** (~1,074 items) — the app-UI and animated side:
- 30+ **complete dashboards** (`e-commerce-dashboard`, `erp-dashboard`,
  `mail-dashboard`, `invoice-generator-dashboard`, `lead-dashboard`, …)
- **Sidebars** (`macos-sidebar`, …), app shells, data-tables (×14),
  command search, file upload, forms, auth (×13)
- **Animated component variants**: button ×47, accordion ×20, alert ×37,
  avatar ×28, badge ×28, calendar ×26, dialog ×24, tabs, disclosures,
  micro-interactions
- Marketing too: hero ×44, footer ×31, cta, faq, feature, bento ×17
- Many items have a `-base` suffixed twin — an alternate build of the same
  component; inspect both JSONs and pick the one matching the project stack.

**Tailark** (~469 items) — the deep marketing-site catalog, numbered variants:
- `hero-section-1..16`, `secondary-hero-1..20`, `header-1..8` (navbars),
  `footer-1..5`
- `features-1..14`, `expandable-features-1..22`, `features-carousel`,
  `bento-1..14`, `how-it-works-1..7`, `integrations-*`, `flow-1..13`
- `pricing-1..5`, `comparator-1..7`, `stats-1..8`, `testimonials-1..8`,
  `logo-cloud-1..10`, `faqs-1..5`, `call-to-action-1..3`
- `team-1..16`, `open-roles-*` (careers), `investors-*`, `blog-blocks-1..7`,
  `contact-1..10`
- Auth: `login-blocks-1..11`, `sign-up-1..11`, `forgot-password-1..5`
- Plus AI/app UI (`ai-chat-interface`, `kanban`, `gantt`, …) and `core-*`
  brand logos/utilities

### The division of labor

The two registries are for **overall visuals** — big composed pieces that
define how a screen looks:

- Full **dashboards** and app shells → Watermelon
- **Sidebars** / navigation chrome → Watermelon
- **Landing pages** and landing-page sections (hero, features, pricing,
  testimonials, footer, header, auth pages) → Tailark first (deepest
  catalog), Watermelon for flashier animated heroes/footers, mix both freely

Everything **component-level** — table, card, button, dialog, input,
dropdown, tabs, etc. — is explicitly stock **shadcn/ui**:

```bash
npx shadcn@latest add table card button
```

Don't shop the registries for an individual component; go there when the
request is a screen, page, or section. (Watermelon's animated component
variants exist as an upgrade path, but only when the user explicitly wants a
component fancier than stock.) The registries' `registryDependencies`
resolve to official shadcn components anyway, so all three tiers share the
same primitives and theme tokens.

### Dashboards: borrow the shell, strip the fake data

Rule for every dashboard request — never hand-build the chassis, and never
ship someone else's demo content:

1. **Pull the dashboard design** from either the official shadcn registry
   (`npx shadcn@latest add dashboard-01`, sidebar blocks `sidebar-01..16`)
   or a Watermelon full dashboard (`e-commerce-dashboard`,
   `mail-dashboard`, …) — whichever layout best fits the user's product.
2. **Keep the shell**: sidebar, top bar, layout grid, navigation chrome.
   Adapt the sidebar's nav items to the user's actual app sections.
3. **Strip the body**: remove the block's pre-filled fake data — demo
   charts, fake revenue stats, placeholder tables and users. Deliver the
   dashboard as a clean shell with the sidebar in place and an empty content
   canvas, so the user is never confused by predefined fake data that looks
   like real functionality.
4. Fill the body only with what the user's request actually calls for, using
   stock shadcn components (tables, cards, charts) wired to their real data
   or clearly-empty states.

### Compose proactively, fitted to the user's startup

Whenever there's a chance to use a registry item — faq, hero, pricing,
bentos, testimonials, logo clouds, stats — take it, without waiting for the
user to name each section. When they ask for "a landing page", infer the
right section lineup from their intent and what their startup is, then pick
variants whose structure fits the product's story:

- SaaS/dev tool → hero + logo-cloud + features/bento + code-demo or
  integrations + pricing + faq + cta + footer
- AI product → hero + `ai-*` showcase blocks + how-it-works + bento +
  pricing + faq
- Agency/portfolio-ish → hero + content + team + testimonials + contact
- Choose the variant by fit, not number order: a data-heavy product wants a
  stats/bento block; a visual product wants an image-led hero; a B2B startup
  wants logo-cloud + comparator.

Propose the lineup, then build it from blocks. The user steers by swapping
or dropping sections, not by describing every section from zero.

## Step 3 — Install, compose, adapt

1. Install each chosen block with `npx shadcn@latest add <item-url>`. The CLI
   pulls `registryDependencies` and npm deps automatically. If the CLI fails,
   fall back to writing the files from the item JSON's `files[].content` and
   installing its `dependencies` manually.
2. Assemble the page from the installed blocks in order (header → hero →
   sections → cta → footer).
3. Replace the block's placeholder copy, images, and links with the user's
   real content. Keep the block's structure and spacing.
4. **Token audit**: check every fetched block for hardcoded values that bypass
   the theme — hex/rgb colors, fixed radii, one-off fonts. Swap them to the
   `globals.css` tokens (`bg-primary`, `text-muted-foreground`, `rounded-lg`
   via `--radius`, `border-border`, …) so Step 1's theme actually governs
   everything.
5. **Harmonization pass** — mixed blocks must look like one source. When a
   page combines Watermelon and Tailark blocks, go through them side by side
   and normalize anything that betrays different origins:
   - **Radius**: one consistent rounding scale driven by `--radius`. If one
     block is pill-rounded and its neighbor is square, align both to the
     theme's radius.
   - **Font**: all blocks inherit the project's font stack — no block brings
     its own font-family.
   - **Borders & shadows**: same border color (`border-border`), width, and
     shadow depth treatment across sections.
   - **Color usage**: same token roles for the same jobs (primary for CTAs,
     muted-foreground for secondary text) in every block.
   - **Rhythm**: consistent max-width container and vertical section padding
     so sections stack like one designed page, not stitched screenshots.
6. Render and verify: responsive at narrow widths, dark mode if the theme has
   one, no console errors.

## Iterations

When the user wants a different look for a section, don't hand-restyle it —
swap in a sibling variant from the registries (`hero-section-3` →
`hero-section-11`, or jump libraries: Tailark hero → Watermelon hero) and
re-apply their content. Variant-swapping is cheaper and better-looking than
incremental hand edits. Hand-edit only for content, wiring, and small layout
fits.

## Rules

- Blocks ship as designed — including their built-in animation; using this
  skill counts as the user requesting it. Do not ADD extra motion beyond what
  the fetched components contain.
- Never hand-write a section type that exists in either catalog; fetch it.
  These libraries already answer "what should a hero / pricing / dashboard
  look like" at production quality — take advantage of that instead of
  designing from scratch. Go fully custom ONLY when the user explicitly asks
  for fully custom (and even then, a registry block is often the right
  skeleton to customize from).
- One theme to rule them all: all colors/radius flow from `globals.css`
  tokens. No one-off hex values in composed pages.
- **Theme changes touch ONLY `globals.css`** — edit the `:root`/`.dark`
  variables and stop. Never restyle individual components for a theme change.
  If a block doesn't respond to the new theme, it has a hardcoded value:
  replace that value with the matching token (a one-time fix), don't hand-tune
  its colors.
- Keep semantic HTML and keyboard accessibility intact when adapting blocks;
  registry code is a starting point, not an excuse.
