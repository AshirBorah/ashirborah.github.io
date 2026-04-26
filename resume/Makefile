# Build the academic CV.
#
# Default: produces ../docs/Ashir-Borah-CV.pdf via three pdflatex passes
# (latexmk isn't installed on this system; passes resolve refs/hyperlinks).
#
# Usage:
#   make           # build CV
#   make clean     # remove build/ but keep the committed PDF
#   make watch     # rebuild on every cv.tex / publications.tex change

LATEX        := pdflatex
LATEXOPTS    := -interaction=nonstopmode -halt-on-error -output-directory=build
CV_SOURCES   := cv.tex publications.tex
CV_TARGET    := ../docs/Ashir-Borah-CV.pdf
RES_SOURCES  := resume.tex
RES_TARGET   := ../docs/Ashir-Borah-Resume.pdf

.PHONY: all cv resume clean watch

all: cv resume

cv: $(CV_TARGET)
resume: $(RES_TARGET)

$(CV_TARGET): $(CV_SOURCES) | build
	$(LATEX) $(LATEXOPTS) cv.tex
	$(LATEX) $(LATEXOPTS) cv.tex
	$(LATEX) $(LATEXOPTS) cv.tex
	cp build/cv.pdf $(CV_TARGET)

$(RES_TARGET): $(RES_SOURCES) | build
	$(LATEX) $(LATEXOPTS) resume.tex
	$(LATEX) $(LATEXOPTS) resume.tex
	cp build/resume.pdf $(RES_TARGET)

build:
	mkdir -p build

clean:
	rm -rf build

watch:
	@command -v inotifywait >/dev/null || { echo "inotifywait required (apt install inotify-tools)"; exit 1; }
	@while true; do \
	  inotifywait -qe modify,close_write $(CV_SOURCES) $(RES_SOURCES); \
	  $(MAKE) all; \
	done
