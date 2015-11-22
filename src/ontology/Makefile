
# This Makefile is for use by the cteno Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

OBO = http://purl.obolibrary.org/obo

initial-build: cteno.obo
build: cteno.obo minidiff
release: cteno.obo diff

test: cteno.obo

TARGETS = cteno.obo cteno.owl imports diffs
copy-release:
	cp -pr $(TARGETS) ../.. && cd ../.. && git add imports/*

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
#cteno.obo: cteno-edit.owl
#	ontology-release-runner $< --reasoner elk --allow-overwrite --asserted --simple

cteno.owl: cteno-edit.owl
	owltools --use-catalog $< --assert-inferred-subclass-axioms $@

cteno.obo: cteno.owl
	owltools --use-catalog $< -o -f obo $@

# ----------------------------------------
# Imports
# ----------------------------------------
all_imports: imports/go_import.owl imports/pato_import.owl

imports/%_import.owl: cteno-edit.owl
	owltools $< $(OBO)/$*.owl --add-imports-from-supports --extract-module -s $(OBO)/$*.owl -c --extract-mingraph  --set-ontology-id $(OBO)/go/extensions/$@ -o $@

# ----------------------------------------
# Bridges
# ----------------------------------------
core.owl: cteno.owl
	owltools --use-catalog $< --extract-bridge-ontologies -x -d . -s cteno --remove-imports-declarations -o core.owl

# ----------------------------------------
# MARKDOWN EXPORT
# ----------------------------------------
markdown: cteno.owl
	owltools $< --merge-imports-closure --ontology-to-markdown md

# ----------------------------------------
# Diffs
# ----------------------------------------

# 
diff: cteno.obo
	cd diffs && touch cteno-diff && rm cteno-*diff* && make

# Minidiff: intra-release differences
minidiff: cteno.obo
	cd diffs  && touch cteno-diff && rm cteno-*diff* && make TGTS='html txt' SRC=../cteno.obo && mail -s cteno-diffs RELEASE-MANAGER@FOO.BAR < cteno-combined-diff.txt

# ----------------------------------------
# Git
# ----------------------------------------
gitcp:
	git commit -m new -a && git push origin master

