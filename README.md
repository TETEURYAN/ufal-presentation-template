<div align="center">
<h1>UFAL Presentation Template</h1>
</div>

<div align="center" style="max-width: 850px; margin: 0 auto;">
<table>
  <tr>
    <td valign="center" style="padding-right: 20px; width: 200px;">
      <img src="https://user-images.githubusercontent.com/91018438/204195385-acc6fcd4-05a7-4f25-87d1-cb7d5cc5c852.png" alt="animated" width="230" />
    </td>
    <td valign="center" style="padding-left: 20px;">
      <p>A reusable Beamer/LaTeX template for UFAL presentations (thesis
defenses, seminars, talks), built on the <code>college-beamer</code> theme
with UFAL branding (logo + Maceió street-map cover).</p>
    </td>
  </tr>
</table>
</div>

Extracted from the slides used in a Computer Science undergraduate thesis
defense at UFAL — see
[ufal-presentation-tesis](https://github.com/TETEURYAN/ufal-presentation-tesis)
for a filled-in example.

## How to use

1. Clone or use this repo as a GitHub template (`Use this template` button).
2. Open `pre.tex` and replace every `[PLACEHOLDER LIKE THIS]` with your own
   content — title, author, email, date, and the bullet points in each frame.
   Add or remove `\begin{frame}...\end{frame}` blocks as needed; the section
   structure (Introduction, Related Work, Methodology, Results, Concluding
   Remarks) is a starting point, not a requirement.
3. Replace `referencias.bib`'s example entry with your own references and
   update the `\cite{...}` keys in `pre.tex` to match.
4. Compile (see below) and check the result before presenting.

## File structure

```
pre.tex                # main presentation file (edit here)
referencias.bib         # your references (biblatex + biber)
collegebeamer.sty       # Beamer theme (local copy of college-beamer)
src/SZU/
├── color-logo.png      # UFAL logo on solid blue (shown on white slides)
├── trans-logo.png      # UFAL logo, white-on-transparent (colored slides)
└── background.png      # cover background: Maceió street map
gallery/
└── maceio-background.png   # source image for src/SZU/background.png
.latexmkrc
.gitignore
```

**Naming quirk inherited from upstream:** the UFAL branding lives under the
`src/SZU/` folder because the theme calls `\usepackage[szu,en]{collegeBeamer}`
— `szu` is just the institution slot that was repurposed to hold the UFAL
assets (color, logo, background). It is **not** Shenzhen University. If you
want a properly named `ufal` option instead, add a new
`\DeclareOption{ufal}{...}` block in `collegebeamer.sty` mirroring `szu`'s,
move the three PNGs to `src/ufal/`, and update the `\usepackage[...]` line
in `pre.tex`.

## How to compile

Requires `xelatex` + `biber` (for the `biblatex` bibliography) + `latexmk`.

```bash
latexmk pre.tex
```

Clean build from scratch:

```bash
latexmk -C
latexmk pre.tex
```

The engine is pinned to XeLaTeX by `.latexmkrc` (`$pdf_mode = 5;`) and by the
`% !TeX program = xelatex` magic comment on the first line of `pre.tex`. If
that first line is ever edited by hand, the magic comment must stay a real
comment (`% !TeX program = xelatex`, with the `%`) — without the `%` it is
invalid LaTeX in the preamble and breaks every compile with `! LaTeX Error:
Missing \begin{document}`.

### Why XeLaTeX

`pre.tex` uses `fontspec` (for the monospace font used in the author's email
on the cover), which requires XeLaTeX or LuaLaTeX — it will not compile with
plain `pdflatex`.

### Font fallback

The cover uses "Cascadia Code" for the author's email line, falling back to
"DejaVu Sans Mono" via `\IfFontExistsTF{Cascadia Code}{...}{...}` on any
system where neither is installed — the build never breaks over a missing
font.

### Dependencies

Besides the default Beamer/TikZ/biblatex stack, this theme needs a few
less-common packages: `extsizes` (for the `9pt` document-class option),
`mfnfss` (for `oldgerm.sty`), `animate`, `media9` (for `pdfbase.sty`),
`ocgx2` (for `ocgbase.sty`), and `zref`. On a full TeX Live install these are
usually already present; on a minimal install (e.g. TinyTeX) install them
with `tlmgr install extsizes mfnfss animate media9 ocgx2 zref`.

## Logo, cover background, and color (`src/SZU/`)

| File               | What it is                                                            |
|---------------------|------------------------------------------------------------------------|
| `color-logo.png`     | UFAL logo mark on a solid blue square (badge, shown top-left on white slides) |
| `trans-logo.png`     | Same mark, white-on-transparent (shown top-left on colored slides — section dividers, references, Q&A) |
| `background.png`     | Cover background: white, with the Maceió street map (from `gallery/maceio-background.png`) positioned on the right |

The theme's main color (`\maincolorRGB`, currently `55, 50, 204`, a
blue/indigo) is set inside `\DeclareOption{szu}{...}` in `collegebeamer.sty`
and matches the solid background used in `color-logo.png`.

**Two real gotchas if you regenerate any of these PNGs:**

1. They must be **8-bit**, not 16-bit RGBA — a 16-bit transparent PNG makes
   `xelatex`/`pgf`/`xdvipdfmx` render the logo corrupted (only a fragment of
   the artwork shows up).
2. Solid-color PNGs made with `convert -size WxH xc:'#RRGGBB'` often carry an
   embedded `cHRM` (chromaticity) chunk, which shifts blue backgrounds toward
   green in some PDF viewers. Fix: pass `-strip` on the final `convert` step
   to drop color metadata.

Export with `-depth 8 -strip` if you regenerate these assets.

The cover (`\maketitle`) uses the theme's default title-page template
(unmodified), white background. One thing to know: the template always
appends `\\` right after `\insertsubtitle`; if `\subtitle{}` is left
undefined (empty), that trailing `\\` has nothing to end and LaTeX throws
`! LaTeX Error: There's no line here to end.` on `\maketitle`. `pre.tex`
works around this with `\subtitle{\,}` (an invisible thin space) instead of
leaving `\subtitle` unset — the same fix applies if `\author{...}` or
`\date{...}` ever end with a stray trailing `\\` right before the closing
brace.

## Language

The theme ships `en` and `zh` (Chinese, needs `xeCJK` + XeLaTeX) language
options — no native `pt` option. `pre.tex` uses
`\usepackage[szu,en]{collegeBeamer}`; all theme strings (Table of Contents,
References, etc.) use the theme's built-in English defaults. Only `\qaStr`
(the closing Q&A line) is overridden locally in `pre.tex` via
`\renewcommand`, without touching `collegebeamer.sty`.

## Font size

`pre.tex` uses `\documentclass[9pt]{beamer}` (Beamer's default is 11pt) to
fit more content per slide — adjust if your content is lighter.

## Credits

Based on the [college-beamer](https://github.com/liu-qilong/college-beamer)
template by Qi-long Liu (Kirov), licensed under
[Creative Commons CC BY 4.0](https://creativecommons.org/licenses/by/4.0/legalcode).
`collegebeamer.sty` here is a locally adapted copy; the `szu` institution
slot's images were replaced with UFAL branding (see above). The other
institution options the original theme ships (`polyu`, `swu`, `hit`, `saes`,
`red`) and their images were removed from this template to keep it focused
on the UFAL variant — see the upstream repo if you need one of those.
