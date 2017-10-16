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

On (ré)explique ici le lien d'agrégation dont on a déjà un peu pu parler lors du TD1 avec le `GreenCarpet`. `Polygon` utilise des objets `Point` comme attribut mais ces points existent indépendamment du polygone et on les ajoute petit à petit.

Classe `RegularPolygon`:

![regular_polygon](/img/regular_polygon.png)

L'héritage arrive ici. On ajoute au polygone régulier les attributs `center` et `radius` pour que les étudiant·e·s comprennent bien que la classe récupère les attributs de la classe parente et peut en avoir d'autres en plus. On réécrit aussi la méthode `add_point` en lui faisant renvoyer une erreur par exemple. On reviendra sur les namespaces pour expliquer que la méthode de cette classe cache celle de `Polygon`.

Ici, on donnera le début du code pour clarifier l'héritage :

{{<highlight python>}}
class RegularPolygon(Polygon):
    def __init__(self, center, radius, n_vertices):
        super().__init__()
        for i in range(n_vertices):
            x = center.x + radius * math.cos(2. * math.pi * i / n_vertices)
            y = center.y + radius * math.sin(2. * math.pi * i / n_vertices)
            p = Point(x, y)
            super().add_point(p)
{{</highlight>}}

On expliquera donc particulièrement l'usage du `super()`:

 - pour l'appel au constructeur de la classe mère
 - pour utiliser la méthode `add_point` de la classe mère et pas celle de `RegularPolygon` (qui est censée renvoyer une erreur).

On peut aussi dire qu'ici on a plutôt une composition entre `RegularPolygon` et `Point` comme les points sont créés à l'intérieur du constructeur du polygone.

L'UML complet donne donc :

![polygone_uml_entier](/img/polygone_uml_entier.png)

### Exercice 2

{{<highlight python>}}
from appJar import gui


class NotreFenetre(gui):
    def __init__(self):
        super().__init__()
        self.setTitle("Notre incroyable programme")
        self.setGeometry(800, 900)
        self.setBg("orange")
        self.setFont(18)


f = NotreFenetre()
f.go()
{{</highlight>}}

Rien de très compliqué mais cela permet de clarifier l'utilisation de l'héritage et de leur montrer que dans la vraie vie on l'utilisera surtout pour hériter de classes compliquées présentes dans des librairies. Insister à nouveau sur le `super` et les namespaces pour tous les `self.setQQChose()`.

Il est parfois utile de passer tous les arguments du constructeur de la classe fille à la classe mère. Ici par exemple on peut spécifier à gui son nom ou sa taille (http://appjar.info/#appearance-counts).

Pour cela, on va les passer *en bloc*, sans avoir besoin de les spécifier tous. On mettra en premier les argument de la classe fille puis de façon générique les arguments de la classe mère :

{{<highlight python>}}
from appJar import gui


class NotreFenetre(gui):
    def __init__(self, taille_font, **kwargs):
        super().__init__(**kwargs)
        self.setTitle("Notre incroyable programme")
        self.setGeometry(800, 900)
        self.setBg("orange")
        self.setFont(taille_font)
    
{{</highlight>}}


Pour plus d'info sur cette pratique, voir par exemple :  https://www.digitalocean.com/community/tutorials/how-to-use-args-and-kwargs-in-python-3

### Exercice 3

On a l'arbre suivant :

![arbre_arithmetique](/img/node_arithmetic.png)

On explique ensuite le composite. On aura donc une classe `DiceComponent` qui est le composant, elle représente l'abstraction d'opérations de dés. Les deux classes `SumDice` et `MultDice` sont les composites, elles représentent les deux opérations élémentaires liées aux dés. Enfin la classe `Dice` est la brique de base, la feuille. On a le schéma suivant :

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

On peut aussi évoquer le fait qu'en python on peut remplacer `add` par `__add__` et `mult` par `__mul__` pour pouvoir utiliser les opérateurs `+` et `*` directement. On peut même ajouter des `__int__` à nos objets à la place de `get_position` : 

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

@startuml

class Point {
    __attributes__

    - x: double
    - y: double

    __methods__

    +__init__(double, double)
    +get_x(): double
    +set_x(double)
    +get_y(): double
    +set_y(double)
    +distance(Point): double
}

class Polygon {
    __attributes__
    
    - vertices: list of Point
    
    __methods__
    
    +__init__()
    +add_point(Point)
    +area(): double
    +perimeter(): double
}

class RegularPolygon {
    __attributes__
    -center: Point
    -radius: double
    
    __methods__
    +__init__(Point center, double radius, int n_sommets)
    +add_point()
}

Polygon <|-- RegularPolygon
Polygon o-- Point
RegularPolygon *-- Point

@enduml

