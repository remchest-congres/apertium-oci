# Wondering aloud sur la question de l'utilisation des paradigmes

En fait comme par hasard on a besoin que nos paradigmes respectent le principe de responsabilité unique.
Pour éviter les répétitions et limiter la taille et la complexité du dico on préfèrera définir plusieurs paradigmes simples
et les combiner ultérieurement.

Par exemple : 

Tenter de définir un seul paradigme qui essaye de faire tout d'un coup (variation ch/it plus définition de la catégorie,
du genre et du nombre) augmente la complexité des données :

```xml
<!-- Un paradigme pour les noms féminins en ch/it-->
<pardef n="nuè/ch__n">
    <e><p><l>chs</l>        <r>ch<s n="n"/><s n="f"/><s n="pl"/></r></p></e>
    <e><p><l>ch</l>         <r>ch<s n="n"/><s n="f"/><s n="sg"/></r></p></e>
    <e><p><l>its</l>        <r>ch<s n="n"/><s n="f"/><s n="pl"/></r></p><par n="v:ch_it"/></e>
    <e><p><l>it</l>         <r>ch<s n="n"/><s n="f"/><s n="sg"/></r></p><par n="v:ch_it"/></e>
</pardef>
```

De plus dans le cas ci-dessus il nous faudrait de toute façon créer un second paradigme pour les noms masculins ayant la variation ch/it :

```xml
<!-- Un paradigme pour les noms masculins en ch/it-->
<pardef n="liè/ch__n">
    <e><p><l>chs</l>        <r>ch<s n="n"/><s n="m"/><s n="pl"/></r></p></e>
    <e><p><l>ch</l>         <r>ch<s n="n"/><s n="m"/><s n="sg"/></r></p></e>
    <e><p><l>its</l>        <r>ch<s n="n"/><s n="m"/><s n="pl"/></r></p><par n="v:ch_it"/></e>
    <e><p><l>it</l>         <r>ch<s n="n"/><s n="m"/><s n="sg"/></r></p><par n="v:ch_it"/></e>
</pardef>
```

On préfèrera donc définir trois paradigmes simples et précis qui pourront être réutilisés et recomposés ailleurs :

    Remarque : Bon ici en vrai on devrais même faire un paradigme pour la gestion du singulier/pluriel mais je vais garder l'exemple simple

```xml
<!-- Un paradigme pour la variation ch/it-->
<pardef n="p:ch_it">
    <e><p><l>ch</l>         <r>ch</r></p></e>
    <e><p><l>it</l>         <r>ch</r></p><par n="v:ch_it"/></e>
</pardef>
```

```xml
<!-- Un paradigme pour les noms féminins-->
<pardef n="p:nom_feminin">
    <e><p><l></l>         <r><s n="n"/><s n="f"/><s n="sg"/></r></p></e>
    <e><p><l>s</l>        <r><s n="n"/><s n="f"/><s n="pl"/></r></p></e>
</pardef>
```

```xml
<!-- Un paradigme pour les noms masculins -->
<pardef n="p:nom_masculin">
    <e><p><l></l>         <r><s n="n"/><s n="m"/><s n="sg"/></r></p></e>
    <e><p><l>s</l>        <r><s n="n"/><s n="m"/><s n="pl"/></r></p></e>
</pardef>
```

Ainsi les mots lièch et nuèch seront représentés dans le dictionnaire sous la forme :

```xml
<e lm="nuèch"><i>nuè</i><par n="p:ch_it"/><par n="p:nom_feminin"/></e>
<e lm="lièch"><i>liè</i><par n="p:ch_it"/><par n="p:nom_masculin"/></e>
```

Si jamais la liste des paradigmes à ajouter à une entrée du dico deviens trop longue et détériore la facilité de lecture de l'entrée, ou
si un groupe de paradigmes sont souvent réutilisés ensemble, il est alors possible de les regrouper dans un paradigme faisant office
d'agrégat :

```xml
<!-- Un paradigme agrégeant les deux autres pour les noms féminins en ch/it-->
<pardef n="p:ch_it/nom_feminin">
    <e><p><l></l>        <r></r></p><par n="p:ch_it"/><par n="p:nom_feminin"/></e>
</pardef>
```

Ainsi le mot nuèch sera représenté dans le dictionnaire sous la forme :

```xml
<e lm="nuèch"><i>nuè</i><par n="p:ch_it/nom_feminin"/></e>
```


Le monde est bien fait, vive le SRP.
