+++
title = "1 - Classes et objets, corrigé"
weight = 1
+++


{{<note>}}
Le but de cette séance est de montrer les concepts fondamentaux de classe et d'objet.
{{</note>}}


## Éléments de cours

{{<note warning>}}
Les étudiants n'ont aucune notion de classe, d'objets ou d'UML. Il faudra donc tout leur expliquer.
{{</note>}}

Avant le début du TD, on peut faire un petit exposé sur les classes/objets. On pourra aborder le formalisme UML en créant le dé. Dire qu'en python, beaucoup de choses sont des [conventions](https://en.wikipedia.org/wiki/Convention_over_configuration) (variable privée, premier nom est self, ...) mais tout le monde s'y tient. 

Trois notions sont fondamentales et il faut y passer du temps :

- méthodes spéciales et attributs spéciaux :
    - qui commencent par `_` : non public (c'est en fait une convention)
    - qui commencent par `__` : privé (non disponible pour les descendants)
    - qui commencent et finissent par `__` : méthodes spécifique de python qui ont un sens (par exemple __str__, __eq__), elles sont utilisés dans des cas précis et documentés.


- l'ordre d'évaluation détermine la [visibilité des
  variables](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html) : tout est toujours logique en
  python et se règle en sachant quel namespace est utilisé. Les namespaces possibles sont rappelés plus bas dans la
  correction de l'exercice dédié.


- attributs des objets/classes :
    - `__init__` pour le constructeur dans lequel on met les attributs sous la forme self.attribute
    - namespace de Class et d'Objet (self.attribut != Class.attribut). D'abort objet puis classe (les méthodes s'y trouvent par exemple) :
        - les attributs de classes s'écrivent comme des méthodes (ils se placent, comme les méthodes, dans le namespace de la classe)
        - `Class.attribute` masqué par `self.attribute` si existe car le namespace de l'objet est vu avant celui de la classe).


## Ressources 

Divers tutos sur le net pour aborder les notions objet/python utilisées cette séance

### Python général

  - le [tuto python](https://informatique.centrale-marseille.fr/tutos/post/python-bases.html) et le [tuto officiel](https://docs.python.org/3/tutorial/index.html). Ils sont censés connaître tout ça (vu en prépa).
  - la [PEP8](https://www.python.org/dev/peps/pep-0008)


### Classes et objets

  - le [tuto python sur les classes](https://docs.python.org/3/tutorial/classes.html). On est là pour leur montrer tout ce qu'il y a dedans, à part (peut-être) la partie sur l'héritage et les itérateurs
  - [Sam & Max](http://sametmax.com/le-guide-ultime-et-definitif-sur-la-programmation-orientee-objet-en-python-a-lusage-des-debutants-qui-sont-rassures-par-les-textes-detailles-qui-prennent-le-temps-de-tout-expliquer-partie-1/) : l'objet d'un point de vue python. Sans philosophie, juste vue comme un moyen d'écrire du code. J'aime. 
  

### Quelques concepts utiles et importants en python 

  - les [arguments par défaut](https://docs.python.org/3/tutorial/controlflow.html#default-argument-values)
  - les [namespaces](https://docs.python.org/3/tutorial/classes.html#python-scopes-and-namespaces)


## TD

> [Les sources tex du sujet](/ressources/TD_1.tex)

> [version pdf du sujet (2 pages par feuille)](/ressources/TD_1_impression.pdf)


### Un dé

Commencez simple avec les éléments minimaux pour créer un dé. Le faire d'abord en UML, mettre des exemples d'utilisation du dé en python, puis mettre le code de la classe.

{{<note warning>}}
 bien expliciter `self`. 
{{< /note >}}


Diagramme UML du dé classique :

![dice](/img/dice_init.png#center)

Montrez bien comment on utilise la classe.

Diagramme UML du dé pipé :

![dice_proba](/img/dice_proba.png#center)

On ajoute juste un attribut pour la distribution de proba sous forme de liste de proba de chaque face.
Pour le `roll`, on peut utiliser le `numpy.random.choice(liste_de_valeurs, p=liste_de_probas)`.

Les namespaces possibles sont :

  - le namespace global
  - la classe, ici `Dice`. Contient tout ce qui est définit dans la classe comme les méthodes ou les constantes.
  - l'objet : ici un objet dice de la classe `Dice`. Contient tout ce qui est défini pour l'objet en particulier (par exemple dans le __init__ quand l'objet est appelé self)
  - les méthodes : ce qui n'existe que de l'exécution de la méthode à la fin de son exécution


Plusieurs namespaces peuvent cohabiter en même temps, pour connaître celui qui va être utilisé, python va du [local au global](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html). S'il n'y a pas d'inclusion (comme une fonction dans une fonction), cela donne :

  1. fonction (inclut les méthodes)
  2. objet
  3. classe
  5. global
  5. built-in (les mots du langage python comme `list` ou encore `range`)

S'il y a inclusion de namespaces, on suit la règle [LEGB](http://sebastianraschka.com/Articles/2014_python_scope_and_namespaces.html#3-legb---local-enclosed-global-built-in)

Pour les exemples :

  - `from dice import Dice` : cherche un fichier "dice.py" dans le répertoire courant. L'exécute avec son propre namespace/ Prend ensuite le nom `Dice` dans `dice.py` et l'ajoute au namespace global. On peut donc utiliser le nom `Dice` qui est définit dans le namespace de dice.py
  - `dice = Dice()` : `Dice` est dans le namespace global grâce à la ligne précédente. On crée l'objet en utilisant le `__init__` dans le namespace `Dice`, ce qui ajoute l'attribut `_position` dans le namespace de l'objet.
  - `print(dice._position)` : dans le namespace de l'objet
  - `dice.roll()` : python cherche le nom `roll`. Il regarde d'abord dans l'objet. Ça n'y est pas. Il regarde donc au dessus : dans le namespace de la classe qui définit le nom `roll` (une fonction). C'est cette fonction qui est utilisée. Comme pour toutes les fonctions définies dans une classe et utilisée par un objet, le premier paramètre (le self dans le namespace de la méthode) est l'objet. On peut donc ensuite l'utiliser dans le namespace de la méthode pour modifier un attribut dans le namespace de l'objet (ici le position de l'objet).
  - `print(dice.get_position())` : pareil qu'au dessus. `get_position` est défini dans la classe.



## TP


Les étudiant·e·s commencent par utiliser le module `random` puis on leur demande d'utiliser celui de `numpy` mais ils oublient de changer l'import (la fonction `choice` du module random de python ne permet pas de choisir les probabilités).


### Dice

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

{{</highlight>}}