# Book Cloth Theme Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build "Book Cloth", a dark-only Obsidian theme styled after Claude Cowork — warm charcoal paper, clay/coral accent, bundled serif (Newsreader) headings.

**Architecture:** A single `theme.css` that (1) declares a `--bc-*` palette in `:root`, (2) sets accent + decoration tokens on `body`, (3) maps `--bc-*` onto Obsidian's official CSS variables under `.theme-dark` (with `.theme-light` aliased to the same values so the theme reads correctly in either app mode), then (4) a small tail of raw-selector overrides for things Obsidian doesn't fully drive through variables (serif headings, H1 rule, tag pills, callouts, scrollbars). Heading font is bundled as base64 `woff2` via `@font-face` so it renders identically everywhere.

**Tech Stack:** Plain CSS. Newsreader font (SIL OFL 1.1) fetched from Google Fonts, latin subset, base64-inlined. No build step.

**Reference:** `../../../obsidian-theme/theme.css` (the sibling Pastel Frost/Dusk theme) is the canonical pattern for which Obsidian variables to set and how — follow its structure.

---

## File Structure

```
obsidian-book-cloth/
  manifest.json      # theme metadata (name, version, author)
  theme.css          # the whole theme: @font-face + :root + body + .theme-dark + tail rules
  README.md          # what it is, install steps, screenshot
  preview.html       # standalone browser showcase (mirrors the palette/type)
  sample/Book Cloth showcase.md   # a note exercising every styled element
  LICENSE            # MIT
  .gitignore
  docs/superpowers/  # spec + this plan (already present)
```

`theme.css` is intentionally one file — that is the Obsidian theme convention and matches the sibling repo.

---

### Task 1: Scaffold repo metadata

**Files:**
- Create: `manifest.json`
- Create: `LICENSE`
- Create: `.gitignore`
- Create: `README.md` (skeleton; filled in Task 6)

- [ ] **Step 1: Write `manifest.json`**

```json
{
   "name": "Book Cloth",
   "version": "0.1.0",
   "minAppVersion": "0.16.0",
   "author": "SurprisedDuck",
   "authorUrl": "https://github.com/SurprisedDuck"
}
```

- [ ] **Step 2: Write `LICENSE`** (MIT, 2026 SurprisedDuck)

```
MIT License

Copyright (c) 2026 SurprisedDuck

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

- [ ] **Step 3: Write `.gitignore`**

```
.DS_Store
.superpowers/
node_modules/
*.log
```

- [ ] **Step 4: Write `README.md` skeleton** (placeholder body, completed in Task 6)

```markdown
# Book Cloth

A dark-only Obsidian theme styled after Claude Cowork.
```

- [ ] **Step 5: Verify `manifest.json` is valid JSON**

Run: `python3 -c "import json; json.load(open('manifest.json')); print('manifest OK')"`
Expected: `manifest OK`

- [ ] **Step 6: Commit**

```bash
git add manifest.json LICENSE .gitignore README.md
git commit -m "Scaffold Book Cloth theme repo metadata

Supported by Claude Opus 4.8"
```

---

### Task 2: Palette + Obsidian variable mapping

This is the core of `theme.css`: the `--bc-*` palette, accent/decoration tokens, and the
mapping onto Obsidian's official variables. The serif `@font-face` and tail overrides come
in later tasks; this task creates `theme.css` with everything below.

**Files:**
- Create: `theme.css`

- [ ] **Step 1: Write `theme.css`** with exactly this content

```css
/*
 * Book Cloth — a dark-only Obsidian theme.
 * A warm, paper-like take on the Claude Cowork look:
 * toasted-charcoal surfaces, a clay/coral accent, serif headings.
 *
 * (c) 2026 SurprisedDuck. MIT.
 * Heading font: Newsreader by Production Type, SIL OFL 1.1.
 *   https://fonts.google.com/specimen/Newsreader
 *
 * NOTE: @font-face blocks (bundled Newsreader woff2) are prepended in Task 3.
 */

