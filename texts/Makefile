#SRC=atom_gascon.tagged.txt continent.tagged.txt bastida.tagged.txt carcassona_ciutat.tagged.txt galaxia.tagged.txt glacier.tagged.txt neptun_prov.tagged.txt uranus_prov.tagged.txt leclercq_gascon.tagged.txt cors_aran.tagged.txt hlama_coming.tagged.txt
#SRC=atom_gascon.tagged.txt continent.tagged.txt bastida.tagged.txt carcassona_ciutat.tagged.txt galaxia.tagged.txt glacier.tagged.txt neptun_prov.tagged.txt uranus_prov.tagged.txt leclercq_gascon.tagged.txt cors_aran.tagged.txt hlama_aran.tagged.txt
SRC=atom_gascon.tagged.txt continent.tagged.txt glacier.tagged.txt cors_aran.tagged.txt hlama_coming.tagged.txt uranus_prov.tagged.txt

all: oci.unigram.prob

oci.unigram.prob: oci.tagged.txt
	apertium-tagger -d -s 0 -u 2 $@ $<

oci.tagged.txt: $(SRC)
	cat $(SRC) > $@

%.tagged.txt: %.vislcg.txt
	grep -v '^;' $< | cg-conv -A -l | sed "s/\$\^/$\\n^/g" | sed 's/¶/\n/g' | grep -v '\*' > $@

clean: 
	rm *.tagged.txt *.prob
