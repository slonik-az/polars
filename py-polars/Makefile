SHELL=/bin/bash
WHEELS_DEBUG = wheels/debug
WHEELS_RELEASE = wheels/release

.PHONY: pre-commit test pip help

help:
	@echo "* To make a <target> with ./.venv activated, issue"
	@echo "  > make <target>"
	@echo "* To make a <target> without venv, issue"
	@echo "  > make VENV='' <target>"
	@echo "    or"
	@echo "  > make <target> VENV=''"
	@echo "* To make a <target> with some other venv activated, issue"
	@echo "  > make VENV='. path/to/venv/bin/activate;' VENV_BIN='/abs/path/to/venv/bin/' <target>"

VENV_BIN  =
THIS_VENV = . $(VENV_BIN)activate;
VENV 	  = $(THIS_VENV)

ifneq ($(CONDA_EXE),)
	CONDA_DEACT = . $(shell $(CONDA_EXE) info --base -q)/etc/profile.d/conda.sh; \
		while [ -n "$$CONDA_PREFIX" ]; do conda deactivate; done;
else
	CONDA_DEACT =
endif

VENV_ACT     = $(CONDA_DEACT) $(VENV)
VENV_ACT_BIN = $(VENV_ACT) $(VENV_BIN)

ifeq ($(VENV),$(THIS_VENV))
VENV_BIN := $(abspath .venv/bin)/
.venv:
	python -m venv  .venv
	$(VENV_ACT_BIN)pip install -U pip
	$(VENV_ACT_BIN)pip install -r build.requirements.txt
	# @unset CONDA_PREFIX && source venv/bin/activate && maturin develop
else ifeq ($(VENV),) # no virtual environment
CONDA_DEACT =
.venv:
	:
else # some other virtual environment
.venv:
	:
endif

develop: .venv
ifeq ($(VENV),)
	maturin develop
else
	unset CONDA_PREFIX; $(VENV_ACT_BIN)maturin develop
endif

develop-release: .venv
	$(VENV_ACT_BIN)maturin develop --release

.PHONY: clean cleaner cleanest CLEAN-repo
clean:
	cargo clean

cleaner: clean
	-rm -rf .venv

cleanest: cleaner
	-rm -rf $(WHEELS_DEBUG)
	-rm -rf $(WHEELS_RELEASE)

CLEAN-repo: # removes all untracked files in the entire repo
	git clean -dx -f

pre-commit: .venv
	$(VENV_ACT_BIN)isort .
	$(VENV_ACT_BIN)black .
	$(VENV_ACT_BIN)blackdoc .
	$(VENV_ACT_BIN)mypy
	$(VENV_ACT_BIN)flake8
	make -C .. fmt_toml
	cargo fmt --all

test: # develop
	$(VENV_ACT_BIN)pytest -v tests

test-with-cov: .venv
	cd tests; $(VENV_ACT_BIN)pytest \
		--cov=polars \
		--cov-report xml \
		--cov-fail-under=90 \
		--import-mode=importlib

doctest:
	cd tests && $(VENV_ACT_BIN)python run_doc_examples.py

build-wheel: .venv
	$(VENV_ACT_BIN)maturin build -o $(WHEELS_DEBUG)

build-wheel-release: .venv
	# $(VENV_ACT_BIN)maturin build -o $(WHEELS_RELEASE) --release
	$(VENV_ACT_BIN)maturin build -o $(WHEELS_RELEASE) --release --rustc-extra-args="-C target-cpu=native"

build: build-wheel
build-release: build-wheel-release

install-wheel: # build-wheel
	$(VENV_ACT_BIN)pip install --no-deps --force-reinstall -U $(WHEELS_DEBUG)/polars-*.whl

install-wheel-release: # build-wheel-release
	$(VENV_ACT_BIN)pip install --no-deps --force-reinstall -U $(WHEELS_RELEASE)/polars-*.whl

install: install-wheel
install-release: install-wheel-release

build-and-install: build-wheel install-wheel
build-and-install-release: build-wheel-release install-wheel-release

#EOF