:root
{
    /* Warm-neutral surfaces (dark only) — darkest → lightest */
    --bc-backdrop:   #1a1a18; /* window backdrop / titlebar */
    --bc-sidebar:    #1f1e1d;
    --bc-elev:       #2c2a27; /* secondary-alt surface / cards */
    --bc-editor:     #262624; /* main editing surface */
    --bc-surface:    #33312e;
    --bc-surface-hi: #3d3b38;
    --bc-row-odd:    #222220; /* table row striping */
    --bc-row-even:   #272623;

    /* Clay / book-cloth accent family */
    --bc-clay:     #cc785c; /* accent — Anthropic "book cloth" */
    --bc-coral:    #d97757; /* links */
    --bc-coral-hi: #e08a6b; /* link / accent hover */
    --bc-clay-rgb: 204,120,92;

    /* Warm off-white ink */
    --bc-text:     #d6d2c6;
    --bc-emphasis: #f5f3ec;
    --bc-muted:    #8f8a80;
    --bc-faint:    #6b675e;

    /* Warm aurora — semantic / syntax */
    --bc-red:    #e0837a;
    --bc-orange: #d9a05b;
    --bc-yellow: #d6c088;
    --bc-green:  #a3b584;
    --bc-blue:   #8faac4;
    --bc-purple: #b89bc4;
    --bc-cyan:   #8fbcb0;

    /* Bundled serif stack for headings (face defined in Task 3) */
    --bc-serif: 'Newsreader', Georgia, 'Times New Roman', serif;
}

body
{
    /* Accent (HSL of #cc785c) — drives --interactive-accent, toggles, handles */
    --accent-h: 15;
    --accent-s: 52%;
    --accent-l: 58%;

    /* Link / tag decorations */
    --link-decoration:                none;
    --link-decoration-hover:          underline;
    --link-external-decoration:       underline dotted;
    --link-external-decoration-hover: underline dotted;
    --tag-decoration:                 none;
    --tag-decoration-hover:           none;
    --tag-padding-x:                  .65em;
    --tag-padding-y:                  .25em;

    /* Weights */
    --bold-weight:     650;
    --tab-font-weight: 600;

    /* Radius scale — soft, paper-card feel */
    --radius-s:        6px;
    --radius-m:        9px;
    --radius-l:        14px;
    --tab-radius:      var(--radius-m);
    --modal-radius:    var(--radius-l);
    --checkbox-radius: 5px;

    /* Headings — weight & rhythm (serif looks best a touch lighter) */
    --h1-weight:       600;
    --h2-weight:       600;
    --h3-weight:       600;
    --h4-weight:       600;
    --h5-weight:       600;
    --h6-weight:       600;
    --heading-spacing: 2.2em;
}

/* Dark-only theme: alias light-mode to the same values so it never renders
   as an unstyled light theme if Obsidian is set to light. */
