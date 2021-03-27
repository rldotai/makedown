# ============================================================================
# A Markdown --> PDF Makefile
# ============================================================================
#
# Usage
# -----
# If everything's setup and you just want to generate the PDFs, all you have
# to do is run `make` or `make generate-pdfs`.
#
# This is assuming you've saved your Markdown files (with extension `.md`)
# in the source directory and all the necessary binaries/directories can be
# found by `make`
# Less tersely:
# 	- The source, build, and output directories all exist,
# 	- Pandoc is installed (check with `pandoc --help`)
# 	- A TeX engine is installed (ie, `xelatex`)
# 	- Your template (if specified) exists
#
#
# Setup & Customization
# ---------------------
# 1. Set the source, build, output directories in this Makefile.
# 2. If you have a particular LaTeX template you want to use, specify its path
#
# This Makefile can create the needed directories via `make initialize` and can
# also download the default Pandoc template for TeX files (using
# `make download-latex-template).
#
# Further customization is up to the user.

# ============================================================================
# Setup
# ============================================================================

# Source, build, and output directories
SOURCE_DIR := source
BUILD_DIR := build
OUTPUT_DIR := output


# Define pandoc options
define PANDOC_OPTIONS
	+RTS -K512m -RTS \
	--to pdf \
	--from markdown+yaml_metadata_block+autolink_bare_uris-auto_identifiers+tex_math_single_backslash+raw_attribute+header_attributes \
	--highlight-style tango \
	--pdf-engine xelatex \
	--strip-comments \
	--metadata date="`date "+%B %e, %Y"`" \
	-V graphics=yes \
	-V fontsize=12pt
endef

# Templates -- can be downloaded via `make download-cool-templates` or use your own
# LATEX_TEMPLATE := pandoc-latex.latex
# LATEX_PREAMBLE := pandoc-preamble.tex

# ----------------------------------------------------------------------------
# Defining variables and options to be used for build process
# ----------------------------------------------------------------------------

PANDOC := pandoc

# Get list of source files
SRC_FILES := $(sort $(wildcard $(SOURCE_DIR)/*.md))

FILENAMES := $(basename $(notdir $(SRC_FILES)))
PDF_FILES := $(addprefix $(OUTPUT_DIR)/,$(addsuffix .pdf,$(FILENAMES)))
OUTPUT_FILES := $(PDF_FILES)

# Define template option for pandoc if the template is specified (and exists)
ifeq ("$(wildcard $(LATEX_TEMPLATE))","")
	TEMPLATE_OPTION :=
else
	TEMPLATE_OPTION := --template $(LATEX_TEMPLATE)
endif

# Define preamble/header option if the preamble is specified (and exists)
ifeq ("$(wildcard $(LATEX_PREAMBLE))","")
	PREAMBLE_OPTION :=
else
	PREAMBLE_OPTION := --include-in-header $(LATEX_PREAMBLE)
endif

# The path to this very Makefile
MAKEFILE_PATH := $(abspath $(lastword $(MAKEFILE_LIST)))

# ============================================================================
# Main rules
# ============================================================================

## help:
## Display this helpful help message.
.PHONY : help
help : $(MAKEFILE_PATH)
	@sed -n 's/^##//p' $<

## pdfs:
## Generate the PDFs from the Markdown source files
.PHONY: pdfs
pdfs: $(PDF_FILES) $(LATEX_TEMPLATE) $(LATEX_PREAMBLE)
	@echo ""
	@echo "Generating PDFs..."


## clean:
## Remove generated files
.PHONY: clean
clean:
	rm -f $(BUILD_DIR)/*
	rm -f $(OUTPUT_DIR)/*

## clean-all:
## Remove generated files AND directories
.PHONY: clean-all
clean-all: clean remove-directories

## watch:
## Watch for changes in source files and rebuild accordingly
.PHONY: watch
watch: pdfs
	@if command -v entr > /dev/null; then \
		ls -d $(SRC_FILES) $(LATEX_TEMPLATE) $(LATEX_PREAMBLE) Makefile | entr -s "make pdfs"; \
	else \
		echo "entr(1) not installed, cannot watch files..."; \
	fi

# ----------------------------------------------------------------------------
# The actual nuts-and-bolts -- don't modify unless you know what you're doing.
# ----------------------------------------------------------------------------

# Rule for copying built PDFs to output
$(OUTPUT_DIR)/%.pdf : $(BUILD_DIR)/%.pdf
	@echo ""
	@echo "Copying built PDF to output..."
	cp $< $@

# Rule for copying files to the build directory
$(BUILD_DIR)/%.md : $(SOURCE_DIR)/%.md
	@echo ""
	@echo "Copying source files to build..."
	cp $< $@

# Rule for generating PDFs from Markdown files
$(BUILD_DIR)/%.pdf: $(BUILD_DIR)/%.md
	@echo ""
	@echo "Converting Markdown to PDF via Pandoc..."

	$(PANDOC) $(PANDOC_OPTIONS) \
	$(TEMPLATE_OPTION) \
	$(PREAMBLE_OPTION) \
	--output $@ \
	$<


# =============================================================================
# Extras
# =============================================================================

# Some additional rules providing various bells and whistles.

# ----------------------------------------------------------------------------
# Initial setup
# ----------------------------------------------------------------------------

## initialize:
## Setup the directory in the expected form
.PHONY: initialize
initialize: create-directories

## create-directories:
## Create the directories expected by this Makefile
.PHONY: create-directories
create-directories:
	mkdir -p $(SOURCE_DIR)
	mkdir -p $(BUILD_DIR)
	mkdir -p $(OUTPUT_DIR)

## remove-directories:
## Remove the directories generated by this Makefile
.PHONY: remove-directories
remove-directories:
	- rmdir $(BUILD_DIR)
	- rmdir $(OUTPUT_DIR)
	- rmdir $(SOURCE_DIR)

## download-cool-templates:
## Download my cool templates from GitHub
.PHONY: download-cool-templates
download-cool-templates:
	wget https://raw.githubusercontent.com/rldotai/makedown/master/makedown/pandoc-latex.latex -O pandoc-latex.latex

## download-pandoc-templates:
## Download the standard pandoc templates from GitHub
.PHONY: download-pandoc-templates
download-pandoc-templates:
	wget https://raw.githubusercontent.com/jgm/pandoc-templates/master/default.latex -O default.latex

# ----------------------------------------------------------------------------
# Listing source files and targets (mainly for debugging)
# ----------------------------------------------------------------------------
## list-sources:
## List the source files found by this Makefile.
.PHONY: list-sources
list-sources:
	@for name in $(SRC_FILES); do\
		echo $${name};\
	done

## list-targets:
## List all targets in Makefile
# (from: https://stackoverflow.com/a/26339924)
.PHONY: list-targets
list-targets:
	@$(MAKE) -pRrq -f $(lastword $(MAKEFILE_LIST)) : 2>/dev/null | awk -v RS= -F: '/^# File/,/^# Finished Make data base/ {if ($$1 !~ "^[#.]") {print $$1}}' | sort | egrep -v -e '^[^[:alnum:]]' -e '^$@$$'

