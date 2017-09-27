+++
title = "1 - Classes et objets"
weight = 1
+++


## Introduction

Dans cette séance nous allons voir les notions de :

  - classe
  - objet
  - attribut
  - méthode  
  - attribut de classe


## Les bases

> [Le sujet](/ressources/TD_1_impression.pdf)


## Test Driven Development

Tout ce que vous allez implémenter dans tous les TP doit être testé par des tests unitaires. On vous demande donc d'aller sur le [site des tutos](https://informatique.centrale-marseille.fr/tutos) pour :

  - se rappeler les [bases de python](https://informatique.centrale-marseille.fr/tutos/post/python-bases.html)
  - utiliser [Pycharm](https://informatique.centrale-marseille.fr/tutos/post/utilisation-pycharm-bases.html)
  - faire de la [programmation par les tests](https://informatique.centrale-marseille.fr/tutos/post/python-tests.html)


## Dice
### Un dé tout simple

Implémentez la première version de la classe `Dice` vue dans le TD1 dans un fichier nommé `dice.py`. Elle doit être capable de :

  - Créer un objet sans paramètre
  - Créer un objet avec une position initiale
  - Connaître et changer la position du dé
  - Lancer le dé
  - Avoir un nombre maximum de faces (identique pour tous les dés, constant)

Pour vérifier que votre code fait ce qu'on lui demande, vous devez créer un fichier de tests `test_dice.py` et configurer un environnement de tests. Votre classe devra passer les tests unitaires suivants que vous ajouterez et ferez passer un par un en ajoutant une fonctionnalité à la fois :

{{<highlight python>}}
def test_dice_creation_no_argument():
    d = Dice()
    assert d.get_position() == 1


def test_dice_creation_initial_position():
    d = Dice(position=5)
    assert d.get_position() == 5


def test_set_position():
    d = Dice()
    assert d.get_position() == 1
    d.set_position(3)
    assert d.get_position() == 3


def test_roll:
    d = Dice()
    d.roll()
    assert 1 <= d.get_position() <= d.NUMBER_FACES
{{</highlight >}}

### Un dé pipé

Nous allons maintenant améliorer notre dé avec la deuxième version de la classe vue en TD : le dé pipé. Il faudra donc ajouter un nouvel attribut permettant de renseigner les probabilités de chaque face.

Ajoutez cet attribut et modifiez les tests impactés par ce changement :

  - `test_dice_creation_no_argument()` pour vérifier la valeur par défaut du nouvel attribut si aucun argument n'est donné au constructeur
  - `test_dice_creation_initial_position()` vérifier la valeur par défaut du nouvel attribut si seule la position initiale est donnée au constructeur

Modifiez la méthode `roll` pour prendre en compte les probabilités. Pour lancer le dé au hasard en respectant les probabilités voulues, vous pouvez utiliser `numpy.random.choice`. Regardez la [doc](https://docs.scipy.org/doc/numpy-dev/reference/generated/numpy.random.choice.html) pour savoir comment l'utiliser et avec quels paramètres.

{{<note>}}
Pour utiliser numpy, vous devez l'importer en haut de votre fichier.
{{</note>}}

Ajoutez les tests unitaires relatifs aux nouvelles fonctionnalités:

  - `test_dice_creation_initial_probabilities` qui teste la création d'un nouvel objet avec une distribution de probabilités particulière
  - `test_set_probabilities` qui teste la modification du nouvel attribut
  - `test_roll_proba` qui teste la prise en compte du nouvel attribut de probabilité d'apparition de chaque face


## GreenCarpet

Implémentez la classe `GreenCarpet` vue en TD qui possède :

  - une liste de cinq objets `Dice` comme attribut. On jouera avec des dés non pipés.
  - une méthode `roll` qui lance les cinq dés ou un dé en particulier.
  - un moyen de connaître la position d'un dé donné.

Cette fois, vous déterminerez et écrirez vous-même les tests unitaires qui vérifient toutes ces fonctionnalités.


## Pour aller plus loin

Python dispose de méthodes spéciales qui peuvent être invoquées en utilisant une syntaxe particulière (par exemple les opérations arithmétiques `+, -, *, \` entre objets que nous avons créés nous-mêmes). Vous en trouverez une liste exhaustive sur la [doc officielle](https://docs.python.org/3/reference/datamodel.html#special-method-names). Nous allons en utiliser ici quelques-unes sur nos deux classes. Ces méthodes se présentent toujours sous la forme `__nom_de_la_methode__`.

### Dice
Essayez de taper :

{{<highlight python>}}
d = Dice()
print(d)
{{</highlight>}}

Vous devriez obtenir quelque chose comme :

{{<highlight python>}}
<dice.Dice object at 0x7f40e518a668>
{{</highlight>}}

Le `print` appelle la méthode `__str__` de notre classe. Nous ne l'avons pas définie, c'est donc la méthode par défaut de tous les objets python qui est appelée. Comme vous le contastez, elle n'est pas très intéressante pour nous. Il faut donc la définir dans notre classe.

Définissez une méthode permettant d'écrire le dé comme une chaîne de caractères sous la forme `"Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0])"`.

Cet méthode devra passer le test suivant :

{{<highlight python>}}
def test_str():
    d = Dice(probabilities=[1, 0, 0, 0, 0, 0])
    assert str(d) == "Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0])"
{{</highlight>}}

On voudrait également pouvoir vérifier si un dé a fait un meilleur lancer qu'un autre en faisant tout simplement :

{{<highlight python>}}
d1 = Dice(1)
d2 = Dice(6)
d1 < d2
{{</highlight>}}

Si vous testez ça avec votre code tel qu'il est, vous obtiendrez :

{{<highlight python>}}
TypeError: '<' not supported between instances of 'Dice' and 'Dice'
{{</highlight>}}

Python vous explique qu'il ne connaît pas cette opérateur pour les objets de notre classe. Pour pouvoir utiliser directement les opérateurs `<` et `>`, il faut définir respectivement les méthodes `__lt__(self, other)` et `__gt__(self, other)`. On pourra aussi ajouter `__eq__` pour tester l'égalité.

Ajoutez les méthodes `__lt__`, `__gt__` et `__eq__` et testez les.

### GreenCarpet

Nos objets `GreenCarpet` représentent plusieurs dés et pour l'instant, pour accéder à un dé en particulier, par exemple le premier, nous devons taper : 

{{<highlight python>}}
carpet = GreenCarpet()
first_dice = carpet.get_dices[0]
{{</highlight>}}
(ou quelque chose d'équivalent selon le nom que vous avez choisi pour l'attribut de la classe `GreenCarpet`).

Il serait pratique de pouvoir se contenter de `carpet[0]`. Cela est possible en implémentant la méthode `__getitem__(self, key)`.

Implémentez la et testez la.