.theme-dark,
.theme-light
{
    /* Backgrounds */
    --background-primary:               var(--bc-editor);
    --background-primary-alt:           var(--bc-editor);
    --background-secondary:             var(--bc-sidebar);
    --background-secondary-alt:         var(--bc-elev);
    --background-modifier-border:       var(--bc-surface);
    --background-modifier-border-hover: var(--bc-surface-hi);
    --background-modifier-hover:        rgba(var(--bc-clay-rgb), .08);

    /* Text */
    --text-normal:    var(--bc-text);
    --text-muted:     var(--bc-muted);
    --text-faint:     var(--bc-faint);
    --text-on-accent: var(--bc-emphasis);

    /* Headings — clean ink gradient (serif applied in Task 3) */
    --h1-color:           var(--bc-emphasis);
    --h2-color:           var(--bc-emphasis);
    --h3-color:           var(--bc-text);
    --h4-color:           var(--bc-text);
    --h5-color:           var(--bc-muted);
    --h6-color:           var(--bc-muted);
    --inline-title-color: var(--bc-emphasis);

    /* Links — clay/coral */
    --text-accent:               var(--bc-coral);
    --text-accent-hover:         var(--bc-coral-hi);
    --link-color:                var(--bc-coral);
    --link-color-hover:          var(--bc-coral-hi);
    --link-external-color:       var(--bc-coral);
    --link-external-color-hover: var(--bc-coral-hi);
    --link-unresolved-color:     var(--bc-muted);

    /* Emphasis */
    --bold-color:   var(--bc-emphasis);
    --italic-color: inherit;

    /* Code */
    --code-normal:     var(--bc-orange);
    --code-background: var(--bc-sidebar);

    /* Highlight (==mark==) */
    --text-highlight-bg: rgba(var(--bc-clay-rgb), .30);
    --text-highlight-fg: var(--bc-emphasis);

    /* Tags */
    --tag-color:            var(--bc-clay);
    --tag-background:        var(--bc-surface);
    --tag-background-hover:  var(--bc-surface-hi);

    /* Checkboxes */
    --checkbox-color:              var(--bc-clay);
    --checkbox-color-hover:        var(--bc-coral-hi);
    --checkbox-border-color:       var(--bc-clay);
    --checkbox-border-color-hover: var(--bc-coral-hi);
    --checklist-done-color:        var(--bc-muted);

    /* Selection & active line */
    --cursor-line-background: rgba(var(--bc-clay-rgb), .07);
    --text-selection:         rgba(var(--bc-clay-rgb), .26);
    --flashing-background:    rgba(var(--bc-clay-rgb), .25);

    /* Semantic colours (callouts, syntax, graph) — warm aurora */
    --color-red:    var(--bc-red);
    --color-orange: var(--bc-orange);
    --color-yellow: var(--bc-yellow);
    --color-green:  var(--bc-green);
    --color-cyan:   var(--bc-cyan);
    --color-blue:   var(--bc-blue);
    --color-purple: var(--bc-purple);
    --color-red-rgb:    224,131,122;
    --color-orange-rgb: 217,160,91;
    --color-yellow-rgb: 214,192,136;
    --color-green-rgb:  163,181,132;
    --color-cyan-rgb:   143,188,176;
    --color-blue-rgb:   143,170,196;
    --color-purple-rgb: 184,155,196;

    /* Accent as RGB (callout-default tint) */
    --color-accent-rgb: var(--bc-clay-rgb);

    /* Tables */
    --table-header-background:       var(--bc-sidebar);
    --table-header-background-hover: var(--bc-surface);
    --table-row-even-background:     var(--bc-row-even);
    --table-row-odd-background:      var(--bc-row-odd);
    --table-row-background-hover:    var(--bc-surface);

    /* Nav / icons / window chrome */
    --nav-item-color-hover:        var(--bc-emphasis);
    --nav-item-color-active:       var(--bc-emphasis);
    --nav-item-background-active:  rgba(var(--bc-clay-rgb), .14);
    --icon-color-hover:            var(--bc-clay);
    --icon-color-focused:          var(--bc-coral-hi);
    --titlebar-text-color-focused: var(--bc-emphasis);
    --tab-text-color-focused-active:         var(--bc-emphasis);
    --tab-text-color-focused-active-current: var(--bc-emphasis);

    /* Graph view */
    --graph-line:            var(--bc-surface);
    --graph-node:            var(--bc-text);
    --graph-node-tag:        var(--bc-clay);
    --graph-node-attachment: var(--bc-green);

    /* Lists & quotes */
    --list-marker-color:       var(--bc-clay);
    --blockquote-border-color: var(--bc-clay);
    --blockquote-color:        var(--bc-muted);

    /* Scrollbars */
    --scrollbar-thumb-bg:        var(--bc-surface);
    --scrollbar-active-thumb-bg: var(--bc-surface-hi);
    --scrollbar-bg:              transparent;

    /* Code syntax highlighting — warm aurora */
    --code-comment:     var(--bc-faint);
    --code-keyword:     var(--bc-clay);
    --code-operator:    var(--bc-text);
    --code-property:    var(--bc-blue);
    --code-punctuation: var(--bc-muted);
    --code-function:    var(--bc-orange);
    --code-tag:         var(--bc-red);
    --code-string:      var(--bc-green);
    --code-value:       var(--bc-purple);
    --code-important:   var(--bc-yellow);
}
```

- [ ] **Step 2: Verify CSS braces balance**

Run: `python3 -c "s=open('theme.css').read(); print('braces', s.count('{'), s.count('}'), 'OK' if s.count('{')==s.count('}') else 'MISMATCH')"`
Expected: equal counts and `OK`.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git commit -m "Add Book Cloth palette and Obsidian variable mapping

Supported by Claude Opus 4.8"
```

