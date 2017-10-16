+++
title = "2 - UI et programmation évènementielle"
weight = 2
+++


## Introduction

Cette séance est consacrée à la programmation évènementielle et son application à la création d'UI en utilisant le design pattern MVC.

## Les bases

> [Le sujet de td](/ressources/TD_2_impression.pdf)



## UI UI joue aux cartes

Vous allez utiliser ce que nous avons fait en TD pour créer une UI permettant de jouer aux cartes.

Les images des cartes sont placées dans le fichier zip [`resources.zip`](/ressources/resources.zip) dézippez le pour pouvoir les utiliser.




### La classe carte

Le code ci-après vous donne la classe carte et ses tests que vous allez utiliser par la suite. Copiez ce code dans deux fichiers `card.py` et `test_card.py`. Vérifiez que tous les tests passent en utilisant [py.test](https://informatique.centrale-marseille.fr/tutos/post/python-tests.html#utilisation-de-l-environnement-de-test-avec-pycharm)

#### card.py

{{<highlight python>}}
class Card:
    SPADES = "spade"
    HEARTS = "heart"
    CLUBS = "club"
    DIAMONDS = "diamond"

    def __init__(self, value, color):
        self._value = value
        self._color = color

    def __str__(self):
        return "Card(" + str(self._value) + ", " + str(self._color) + ")"

    def image(self):
        return "resources/Playing_card_" + self._color + "_" + str(self._value) + ".gif"

{{</highlight>}}  

#### test_card.py     

{{<highlight python>}}
from card import Card


def test_creation():
    motorhead = Card(1, Card.SPADES)
    assert motorhead._value == 1
    assert motorhead._color == Card.SPADES


def test_str():
    assert str(Card(1, Card.SPADES)) == 'Card(1, spade)'


def test_image():
    assert Card(11, Card.DIAMONDS).image() == "resources/Playing_card_diamond_11.gif"

{{</highlight>}}  


### Choix d'une carte

Le code suivant crée une carte selon ce qui est sélectionné dans les *option boxes*.
Exécutez le code et comprenez comment tout ceci fonctionne. Assurez vous que vous avez bien dézippé le fichier `resources.zip` pour que python trouve bien les différentes images.



##### main.py

{{<highlight python>}}
    from appJar import gui

    from card import Card


    app = gui()


    app.addLabelOptionBox("color", [Card.DIAMONDS, Card.SPADES, Card.CLUBS, Card.HEARTS], 0, 0)
    app.addLabelOptionBox("value", [str(i) for i in range(1, 14)], 0, 1)


    def on_click(button):
        color = app.getOptionBox("color")
        value = app.getOptionBox("value")
        app.setImage("card_image", Card(int(value), color).image())

    app.addButton("show card", on_click, 1, 0, 2)
    app.addImage("card_image", "resources/empty.gif", 2, 0, 2)

    app.go()
{{</highlight>}}

## Création d'un deck

Un deck est un tas de cartes. Nous allons créer petit à petit cette classe en lui ajoutant des fonctionnalités. Vous allez ici expérimenter votre première vraie expérience de [TDD](https://fr.wikipedia.org/wiki/Test_driven_development). 

### Préparation

On commence ainsi par ajouter une classe vide dans le fichier `card.py` :

#### card.py

{{<highlight python>}}
class Deck:
    pass
{{</highlight>}}
    
    
Et un test vérifiant que l'on peux créer un Deck dans `test_card.py`

#### test_card.py

{{<highlight python>}}
from card import Deck

def test_deck_creation():
    deck = Deck()
    assert deck is not None
{{</highlight>}}
    

Vérifiez que les tests passent.

### Cycle de développement

On va maintenant ajouter petit à petit les tests qui vont nous permettre d'ajouter des fonctionnalités à la classe. On va toujours suivre le même *pattern* :

1. ajouter un test et le voir planter en exécutant tous les tests
2. ajouter la fonctionnalité voulue à la classe
3. exécutez les tests et voir le tout fonctionner.


### Fonctionnalité 1 : ajout de cartes


Un deck est un conteneur. Il doit être vide à la création et on doit pouvoir lui ajouter des cartes. 

On commence par un test vérifiant que l'on a créé une méthode `__len__` permettant d'utiliser la commande `len()` de python : 

{{<highlight python>}}
def test_deck_empty_at_creation():
    deck = Deck()
    assert len(deck) == 0
{{</highlight >}}

Le test va rater puisque vous n'avez pas pas encore écrit le code correspondant. 


On ajoute un test qui vérifie que l'on peut ajouter une carte au deck : 

{{<highlight python>}}
def test_add_card():
    deck = Deck()
    deck.add(Card(1, Card.SPADES))
    assert len(deck) == 1
{{</highlight >}}

Puis on écrit le code correspondant (on pourra ajouter un attribut liste au deck. N'oubliez alors pas de l'initialiser dans le constructeur). On ne sait cependant pas si la bonne carte a été ajoutée. On va régler ce problème avec la fonctionnalité suivante.

### Ajout/suppression de la carte du dessus

Un deck doit être une LIFO. Pour être sur que ceci soit ok, on va ajouter une méthode permettant de rendre la carte du dessus 


On commence par les tests :

{{<highlight python>}}

def test_show():
    deck = Deck()
    deck.add(Card(1, Card.SPADES))
    card = Card(2, Card.SPADES)
    deck.add(card)
    assert deck.show() is card
{{</highlight >}}


Dans le test ci-dessus, on utilise le mot clé `is` qui signifie le même objet. Ainsi, même is deux objet sont similaires, `assert [] == []` rendra vrai (deux liste différente mais ayant le même contenu), elles ne sont pas les même `assert [] is []` rendra faux.



A vous de complèter le code. 


On peut enfin créer le test pour rendre la carte du dessus du deck.

On commence par tester le fait que l'on rend `None` si le deck est vide:


{{<highlight python>}}
def test_remove_from_empty_deck():
    deck = Deck()
    
    assert deck.remove() is None
{{</highlight >}}

Faites le code correspondant.


Puis que l'on récupère bien les cartes dans l'ordre  : 

{{<highlight python>}}
def test_remove():
    deck = Deck()
    deck.add(Card(1, Card.SPADES))
    card = Card(2, Card.SPADES)
    deck.add(card)
    
    assert deck.remove() is card
    assert len(deck) == 1
{{</highlight >}}


## UI


On vous demande d'utiliser l'UI précédente pour avoir :

- deux decks côte à côte initialement vides
- un bouton qui ajoute une carte (que l'on choisit grâce à deux menus déroulants) au premier deck
- un bouton permettant de passer une carte du premier au second deck. 

Une façon (élégante) de gérer l'affichage des cartes pourra être de coder une méthode `image` à deck


Vous pouvez une fois ceci réalisé ajouter deux chaînes de caractères permettant de savoir le nombre de cartes dans chaque deck.    



## Comparaisons de cartes

En utilisant les méthodes de comparaison vues à [la toute fin du TP1]({{< ref "post/classe-et-objet.md#pour-aller-plus-loin" >}}), codez les méthodes permettant de faire passer les tests suivants : 

{{<highlight python>}}
def test_card_equality():
    assert Card(1, Card.SPADES) == Card(1, Card.SPADES)
    assert Card(1, Card.SPADES) != Card(1, Card.DIAMONDS)

    assert Card(1, Card.SPADES) != Card(13, Card.DIAMONDS)


def test_card_lesser_than():
    assert not Card(3, Card.SPADES) < Card(3, Card.SPADES)
    assert Card(2, Card.SPADES) < Card(3, Card.SPADES)


def test_card_larger_than():
    assert not Card(3, Card.SPADES) > Card(3, Card.SPADES)
    assert Card(3, Card.SPADES) > Card(2, Card.SPADES)
    
{{</highlight >}}    


On pourra ensuite ajouter entre les 2 decks un champ permettant de savoir si la carte du deck de gauche est égale, inférieure ou supérieure à la carte du deck de droite.