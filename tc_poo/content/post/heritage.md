+++
title = "3 - L'héritage"
weight = 3
+++


## Introduction

Cette séance est consacrée à la notion d'héritage et à son utilisation. Nous allons donc définir l'héritage et l'utiliser dans un cas simple. Nous allons aussi aborder le problème des entrées utilisateur lors du TP.

## Les bases

> [Le sujet de td](/ressources/td_3.pdf)

## Une fenêtre personnalisée

Comme nous l'avons vu en TD, l'héritage est souvent utile pour adapter à notre usage une classe complexe et existant dans une librairie. Cela permet de ne pas réécrire soi-même tout le code de cet objet parfois compliqué mais aussi de ne pas devoir appliquer chaque fois les mêmes changements à des instances différentes.

Créez une classe `CustomWindow` héritant de la classe `gui` de `appjar` avec certaines caractéristiques que vous choisirez (vous trouverez toutes les modifications disponibles dans la [doc appJar](http://appjar.info/pythonGuiOptions/)), par exemple :

 - la taille
 - la position sur l'écran
 - la couleur
 - la police.

 Créez deux fenêtres différentes de la classe `CustomWindow` ainsi qu'une fenêtre de la classe `gui` dans laquelle vous mettrez ce que vous voulez (quelques labels par exemple) et constatez la différence entre vos fenêtres et une fenêtre normale. On rappelle que si on a un objet `app = gui()`, on peut ajouter du texte dans la fenêtre en faisant `app.addLabel("nom_du_label", "texte à ajouter", coordonnées x, coordonnées y)`.


## Récupérer des entrées utilisateurs

Nous avons déjà vu comment réagir à certaines actions de l'utilisateur à travers la programmation événementielle et la gestion des boutons. Une autre interaction courante avec l'utilisateur est la récupération d'entrées effectuées dans un champ texte.

Le code suivant est une implémentation basique de la récupération d'une entrée dans un champ texte :

{{<highlight python>}}

from appJar import gui

app = gui("TP3")


def get_integer(button):
    print(app.getEntry("user_entry"))

app.addLabel("user_int", "Entrez un entier:", 0, 0)
app.addEntry("user_entry", 0, 1)
app.addButton("OK", get_integer, 0, 3)

app.go()

{{</highlight>}}

Que fait ce code ? Que se passe-t-il si vous désobéissez et entrez dans le champ texte quelque chose qui n'est pas un entier ?

En pratique, on ne peut pas se contenter d'espérer que l'utilisateur suivra les consignes données plus ou moins explicitement. Il faudra toujours vérifier que ce que l'utilisateur tape dans le champ de texte correspond à ce dont on a besoin. Tout ce que tape l'utilisateur dans un champ de texte est (comme le nom du champ l'indique) un texte c'est-à-dire une chaîne de caractères. La fonction `int()` permet de transformer une chaîne de caractères en un entier si cela est possible. Sinon elle renvoie une erreur. Utilisez cette fonction pour transformer l'entrée de l'utilisateur en un entier avant de l'afficher.

Si l'utilisateur entre n'importe quoi, vous devriez voir une erreur dans la console. On appelle les erreurs des [exceptions](https://docs.python.org/2/library/exceptions.html). Il existe un certain nombre de classes d'exceptions prédéfinies comme `ValueError`, `ZeroDivisionError` ou encore `TypeError` que vous avez sûrement déjà rencontrées. En programmation, on peut "attraper" les erreurs c'est à dire essayer de faire quelque chose et continuer l'exécution en sachant quoi faire même si une exception a été levée. On utilise pour ça la syntaxe suivante :

{{<highlight python>}}

try:
	fonction qui peut renvoyer une erreur
except ClasseDeLErreurAAttraper:
	comportement si une erreur se produit

{{</highlight>}}

Si la ligne du bloc `try` fonctionne sans erreur de la classe mentionnée, on continue le programme normalement, sinon, on applique le comportement indiqué dans le bloc `except`.

Avec la fonction `int` appliquée à une chaîne de caractères qu'on ne peut pas transformer en entier, l'exception qui est levée est une `ValueError`. Utilisez le `try`, `except` pour modifier la fonction `get_integer` pour qu'un message que vous écrirez vous-même s'affiche dans la console si on n'entre pas un entier.

C'est mieux mais l'utilisateur n'a toujours pas d'informations sur son erreur directement sur l'interface. Utilisez une [error box](http://appjar.info/pythonDialogs/#message-boxes) dans la fonction `get_integer` pour afficher un pop-up d'erreur à l'utilisateur si son entrée ne correspond pas à ce qu'on attend.


## Un dé qui compte

Nous allons ici réutiliser la classe `Dice` entamée la dernière fois. Si vous n'avez pas votre classe `Dice` ou si vous voulez être sûr de partir sur de bonnes bases, vous pouvez utiliser l'implémentation minimale suivante :

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

ainsi que les tests associés :

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


Nous voulons créer une version particulière d'un dé : un dé permettant de conserver les statistiques de ses lancers. Vous ferez des tests unitaires pour chaque fonctionnalité de ce nouvel objet.

Implémentez la classe `StatDice` qui hérite de `Dice`, retient le nombre de fois que chaque valeur possible a été obtenue et permet de calculer les statistiques associées. Vous devez donc écrire :

 - la méthode `__init__` sans oublier d'appeler le constructeur de la classe mère et un test unitaire qui vérifie que le nouvel attribut est bien initialisé,
 - une nouvelle méthode `set_position` qui utilise la méthode `set_position` du dé classique et met à jour les décomptes de lancers du dé et un test unitaire pour vérifer que les comptes sont mis à jour,
 - une méthode `stats` qui renvoie les fréquences d'apparition de chaque valeur et un test unitaire qui vérifie que le calcul est bien fait.

On pourra utiliser les [dictionnaires](https://docs.python.org/2/tutorial/datastructures.html#dictionaries) qui permettent d'associer une clé à une valeur pour faciliter certaines opérations.

Prenez le temps de tester votre code (vous pourrez entre autres faire beaucoup de lancers d'un dé et vérifier qu'il suit la loi de probabilité que vous lui avez donnée) et de bien comprendre quelle méthode est appelée et pourquoi quand vous utilisez un `StatDice`.

## Afficher les statistiques de lancer de dé

Nous allons maintenant créer une petite interface graphique permettant à un utilisateur de :

 - renseigner les pourcentages d'apparition de chaque face d'un dé à six faces
 - lancer 'x' fois un dé respectant ces pourcentages
 - voir s'afficher les statistiques des lancers.

Commencez par dessiner sur une feuille l'interface que vous voulez réaliser pour répondre à ce problème.

Réalisez l'interface en plaçant les différents champs de texte, label et boutons. Vous pouvez utiliser votre propre classe de fenêtres réalisée au début de ce TP.

Écrivez la fonction liées au bouton et tout ce qui manque en utilisant votre classe `StatDice` pour que votre programme fasse ce qu'on lui demande ! Vous prendez bien soin de vérifier les entrées de l'utilisateur pour ne pas avoir de dé dont les pourcentages d'apparition des faces somment à plus de 100% par exemple

Vous pouvez utiliser les [validation entries](http://appjar.info/pythonWidgets/#label) de `appJar` pour indiquer quel champ est mal renseigné.


