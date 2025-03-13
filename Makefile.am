###############################################################################
## Makefile for apertium-oci
###############################################################################

LANG1=oci
BASENAME=apertium-$(LANG1)

VAR1=_gascon

# CHANGES : ici on indique notre fichier de préférences dans la liste des fichiers à générer par le make

TARGETS_COMMON =			\
	$(LANG1).automorf.bin		\
	$(LANG1).automorf.att.gz	\
	$(LANG1).autogen.bin		\
	$(LANG1).autogen.att.gz		\
	$(LANG1).autopgen.bin		\
	$(LANG1).rlx.bin		\
	$(LANG1).prefs.rlx.bin \
	$(LANG1)$(VAR1).prefs.rlx.bin

# Use this goal for creating .deps, otherwise make -j2 will give problems:
@ap_include@

###############################################################################
## Occitan transducer
###############################################################################

.deps/$(LANG1).metadix: $(BASENAME).$(LANG1).metadix .deps/.d
	xsltproc --stringparam alt $(LANG1) alt.xsl $(BASENAME).$(LANG1).metadix >$@

.deps/$(LANG1).dix: .deps/$(LANG1).metadix .deps/.d
	xsltproc buscaPar.xsl .deps/$(LANG1).metadix | uniq >$$$$tmp1 && \
	xsltproc $$$$tmp1 .deps/$(LANG1).metadix > $@ && rm $$$$tmp1

.deps/$(LANG1).post.dix: $(BASENAME).post-$(LANG1).dix .deps/.d
	xsltproc --stringparam alt $(LANG1) alt.xsl $(BASENAME).post-$(LANG1).dix >$@

###############################################################################

$(LANG1).automorf.bin: .deps/$(LANG1).dix $(BASENAME).$(LANG1).acx
	apertium-validate-dictionary .deps/$(LANG1).dix
	lt-comp lr .deps/$(LANG1).dix $@ $(BASENAME).$(LANG1).acx

$(LANG1).autogen.bin: .deps/$(LANG1).dix
	apertium-validate-dictionary .deps/$(LANG1).dix
	lt-comp rl .deps/$(LANG1).dix $@

$(LANG1).autogen.att.gz: $(LANG1).autogen.bin
	lt-print $< | gzip -n -9 -c > $@

$(LANG1).automorf.att.gz: $(LANG1).automorf.bin
	lt-print $< | gzip -n -9 -c > $@

$(LANG1).autopgen.bin: .deps/$(LANG1).post.dix
	lt-comp lr $< $@

###############################################################################
## Spell checker
###############################################################################

$(LANG1).zhfst: .deps/acceptor.default.hfst .deps/errmodel.default.hfst
	rm -f $@
	zip -Z store -j $@ .deps/acceptor.default.hfst .deps/errmodel.default.hfst speller/index.xml

.deps/editdist.default.hfst: speller/editdist.default.txt .deps/acceptor.default.hfst
	python dev/editdist.py -v -s -d 1 -e '@0@' -i $< -a .deps/acceptor.default.hfst >$@.txt
	hfst-txt2fst -i $@.txt -e '@0@' -o $@
	rm -f $@.txt

.deps/editstrings.default.hfst: .deps/strings.default.hfst .deps/editdist.default.hfst
	hfst-disjunct $^ | hfst-minimise | hfst-repeat -f 1 -t 2 -o $@

.deps/errmodel.default.hfst: .deps/words.default.hfst .deps/editstrings.default.hfst
	hfst-disjunct $^ | hfst-fst2fst -f olw -o $@

.deps/words.default.hfst: speller/words.default.txt
	grep -v -e "^#" -e "^$$" $< | hfst-strings2fst -j -o $@

.deps/strings.default.hfst: speller/strings.default.txt .deps/anystar.hfst
	grep -v -e "^#" -e "^$$" $< | hfst-strings2fst -j | hfst-concatenate .deps/anystar.hfst - | hfst-concatenate - .deps/anystar.hfst -o $@

