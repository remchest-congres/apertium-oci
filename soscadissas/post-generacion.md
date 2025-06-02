le pb de la post génération c'est que

- en fonction des variations on ne va pas vouloir forcément faire les mêmes choix de pgen
- mais la pgen passe après la dernière étape de désambiguisation => a ce moment là on a plus aucune information des variations/dialectes selectionnés par l'user
- mais pourtant il nous faut réagir en fonction

une solution est de marquer la présence d'une variation avant la pgen de manière durable (aka qui ne sera pas retirée lors de la désamb)

par exemple :

cas de test : 

    fr : 	sur le portail
    basique : 
      gasc : sus lo portau
      lang : sus lo portal

    apres post gen :
      gasc :	suu portau
      lang :	sul portal

dans le dico monolingue on peut renseigner la paire
  detlo <-> lo<det><def><m><sg>

et y appliquer un paradigme :

  <pardef n="pg:ul_uu">
    <e><p><l></l>           <r></r></p></e>
    <e><p><l>_uu</l>        <r></r></p><par n="v:ul_uu"/></e>
  </pardef>

ainsi si la variable ul_uu est présente en entrée, le dico monoligue produira
  lo --> detlo_uu
et sinon il produira 
  lo --> detlo

la post-generation pourra ainsi contenir les règles:
  <e>
    <p>
      <l>sus<b/><a/>detlo_uu<b/></l>
      <r>suu<b/></r>
    </p>
  </e>
  <e>
    <p>
      <l>sus<b/><a/>detlo<b/></l>
      <r>sul<b/></r>
    </p>
  </e>


MAIS ! qu'en est-il de "lo" tout seul ? commen faire que "le" produise "lo" dans les cas normaux ?

=> il faut évidemment un fallback dans la postgen (en dernière position je pense ?)
  plus il faut que ce fallback ait un paradigme lui permettant de matcher avec l'ensemble des marqueurs possibles..........


  <pardefs>
    <pardef n="detlo_marks">
      <e>
				<p>
					<l></l>
					<r></r>
				</p>
			</e>
      <e>
				<p>
					<l>_uu</l>
					<r></r>
				</p>
			</e>
      <!-- et tous les autres marqueurs qui peuvent suivre detlo... relou -->
    </pardef>
	</pardefs>

  <section>
    <e>
      <p>
        <l><a/>detlo</l>
        <r>lo</r>
      </p>
      <par n="detlo_marks" />
    </e>
  </section>


==========================================================

ATATATATATATA

dans le répo apertium-nno on a ça pour faire une regex :

  <pardefs>
    <pardef n="letter">
      <e><re>[abcdefghijklmnopqrstuvwxyzæøåcqwxzcqwxzéèêóòâôéêèóôòâáàáàääööššččðđðýýññüüííıi̇ëë]</re></e>
    </pardef>
  </pardefs>

est ce que ça pourrais pas être utile ici ????
au moins pour le paradigme "fallback"

=> mh ça marche pas de fou..........
  => a retenter avec un cerveau tout neuf