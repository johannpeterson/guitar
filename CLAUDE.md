# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

LaTeX sources for printable guitar reference sheets. There is no application code, test suite or linter. Each `.tex` file is a standalone document that builds its own PDF, with no shared preamble or includes. PDFs are tracked in git, so rebuild and commit the PDF along with its source.

The sheets come in two families:

- **`guitarchordschemes` diagrams:** `chord sheet.tex` (scales), `triads 3x8.tex`, and the `blank chords` and `chords double slack F` files, which print empty chord grids to fill in by hand.
- **Hand-drawn TikZ fretboards:** `fretboard.tex`, `phin.tex`, `phin_page.tex` and `Segovia.tex`. `phin.tex` draws a 3-string phin with only some frets. `phin.tex`, `phin_page.tex` and `Segovia.tex` hard-code the 12edo fret ratio, and only `fretboard.tex` handles other tunings.

The `.nb` files are Mathematica notebooks.

## Dropbox placeholders

This folder, including `.git/`, is in Dropbox, so files can become online-only placeholders: 0 bytes on disk, with a `com.dropbox.placeholder` xattr. Reading one returns nothing and does not download it. A 0-byte file here is not necessarily empty, so never overwrite, delete or commit one on that assumption, and don't report its contents as blank.

Check with `xattr <file>`. If a file you need is a placeholder, ask the user to make it available offline in Dropbox. If every git command fails with `not a git repository` even though `.git/` exists, its files are probably placeholders.

## Build

```sh
pdflatex -interaction=nonstopmode -file-line-error "chord sheet.tex"
```

- Quote filenames, since many contain spaces.
- Always pass `-interaction=nonstopmode`. Without it, a LaTeX error leaves `pdflatex` waiting for input at a prompt.
- The user compiles from TeXShop, which runs `pdflatex --file-line-error --synctex=1 --shell-escape <file>`.
- `chord sheet.tex` builds to 4 pages. Its roughly 17pt `Overfull \hbox` warnings, one per diagram, are expected.

## Scale diagrams (`chord sheet.tex`)

Each scale is a `\scales[...]` block from the `guitarchordschemes` package, wrapped in `\resizebox{\textwidth}{!}{...}` and separated by `\vspace{\baselineskip}`.

Notes in `finger` and `root` are written `fret/string:finger`:

- **String:** 1 is high E and 6 is low E.
- **Fret:** counted relative to the diagram's `position`, not the nut. In `position = II`, fret `1` is the 2nd fret, so `2/5` in C Major is C on the 5th string, 3rd fret. Fret `0` is an open string, as in `0/6`, which has no finger number.
- **Finger:** 1 to 4.
- **Roots:** root notes go in `root`, not `finger`.

`fret-number` sets how many frets the diagram shows, so it must be at least as large as the highest fret used.

## `fretboard.tex`

Draws a fretboard from scratch in TikZ rather than using `guitarchordschemes`, with the nut at x = 0. The picture is rotated -90 degrees, so x runs down the page and the nut is at the top. The drawing lives in the `\fretboard` macro, and the page places two of them side by side. Set `\numstrings`, `\edo` (equal divisions of the octave), `\numfrets`, `\tuning` (open notes, left to right) and `\boardtitle` (defaults to the `\edo` value) at the top of the file. To give the boards different tunings, redefine `\tuning` before each `\fretboard`.

`\notenames` is optional: it lists every scale note ascending, one entry per step of `\edo`, and the names are shown just above each fret. Each string's names start from its `\tuning` note, which must match a `\notenames` entry exactly, so `\tuning` and `\notenames` must be spelled the same way. The lookup helpers are written in expl3. Fret `n` sits at `\scalelength * (1 - 2^(-n / \edo))`, and `\scalelength` (cm) is computed so the board always fills `\boardheight`.

Only the playable part is drawn: strings stop `\stringrunout` past the last fret. They are left open-ended, and no bridge is drawn; the true bridge would be at x = `\scalelength`, off the drawing.