---

### Task 3: Bundle Newsreader + serif heading rules

Fetch the latin-subset Newsreader `woff2` files, base64-inline them as `@font-face` at the
**top** of `theme.css`, then add the rules that put the serif on headings and the inline
title and give H1 a clay underline.

**Files:**
- Modify: `theme.css` (prepend `@font-face`, append heading rules)
- Temp: `/tmp/bc-fonts/` (scratch; not committed)

- [ ] **Step 1: Fetch the three latin woff2 faces and base64-encode them**

Google Fonts serves a different subset per `User-Agent`; a modern UA yields `woff2`. The
latin subset is the block following the `/* latin */` comment. Run:

```bash
mkdir -p /tmp/bc-fonts && cd /tmp/bc-fonts
UA='Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120 Safari/537.36'
curl -s -A "$UA" 'https://fonts.googleapis.com/css2?family=Newsreader:ital,wght@0,400;0,600;1,400&display=swap' -o newsreader.css
# Extract the woff2 URL from each /* latin */ block (one per face, in declared order:
#   400 normal, 600 normal, 400 italic).
python3 - <<'PY'
import re
css = open('newsreader.css').read()
blocks = css.split('/* latin */')[1:]   # text after each 'latin' marker
urls = []
for b in blocks:
    m = re.search(r"url\((https://[^)]+\.woff2)\)", b)
    if m: urls.append(m.group(1))
# Keep the first 3 latin faces (400, 600, italic-400), in order.
for i,u in enumerate(urls[:3]):
    print(i, u)
open('urls.txt','w').write('\n'.join(urls[:3]))
PY
i=0; for u in $(cat urls.txt); do curl -s "$u" -o face_$i.woff2; i=$((i+1)); done
ls -la face_*.woff2
for f in face_0 face_1 face_2; do base64 < $f.woff2 | tr -d '\n' > $f.b64; wc -c $f.b64; done
```

Expected: three `face_N.woff2` files (each roughly 20–60 KB) and three `face_N.b64` files.
If `urls.txt` has fewer than 3 lines, the API layout changed — open `newsreader.css` and
copy the `latin` `woff2` URLs manually in the order 400, 600, italic-400.

- [ ] **Step 2: Prepend `@font-face` blocks to `theme.css`**

Insert these three blocks immediately after the top header comment (before `:root`),
pasting each base64 string from `face_0.b64` (400), `face_1.b64` (600), `face_2.b64`
(italic 400) into the matching `url(...)`:

```css
@font-face {
    font-family: 'Newsreader';
    font-style: normal;
    font-weight: 400;
    font-display: swap;
    src: url(data:font/woff2;base64,PASTE_face_0_b64) format('woff2');
}
@font-face {
    font-family: 'Newsreader';
    font-style: normal;
    font-weight: 600;
    font-display: swap;
    src: url(data:font/woff2;base64,PASTE_face_1_b64) format('woff2');
}
@font-face {
    font-family: 'Newsreader';
    font-style: italic;
    font-weight: 400;
    font-display: swap;
    src: url(data:font/woff2;base64,PASTE_face_2_b64) format('woff2');
}
```

