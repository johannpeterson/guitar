# Guitar reference sheets

LaTeX sources for printable guitar reference sheets. Each PDF is tracked in git, so rebuild and commit it when you change its source.

## Requirements

You need `pdflatex` from a recent TeX distribution such as TeX Live. `fretboard.tex` uses `\fpeval`, which needs a LaTeX release from about 2022 or later. `chord sheet.tex` and the other chord sheets also need the `guitarchordschemes` package.

## Rendering

Run these commands from this folder. Each one needs only a single pass and writes the PDF here, along with `.aux` and `.log` files that git ignores.

Most PDFs have a matching `.tex` file. To build one, pass its name to `pdflatex`, quoting names that contain spaces:

```sh
pdflatex -interaction=nonstopmode -file-line-error "chord sheet.tex"
```

`-interaction=nonstopmode` stops a LaTeX error from leaving `pdflatex` waiting at a prompt, and `-file-line-error` shows the file and line of each error.

| PDF | Source | Pages | Contents |
| --- | --- | --- | --- |
| `blank chords 4x6.pdf` | `blank chords 4x6.tex` | 1 | Empty chord grids |
| `blank chords no loop.pdf` | `blank chords no loop.tex` | 1 | Empty chord grids |
| `blank chords slack no loop.pdf` | `blank chords slack no loop.tex` | 1 | Empty chord grids |
| `chords double slack F.pdf` | `chords double slack F.tex` | 1 | Empty chord grids |
| `chord sheet.pdf` | `chord sheet.tex` | 4 | Scale diagrams |
| `triads 3x8.pdf` | `triads 3x8.tex` | 1 | Triad diagrams |
| `Segovia.pdf` | `Segovia.tex` | 1 | Segovia scale fingerings |
| `phin.pdf` | `phin.tex` | 1 | 3-string phin fretboard |
| `phin_page.pdf` | `phin_page.tex` | 1 | Phin fretboard page |
| `fretboard.pdf` | `fretboard.tex` | 2 | 19 EDO and 12 EDO fretboards |

To rebuild all of these at once in `zsh` or `bash`:

```sh
for f in *.tex; do pdflatex -interaction=nonstopmode -file-line-error "$f"; done
```

This loop doesn't build the two single-layout fretboard PDFs below.

### Single-layout fretboards

`fretboard 12edo.pdf` and `fretboard 19edo.pdf` have no `.tex` file of their own. They come from `fretboard.tex`, using a settings file in `fretboards/`:

```sh
pdflatex -interaction=nonstopmode -file-line-error -jobname="fretboard 12edo" '\def\onlyfretboard{12edo}\input{fretboard}'
pdflatex -interaction=nonstopmode -file-line-error -jobname="fretboard 19edo" '\def\onlyfretboard{19edo}\input{fretboard}'
```

`\onlyfretboard` names the settings file to load, and `-jobname` names the output PDF. The files in `fretboards/` don't build on their own.

### `scales.pdf`

`scales.pdf` isn't built with LaTeX. It was exported from Mathematica 10.3 in 2017, and `fretboard.nb` contains the `Export` call. To regenerate it, open that notebook in Mathematica and run that call. It exports an earlier output (`%102`), so you'll need to point it at the right result in a new session.
