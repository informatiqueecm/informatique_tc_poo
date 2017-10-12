+++
title = "3 - L'héritage"
weight = 3
+++


## Introduction

Cette séance est consacrée à la notion d'héritage et à son utilisation. Nous allons donc définir l'héritage et l'utiliser dans un cas simple. Nous allons aussi aborder le problème des entrées utilisateur lors du TP.

## Les bases

> [Le sujet de td](/ressources/td_3.pdf)

## Interface d'un dé

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

On propose le code suivant pour une interface très minimale représentant un dé et un bouton qui pour l'instant ne fait rien :

{{<highlight python>}}

from appJar import gui
from dice import Dice

app = gui()
app.setGeometry(200, 200)

def press_roll(button):
    pass

dice = Dice()
app.addLabel("position", "Position : ", 0, 0)
app.addLabel("dice", str(dice.get_position()), 0, 1)
app.addButton("Roll", press_roll, 0, 2)

app.go()

{{</highlight>}}

Modifiez la fonction `press_roll` (qui ne fait rien pour l'instant) pour qu'elle lance le dé et mette à jour le label contenant la position du dé. Vous utiliserez la méthode `set_label(id_label_a_modifier, txt_a_mettre)` de la classe gui (http://appjar.info/pythonWidgets/#set-labels).


## Un dé qui compte


Nous voulons créer une version particulière d'un dé : un dé permettant de conserver les statistiques de ses lancers. Vous ferez des tests unitaires pour chaque fonctionnalité de ce nouvel objet.

Implémentez la classe `StatDice` qui hérite de `Dice`, retient le nombre de fois que chaque valeur possible a été obtenue et permet de calculer les statistiques associées. Vous devez donc écrire :

 - la méthode `__init__` sans oublier d'appeler le constructeur de la classe mère et un test unitaire qui vérifie que le nouvel attribut est bien initialisé,
 - une nouvelle méthode `set_position` qui utilise la méthode `set_position` du dé classique et met à jour les décomptes de lancers du dé et un test unitaire pour vérifer que les comptes sont mis à jour,
 - une méthode `stats` qui renvoie les fréquences d'apparition de chaque valeur et un test unitaire qui vérifie que le calcul est bien fait,
 - une méthode `mean` qui renvoie la moyenne des lancers du dé.

On pourra utiliser les [dictionnaires](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) qui permettent d'associer une clé à une valeur pour faciliter certaines opérations.

Prenez le temps de tester votre code (vous pourrez entre autres faire beaucoup de lancers d'un dé et vérifier qu'il suit la loi de probabilité que vous lui avez donnée) et de bien comprendre quelle méthode est appelée et pourquoi quand vous utilisez un `StatDice`.

## Afficher les statistiques de lancer de dé

Nous allons maintenant modifier notre interface graphique pour lancer un `StatDice` et afficher la moyenne des lancers effectués jusqu'à maintenant avec le dé.

Modifiez le code de l'interface précédente pour afficher en plus la moyenne des lancers effectués avec le dé. Le bouton "Roll" doit donc maintenant en plus mettre à jour la moyenne.

Ajoutez un bouton "Roll 10 times" pour lancer le dé 10 fois d'un coup et un autre "Roll 100 times" pour le lancer 100 fois.

## Récupérer des entrées utilisateurs

Nous avons déjà vu comment réagir à certaines actions de l'utilisateur avec la gestion des boutons. Une autre interaction courante avec l'utilisateur est la récupération d'entrées effectuées dans un champ texte. Nous allons maintenant permettre à l'utilisateur de taper dans un champ texte le nombre de fois qu'il veut lancer le dé.

Ajoutez le code suivant dans votre interface. Veillez à remplacer le `x` par une coordonnée qui ne casse pas l'interface que vous avez déjà faite.

{{<highlight python>}}

def get_integer(button):
    print(app.getEntry("user_entry"))

app.addLabel("user_int", "Custom rolls (int) : ", x, 0)
app.addEntry("user_entry", x, 1)
app.addButton("OK", get_integer, x, 3)

{{</highlight>}}

Que fait le bouton OK ? Que se passe-t-il si vous désobéissez et entrez dans le champ texte quelque chose qui n'est pas un entier ?

En pratique, on ne peut pas se contenter d'espérer que l'utilisateur suivra les consignes données plus ou moins explicitement. Il faudra toujours vérifier que ce que l'utilisateur tape dans le champ de texte correspond à ce dont on a besoin. Tout ce que tape l'utilisateur dans un champ de texte est (comme le nom du champ l'indique) un texte c'est-à-dire une chaîne de caractères. La fonction `int()` permet de transformer une chaîne de caractères en un entier si cela est possible. Sinon elle renvoie une erreur. Utilisez cette fonction pour transformer l'entrée de l'utilisateur en un entier avant de l'afficher.

Si l'utilisateur entre n'importe quoi, vous devriez voir une erreur dans la console. On appelle les erreurs des [exceptions](https://docs.python.org/2/library/exceptions.html). Il existe un certain nombre de classes d'exceptions prédéfinies comme `ValueError`, `ZeroDivisionError` ou encore `TypeError` que vous avez sûrement déjà rencontrées. Elles héritent toutes de la classe `Exception`. En programmation, on peut "attraper" les erreurs c'est à dire essayer de faire quelque chose et continuer l'exécution en sachant quoi faire même si une exception a été levée. On utilise pour ça la syntaxe suivante :

{{<highlight python>}}

try:
    fonction qui peut renvoyer une erreur
except ClasseDeLErreurAAttraper:
    comportement si une erreur se produit

{{</highlight>}}

Si la ligne du bloc `try` fonctionne sans erreur de la classe mentionnée, on continue le programme normalement, sinon, on applique le comportement indiqué dans le bloc `except` puis on continue le programme normalement.

Si on met `except Exception`, on exécutera le code du `except` quelle que soit l'exception levée. En effet, comme dit précédemment, toutes les exceptions héritent de la classe `Exception`. Par contre, si on met `except ValueError`, seules les exceptions de la classe `ValueError` ou des éventuelles classes filles de cette classe déclencheront le `except`.

Avec la fonction `int` appliquée à une chaîne de caractères qu'on ne peut pas transformer en entier, l'exception qui est levée est une `ValueError`. Utilisez le `try`, `except` pour modifier la fonction `get_integer` pour qu'un message que vous écrirez vous-même s'affiche dans la console si on n'entre pas un entier.

C'est mieux mais l'utilisateur n'a toujours pas d'informations sur son erreur directement sur l'interface. Utilisez une [error box](http://appjar.info/pythonDialogs/#message-boxes) dans la fonction `get_integer` pour afficher un pop-up d'erreur à l'utilisateur si son entrée ne correspond pas à ce qu'on attend.

## Améliorer son interface

Ajoutez un bouton permettant de recommencer avec un nouveau dé.

Ajoutez des champs permettant à l'utilisateur de choisir les pourcentages d'apparition de chaque face. Pensez à vérifier que l'utilisateur a bien entré des pourcentages qui somment à 100.

Ajoutez l'affichage du nombre d'apparitions de chaque face en plus de la moyenne.