Practical insertion: write the three blocks to `/tmp/bc-fonts/faces.css` (using the `.b64`
contents), then assemble: `cat header.part faces.css body.part > theme.css`, OR edit in
place so the blocks land between the closing `*/` of the header and `:root {`.

- [ ] **Step 3: Append serif heading rules to the end of `theme.css`**

```css
/* ── Serif headings (the Cowork signature) ───────────────────────── */
/* Reading view + live preview headings, the inline title, and the
   outline/heading panes all get the bundled serif. Body stays sans. */
.theme-dark .markdown-rendered :is(h1, h2, h3, h4, h5, h6),
.theme-dark .markdown-rendered .heading,
.theme-dark .inline-title,
.theme-dark .HyperMD-header,
.theme-dark .cm-s-obsidian .cm-header,
.theme-dark .view-header-title {
    font-family: var(--bc-serif);
    letter-spacing: -0.005em;
}

/* H1 gets a soft clay underline rule. */
.theme-dark .markdown-rendered h1 {
    border-bottom: 1px solid rgba(var(--bc-clay-rgb), .35);
    padding-bottom: .18em;
}
```

- [ ] **Step 4: Verify the font data URIs are present and braces still balance**

Run:
```bash
grep -c 'data:font/woff2;base64,' theme.css   # expect 3
python3 -c "s=open('theme.css').read(); print('braces OK' if s.count('{')==s.count('}') else 'MISMATCH')"
```
Expected: `3` and `braces OK`.

- [ ] **Step 5: Commit**

```bash
git add theme.css
git commit -m "Bundle Newsreader serif and apply to headings

Supported by Claude Opus 4.8"
```

---

### Task 4: Tail overrides — callouts, pills, cards, scrollbars

Elements Obsidian doesn't fully drive through variables. Append to the end of `theme.css`.

**Files:**
- Modify: `theme.css` (append)

- [ ] **Step 1: Append the override rules**

```css
/* ── Rounded floating panes (paper cards on a backdrop) ──────────── */
.theme-dark .workspace-split.mod-root { background: var(--bc-backdrop); }
.theme-dark .workspace-tabs .workspace-leaf { margin: 3px; overflow: hidden; }
.theme-dark .workspace-tabs .workspace-leaf,
.theme-dark .workspace-tab-container { border-radius: var(--radius-l); }

/* ── Tags as soft clay pills ─────────────────────────────────────── */
.theme-dark .markdown-rendered a.tag,
.theme-dark .cm-s-obsidian .cm-hashtag {
    border-radius: 999px;
    font-size: .82em;
    font-weight: 550;
}

/* ── Keep ==highlight== foreground readable in both modes ────────── */
.theme-dark .markdown-rendered mark,
.theme-dark .cm-highlight,
.theme-dark span.cm-highlight { color: var(--text-highlight-fg); }

/* ── Rounded images & embeds ─────────────────────────────────────── */
.theme-dark .markdown-rendered img,
.theme-dark .markdown-rendered .internal-embed,
.theme-dark .cm-s-obsidian .image-embed img { border-radius: var(--radius-m); }

/* ── Reading-view code blocks: subtle warm border ────────────────── */
.theme-dark .markdown-rendered pre {
    border: 1px solid var(--bc-surface);
    border-radius: var(--radius-m);
}

/* ── Callouts: warm card with tinted title row ───────────────────── */
.theme-dark .callout {
    border-radius: var(--radius-m);
    border: 1px solid rgba(var(--callout-color), .25);
    background: rgba(var(--callout-color), .06);
    mix-blend-mode: normal;
}
.theme-dark .callout-title { padding: .55em .75em; }

/* ── Inputs & buttons: warm surfaces, clay CTA ───────────────────── */
.theme-dark button.mod-cta,
.theme-dark .mod-cta {
    background: var(--bc-clay);
    color: var(--bc-emphasis);
}
.theme-dark button.mod-cta:hover,
.theme-dark .mod-cta:hover { background: var(--bc-coral-hi); }

/* ── Active tab: clay underline accent ───────────────────────────── */
.theme-dark .workspace-tab-header.is-active.mod-active::after {
    content: "";
    position: absolute;
    left: 8px; right: 8px; bottom: 0;
    height: 2px;
    border-radius: 2px;
    background: var(--bc-clay);
}
```

