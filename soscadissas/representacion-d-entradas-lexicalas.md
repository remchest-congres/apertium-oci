# Concernant la manière de représenter les mots dans notre dictionnaire...

## Principe de la question des variétés

Pour la gestion des variations le principe est de créer une `ambiguité` dans notre dictionaire. Celle-ci sera ensuite "désambiguisée" en fonction de paramètres renseignés par l'utilisateur ou en fonction d'une liste de paramètres définis en dur pour un dialecte donné.

Par exemple pour le mot `nuit` en français, le système produira simultanément les sorties :

    nuèch
    nuò<v:ue_uo>ch
    nuèit<v:ch_it>

```sh
$ echo 'nuit' | apertium -d . fra-oci-dgen
^nuèch<n><f><sg>/nuèch/nuò<v:ue_uo>ch/nuèit<v:ch_it>$
```

Ensuite une règle de désambiguisation permet de dire :

- <b>SI</b> la variable `ue_uo` a été renseignée par l'utilisateur <br/><b>ALORS</b> choisi l'entrée possédant le tag `<v:ue_uo>`
- <b>SINON SI</b> la variable `ch_it` a été renseignée par l'utilisateur <br/><b>ALORS</b> choisi l'entrée possédant le tag `<v:ch_it>`
- <b>SINON</b> choisi l'entrée qui n'a aucun tag

La forme ainsi générée sera donc adaptée aux variations choisies par l'utilisateur.

    Remarque: la syntaxe des contraintes gramaticales des règles de désambiguisation est légèrement différente de ce que je viens de présenter.
    Elle n'utilise pas véritablement de `si...sinon si...` mais cela revient au même résultat.

## Créer une ambiguité

Même si le principe reste le même, il existe différentes manières de créer une ambiguité dans le dictionnaire. Chacune ayant des avantages et des inconvéniants.

Je vais donc essayer de présenter ici chacune de ces solutions, leurs avantages et inconvéniants.

A mon sens le choix de l'une ou l'autre solution dépendra du mot concerné, de ses variations et des règles métier qui s'appliquent. Je ne m'attend pas à trouver une solution universelle.

# Solution 1 : dupliquer les entrées

La manière la plus simple de créer une ambiguité sur un mot est de créer un entrée pour chacune des formes différentes du mot.

Par exemple pour le mot `nuèch` et ses variantes `nuèit` et `nuòch` :

```xml
<!-- 
chaque forme dispose de son entrée, les formes considérés comme des variations étant identifiées par 
un paradigme spécifique 
-->
<e lm="nuèch">
    <p>
        <l>nuèch</l>
        <r>nuèch<s n="n"/><s n="f"/><s n="sg"/></r>
    </p>
</e>

<e lm="nuèit">
    <p>
        <l>nuèit</l>
        <r>nuèch<s n="n"/><s n="f"/><s n="sg"/></r>
    </p>
    <par n="v:ch_it"/>
</e>

<e lm="nuòch">
    <p>
        <l>nuòch</l>
        <r>nuèch<s n="n"/><s n="f"/><s n="sg"/></r>
    </p>
    <par n="v:ue_uo"/>
</e>
```
## Avantages

Ce type de définition à l'avantage d'être exhaustif et strict, l'ensemble des possibilités sont visibles d'un seul coup. Le nombre et la forme des mots pouvant être reconnus ou réalisés est strictement limité.

## Inconvéniants

Ce type de définition possède plusieurs inconvéniants :
- Lourdeur d'écriture : écrire une entrée pour chaque variation est chronophage et source d'erreur humaines
- Difficulté de maintenabilité : de nombreuses lignes extrèmement semblables rendent la lecture du code et donc la maintenabilité et l'évolution compliquée
- Cette solution précipite une grande augmentation de la taille du dictionnaire ce qui, en plus d'augementer la difficulté de lecture et d'écriture, pourra avoir un impact sur les performances notamment sur le temps de compilation et sur le transfert des fichiers de données (du à leur poid).

