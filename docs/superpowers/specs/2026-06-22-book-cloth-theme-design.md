# Book Cloth — Obsidian theme design

**Date:** 2026-06-22
**Author:** SurprisedDuck
**Status:** Approved, pre-implementation

## Summary

Book Cloth is a **dark-only** Obsidian theme styled after **Claude Cowork**: a warm,
toasted-charcoal "paper" surface (warm-neutral greys, never blue-grey), a clay/coral
accent, warm off-white ink, and **serif headings**. It mirrors the repo shape of the
existing Pastel Frost theme.

- Repo: `SurprisedDuck/obsidian-book-cloth`
- Folder: `obsidian-book-cloth/`
- License: MIT

## Goals

- Capture the recognizable Claude/Anthropic "Cowork" feel in a dark Obsidian theme:
  warm paper, clay accent, serif display type.
- Full coverage of Obsidian surfaces (chrome + editor + UI), not just the editor.
- Consistent rendering on any machine (bundled heading font).

## Non-goals (YAGNI)

- No light mode.
- No settings-toggle style variants (Style Settings plugin support).
- No per-color customization UI.

## Palette

Warm-neutral ramp, darkest → lightest. All greys are warm (slight brown/amber bias),
never cool/blue.

| Role | Hex |
| --- | --- |
| Backdrop / titlebar (darkest) | `#1a1a18` |
| Sidebar | `#1f1e1d` |
| Editor surface | `#262624` |
| Elevated / cards (secondary-alt) | `#2c2a27` |
| Surface / hover | `#33312e` |
| Surface high | `#3d3b38` |
| Border | `#38362f` |
| Border (stronger) | `#423f39` |
| Table row odd / even | `#222220` / `#272623` (subtle stripe) |

Text:

| Role | Hex |
| --- | --- |
| Text (primary) | `#d6d2c6` |
| Emphasis | `#f5f3ec` |
| Muted | `#8f8a80` |
| Faint | `#6b675e` |

Accent family (clay / book cloth):

| Role | Hex |
| --- | --- |
| Clay (accent) | `#cc785c` |
| Coral (links) | `#d97757` |
| Coral bright (link hover / active) | `#e08a6b` |

Accent as HSL (drives `--interactive-accent`, toggles, selection handles, active tab
indicator): **h 15°, s 52%, l 58%**. Provide an RGB triplet `204,120,92` for `rgba()`
tints.

Semantic / syntax — a warm "aurora":

| Role | Hex |
| --- | --- |
| Red | `#e0837a` |
| Orange | `#d9a05b` |
| Yellow | `#d6c088` |
| Green (sage) | `#a3b584` |
| Blue | `#8faac4` |
| Purple | `#b89bc4` |
| Cyan/teal | `#8fbcb0` |

## Typography

- **Headings:** serif. Bundle **Newsreader** (SIL OFL 1.1), latin subset, embedded as
  base64 `woff2` directly in `theme.css` via `@font-face`. Weights: 400, 600, and 400
  italic. Fallback stack: `'Newsreader', Georgia, 'Times New Roman', serif`. Apply to
  `--h1`..`--h6` (`--font-text` stays sans). H1 gets a subtle clay underline rule
  (bottom border in clay at low opacity).
- **Body / UI:** Obsidian's default sans (Inter) via `--font-text-theme` left unset so
  the user's interface font / default applies; matches the Styrene-like Cowork body.
- **Code:** default monospace (`--font-monospace-theme` unset), warm-tuned token colors.

Bundling rationale: embedding the heading font guarantees identical rendering on any
machine without a network fetch. Latin-subset + 3 faces keeps the base64 payload to a
reasonable size for a single-file theme.

## Scope of styling

**Chrome:** title bar, ribbon, status bar, left/right sidebars, tab headers (clay active
indicator), scrollbars (warm, low-contrast).

**Editor (reading + live preview):** serif headings, bold/italic, internal & external
links and tags (clay; tags rendered as soft pills), blockquotes, **callouts** (tinted
background + left rule keyed to callout type using the semantic palette), fenced code
blocks + inline code, tables (subtle row striping, warm header), task checkboxes (clay
when checked), lists, horizontal rules, footnotes, embeds.

**UI components:** buttons (default + CTA in clay), text inputs, toggles, dropdowns,
modals, settings panes, command palette, search + search results, file explorer
(active/hover states), graph view node/line colors, text selection (clay-tinted),
tooltips.

## Deliverables (mirrors Pastel Frost)

```
obsidian-book-cloth/
  manifest.json      # name "Book Cloth", version 0.1.0, author SurprisedDuck
  theme.css          # single file: CSS variables + @font-face (inlined fonts) + rules
  README.md          # install + screenshot
  preview.html       # standalone visual preview (no Obsidian needed)
  sample/            # a sample note exercising every styled element
  docs/              # this design doc + plan
  LICENSE            # MIT
  .gitignore
```

Git: `git init`, commit under the SurprisedDuck identity (auto via `~/.gitconfig`
`includeIf` once the remote/path resolves to a `github.com/SurprisedDuck` repo). Commits
omit the Co-Authored-By trailer; credit the model in the body (e.g. "Supported by
Claude Opus 4.8").

## Theme.css structure

1. Header comment (name, author, license, Newsreader OFL attribution).
2. `@font-face` blocks (Newsreader, base64 woff2).
3. `:root` — Book Cloth palette custom properties (`--bc-*`).
4. `body.theme-dark` (and `.theme-light` aliased to the same dark values so the theme
   reads correctly even if Obsidian is in light mode) — map `--bc-*` onto Obsidian's
   official variables (`--background-primary`, `--text-normal`, `--interactive-accent`,
   `--h1`…, link/tag/code/table variables, etc.).
5. Targeted rule overrides for elements Obsidian doesn't fully drive through variables
   (callout tints, tag pills, H1 underline, scrollbars, graph view).

Prefer Obsidian's official CSS variables over hard selectors wherever possible; reserve
raw selectors for the handful of elements that need them.

## Testing / verification

- `preview.html` renders all styled elements; open in a browser to eyeball the look.
- Install into a real Obsidian vault (`.obsidian/themes/Book Cloth/`) and confirm in
  reading view, live preview, and the major UI surfaces (settings, command palette,
  graph). This is the acceptance check.

## Risks / open considerations

- Base64 font payload size — mitigate with latin subset and 3 faces only.
- `theme-light` aliasing: deliberately make light mode render the dark palette (theme is
  dark-only) rather than leaving an unstyled light mode.
```
