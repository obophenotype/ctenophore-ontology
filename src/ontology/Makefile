
# This Makefile is for use by the cteno Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

OBO = http://purl.obolibrary.org/obo

initial-build: cteno.obo
build: cteno.obo minidiff
release: cteno.obo diff

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
cteno.obo: cteno-edit.owl
	ontology-release-runner $< --allow-overwrite --asserted --simple

# ----------------------------------------
# Imports
# ----------------------------------------
all_imports: imports/go_import.owl imports/pato_import.owl

imports/%_import.owl: cteno-edit.owl
	owltools $< $(OBO)/$*.owl --add-imports-from-supports --extract-module -s $(OBO)/$*.owl -c --extract-mingraph  --set-ontology-id $(OBO)/go/extensions/$@ -o $@

# ----------------------------------------
# Diffs
# ----------------------------------------

# 
diff: cteno.obo
	cd diffs && touch cteno-diff && rm cteno-*diff* && make

# Minidiff: intra-release differences
minidiff: cteno.obo
	cd diffs  && touch cteno-diff && rm cteno-*diff* && make TGTS='html txt' SRC=../cteno.obo && mail -s cteno-diffs RELEASE-MANAGER@FOO.BAR < cteno-combined-diff.txt

