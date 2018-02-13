+++
title = "2 - Composition et agrégation, corrigé"
weight = 2
+++


{{<note>}}
Le but de cette séance est de consolider les concepts fondamentaux de classe et d'objet et d'ajouter les notions de
composition, d'agrégation et d'attributs de classe. Vous (re)découvrirez aussi les tests unitaires.
{{</note>}}


## Éléments de cours
Il faut leur expliquer les liens qui peuvent exister entre différentes classes quand l'une d'elle utilise un objet d'une
autre classe en attribut et plus particulièrement :
 
 - agrégation : quand les objets utilisés sont créés en dehors,
 - composition : quand les objets utilisés sont créés dans le constructeur de la classe qui les utilise.
On expliquera l'idée, la réalisation et l'UML correspondant.


## TD

> [Les sources tex du sujet](/ressources/TD_2.tex)

> [version pdf du sujet (2 pages par feuille)](/ressources/TD_2_impression.pdf)

### Fonctions et namespaces

Voir le corrigé de la première séance pour les namespaces. Ici on montre que :
  
  - une méthode ou fonction est une variable comme une autre
  - l'ordre d'évaluation des namespace permet de créer des fonctions à partir de fonctions
  
#### Les fonctions sont des variables comme les autres

Pas de réelles difficultés, on associe juste la méthode append définie dans la class `list` et appliquée à l'objet `une_liste` à une variable nommée `ma_liste` du namespace global. 

Lorsque l'on utilise cette variable, c'est une fonction (elle est de type `<class 'builtin_function_or_method'>`) et elle ajoute l'argument à la liste de nom `ma_liste`.
	
#### Fonctions de fonction

La première fonction teste que le retour est bien une fonction et que 0 est un élément neutre.
La seconde essai sur un exemple différent de 0.

{{<highlight python>}}
def test_ajoute_0():
    ajoute_0 = ajoute(0)
    assert ajoute_0(0) == 0

def test_ajoute_different():
	assert ajoute(41)(1) == ajoute(1)(41) == 42
{{</highlight>}}

Question piège. Qu'est censé rendre `ajoute("truc")("bidule")` ? Si on est dans le mode de pensée python, on utilise la concaténation de chaînes de caractères et donc c'est censé rendre "trucbidule". Et c'est le cas avec la proposition de code suivant (attention, pour les chaînes de caractères, `+` n'est pas commutatif...): 


{{<highlight python>}}
def ajoute(valeur_a_ajouter):
	def ajoute(valeur):
		return valeur_a_ajouter + valeur
	return ajoute
{{</highlight>}}


Bien faire les namespaces et les noms pour comprendre comment tout ça marche pour de vrai (ici le `ajoute` local masque le `ajoute` du namespace qui possède le nom de la fonction `ajoute` du dessus).

### Des Dés

![greencarpet](/img/greenCarpet.png#center)

Ici on montre la composition, les dés appartiennent au jeu de dés, on les crée donc lors de l'initialisation de notre
jeu.

Le code est dans la correction du TP en dessous.

### Des cartes

On va montrer ici les attributs de classes avec les différentes couleurs de cartes et l'agrégation avec les decks qui
sont des tas de cartes qui existent déjà indépendamment.

![carddeck](/img/card_deck.png#center)

## TP

{{<highlight python>}}

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

    def roll(self, index=None):
        if index:
            self.dices[index].roll()
        else:
            for dice in self.dices:
                dice.roll()

    def get_dice_by_id(self, item):
        return self.dices[item]

    def __getitem__(self, item):
        return self.dices[item]

    def appears_more_than(self, nb_times):
        count = [0] * 7
        for dice in self.dices:
            count[dice.get_position()] += 1
        for i in range(len(count)):
            if count[i] >= nb_times:
                return True
        return False

    def is_pair(self):
        return self.appears_more_than(2)

    def is_three_of_a_kind(self):
        return self.appears_more_than(3)

    def is_square(self):
        return self.appears_more_than(4)

    def roll_until_number(self, number):
        while not self.appears_more_than(number):
            self.roll()

    def roll_until_pair(self):
        self.roll_until_number(2)

    def roll_until_three_of_a_kind(self):
        self.roll_until_number(3)

    def roll_until_square(self):
        self.roll_until_number(4)





{{</highlight>}}

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


def test_is_three_of_a_kind():
    carpet = GreenCarpet()
    assert carpet.is_three_of_a_kind()
    carpet.dices[0].set_position(4)
    assert carpet.is_three_of_a_kind()
    carpet.dices[1].set_position(4)
    carpet.dices[2].set_position(4)
    assert carpet.is_three_of_a_kind()
    carpet.dices[0].set_position(5)
    assert not carpet.is_three_of_a_kind()


def test_is_square():
    carpet = GreenCarpet()
    assert carpet.is_square()
    carpet.dices[0].set_position(4)
    assert carpet.is_square()
    carpet.dices[1].set_position(4)
    carpet.dices[2].set_position(4)
    carpet.dices[3].set_position(4)
    assert carpet.is_square()
    carpet.dices[0].set_position(5)
    assert not carpet.is_square()


def test_roll_until():
    carpet = GreenCarpet()
    carpet.roll_until_number(2)
    assert carpet.is_pair()



{{</highlight>}}

## PlantUML

@startuml


  class Dice {
  -_position
  -_probabilities
  + __init__(position=1, probabilities=None)
  +get_position()
  +set_position(position)
  +get_probabilities()
  +set_probabilities(proba)
  +roll()
}

class GreenCarpet {
 - _dices
    
 + __init__()
 + roll()
 + get_dice_by_id()
}

Dice --* GreenCarpet

@enduml

class Card { 
__class_attributes__
 - SPADES
 - HEARTS
 - CLUBS
 - DIAMONDS
    
__attributes__
 - value
 - color
    
__methods__
 +__init__(value, color)
}

class Deck {
    __attributes__
    - cards
    __methods__
    +__init__()
    +add(card)
    +show()
    +remove()
    
}

Card --o Deck



