+++
title = "2 - Composition et agrégation"
weight = 1
+++


## Introduction

Cette séance vise à consolider ce que vous avez appris la dernière fois sur les objets en général et d'ajouter à cela
les notions de :

 - composition,
 - agrégation,
 - attribut de classe.

## Les bases

> [Le sujet de td](/ressources/TD_2_impression.pdf)

## Test Driven Development

Tout ce que vous allez implémenter dans tous les TP à partir de celui-ci doit être vérifié par des tests unitaires. 

Suivez les étapes [la programmation par les
tests](https://informatique.centrale-marseille.fr/tutos/post/python-tests.html) pour découvrir ou vous remémorer le 
principe des tests unitaires. En particulier, vérifiez que vous avez bien retenu et compris :

 - à quoi sert le mot clé `assert`,
 - comment créer un environnement de tests dans PyCharm,
 - comment lancer les tests,
 - comment bien séparer les tests du code.


## Dice et tests unitaires

On vous propose l'implémentation minimale suivante de la classe `Dice` que vous avez du réaliser la dernière fois ainsi que des
tests unitaires associés. Configurez l'environnement de tests pour faire passer les tests unitaires. Prenez le temps de
regarder la structure des tests.

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

{{</highlight>}}

## GreenCarpet

Implémentez la classe `GreenCarpet` vue en TD en écrivant **au fur et à mesure** les tests unitaires nécessaires. On
utilisera la méthode du Test Driven Development qui se décompose en plusieurs étapes :
 
 - écrire un test qui teste une fonctionnalité qu'on veut implémenter,
 - vérifier qu'il échoue (car le code qu'il teste n'existe pas), afin de vérifier que le test est valide,
 - écrire juste le code correspondant au test,
 - vérifier que le test passe,
 - recommencer avec une autre fonctionnalité.

 Pour rappel, cette classe possède :
  
  - une liste de cinq objets `Dice` comme attribut. On jouera avec des dés non pipés.
  - une méthode `roll` qui lance les cinq dés ou un dé en particulier.
  - un moyen de connaître la position d'un dé donné.

Ajoutez une méthode permettant de relancer les dés tant qu'on n'obtient pas une paire, un brelan, etc.
    