- [ ] **Step 2: Verify braces still balance**

Run: `python3 -c "s=open('theme.css').read(); print('braces OK' if s.count('{')==s.count('}') else 'MISMATCH')"`
Expected: `braces OK`.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git commit -m "Add callout, tag-pill, card and CTA overrides

Supported by Claude Opus 4.8"
```

---

### Task 5: Standalone preview + sample note

A browser preview that mirrors the palette and type (no Obsidian needed) plus a sample
vault note that exercises every styled element for screenshots / in-app verification.

**Files:**
- Create: `preview.html`
- Create: `sample/Book Cloth showcase.md`

- [ ] **Step 1: Write `preview.html`**

A self-contained HTML page that re-declares the `--bc-*` palette inline and renders a
representative note (titlebar, sidebar, serif H1 with clay rule, body text, clay link, a
tag pill, a tinted callout, inline + block code, a 2-row striped table, a checkbox list).
Use `@font-face` pointing at the Google Fonts woff2 URL (network, fine for a preview) OR
the `--bc-serif` fallback stack. Structure mirrors `../obsidian-theme/preview.html`.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Book Cloth — preview</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Newsreader:ital,wght@0,400;0,600;1,400&display=swap');
  :root{
    --bc-backdrop:#1a1a18;--bc-sidebar:#1f1e1d;--bc-elev:#2c2a27;--bc-editor:#262624;
    --bc-surface:#33312e;--bc-surface-hi:#3d3b38;--bc-row-odd:#222220;--bc-row-even:#272623;
    --bc-clay:#cc785c;--bc-coral:#d97757;--bc-coral-hi:#e08a6b;--bc-clay-rgb:204,120,92;
    --bc-text:#d6d2c6;--bc-emphasis:#f5f3ec;--bc-muted:#8f8a80;--bc-faint:#6b675e;
    --bc-green:#a3b584;--bc-serif:'Newsreader',Georgia,serif;
    --sans:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Inter,sans-serif;
  }
  *{box-sizing:border-box}
  body{margin:0;background:var(--bc-backdrop);color:var(--bc-text);font-family:var(--sans);
       font-size:15px;line-height:1.65;display:flex;min-height:100vh}
  .sidebar{width:230px;background:var(--bc-sidebar);padding:16px 12px;color:var(--bc-muted);font-size:13px}
  .sidebar .vault{color:var(--bc-emphasis);font-weight:600;margin-bottom:12px}
  .sidebar .item{padding:5px 10px;border-radius:8px}
  .sidebar .item.active{background:rgba(var(--bc-clay-rgb),.14);color:var(--bc-emphasis)}
  .editor{flex:1;background:var(--bc-editor);padding:40px 56px;max-width:760px}
  h1{font-family:var(--bc-serif);color:var(--bc-emphasis);font-weight:600;font-size:2.1em;
     margin:0 0 .1em;border-bottom:1px solid rgba(var(--bc-clay-rgb),.35);padding-bottom:.18em}
  h2{font-family:var(--bc-serif);color:var(--bc-emphasis);font-weight:600;margin-top:1.6em}
  .meta{color:var(--bc-muted);font-size:.85em;margin-bottom:1.4em}
  a{color:var(--bc-coral);text-decoration:none}
  a:hover{color:var(--bc-coral-hi);text-decoration:underline}
  .tag{display:inline-block;background:var(--bc-surface);color:var(--bc-clay);
       padding:.25em .65em;border-radius:999px;font-size:.82em;font-weight:550}
  .callout{border-radius:9px;border:1px solid rgba(var(--bc-clay-rgb),.25);
           background:rgba(var(--bc-clay-rgb),.06);padding:.2em 1em;margin:1.2em 0}
  .callout .t{color:var(--bc-clay);font-weight:600}
  code{background:var(--bc-sidebar);padding:.12em .4em;border-radius:5px;
       color:#d9a05b;font-family:ui-monospace,Menlo,monospace;font-size:.88em}
  pre{background:var(--bc-sidebar);border:1px solid var(--bc-surface);border-radius:9px;
      padding:14px 16px;overflow:auto}
  pre code{background:none;padding:0;color:var(--bc-text)}
  table{border-collapse:collapse;width:100%;margin:1.2em 0;font-size:.92em}
  th{background:var(--bc-sidebar);text-align:left;padding:8px 12px;color:var(--bc-emphasis)}
  td{padding:8px 12px}
  tr:nth-child(odd) td{background:var(--bc-row-odd)}
  tr:nth-child(even) td{background:var(--bc-row-even)}
  ul.tasks{list-style:none;padding-left:0}
  ul.tasks li{margin:.3em 0}
  .box{display:inline-block;width:15px;height:15px;border:1.5px solid var(--bc-clay);
       border-radius:5px;vertical-align:-2px;margin-right:8px}
  .box.done{background:var(--bc-clay)}
  blockquote{border-left:3px solid var(--bc-clay);margin:1.2em 0;padding:.2em 1em;color:var(--bc-muted)}
</style>
</head>
<body>
  <div class="sidebar">
    <div class="vault">📚 Vault</div>
    <div class="item active">📄 Book Cloth showcase</div>
    <div class="item">📄 Meeting notes</div>
    <div class="item">📄 Roadmap</div>
  </div>
  <div class="editor">
    <h1>Book Cloth</h1>
    <div class="meta">2026-06-22 · <span class="tag">#theme</span></div>
    <p>A warm, paper-like workspace for Obsidian, styled after Claude Cowork. Headings
       are set in <a href="#">Newsreader</a>, links wear a clay-coral hue, and the page
       sits on toasted-charcoal paper.</p>
    <div class="callout"><p><span class="t">💡 Note</span></p><p>Callouts are gentle clay-tinted cards.</p></div>
    <h2>Details</h2>
    <p>Inline <code>code</code> uses a warm surface. Block code:</p>
    <pre><code>def greet(name):
    return f"Hello, {name}"</code></pre>
    <blockquote>Paper should feel calm, not loud.</blockquote>
    <table>
      <tr><th>Element</th><th>Color</th></tr>
      <tr><td>Accent</td><td>Clay #CC785C</td></tr>
      <tr><td>Ink</td><td>Warm off-white</td></tr>
    </table>
    <ul class="tasks">
      <li><span class="box done"></span>Pick the palette</li>
      <li><span class="box"></span>Ship the theme</li>
    </ul>
  </div>
</body>
</html>
```

