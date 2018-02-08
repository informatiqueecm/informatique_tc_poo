+++
title = "3 - L'héritage"
weight = 3
+++


## Éléments de cours

Les étudiants n'ont jamais entendu parler d'héritage, il faut donc leur introduire la notion en général et la [syntaxe python](https://docs.python.org/2/tutorial/classes.html#inheritance). 

On leur parlera aussi de:

 - la classe `object`
 - le mot clé `super` (https://docs.python.org/2/library/functions.html#super) et le fait qu'il faut faire un appel au constructeur de la classe parente dans le constructeur de la classe fille
 - pourquoi pas de l'héritage multiple possible en python mais sans détailler ainsi que du [MRO](https://www.python.org/download/releases/2.3/mro/)


## TD

### Exercice 1
L'idée est juste de présenter avec quelque chose de simple et facile à se représenter la notion d'héritage. On donnera l'UML des classes dans tous les cas et le code seulement s'il est lié à l'héritage.

Classe `Point` :

![point](/img/point.png)

Rien de particulier, on ne donne que l'UML.

Classe `Polygon`:

![polygon](/img/polygon.png)

On (ré)explique ici le lien d'agrégation dont on a déjà un peu pu parler lors du TD2. `Polygon` utilise des objets `Point` comme attribut mais ces points existent indépendamment du polygone et on les ajoute dans l'attribut `vertices` lors de la création de l'objet.

Classe `Triangle`:

![triangle](/img/triangle.png)

L'héritage arrive ici. On fait une version restreinte du polygone très simple. Cela permet de bien expliquer qu'on appelle le constructeur de `Polygon` dans le constructeur de `Triangle`.

Ici, on donnera le code pour clarifier l'héritage et expliquer la syntaxe python :

{{<highlight python>}}

class Triangle(Polygon):
  def __init__(self, point1, point2, point3):
    super().__init__([point1, point2, point3])

{{</highlight>}}

On expliquera donc particulièrement l'usage du `super()` pour l'appel au constructeur de la classe mère mais on expliquera aussi que `super()` permet d'avoir accès à tout ce qu'on veut dans la classe mère, comme le montrera l'exercice suivant.

L'UML complet donne donc :

![polygone_uml_entier](/img/polygone_uml_entier.png)

### Exercice 2

La classe `Personnage` ne pose normalement pas de problèmes :

![personnage](/img/personnage.png)

On pourra préciser le code de `taper` et `se_faire_taper` qui permettra à chacun de se faire taper comme il l'entend.

{{<highlight python>}}

def taper(self, personnage):
    personnage.se_faire_taper(self)

def se_faire_taper(self, personnage):
    self.set_vie(self.get_vie - personnage.attaque)

{{</highlight>}}


On ajoute la guerrière :

![guerriere](/img/guerriere.png)

On donnera ici une partie code (pas la peine d'écrire la méthode `bloque` qui fait le tirage pour savoir si on bloque ou pas), surtout pour bien :

  - insister sur le `super().__init__()` au début du constructeur de la classe fille,
  - montrer qu'on ajoute un attribut à la guerrière par rapport au personnage normal,
  - expliquer la méthode `se_faire_taper(personnage)` dans laquelle on utilisera la méthode `se_faire_taper` de la classe `Personnage` seulement si la guerrière ne bloque pas le coup. On montre donc bien le `super().methode_de_la_mere()` qui permet d'accéder à la méthode de la classe mère même si on a écrit une méthode du même nom dans la classe fille.


{{<highlight python>}}

class Guerriere(Personnage):
    def __init__(self, vie, attaque, blocage):
        super().__init__(vie, attaque)
        self.blocage = blocage

    def se_faire_taper(self, personnage):
        if not self.bloque(personnage):
            super().se_faire_taper(personnage)

{{</highlight>}}

On prendra le temps de faire des exemples d'utilisation et de montrer ce qu'on peut faire et pourquoi on peut le faire (qu'est-ce qui est appelé quand on fait `guerriere.se_faire_taper(bonhomme)` avec un objet `guerriere` de la classe `Guerriere` par exemple)

Le magicien permet de montrer l'ajout d'une méthode qui n'était pas du tout dans la classe mère.

![magicien](/img/magicien.png)

On peut mettre le code s'ils en ont besoin mais a priori rien de très difficile s'ils ont compris ce qui se passe avant.


On mentionnera aussi le fait que l'héritage est utile quand on veut utiliser des classes définies dans un module quelconque et la mettre un peu à notre sauce.

### Exercice 3

Cet exercice est là uniquement si il reste du temps et que tout le reste est bien compris.

On a l'arbre suivant :

![arbre_arithmetique](/img/node_arithmetic.png)

On explique ensuite le composite. On aura donc une classe `DiceComponent` qui est le composant, elle représente l'abstraction d'opérations de dés. Les deux classes `SumDice` et `MultDice` sont les composites, elles représentent les deux opérations élémentaires liées aux dés. Enfin la classe `Dice` est la brique de base, la feuille. 

Je propose d'expliquer et détailler d'abord le `MultDice` comme s'il était un objet représentant une multiplication d'un objet `Dice` (et pas encore `DiceComponent`) par un réel. On montrera qu'on peut ensuite écrire les méthodes qui vont bien `roll` et `get_position` pour récupérer le résultat du calcul de la multiplication et lancer cet objet représentant un dé multiplié (i.e lancer le dé normal associé) comme si c'était un `Dice` normal. On fait ensuite de même pour le `SumDice` en faisant comme si on allait se contenter de représenter la somme de deux `Dice`. 

Enfin, on explique le `DiceComponent` qui permet de tout considérer de la même manière. On remplace les `Dice` utilisés dans `SumDice` et `MultDice` par des `DiceComponent`. On montre que ça marche bien car quand on `roll` un `SumDice` par exemple, on va `roll` chacun de ses enfants et ces enfants étant des `DiceComponent`, ils ont bien une méthode `roll` eux aussi.

On a finalement le schéma suivant :

![composite](/img/composite.png)

Et le code :

{{<highlight python>}}
import random


class DiceComponent:
    def add(self, other):
        return SumDice(self, other)

    def mult(self, coeff):
        return MultDice(self, coeff)


class Dice(DiceComponent):
    NUMBER_FACES = 6

    def __init__(self, position=1):
        self.position = position

    def get_position(self):
        return self.position

    def roll(self):
        self.position = random.randint(1, self.NUMBER_FACES)


class SumDice(DiceComponent):
    def __init__(self, left, right):
        super().__init__()
        self.left = left
        self.right = right

    def get_position(self):
        return self.left.get_position() + self.right.get_position()

    def roll(self):
        self.left.roll()
        self.right.roll()


class MultDice(DiceComponent):
    def __init__(self, dice, mult):
        super().__init__()
        self.dice = dice
        self.mult = mult

    def get_position(self):
        return self.mult + self.dice.get_position()

    def roll(self):
        self.dice.roll()
{{</highlight>}}

Ce code permet de faire des choses comme :

{{<highlight python>}}

d1 = Dice(1)
d2 = Dice(2)
d1.add(d2).get_position() # qui renvoie 3

d1.mult(3).get_position() # qui renvoie 3

{{</highlight>}}

On peut aussi évoquer le fait qu'en python on peut remplacer `add` par `__add__` et `mult` par `__mul__` pour pouvoir utiliser les opérateurs `+` et `*` directement. On peut même ajouter des `__int__` à nos objets à la place de `get_position` mais cela dépasse le cadre de ce qu'on demande aux étudiants de savoir faire : 

{{<highlight python>}}

import random


class DiceComponent:
    def __add__(self, other):
        return SumDice(self, other)

    def __mul__(self, coeff):
        return MultDice(self, coeff)


class Dice(DiceComponent):
    NUMBER_FACES = 6

    def __init__(self, position=1):
        self.position = position

    def __int__(self):
        return self.position

    def roll(self):
        self.position = random.randint(1, self.NUMBER_FACES)


class SumDice(DiceComponent):
    def __init__(self, left, right):
        super().__init__()
        self.left = left
        self.right = right

    def __int__(self):
        return int(self.left) + int(self.right)

    def roll(self):
        self.left.roll()
        self.right.roll()
        return self


class MultDice(DiceComponent):
    def __init__(self, dice, mult):
        super().__init__()
        self.dice = dice
        self.mult = mult

    def __int__(self):
        return self.mult * int(self.dice)

    def roll(self):
        self.dice.roll()
        return self

{{</highlight>}}

De cette manière, on peut faire : 

{{<highlight python>}}

d1 = Dice(1)
d2 = Dice(2)
int(d1 + d2) # qui renvoie 3

int(d1 * 3) # qui renvoie 3

int((d1 + d2) * 3 + d1) # 10

int((d1 + d2).roll())

{{</highlight>}}

## TP

### L'interface du dé

On met juste les deux lignes qui vont bien dans la fonction `press_roll` :

{{<highlight python>}}

from appJar import gui
from dice import Dice

app = gui()
app.setGeometry(500, 200)

def press_roll(button):
    dice.roll()
    app.setLabel("dice", str(dice.get_position()))

dice = Dice()
app.addLabel("position", "Position : ", 0, 0)
app.addLabel("dice", str(dice.get_position()), 0, 1)
app.addButton("Roll", press_roll, 0, 2)

app.go()

{{</highlight>}}

### StatDice

Il faut insister sur l'appel du `super().__init__()` dans le constructeur. On réécrit uniquement la fonction `set_position` car on l'utilise dans le `roll` de `Dice` (attention si certain·e·s étudiant·e·s utilisent leur propre code de la classe, ils n'avaient peut-être pas fait le `roll` en utilisant le `set_position` et leur dé ne conservera donc pas les statistiques). Veillez à bien expliquer le `super().set_position(new_position)` pour qu'ils comprennent bien comment réutiliser les choses écrites dans la classe mère même si on définit une méthode du même nom.

{{<highlight python>}}

class StatDice(Dice):
    def __init__(self, position=1):
        super().__init__(position)
        self.memory = {value: 0 for value in range(1, self.NUMBER_FACES + 1)}

    def get_memory(self):
        return self.memory

    def set_position(self, new_position):
        super().set_position(new_position)
        self.memory[new_position] += 1

    def stats(self):
        n_roll = sum(self.memory.values())
        return {value: self.memory[value] / n_roll for value in self.memory}

    def mean(self):
        n_roll = sum(self.memory.values())
        return sum(value * self.memory[value] for value in self.memory) / n_roll

{{</highlight>}}

### Afficher les stats

On leur fait ensuite ajouter des choses sur l'interface en s'inspirant du code donné avant, pour un résultat qui ressemble à :

{{<highlight python>}}

from appJar import gui
from stats_dice import StatDice

app = gui()
app.setGeometry(500, 200)

def press_roll(button):
    n_roll = 0
    if button == "Roll":
        n_roll = 1
    elif button == "Roll 10 times":
        n_roll = 10
    elif button == "Roll 100 times":
        n_roll = 100
    for i in range(n_roll):
        dice.roll()
    app.setLabel("dice", str(dice.get_position()))
    app.setLabel("dice_mean", str(dice.mean()))

dice = StatDice()
app.addLabel("position", "Position : ", 0, 0)
app.addLabel("dice", str(dice.get_position()), 0, 1)
app.addButton("Roll", press_roll, 0, 2)
app.addButton("Roll 10 times", press_roll, 0, 3)
app.addButton("Roll 100 times", press_roll, 0, 4)
app.addLabel("mean", "Mean : ", 1, 0)
app.addLabel("dice_mean", "0", 1, 1)

app.go()

{{</highlight>}}

Pour info, le bouton transmis en paramètre des fonctions qu'on associe à un bouton est tout simplement la chaîne de caractères le représentant, donc ce qui est écrit dedans.

### Les entrées utilisateur

On veut ici montrer rapidement comment récupérer des choses données par l'utilisateur et plus particulièrement leur expliquer qu'il faut toujours vérifier que ce que l'utilisateur entre correspond à ce qu'on attendait pour éviter l'apocalypse. Le bout de code final correspondant juste à la lecture du champ ressemblerait à :

{{<highlight python>}}

def get_integer(button):
    try:
        user_int = int(app.getEntry("user_entry"))
    except ValueError:
        app.errorBox("Vous êtes vilain", "On vous avait demandé un entier")
        user_int = -1
    print(user_int)

app.addLabel("user_int", "Enter an integer:", 0, 0)
app.addEntry("user_entry", 0, 1)
app.addButton("OK", get_integer, 0, 3)

{{</highlight>}}

Ils n'ont jamais vu de `try`, `except` donc il faut bien leur [expliquer](https://docs.python.org/2/tutorial/errors.html).


### L'interface plus complète

Vous trouverez un exemple de code dessous. Il y a peu de chances que des étudiant·e·s arrivent à le faire entièrement mais sait-on jamais. Avec les validationEntry, on peut colorer les cases en vert ou rouge selon que l'entrée convient ou pas, ce qui permet d'éviter d'envoyer des pop-ups tout le temps et de bien pointer où est le problème. Par contre, la version de appJar des machines de l'école n'est peut-être pas suffisamment récente pour faire ça. On pourrait ajouter une somme des probas déjà entrées pour ne pas avoir besoin de compter soi-même. Les [events](http://appjar.info/pythonEvents/) permettent de le faire en liant une fonction aux champs de texte qui sera appelée dès que l'utilisateur modifie le champ.

{{<highlight python>}}

stat_dice = StatDice()

def roll_dice(button):
    probabilities = [0] * 6
    for i in range(1, 7):
        try:
            user_entry = int(app.getEntry("entry_"+str(i)))
        except ValueError:
            user_entry = -1
        if user_entry > 0:
            app.setEntryValid("entry_"+str(i))
            probabilities[i-1] = user_entry
        else:
            app.setEntryInvalid("entry_"+str(i))
    if sum(probabilities) == 100:
        stat_dice.probabilities = [p/100 for p in probabilities]
        try:
            n_roll = int(app.getEntry("nb_roll"))
        except ValueError:
            n_roll = -1
        if n_roll < 0:
            app.setEntryInvalid("nb_roll")
        else:
            app.setEntryValid("nb_roll")
            for i in range(n_roll):
                stat_dice.roll()
            results = stat_dice.stats()
            for i in range(1, 7):
                app.setLabel("result_"+str(i), results[i])
    else:
        app.errorBox("Erreur", "Les probabilités ne somment pas à 1")


app = gui()

app.startLabelFrame("Configuration")
app.addLabel("readme", "Entrez les pourcentages d'apparition de chaque face du dé :")
for i in range(1, 7):
    app.addLabel("proba_"+str(i), str(i), 1, i)
    app.addValidationEntry("entry_"+str(i), 2, i)
app.addLabel("times", "Nombre de lancers : ", 3, 0)
app.addValidationEntry("nb_roll", 3, 1)
app.stopLabelFrame()

app.addButton("Roll", roll_dice, 4)

app.startLabelFrame("Statistics")
for i in range(1, 7):
    app.addLabel("stat_"+str(i), str(i), 5, i)
    app.addLabel("result_"+str(i), str(0), 6, i)
app.stopLabelFrame()

app.go()

{{</highlight>}}

### PlantUML

Le TextUML des classes données pour info et modification si besoin :

@startuml

class Point { 
    ___attributes
    - x: float
    - y: float
    
    __methods__
    
    +__init__(float, float)
    +get_x(): float
    +set_x(float)
    +get_y(): float
    +set_y(float)
    +distance(Point): float
}


class Polygon { 
  __attributes__
  - vertices: list of Point

  __methods__

  +__init__(list of Point)
  +area(): float
  +perimeter(): float
}


class Triangle { 
    __methods__
    + __init__(Point, Point, Point)
}

Polygon <|-- Triangle
Polygon o-- Point

@enduml

@startuml

class Personnage { 
    ___attributes__
    - vie: int
    - attaque: int
    
    __methods__
    
    +__init__(vie, attaque)
    +get_vie(): int
    +set_vie(int)
    +get_attaque(): int
    +set_attaque(int)
    +taper(Personnage)
    +se_faire_taper(Personnage)
}

class Guerriere { 
  __attributes__
  - blocage: int

  __methods__

  +__init__(vie, attaque, blocage)
  +se_faire_taper(Personage)
}

class Magicien {
    __attributes__
  - attaque_magique: int

  __methods__

  +__init__(vie, attaque, attaque_magique)
  +lancer_sort()
}

Personnage <|-- Guerriere
Personnage <|-- Magicien
@enduml