.deps/anystar.hfst:
	echo "?*;" | hfst-regexp2fst -S -o $@

.deps/$(LANG1).autogen.bin: .deps/spell.$(LANG1).dix
	lt-comp rl $< $@

.deps/acceptor.default.hfst: .deps/$(LANG1).autogen.bin
	lt-print .deps/$(LANG1).autogen.bin | sed 's/ε/@0@/g' | sed 's/ /@_SPACE_@/g' | hfst-txt2fst | hfst-fst2fst -t | hfst-project --project=lower | hfst-minimise |hfst-fst2fst -f olw -o $@

.deps/spell.$(LANG1).dix: .deps/$(LANG1).dix .deps/.d
	echo '<dictionary>' > $@
	cat $< | xmllint  --xpath './/alphabet|.//sdefs|.//pardefs|.//section[@id="main"]' - >> $@
	echo '</dictionary>' >> $@
	cat .deps/spell.$(LANG1).dix | xmllint --encode utf8 - > .deps/spell.$(LANG1).dixtmp
	mv .deps/spell.$(LANG1).dixtmp $@



###############################################################################
## Disambiguation rules
###############################################################################

$(LANG1).rlx.bin: $(BASENAME).$(LANG1).rlx
	cg-comp $< $@

###############################################################################
## Variant preferences
###############################################################################

# CHANGES : on compile le fichier de règles pour les préférences

$(LANG1).prefs.rlx.bin: $(BASENAME).$(LANG1).prefs.rlx
	cg-comp $< $@

$(LANG1)$(VAR1).prefs.rlx.bin: $(BASENAME).$(LANG1)$(VAR1).prefs.rlx
	cg-comp $< $@

###############################################################################
## Distribution
###############################################################################
EXTRA_DIST=$(BASENAME).$(LANG1).metadix		\
		$(BASENAME).$(LANG1).acx	\
		$(BASENAME).$(LANG1).rlx	\
		$(BASENAME).post-$(LANG1).dix	\
		$(LANG1).prob			\
		modes.xml \
		alt.xsl \
		buscaPar.xsl \
		principal.xsl

###############################################################################
## Installation stuff
###############################################################################
#
#   apertium_oci_dir: This is where the compiled binaries go
#   apertium_oci_srcdir: This is where the source files go

apertium_ocidir=$(prefix)/share/apertium/$(BASENAME)/
apertium_oci_srcdir=$(prefix)/share/apertium/$(BASENAME)/

EXTRA_TARGETS=$(LANG1).prob

if HAVE_HFSTOSPELL
EXTRA_TARGETS += $(LANG1).zhfst
endif # HAVE_HFSTOSPELL

apertium_oci_DATA=$(TARGETS_COMMON) $(EXTRA_TARGETS)

pkgconfigdir = $(prefix)/share/pkgconfig
pkgconfig_DATA = $(BASENAME).pc

# Modes are not installed since they're all just "debug modes". All
# modes are created by this goal.
modes/$(LANG1)-morph.mode: modes.xml
	apertium-validate-modes modes.xml
	apertium-gen-modes modes.xml

noinst_DATA=modes/$(LANG1)-morph.mode

install-data-local: install-modes
	test -d $(DESTDIR)$(apertium_oci_srcdir) || mkdir -p $(DESTDIR)$(apertium_oci_srcdir)
	$(INSTALL_DATA) $(BASENAME).$(LANG1).metadix $(DESTDIR)$(apertium_oci_srcdir)
	$(INSTALL_DATA) $(BASENAME).$(LANG1).rlx $(DESTDIR)$(apertium_oci_srcdir)
	$(INSTALL_DATA) $(BASENAME).post-$(LANG1).dix $(DESTDIR)$(apertium_oci_srcdir)

CLEANFILES = $(TARGETS_COMMON)
clean-local:
	-rm -rf .deps modes

###############################################################################
## Test
###############################################################################

test: all
	apertium-regtest test
