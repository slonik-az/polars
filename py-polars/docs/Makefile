# Minimal makefile for Sphinx documentation
#

VENV ?= source ../.venv/bin/activate;

# You can set these variables from the command line, and also
# from the environment for the first two.
SPHINXOPTS    ?=
SPHINXBUILD   ?= sphinx-build
SOURCEDIR     = source
BUILDDIR      = build

# Put it first so that "make" without argument is like "make help".
help:
	@echo "* To make a <target> with ./.venv activated, issue"
	@echo "  > make <target>"
	@echo "* To make a <target> without venv, issue"
	@echo "  > make VENV='' <target>"
	@echo "    or"
	@echo "  > make <target> VENV=''"
	@echo "* To make a <target> with some other venv activated, issue"
	@echo "  > make VENV='. path/to/venv;' <target>"
	@echo "--------------------------------------------------------------------------------"
	@:; $(VENV)$(SPHINXBUILD) -M help "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

.PHONY: help Makefile

# Catch-all target: route all unknown targets to Sphinx using the new
# "make mode" option.  $(O) is meant as a shortcut for $(SPHINXOPTS).
%: Makefile
	$(VENV)$(SPHINXBUILD) -M $@ "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)