- [ ] **Step 2: Write `sample/Book Cloth showcase.md`**

A markdown note using every styled construct, for in-Obsidian verification:

```markdown
# Book Cloth showcase

2026-06-22 · #theme #demo

A warm, paper-like workspace styled after Claude Cowork. This note exercises
**bold**, *italic*, ==highlight==, an [[internal link]], an [external link](https://claude.ai),
and `inline code`.

## Headings stay serif

### Third level
#### Fourth level

> A blockquote in warm muted ink, with a clay rule.

> [!note] Note callout
> Gentle clay-tinted card.

> [!warning] Warning callout
> Uses the warm aurora orange/red.

```python
def greet(name: str) -> str:
    return f"Hello, {name}"
```

| Element | Color       |
| ------- | ----------- |
| Accent  | Clay #CC785C |
| Ink     | Warm off-white |

- [x] Pick the palette
- [ ] Ship the theme

#tag-pill
```

- [ ] **Step 3: Verify `preview.html` renders correctly in a browser**

Open the file and confirm visually: warm charcoal background, serif H1 with a clay
underline, clay-coral link, rounded clay tag pill, tinted callout card, striped table,
clay checkbox. Use whichever is available:
- `open preview.html` (macOS) then screenshot, or
- serve + inspect: `python3 -m http.server 8765` in the repo, view `http://localhost:8765/preview.html`.

