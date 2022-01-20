SRC_LENGADOC=continent_leng.tagged.txt diariliceu_leng.tagged.txt glacier_leng.tagged.txt jocnombres_leng.tagged.txt uranus_leng.tagged.txt
# bastida_leng, carcassona_leng, galaxia_leng, neptun_leng
SRC_GASCON=acidnucleic_gascon.tagged.txt alejandrolanusse_gascon.tagged.txt amassada_gascon.tagged.txt atom_gascon.tagged.txt aush_gascon.tagged.txt avesac_gascon.tagged.txt basalexicau_gascon.tagged.txt bilcato_gascon.tagged.txt bolivia_gascon.tagged.txt boisgontier_gascon.tagged.txt caraecara_gascon.tagged.txt cors_aran.tagged.txt danielfabre_gascon.tagged.txt eac_gascon.tagged.txt hlama_coming.tagged.txt lascausas_gascon.tagged.txt leclercq_gascon.tagged.txt linguatec_gascon.tagged.txt puntrevoc_gascon.tagged.txt revoc_gascon.tagged.txt trensoccitania_gascon.tagged.txt votzpresent_gasc.tagged.txt

all: oci_leng.unigram.prob oci_gasc.unigram.prob

oci_leng.unigram.prob: oci_leng.tagged.txt
	apertium-tagger -d -s 0 -u 2 $@ $<

oci_gasc.unigram.prob: oci_gasc.tagged.txt
	apertium-tagger -d -s 0 -u 2 $@ $<

oci_leng.tagged.txt: $(SRC_LENGADOC)
	cat $(SRC_LENGADOC) > $@

oci_gasc.tagged.txt: $(SRC_GASCON)
	cat $(SRC_GASCON) > $@

%.tagged.txt: %.vislcg.txt
	grep -v '^;' $< | cg-conv -A -l | sed "s/\$\^/$\\n^/g" | sed 's/¶/\n/g' | grep -v '\*' > $@

clean: 
	rm *.tagged.txt *.prob
