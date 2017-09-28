+++
title = "2 - UI et programmation évènementielle"
weight = 2
+++


## Éléments de cours

<note>
Le but de cette séance est montrer la méthode de programmation évènementielle, utilisée lorque l'on construit une UI.
</note>

 - http://appjar.info
 - https://fr.wikipedia.org/wiki/Programmation_événementielle
 - http://sametmax.com/quest-de-que-mvc-et-a-quoi-ca-sert/ 


## TD

> [Les sources tex du sujet](/ressources/TD_2.tex)

> [version pdf du sujet (2 pages par feuille)](/ressources/TD_2_impression.pdf)

### Fonctions et namespaces

Voir le corrigé de la première séance pour les namespaces. Ici on montre que :
  
  - une méthode ou fonction est une variable comme une autre
  - l'ordre d'évaluation des namespace permet de créer des fonctions à partir de fonctions
  
#### Les fonctions sont des variables comme les autres

Pas de réelle difficultés, on associe juste la méthode append définie dans la class `list` et appliquée à l'objet `une_liste` à une variable nommée `ma_liste` du namespace global. 

Lorsque l'on utilise cette variable, c'est une fonction (elle est de type `<class 'builtin_function_or_method'>`) et elle ajoute l'argument à la liste de nom `ma_liste`.
	
#### Fonctions de fonction

La première fonction teste que le retour est bien une fonction et que 0 est un élément neutre.
La seconde essai sur un exemple différent de 0.

{{< highlight python >}}
def test_ajoute_0():
    ajoute_0 = ajoute(0)
    assert ajoute_0(0) == 0

def test_ajoute_different():
	assert ajoute(41)(1) == ajoute(1)(41) == 42
{{< /highlight >}}

Question piège. Qu'est censé rendre `ajoute("truc")("bidule")` ? Si on est dans le mode de pensée python, on utilise la concaténation de chaînes de caractères et donc c'est censé rendre "trucbidule". Et c'est le cas avec la proposition de code suivant (attention, pour les chaînes de caractères, `+` n'est pas commutatif...): 


{{< highlight python >}}
def ajoute(valeur_a_ajouter):
	def ajoute(valeur):
		return valeur_a_ajouter + valeur
	return ajoute
{{< /highlight >}}


Bien faire les namespaces et les noms pour comprendre comment tout ça marche pour de vrai (ici le `ajoute` local masque le `ajoute` du namespace qui possède le nom de la fonction `ajoute` du dessus).

### Programmation d'UI


{{<note>}}
Pour tout design d'UI on prendra soin de tout justifier. C'est la partie visible de votre code, il est donc indispensable que ce soit travaillé. Une mauvaise UI est  synonyme de mauvaise application.	
{{</note>}}

#### Design