# Solution 2 : une entrée pour les gouverner toutes

Grace à l'utilisation et la combinaison de nombreux paradigmes simples il est techniquement possible de définir la totalité ou la quasi-totalité (selon les cas) des variations d'un mot dans une seule entrée.

Par exemple pour le mot `nuèch` et ses variantes `nuèit` et `nuòch` :

```xml
<e lm="nuèch">
    <i>n</i>                    <!-- ce bout du mot est invariable-->
    <par n="p:ue_uo"/>          <!-- ce paradigme gère la variation uè/uò-->
    <par n="p:ch_it"/>          <!-- ce paradigme gère la variation ch/it-->
    <par n="p:nom_feminin"/>    <!-- ce paradigme ajoute les tags propres à un nom féminin -->
</e>
```

## Avantages

Cette écriture est courte et conscise. Elle pourra aussi s'avérer facile à lire à condition que les paradigmes soient bien nommés.

Cette solution assure de minimiser la taille de notre dictionnaire et donc de faciliter sa maintenance et son évolution. Cela permetra aussi certainement de réduire le temps de compilation du dictionnaire.

## Inconvéniants

Cette solution a pour incovéniant qu'elle permet de générer des formes n'existant pas dans la vrai vie.

En effet cette solution permet générer les formes suivantes : 

    nuèch
    nuèit
    nuòch
    nuòit

la forme `nuòit` pourra donc techniquement être produite (si un utilisateur entre la bonne séquence de paramètres) alors que je ne crois pas qu'elle existe en réalité.

# Solution 3 : un mix des deux autres

les deux solutions précédentes ne s'excluent pas l'une l'autre et peuvent être combinées pour
répondre à des cas métiers précis.

Par exemple dans le cas de `nuèch`/`nuèit`/`nuòch` vu précédemment on peut combiner une duplication
de l'entrée avec une utilisation de paradigmes de variations pour arriver exactement à la solution attendue :

```xml
<!-- une définition pour l'ambiguité nuèch/nuòch -->
<e lm="nuòch">
    <i>n</i>                    <!-- partie du mot invariable, il commencera toujours pas un 'n' -->
    <par n="p:ue_uo"/>          <!-- ce paradigme gère la variation uè/uò-->
    <i>ch</i>                   <!-- partie du mot invariable, après 'uè' ou 'uò' on aura toujours 'ch' -->
    <par n="p:nom_feminin"/>    <!-- ce paradigme ajoute les tags propres à un nom féminin -->
</e>

<!-- une autre définitions pour l'ambiguité nuèch/nuèit -->
<e lm="nuèch">
    <i>nuè</i>                  <!-- partie du mot invariable, il commencera toujours pas un 'nuè' -->
    <par n="p:ch_it"/>          <!-- ce paradigme gère la variation ch/it, après 'nuè' on autorise la variation ch/it -->
    <par n="p:nom_feminin"/>    <!-- ce paradigme ajoute les tags propres à un nom féminin -->
</e>
```

Cet exemple permet donc de limiter le système à ne reconnaitre et produire que les formes souhaités :

    nuèch
    nuèit
    nuòch

## Avantages

- Cette solution reste plus courte en ériture que la [solution 1](#solution-1--dupliquer-les-entrées), permettant ainsi de restreinte la quantité de données
- Cette solution permet une gestion plus stricte des variations autorisées que la [solution 2](#solution-2--une-entrée-pour-les-gouverner-toutes), permettant de s'assurer que le système ne puisse pas reconnaitre ou produire des formes inexistantes dans la réalité

## Inconvéniants

- Cette solution demande une étude précise pour chaque entrée car elle nécessite une bonne conaissance de l'exclusion ou des combinaisons possibles entre les différentes variations d'un mot
- Cette solution rend l'évolution du système plus difficile. Les contraintes appliquées par cette solution étant fortes, ajouter une nouvelle variation qui n'était pas prévue au début pourra forcer la réécriture de toutes les définitions d'un mot.

