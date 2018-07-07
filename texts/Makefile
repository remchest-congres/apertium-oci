SRC=atòm.tagged.txt

all: oci.unigram.prob

oci.unigram.prob: oci.tagged.txt
	apertium-tagger -d -s 0 -u 2 $@ $<

oci.tagged.txt: $(SRC)
	cat $(SRC) > $@

%.tagged.txt: %.vislcg.txt
	grep -v '^;' $< | cg-conv -A -l | sed "s/\$\^/$\\n^/g" | sed 's/¶/\n/g' | grep -v '\*' > $@

clean: 
	rm *.tagged.txt *.prob
