
# This Makefile is for use by the cteno Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

OBO = http://purl.obolibrary.org/obo
ONT=cteno
BASE=$(OBO)/$(ONT)
ROBOT = robot
USECAT = --use-catalog

initial-build: cteno.obo
build: all_imports cteno.obo 
release: all_imports cteno.obo copy-release

test: cteno.obo

TARGETS = cteno.obo cteno.owl taxonomy.owl imports diffs
copy-release:
	cp -pr $(TARGETS) ../.. && cd ../.. && git add imports/*

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
#cteno.obo: cteno-edit.owl
#	ontology-release-runner $< --reasoner elk --allow-overwrite --asserted --simple

cteno.owl: cteno-edit.owl
	robot reason -i $< -r elk relax reduce -r elk annotate -V $(BASE)/releases/`date +%Y-%m-%d`/$(ONT).owl -o $@

cteno.obo: cteno.owl
	owltools --use-catalog $< --merge-imports-closure -o -f obo --no-check $@.tmp && grep -v ^owl-axioms $@.tmp > $@

# ----------------------------------------
# Imports
# ----------------------------------------
IMPS = go chebi uberon cl bspo ro
all_imports: all_imports_obo all_imports_owl

all_imports_owl: $(patsubst %,imports/%_import.owl,$(IMPS))
all_imports_obo: $(patsubst %,imports/%_import.obo,$(IMPS))

imports/seed.txt: cteno-edit.owl
	owltools $(USECAT) $< --merge-support-ontologies --export-table $@.tmp && cut -f1 $@.tmp > $@

imports/%_seed.txt: cteno-edit.owl
	owltools $(USECAT) $< --remove-import-declaration $(OBO)/cteno/imports/%_import.owl --merge-support-ontologies --export-table $@.tmp && cut -f1 $@.tmp > $@
imports/ro_seed.txt:
	echo using existing ro seed

imports/%_import.owl: mirror/%.owl imports/%_seed.txt
	$(ROBOT) extract -i $< -T imports/$*_seed.txt --method BOT -O $(OBO)/$@ -o $@
.PRECIOUS: imports/%_import.owl

imports/%_import.obo: imports/%_import.owl
	owltools $< -o -f obo $@
##	$(ROBOT) convert -i $< -o $@.tmp.obo && mv $@.tmp.obo $@

mirror/%.owl:
	owltools $(OBO)/$*.owl --make-subset-by-properties BFO:0000050 RO:0002202  RO:0002207  // --extract-mingraph --set-ontology-id $(OBO)/$*.owl -o $@
.PRECIOUS: mirror/%.owl

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

