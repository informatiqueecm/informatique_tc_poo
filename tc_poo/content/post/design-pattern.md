+++
title = "1 - Design patterns"
weight = 5
+++


## Introduction aux design patterns



Séance consacrée aux [design pattern](https://fr.wikipedia.org/wiki/Patron_de_conception). Ces façons de faire, sorte d'algorithmie objet permet de résoudre nombre de problèmes courants en développement. Cela permet d'éviter de faires des [erreurs classiques](http://sahandsaba.com/nine-anti-patterns-every-programmer-should-be-aware-of-with-examples.html), aussi appelées [anti-pattern](https://fr.wikipedia.org/wiki/Antipattern).

Comme ce cours est dispensé en utilisant le langage python, on utilisera beaucoup le typage dynamique de celui-ci pour ces exemples (appelé [duck typing](http://sametmax.com/quest-ce-que-le-duck-typing-et-a-quoi-ca-sert/), mais les design pattern fonctionnent quelques soient le langage utilisé (ils ont d'ailleurs [initialement été écrit](https://fr.wikipedia.org/wiki/Design_Patterns) pour le C++).





## Les bases


On se rappelle comment bien commencer un projet, avec trois fichiers : 

* le code
* les tests
* le programme
    
Dans le doute rafraîchissez vous l'esprit avec le tuto sur [la programmation par les
tests](https://informatique.centrale-marseille.fr/tutos/post/python-tests.html).


On commence avec les 3 fichiers ci-après. Exécutez le main et les tests. Les 3 tests doivent passer. 


#### main.py

{{< highlight python >}}

from choice import Choice

d6 = Choice(list(range(1, 7)))

for etape in range(100):
    print(d6.roll().get_position())


{{< /highlight >}}


#### choice.py


{{< highlight python >}}

import random


class Choice:
    def __init__(self, choices):
        self.choices = choices
        self.position = choices[0]

    def get_position(self):
        return self.position

    def roll(self):
        self.position = self.choices[random.randint(0, len(self.choices) - 1)]
        return self

{{< /highlight >}}


Regardez le code de la méthode `roll` pourquoi rendre `self` ?

#### tests.py

{{< highlight python >}}

from choice import Choice


def test_init():
    d1 = Choice([1])
    assert d1.choices == [1]


def test_initial_position():
    d1 = Choice([1, 2])
    assert d1.get_position() == 1


def test_roll():
    d1 = Choice([1])
    d1.roll()
    assert d1.get_position() == 1

{{< /highlight >}}

## Première amélioration


Choice prend l'objet `choices` et le garde. Cela pose plusieurs problèmes. En particulier :

* on est obligé de passer des listes en paramètres, pas d'ensembles.
* si on modifie l'objet passé en paramètre, cela change le comportement de choice.
* on expose l'attribut `choices` par un attribut public.
    
Pour résoudre ces soucis on va copier l'objet `choices` et rendre l'attribut non modifiable. Pour cela, on va écrire des tests pour mettre en  lumière le problème puis corriger le code. Il faudra certainement changer d'autres tests dans le processus de réécriture du code.

On commence par écrire un petit test : 

{{< highlight python >}}

def test_sets():
    assert Choice({1}).roll().get_position() == 1
    
{{< /highlight >}}

Regardez-le planter. Puis proposez une solution. 

Cette solution devrait également corriger le second problème. Vérifiez-le en ajoutant le test : 

{{< highlight python >}}

def test_copy_choices():
    initial_list = ["a"]
    d1 = Choice(initial_list)
    initial_list[0] = "CHANGE"
    d1.roll()
    assert d1.get_position() == "a"
    
{{< /highlight >}}

Enfin, on peut tester la troisième objection avec le test ci-après. Remarquez que ce test cherche à produire une erreur : modifier un élément [non mutable](https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747).


{{< highlight python >}}

def test_no_modification():
    d1 = Choice([1])
    with pytest.raises(Exception):
        d1.choices[0] = 2
   
{{< /highlight >}}

 La façon dont pytest gère les erreurs est décrite [ici](https://docs.pytest.org/en/latest/assert.html#assertions-about-expected-exceptions) : le test précédent est vrai si le code plante et produit une erreur. essayer de comprendre la structure de ce test.


> Pour aller plus loin : on pourrait aussi empêcher le changement de l'attribut `choices`. Pour cela, on utilise les [properties](http://dauzon.com/lire-Python-Getters-et-setters-passez-votre-chemin-31) de python.


## pattern factory

### Premières expérimentations

Commencez par créer dans le fichier `main.py` un objet simulant 1 dé à six face (dont les faces valent 1, 2, 3, 4, 5 ou 6). On ne va pas faire de test pour cela, car nos tests sont censés couvrir ce genre d'usage. 

En revanche, on peut les probabilités d'apparitions de chaque face :

1. lancez N fois le dé (N = 1000) 
2. comparez les probabilités d'apparition de chaque face par rapport à la théorie (1/6)

Pour aller vite, on pourra utiliser la classe `Counter` du module `collections` de python (voir [la définition](https://docs.python.org/3.7/library/collections.html#collections.Counter), ou [des exemples](https://data-flair.training/blogs/python-counter/)). Le module [collections](https://docs.python.org/3.7/library/collections.html#module-collections) de python, c'est plein de bonnes choses. 

> N'hésitez pas à regarder les différents mmodules de la [bibliothèque standard](https://docs.python.org/3.7/library/index.html) de python avant de recoder la roue...


{{< note >}}

On prendra soin de créer une constante N, pour éviter l'anti-pattern "magic number"

{{< /note >}}

Refaite la même chose pour simuler un lancer de 2 dé à 6 faces (2d6 si on jargonne) avec un seul objet `Choice`.  


### On place le tout dans le fichier de la classe

Une fois que vous êtes satisfait de vos fonctions, ajoutez les dans le fichier `choices.py`, ce qui rajoutera des fonctions de créations d'objets.

Comme maintenant ce sont des méthodes de votre programme et non plus une utilisation de votre code, il faut ajouter des tests pour ces deux fonctions. Faites le. 

{{ < note >}}
Pour que vos tests ne soient pas trop fastidieux, vous pouvez vérifier que les possibilités correspondent aux comptes que vous avez effectué avec les objets Counter (du module collections).
{{ < /note >}}

> Pour aller plus loin : Utilisez les [méthodes de classe](https://realpython.com/instance-class-and-static-methods-demystified/#class-methods) pour votre factory. 



## Memento

### Adaptation des objets au pattern

Le pattern memento nécessite de pouvoir changer la valeur de nos objets. On va donc rajouter une méthode `set_position` à nos objets choice. 

On sait maintenant comment faire : 

1. faite un test vérifiant que `set_position` existe et fonctionne,
2. regardez le planter,
3. ajouter une méthode `set_position` à `Choice`,
4. regardez le test réussir.

Pour l'instant, on fera une méthode st_position la plus simple possible car elle ne sera utilisée que pour le memento. En particulier, on ne verifiera pas la validité de la valeur remise dans choice, ce n'est pas utile maintenant. Le coder serait du codage préventif et c'est [YAGNI](https://fr.wikipedia.org/wiki/YAGNI).

### Création d'un memento

Créez la classe `Memento` dans le fichier `memento.py` et ses tests dans le fichiers :`test_memento.py`.

La classe DiceMemento doit avoir :

- un objet comme paramètre du constructeur ayant les méthode `get_position` et `set_position`. Ici un Choice.
- une méthode `restore()` qui remet au dé sauvé de reprendre la valeur qu'il avait à la création du memento.



Vous pouvez par exemple transformer le code ci-après en test(s) :


{{< highlight python >}}
dice = Choice.dice()
dice.set_position(2)

memento = Memento(dice)
dice.set_position(6)

memento.restore()
print(dice.get_position())  # doit valoir 2
{{< /highlight >}}

### Undo list

Nous pouvons maintenant créer une classe `Undo` (dans le fichier `undo.py`) qui va nous permettre de sauver des dés (et leurs valeurs) et de les restaurer à la demande. Cette classe doit pouvoir :

- sauver un dé avec la méthode : `save(memento)` (un `ChoiceMemento` sera créé exemple comme ça : `ChoiceMemento(dice)`)
- restaurer la dernière valeur sauvée avec la méthode `restore()`
- connaître le nombre d'items sauvegardés avec une méthode `nb_undos()`

Bien sur vous créerez un fichier de tests `test_undo.py` qui testera les 3 fonctionnalités ci-dessus.

{{< highlight python >}}
dice = Choice.dice()

undo = Undo(dice)

dice.set_position(5)
print(dice.get_position()) # vaut 5
undo.save(DiceMemento(dice))
dice.roll() # dès que l'on change la valeur (ici possiblement différent de 5)
undo.save(DiceMemento(dice)) # on sauve dans un memento

undo.restore()
print(dice.get_position()) # vaut 5

{{< /highlight >}}


