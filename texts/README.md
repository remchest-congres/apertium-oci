
# Steps

* First convert the raw file to a vislcg format file using:

```
cat atòm.raw.txt | apertium -d ../ oci-disam > atòm.vislcg.txt
```

* Then edit the file `atòm.vislcg.txt` and fix the tokenisation, disambiguate it and add
missing analyses

## Training

### To train the unigram tagger

* The file must not contain any unknown words, otherwise you will get the following 
error:

```
apertium-tagger: can't train LexicalUnit comprising empty Analysis std::vector
Try 'apertium-tagger --help' for more information.
```