![me2](/img/fenetre_1_bouton.png#center)

La fenêtre ci-dessus est un exemple possible. D'autres design sont bien sûr acceptables, mais on doit pouvoir tout justifier. Ici :

  - tout doit avoir un nom (boutons et fenêtre ici)
  - on a mis le bouton à droite pour que le rapport avec le texte soit clair 
  

On testerait en vérifiant que : 

  - initialement la valeur du champ texte vaut 1
  - en cliquant sur le bouton, la valeur augmente (on clique 2 fois pour être sur)
  
  
#### Modèle MVC


Le modèle [MVC](https://fr.wikipedia.org/wiki/Modèle-vue-contrôleur) n'est qu'une possiblité pour créer des interfaces de façon *propre* (c'est à dire facilement modifiable). Le principe directeur de toute méthode est de pouvoir retrouver facilement ses petits et donc de séparer au maximum les rôles de chaque partie. POur le modèle MVC on a 3 parties : l'affichage (Vue), les données (Modèle) et les liens entre les 2 (Contrôleur).


Tout ce qui est affiché dans une interface, ou tapé par un utilisateur est du texte (même ce qui est entier) : il ne faut pas utiliser le champ texte comme zone de stockage.

Parlez également du fait que l'on ne contrôle pas lorsque le bouton est appelé. Il faut créer une fonction/méthode qui sera appelée lorsque le bouton sera cliqué. On appelle cela de la programmation évènementielle : on ne fait que réagir aux actions de l'utilisateur ou du système.


![mvc](/img/MVC.png#center)

{{<highlight uml>}}
@startuml
@startuml

title Modèle MVC


class Vue {
  - str texte
  - bouton
  
  + str get_texte()
  + set_texte(str)
  + associe_action(fct)
}


class Modèle {
    - int valeur
    
    +int get_valeur()
    + set_valeur(int)
}
class Contrôleur {
  + ajoute_1()
}

Vue <|-up- Modèle: Mise à jour
Contrôleur  <|-up-|> Vue
Contrôleur  <|-up-|> Modèle

@enduml
{{</highlight>}}


Note sur les UML : 

  - on n'a pas mis de type pour le bouton. Le type en uml est facultatif.
  - on n'a pas mis de nom de variable pour les méthodes
  
  
Pour le test unitaire, il ne faut pas utiliser la vue. On vérifie donc uniquement que le controlleur fonctionne et modifie bien le modèle.


#### MVC et appjar.info

Le modèle MVC permet de créer des classes, mais dans l'application elles ne sont pas forcément nécessaires : 

  - pour le modèle, il n'y a que des attributs auxquels on accède. Le fait qu'ils soient privé n'est vraiment pas indspensable. Du coup, pour ne pas réinventer la roue, on utilise un dictionnaire pour cette classe. 
  - pour le contrôleur, il n'y a qu'une méthode. On le remplace par une unique fonction.
  
Le modèle UML n'est qu'une aide pour le découpage du code. Le suivre aveuglément ajoute de la complexité inutilement :
  
  - Vue : l'objet de nom `app` et de classe `gui`. 
  - Modèle : le dictionnaire de nom `model`
  - Contrôleur : la fonction `press`


La bibliothèque http://appjar.info/ est documentée et utile pour s'initier aux UIs.

Pour le code : 

  - la vue : on crée des objets graphiques de noms `"valeur"` (un label) et `"plus_un"` (un bouton). Les getter et setter sont accessibles via ces noms. On crée les objets (`addLabel` par exemple), puis on les manipule (`setLabel`). Les nombres à la fin de la création correspondent à ligne et colonne de la fenêtre.
  - on peut utiliser dans la fonction `press` des choses qui ne sont pas encore définies puisque le code ne sera lu que lorsque l'on pressera le bouton. A cette étape là tout sera défini.
  - l'action du contrôleur est passé à la vue en donnant la fonction utilisée pour le clic : lorsque l'on clique sur l'entité de la vue appelée "plus_un", on exécute la fonction `press` avec comme paramètre ce sur quoi on a cliqué (ie. le bouton). On n'a pas besoin de ce paramètre ici.
  - `app.go()` crée la fenêtre. Tout le code en-dessous n'est donc pas lu tant que la fenêtre est active. Il faut donc que tout le contrôleur soit défini avant cette ligne.
 

{{< note warning>}}
Dans le contrôleur, on utilise une structure du type `value = value + 1`. On crée donc un nouveau nom `value` dans le namespace concerné qui est égal à la valeur de l'ancien nom `value` + 1.

On ne peut donc pas utiliser uniquement comme Modèle une variable nommée `value` dans le namespace global car si on peut lire un nom (donc `value`) on ne peut créer de nom que dans son propre namespace, ici celui de la fonction (à moins de dire que `value` est de type `global` mais il est déconseillé de faire ce genre de choses. Les namespaces existent pour une raison et passer outre va _forcement_ finir en catastrophe).

Ici, on utilise le nom `model` du namespace global, mais on modifie une variable de son namespace à lui de nom `value`.
{{< /note>}}
   
#### Fenêtre 2 boutons

![me2](/img/fenetre_2_boutons.png#center)

Le bouton {{< menu_code >}}-1{{< /menu_code >}} a été mis à gauche car on lit de gauche à droite (-1, texte, +1 se comprend *intuitivement*)

En rendant le texte éditable, il faut interdire de taper autre chose que des entiers et copier la nouvelle valeur dans le modèle.

{{<note>}}
	En créant des interfaces qui interagissent avec un utilisateur, il faut toujours avoir à l'esprit la règle de Murphy qui stipule que s'il peut y avoir des erreurs, il y en aura. Il faut donc toujours faire en sorte que l'erreur ne soit pas possible ou tout du moins vérifier ce qui est écrit par un utilisateur. 
{{</note>}}	


{{<highlight python >}}
	from appJar import gui

	model = {
	    "value": 0
	}

	def press_plus1(button):
	    model["value"] += 1
	    app.setLabel("value", str(model["value"]))

	def press_moins1(button):
	    if model["value"] > 0:
	         model["value"] -= 1
         
	    app.setLabel("value", str(model["value"]))


	app = gui()
	app.addButton("-1", press_moins1, 0, 0)
	app.addLabel("value", str(model["value"]), 0, 1)
	app.addButton("+1", press_plus1, 0, 2)

	app.go()
	print("c'est fini.")
{{< /highlight >}}

On a mis le bouton {{< menu_code >}}-1{{< /menu_code >}} à gauche pour que l'on comprenne mieux la relation entre les différentes partie de l'UI.