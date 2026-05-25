# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A bachelor's/diploma thesis (FINKI, UKIM) written in **Macedonian Cyrillic**. Topic: comparative analysis of classical photogrammetry vs. neural methods (NeRF, Gaussian splatting) for 3D reconstruction from 2D images. The repo contains only the LaTeX source — there is no code, no tests, no package manager.

## Building the PDF

The build engine **must be XeLaTeX or LuaLaTeX**, not pdfLaTeX. `Source files/setup.tex` loads `fontspec` and uses `\babelprovide[import, main]{macedonian}`, which requires a Unicode-aware engine. The `main.log` currently in the repo is from a failed pdfLaTeX run (Unicode Cyrillic errors at U+0408 etc.) — ignore it.

Typical full build (run from repo root; mind the space in `Source files/`):

```sh
xelatex main.tex
bibtex main
xelatex main.tex
xelatex main.tex
```

Or with latexmk:

```sh
latexmk -xelatex main.tex
```

There is no Makefile, `.latexmkrc`, or CI config — those commands are the contract.

## Source layout

- `main.tex` — root document. Defines the metadata macros (`\ProjectTitle`, `\ProjectMentor`, `\ProjectCandidate`, `\ProjectDate`, etc.) and `\input`s each chapter from `Source files/` in order. Edit metadata here, not in the chapters.
- `Source files/setup.tex` — all `\usepackage` calls, page geometry, `fancyhdr` footer, `lstlisting` style, and the custom `titlesec` chapter format (suppresses the word "Chapter", keeps just the number). Add new packages here.
- `Source files/00_titlepage.tex` … `06_conclusion.tex` — chapters, included by `\input` from `main.tex`. The numeric prefix is the document order. `03_relatedwork.tex` is currently commented out in `main.tex`.
- `Source files/bibliography.tex` — wraps `\bibliography{refs}` with `bibliographystyle{unsrturl}`.
- `refs.bib` — BibTeX entries (cited via `\cite{key}` from chapters).
- `Miscellaneous/` — all figures referenced by `\includegraphics`. Per-dataset result subdirs (`treeStumpResults/`, `truckResults/`, `roomResults/`, `legoBonsaiResults/`, `rockResults/`, `coinResults/`, `photogrammetrySettings/`) hold the comparison images for chapter 5.
- `template/` — the `UKIM_FINKI_IO.pdf` faculty header used on the title page, plus the EPS logo.

## Conventions worth knowing

- Body text and figure captions are Macedonian Cyrillic. Keep new prose consistent with surrounding language; do not translate without being asked.
- Chapter files start with `\chapter{...}` (sometimes preceded by `\newpage`) and rely on the `setup.tex` `titleformat` override — don't redefine chapter formatting locally.
- Figures live under `Miscellaneous/...` and are included with paths relative to the repo root (e.g. `\includegraphics[width=0.95\linewidth]{Miscellaneous/incremental-sfm.png}`). Keep that convention; don't add a `graphicspath`.
- Result tables in `05_results.tex` follow a fixed column layout: *Algorithm | Time to final model (s) | PSNR (dB) | SSIM | LPIPS*. Match it when adding new datasets.
- The `lh-lcy` package is loaded for Cyrillic encoding support alongside `fontspec` — leave both.
