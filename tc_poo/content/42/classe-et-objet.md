+++
title = "1 - Classes et objets, corrigé"
weight = 1
+++




## Éléments de cours


{{<note>}}
Le but de cette séance est de montrer les concepts fondamentale de classe et d'objet.
{{</note>}}

### Python général

  - le [tuto python](https://informatique.centrale-marseille.fr/tutos/post/python-bases.html) et le [tuto officiel](https://docs.python.org/3/tutorial/index.html). Ils sont sencé connaitre tout ça (vu en prépa).
  - la [PEP8](https://www.python.org/dev/peps/pep-0008)


### classes et objets

  - le [tuto python sur les classes](https://docs.python.org/3/tutorial/classes.html) sur les classes. On est là pour leur montrer tout ce qu'il y a dedans, à part (peut-être) la partie sur l'héritage et les itérateurs
  - [Sam & Max](http://sametmax.com/le-guide-ultime-et-definitif-sur-la-programmation-orientee-objet-en-python-a-lusage-des-debutants-qui-sont-rassures-par-les-textes-detailles-qui-prennent-le-temps-de-tout-expliquer-partie-1/
) : l'objet d'un point de vue python. Sans philosophie, suste vue comme un moyen d'écrire du code. J'aime. 
  

### Quelques concepts utiles et importants en python 

  - les [arguments par défaut] (https://docs.python.org/3/tutorial/controlflow.html#default-argument-values)
  - les [namespaces](https://docs.python.org/3/tutorial/classes.html#python-scopes-and-namespaces)
    - l'ordre d'évaluation détermine la [visibilité des variables](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html) : tout est toujours logique en python et se règle en sachant quel namespace est utilisé
    - `Class.attribute` masqué par `self.attributeù` si existe car le namespace de l'objet est vu avant celui de la classe.

  - attributs des objets/classes :

    - `__init__` pour le constructeur dans lequel on met les attributs sous la forme self.attribute
    - les attributs de classes s'écrivent comme des méthodes

  - méthodes spéciales et attributs spéciaux :
    - qui commencent par `_` : non public (c'est en fait une convention)
    - qui commencent par `__` : privé (non disponible pour les descendants)
    - qui commencent et finissent par `__` : méthodes spécifique de python qui ont un sens (par exemple __str__, __eq__), elles sont utilisés dans des cas précis et documentés.


## TD

> [Les sources tex du sujet](/ressources/TD_1.tex)

> [version pdf du sujet (2 pages par feuille)](/ressources/TD_1_impression.pdf)

### Un dé

Diagramme UML du dé classique :

![dice](/img/dice_init.png)

Les tests sont en dessous dans la correction du TP.

Diagramme UML du dé pipé :

![dice_proba](/img/dice_proba.png)

On ajoute juste un attribut pour la distribution de proba sous forme de liste de proba de chaque face.
Pour le `roll`, on peut utiliser le `numpy.random.choice(liste_de_valeurs, p=liste_de_probas)`.

Les namespaces possibles sont :

  - le namespace global
  - la classe, ici `Dice`. Contient tout ce qui est définit dans la classe comme les méthodes ou les constantes.
  - l'objet : ici un objet dice de la classe `Dice`. Contient tout ce qui est défini pour l'objet en particulier (par exemple dans le __init__ quand l'objet est appelé self)
  - les méthodes : ce qui n'existe que de l'exécution de la méthode à la fin de son exécution


Plusieurs namespaces peuvent cohabiter en même temps, pour connaître celui qui va être utilisé, python va du [local au global(http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html). S'il n'y a pas d'encapsulation (comme une fonction dans une fonction), cela donne :

  1. fonction (inclut les méthodes)
  2. objet
  3. classe
  5. global
  5. built-in (les mots du langage python comme `list` ou encore `range`)

S'il y a encapsulation, on suit la règle [LEGB](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html#3-legb---local-enclosed-global-built-in)

Pour les exemples :

  - `from dice import Dice` : cherche un fichier "dice.py" dans le répertoire courant. L'exécute avec son propre namespace/ Prend ensuite le nom `Dice` dans `dice.py` et l'ajoute au namespace global. On peut donc utiliser le nom `Dice` qui est définit dans le namespace de dice.py
  - `dice = Dice()` : `Dice` est dans le namespace global grâce à la ligne précédente. On crée l'objet en utilisant le `__init__` dans le namespace `Dice`, ce qui ajoute l'attribut `_position` dans le namespace de l'objet.
  - `print(dice._position)` : dans le namespace de l'objet
  - `dice.roll()` : python cherche le nom `roll`. Il regarde d'abord dans l'objet. Ça n'y est pas. Il regarde donc au dessus : dans le namespace de la classe qui définit le nom `roll` (une fonction). C'est cette fonction qui est utilisée. Comme pour toutes les fonctions définies dans une classe et utilisée par un objet, le premier paramètre (le self dans le namespace de la méthode) est l'objet. On peut donc ensuite l'utiliser dans le namespace de la méthode pour modifier un attribut dans le namespace de l'objet (ici le position de l'objet).
  - `print(dice.get_position())` : pareil qu'au dessus. `get_position` est défini dans la classe.
  - `print(dice.NUMBER_FACES)` : comme avant, python recherche d'abord dans l'objet, ça n'y est pas. Il regarde donc dans le namespace de la classe.


Et le GreenCarpet :

![green_carpet](/img/greenCarpet.png)

Le code est en dessous dans la correction du TP.


## TP
### Dice et GreenCarpet

{{<highlight python >}}
import numpy.random

class Dice:
  NUMBER_FACES = 6

  def __init__(self, position=1, probabilities=None):
    self.position = position
    if probabilities:
      self.probabilities = probabilities
    else:
      self.probabilities = [1 / self.NUMBER_FACES] * self.NUMBER_FACES

  def get_position(self):
    return self.position

  def set_position(self, new_position):
    self.position = new_position

  def roll(self):
    self.position = numpy.random.choice([i for i in range(1, self.NUMBER_FACES + 1)], p=self.probabilities)

  def __str__(self):
    return "Dice(position=" + str(self.position) + ", probabilities=" + str(self.probabilities)

  def __eq__(self, other):
    return self.position == other.position and self.probabilities == other.probabilities

  def __lt__(self, other):
    return self.position < other.position

  def __gt__(self, other):
    return self.position > other.position


class GreenCarpet:
  def __init__(self):
    self.dices = [Dice() for i in range(5)]

  def get_dices(self):
    return self.dices

  def roll(self):
    for dice in self.dices:
      dice.roll()

  def get_dice_by_id(self, item):
    return self.dices[item]

  def __getitem__(self, item):
    return self.dices[item]
{{</highlight>}}

### Tests

{{<highlight python>}}
from dice import *


def test_dice_creation_no_argument():
    dice = Dice()
    assert dice.get_position() == 1
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


def test_dice_creation_initial_position():
    dice = Dice(position=5)
    assert dice.get_position() == 5
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


def test_str():
    dice = Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0])
    assert str(dice) == "Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0]"


def test_set_position():
    dice = Dice()
    assert dice.get_position() == 1
    dice.set_position(3)
    assert dice.get_position() == 3


def test_roll_no_proba():
    dice = Dice()
    dice.roll()
    assert 1 <= dice.get_position() <= dice.NUMBER_FACES


def test_roll_proba():
    dice = Dice(probabilities=[1, 0, 0, 0, 0, 0])
    dice.roll()
    assert dice.get_position() == 1


def test_eq():
    dice = Dice(position=1)
    different_dice = Dice(position=2)
    equal_dice = Dice(position=1)
    assert dice != different_dice
    assert dice == equal_dice


def test_lt():
    small_dice = Dice(position=1)
    large_dice = Dice(position=2)
    assert small_dice < large_dice


def test_gt():
    small_dice = Dice(position=1)
    large_dice = Dice(position=2)
    assert large_dice > small_dice


def test_green_carpet_creation():
    carpet = GreenCarpet()
    assert len(carpet.dices) == 5


def test_green_carpet_roll():
    carpet = GreenCarpet()
    for dice in carpet.get_dices():
        assert 1 <= dice.position <= dice.NUMBER_FACES


def test_get_dice_by_id():
    carpet = GreenCarpet()
    second_dice = Dice()
    carpet.dices[1] = second_dice
    assert carpet.get_dice_by_id(1) == second_dice
{{</highlight>}}