+++
title = "3 - L'héritage"
weight = 3
+++


## Introduction

Cette séance est consacrée à la notion d'héritage et à son utilisation. Nous allons donc définir l'héritage et l'utiliser dans des cas simples. 

## Les bases

> [Le sujet de td](/ressources/td_3_impression.pdf)

## Les bases de l'héritage

Écrivez une classe `A` qui a :

 - un attribut `a`
 - une méthode `truc_que_fait_a()` qui affiche "Truc défini dans la classe mère"
 - une méthode `autre_truc()` qui affiche "Autre truc dans la classe mère"

Écrivez une classe `B` qui hérite de A et qui a :

 - un attribut `b`
 - une méthode `autre_truc()` qui affiche "C'est mon autre truc à moi"
 - une méthode `que_de_b()` qui affiche "Méthode seulement de la classe fille"

Faites bien attention à utiliser proprement le mot-clé `super` dans le constructeur de la classe fille.

Créez un objet `objet_a` de la classe `A` et un objet `objet_b` de la classe `B`. Essayez les lignes suivantes (une à la
fois) et prenez le temps de comprendre ce qu'elles font et pourquoi.

{{<highlight python>}}

print(objet_a.a)
print(objet_a.b)
print(objet_b.a)
print(objet_b.b)
objet_a.truc_que_fait_a()
objet_a.autre_truc()
objet_a.que_de_b()
objet_b.truc_que_fait_a()
objet_b.autre_truc()
objet_b.que_de_b()

{{</highlight>}}


## Le dé

Nous allons ici réutiliser la classe `Dice` entamée la dernière fois. Pour être sûr de repartir sur de bonnes bases, utilisez l'implémentation minimale suivante (dans un fichier `dice.py`) :

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
        self.set_position(numpy.random.choice([i for i in range(1, self.NUMBER_FACES + 1)], p=self.probabilities))

{{</highlight>}}

ainsi que les tests associés (dans un fichier `test_dice.py`):

{{<highlight python>}}

def test_dice_creation_no_argument():
    dice = Dice()
    assert dice.get_position() == 1
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


def test_dice_creation_initial_position():
    dice = Dice(position=5)
    assert dice.get_position() == 5
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


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

Créez un [environnement de tests](https://informatique.centrale-marseille.fr/tutos/post/python-tests.html#utilisation-de-l-environnement-de-test-avec-pycharm) et vérifiez que les tests passent.

## Un dé qui compte


Nous voulons créer une version particulière d'un dé : un dé permettant de conserver les statistiques de ses lancers. Vous ferez des tests unitaires pour chaque fonctionnalité de ce nouvel objet.

Implémentez la classe `StatDice` qui hérite de `Dice`, retient le nombre de fois que chaque valeur possible a été obtenue et permet de calculer les statistiques associées. Vous devez donc écrire :

 - la méthode `__init__` sans oublier d'appeler le constructeur de la classe mère,
 - une nouvelle méthode `set_position` qui utilise la méthode `set_position` du dé classique et met à jour les décomptes de lancers du dé et un test unitaire pour vérifier que les comptes sont mis à jour,
 - une méthode `stats` qui renvoie les fréquences d'apparition de chaque valeur et un test unitaire qui vérifie que le calcul est bien fait,
 - une méthode `mean` qui renvoie la moyenne des lancers du dé et un test unitaire.

On pourra utiliser les [dictionnaires](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) qui permettent d'associer une clé à une valeur pour faciliter certaines opérations.

Prenez le temps de tester votre code (vous pourrez entre autres faire beaucoup de lancers d'un dé et vérifier qu'il suit la loi de probabilité que vous lui avez donnée) et de bien comprendre quelle méthode est appelée et pourquoi quand vous utilisez un `StatDice`.