Expected: matches the dark mockups approved during brainstorming.

- [ ] **Step 4: Commit**

```bash
git add preview.html "sample/Book Cloth showcase.md"
git commit -m "Add standalone preview and sample showcase note

Supported by Claude Opus 4.8"
```

---

### Task 6: README, in-app acceptance, finalize

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Install into a real Obsidian vault and verify**

Locate a vault and copy the theme in:
```bash
# Find vaults (macOS):
cat "$HOME/Library/Application Support/obsidian/obsidian.json" 2>/dev/null
# For a chosen vault path $VAULT:
mkdir -p "$VAULT/.obsidian/themes/Book Cloth"
cp theme.css manifest.json "$VAULT/.obsidian/themes/Book Cloth/"
```
In Obsidian: Settings → Appearance → Themes → "Book Cloth". Open the sample showcase note
and confirm reading view, live preview, settings pane, and command palette all read warm
with serif headings. (If no vault is available, rely on `preview.html` as the acceptance
artifact and note this in the commit.)

- [ ] **Step 2: Capture a screenshot** of the sample note in Obsidian (or `preview.html`) and
save as `screenshot.png` in the repo root.

- [ ] **Step 3: Write the full `README.md`**

```markdown
# Book Cloth

A dark-only Obsidian theme styled after **Claude Cowork** — warm toasted-charcoal
paper, a clay/coral accent, warm off-white ink, and serif (Newsreader) headings.

![Book Cloth](screenshot.png)

## Install

**Manual:**
1. Create the folder `<your vault>/.obsidian/themes/Book Cloth/`.
2. Copy `manifest.json` and `theme.css` into it.
3. In Obsidian: **Settings → Appearance → Themes → Book Cloth**.

## Features

- Warm, paper-like dark surfaces (never blue-grey).
- Clay/coral accent (`#CC785C`) on links, tags, checkboxes, and the active tab.
- Serif headings via bundled Newsreader (no network fetch).
- Full coverage: editor, callouts, tables, code, plus app chrome and UI.

## Credits

- Heading font: [Newsreader](https://fonts.google.com/specimen/Newsreader) (SIL OFL 1.1).
- Theme © 2026 SurprisedDuck, MIT.
```

- [ ] **Step 4: Commit**

```bash
git add README.md screenshot.png
git commit -m "Add README and screenshot

Supported by Claude Opus 4.8"
```

- [ ] **Step 5 (optional): Push to GitHub** — only if the user asks. The repo remote is
already set to `https://github.com/SurprisedDuck/obsidian-book-cloth`; create it with
`gh repo create SurprisedDuck/obsidian-book-cloth --public --source=. --push`.

---

## Notes for the implementer

- **Verification is visual, not unit-tested.** CSS has no meaningful unit tests; the
  brace-balance checks catch syntax slips, and `preview.html` + the in-Obsidian install are
  the real acceptance checks.
- **Commit messages** end with `Supported by Claude Opus 4.8` and **no** `Co-Authored-By`
  trailer (user preference). The SurprisedDuck git identity applies automatically because
  the remote is under `github.com/SurprisedDuck`.
- **Don't push** unless the user explicitly asks.
```
