+++
title = "4 - Design Pattern : cas du undo"
weight = 4
+++

{{<note>}}
Le but de cette séance est de faire sentir la notion de design pattern. 
{{</note>}}



## Éléments de cours

On essayera de leur montrer quelques diagrammes UML de design pattern, puis leurs applications dans des cas concrets. Montrer les 3 grands types de design pattern et donnez un exemple pour chaque.

On peut prendre la liste initiale des design pattern du [GOF](https://en.wikipedia.org/wiki/Design_Patterns) mais je déconseille singleton qui tient plus de l'anti-pattern que du pattern...

On pourra par exemple : 

- type structural : composite. Vu à la troisième séance
-  type creational : factory. Créer les objet via des méthodes presque sans paramètre et avec un nom adapté plutôt qu'avec un contructeur avec des milliers de paramètres
- type behavioural : strategy. Même fonction mais type d'algorithme différent (pour un tri par exemple)


Parler aussi des anti-pattern, c'est à dire des mauvaise façon de faire qui arrive d'elle même si on ne fait pas attention. Voir par exemple : http://sahandsaba.com/nine-anti-patterns-every-programmer-should-be-aware-of-with-examples.html



## TD



### Observer

{{< note warning >}}
TBD.
{{< /note >}}

Pour les UI, on s'abonne à des *events* comme par exemple "cliquer sur ce bouton", "mettre du texte dans ce champ", "la souris passe sur cet objet", etc. Un exemple simple pour voir tout ça en action est de faire du javascript. 

Attention cependant, si le principe est simple, sa mise en œuvre est souvent plus compliquée. Ainsi, l'héritage des objets graphiques entre eux est pris en compte (arbre DOM en html par exemple) et on ajoute souvent une phase de remontée des évènements (phase de *bubble*). Voir par exemple  https://www.w3.org/TR/uievents/ pour le web. 

### Memento


L'UML de memento possède : 

 * un attribut `stored_state` qui contient l'état antérieur de l'objet que le veut stocker
 * un attribut `object_to_restore` contenant l'objet dont l'état a été sauvé
 * une méthode `restore` permettant de remettre l'état sauvé à l'objet stocké.

{{< highlight python>}}
class Memento:
    def __init__(self, stored_state, object_to_restore):
        self._stored_state = stored_state
        self._object_to_restore = object_to_restore
    
    def restore(self):
        pass
{{< /highlight >}}

Pour un Dé, on peu utiliser l'héritage : 


{{< highlight python>}}
class DiceMemento(Memento):
    def __init__(self, stored_state, object_to_restore):
        super.__init__(stored_state, object_to_restore)
    
    def restore(self):
        self._object_to_restore.set_value(self._stored_state)
{{< /highlight >}}

#### Undo

{{< highlight python>}}
class Undo:
    def __init__(self):
        self._actions = []

    def save(self, memento):
        self._actions.append(memento)
    
    def restore(self):
        if self._actions:
            last_memento = self._actions.pop()
            last_memento.restore()
{{< /highlight>}}

On peut utiliser le code comme suit : 

{{< highlight python>}}
dice = Dice()

undo = Undo()

for i in range(10):
    undo.save(DiceMemento(dice))
    print(dice.roll().get_value())
state.save(DiceMemento(dice))

print("--------")

for i in range(10):
    undo.restore()
    print(dice.get_value())
{{< /highlight>}}

Qui pourra rendre le code suivant : 

{{< highlight python>}}
    1
    2
    4
    3
    2
    5
    3
    2
    4
    4
    --------
    4
    4
    2
    3
    5
    2
    3
    4
    2
    1
{{< /highlight>}}


#### Redo

Pour le redo, on utilise un undo d'undo. Pour des objets plus compliqués, on préfèrera surement refaire un Memento plutôt que d'utiliser celui déja stocké pour le undo.


Le dernier élément du undo doit toujours être l'état courant de l'objet. On initialise donc le UndoRedo avec l'état courant. Après chaque changment d'état de l'objet on le sauvera. 

{{< highlight python>}}
class UndoRedo:
    def __init__(self, current_state_memento):
        self._undo = [current_state_memento]
        self._redo = []

    def save(self, current_state_memento):        
        self._undo.append(current_state_memento)
        self._redo = []
    
    def undo(self):
        if len(self._undo) > 1:  #  current_state and someting to restore        
            self._redo.append(self._undo.pop())  # move current_state
            self._undo[-1].restore()   # restore old state. Last elem of undo si current state

    def redo(self):
        if self._redo:
            self._undo.append(self._redo.pop())
            self._undo[-1].restore()
            
{{< /highlight >}}



### Iterateurs

{{< note warning >}}
TBD.
{{< /note >}}


Le module [itertools](https://docs.python.org/3/library/itertools.html) de python contient de nombreux utilitaires pour créer des itérateurs. ON peut aussi, mais cela dépasse un peu le scope du cours utiliser des générateurs (avec la commande `yield`) pour créer automatiquement des itérateurs. Un exemple célèbre est la fonction de Fibonacci (pris de https://www.python-course.eu/python3_generators.php) : 

{{< highlight python>}}
def fibonacci(n):
    """ A generator for creating the Fibonacci numbers """
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n): 
            return
        yield a
        a, b = b, a + b
        counter += 1

f = fibonacci(5)
for x in f:
    print(x) # 


{{< /highlight >}}
## TP

### UI


{{< highlight python>}}
import datetime

from appJar import gui
from dice import Dice


dice = Dice()
DICE_LABEL = "dice value"

MESSAGE_LABEL = "message value"


def on_click(button):
    dice.roll()

    app.setLabel(DICE_LABEL, dice.get_value())
    app.addListItem(MESSAGE_LABEL, str(datetime.datetime.now()) + ": " + str(dice.get_value()))

app = gui()

app.setGeometry(400, 200)

app.addLabel(DICE_LABEL, dice.get_value(), 0, 0)
app.addButton("roll", on_click, 0, 1)


app.addListBox(MESSAGE_LABEL, [str(datetime.datetime.now()) + ": " + "Begin",
                               str(datetime.datetime.now()) + ": " + str(dice.get_value())], 1, 0, 2)

app.go()
{{< /highlight>}}



### Memento


#### memento.py

{{< highlight python>}}
class Memento:
    def __init__(self, dice):
        self.dice = dice
        self.value = dice.get_value()

    def restore(self):
        self.dice.set_value(self.value)
{{< /highlight >}}


#### test_memento.py

{{< highlight python>}}
from dice import Dice
from memento import DMemento


def test_memento():
    dice = Dice()
    dice.set_value(2)

    memento = DiceMemento(dice)
    dice.set_value(6)

    memento.restore()
    assert dice.get_value() == 2
{{< /highlight >}}

### Undo

#### undo.py

{{< highlight python>}}
from memento import Memento

class Undo:
    def __init__(self):
        self._undo_item = []

    def __len__(self):
        return len(self._undo_item)

    def save(self, dice):
        self._undo_item.append(Memento(dice))

    def restore(self):
        self._undo_item.pop().restore()
{{< /highlight>}}


#### test_undo.py

{{< highlight python>}}
from dice import Dice
from undo import Undo


def test_len():
    undo = Undo()
    assert len(undo) == 0

    undo.save(Dice())
    assert len(undo) == 1


def test_save_restore():
    dice = Dice()
    undo = Undo()

    undo.save(dice)
    dice.set_value(5)
    undo.save(dice)

    undo.restore()
    assert dice.get_value() == 5
    undo.restore()
    assert dice.get_value() == 1
{{< /highlight>}}


### UI

{{< highlight python >}}
import datetime

from appJar import gui
from dice import Dice
from undo import Undo

dice = Dice()
DICE_LABEL = "dice value"

MESSAGE_LABEL = "message value"

undo = Undo()

app = gui()
app.setGeometry(400, 200)


def on_undo(button):
    if len(undo) > 0:
        undo.restore()
        app.setLabel(DICE_LABEL, dice.get_value())
        app.addListItem(MESSAGE_LABEL, str(datetime.datetime.now()) + ": undo value to " + str(dice.get_value()))


def on_click(button):
    undo.save(dice)
    dice.roll()

    app.setLabel(DICE_LABEL, dice.get_value())
    app.addListItem(MESSAGE_LABEL, str(datetime.datetime.now()) + ": " + str(dice.get_value()))


app.addButton("undo", on_undo, 0, 0, 2)
app.addLabel(DICE_LABEL, dice.get_value(), 1, 0)
app.addButton("roll", on_click, 1, 1)
app.addListBox(MESSAGE_LABEL, [str(datetime.datetime.now()) + ": " + "Begin",
                               str(datetime.datetime.now()) + ": " + str(dice.get_value())], 2, 0, 2)

app.go()
{{< /highlight>}}
