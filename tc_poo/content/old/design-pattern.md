+++
title = "4 - Design Pattern : cas du undo"
weight = 4
+++


## Introduction

Séance consacrée aux [design pattern](https://fr.wikipedia.org/wiki/Patron_de_conception). Ces façons de faire, sorte d'algorithmie objet permet de résoudre nombre de problèmes courants en développement.

On utilisera beaucoup le typage dynamique de python pour ces exemples (appelé [duck typing](http://sametmax.com/quest-ce-que-le-duck-typing-et-a-quoi-ca-sert/). Ce nom vient des Monty Python et plus particulièrement la séquence où [l'on brûle une sorcière](https://www.youtube.com/watch?v=gUXB_jLiT3A) dans Sacré Graal. Le [nom même du langage](https://en.wikipedia.org/wiki/Python_(programming_language)#History) vient d'eux et pas de l'[animal](https://www.youtube.com/watch?v=NoX-4Hm1rPU). Les informaticiens ont une passion presque maladive pour les Monthy Python. Par exemple le terme de spam vient aussi d'eux, ou plutôt d'un de [leur sketch](https://www.youtube.com/watch?v=cFrtpT1mKy8) de leur émission de la BBC qui s'appelait le Monthy Python's flying circus (un de mes sketch préféré étant l'[expédition au Kilimandjaro](https://www.youtube.com/watch?v=1T9Yp-2TYzo) mais on pourrait en citer des dizaines d'autres)), mais les design pattern fonctionnent quelques soient le langage utilisé (ils ont d'ailleurs [initialement été écrit](https://fr.wikipedia.org/wiki/Design_Patterns) pour le C++).

Cela ne résout bien sûr pas des problèmes aussi compliqués que [tracer 7 lignes rouges perpendiculaires entre elles](https://www.youtube.com/watch?v=vH0rhNx3dok) mais cela permet d'éviter de faires des [erreurs classiques](http://sahandsaba.com/nine-anti-patterns-every-programmer-should-be-aware-of-with-examples.html), aussi appelées [anti-pattern](https://fr.wikipedia.org/wiki/Antipattern).

## Les bases

> [Le sujet de td](/ressources/td_4_impression.pdf)

## Un Dés

Reprenez le [code du dé du dernier tp]({{< ref "/post/heritage.md#interface-d-un-dé" >}}) et utilisez le dans l'UI ci-après.

#### main.py initial

{{< highlight python >}}

from appJar import gui
from dice import Dice


dice = Dice()
DICE_LABEL = "dice value"


def on_click(button):
    dice.roll()

    app.setLabel(DICE_LABEL, dice.get_position())

app = gui()
app.setGeometry(400, 200)

app.addLabel(DICE_LABEL, dice.get_position(), 0, 0)
app.addButton("roll", on_click, 0, 1)


app.go()

{{< /highlight >}}


### UI 

Ajoutez une ligne de texte contenant l'heure et la valeur du nouveau dé à chaque fois que l'on clique.

Pour cela, vous pourrez utiliser : 

- les [listbox](http://appjar.info/pythonWidgets/#listbox) pour ajouter les lignes de messages (1ttention,  les list box ont une hauteur par défaut. En choisissant 200 comme hauteur de la fenêtre, vous devriez pouvoir voir et la 1ère ligne (la valeur du dé et le bouton {{< menu_code >}} roll{{< /menu_code >}}) et la 2nde (la *listbox*)).
- Pour le temps, on pourra utiliser les objets [datetime](https://docs.python.org/3/library/datetime.html#datetime-objects)

### Memento

Créez la classe `DiceMemento` dans le fichier `dice_memento.py` et ses tests dans le fichiers :`test_dice_memento.py`.

La classe DiceMemento doit avoir :

- un `Dice` comme paramètre du constructeur.
- une méthode `restore()` qui remet au dé sauvé de reprendre la valeur qu'il avait à la création du memento.


Vous pouvez par exemple transformer le code ci-après en test(s) :


{{< highlight python >}}
dice = Dice()
dice.set_position(2)

memento = DiceMemento(dice)
dice.set_position(6)

memento.restore()
print(dice.get_position())  # doit valoir 2
{{< /highlight >}}

### Undo list

Nous pouvons maintenant créer une classe `Undo` (dans le fichier `undo.py`) qui va nous permettre de sauver des dés (et leurs valeurs) et de les restaurer à la demande. Cette classe doit pouvoir :

- sauver un dé avec la méthode : `save(memento)` (un `DiceMemento` sera créé exemple comme ça : `DiceMemento(dice)`)
- restaurer la dernière valeur sauvée avec la méthode `restore()`
- connaître le nombre d'items sauvegardés avec une méthode `nb_undos()`

Bien sur vous créerez un fichier de tests `test_undo.py` qui testera les 3 fonctionnalités ci-dessus.

{{< highlight python >}}
dice = Dice()

undo = Undo(dice)

dice.set_position(5)
print(dice.get_position()) # vaut 5
undo.save(DiceMemento(dice))
dice.roll() # dès que l'on change la valeur (ici possiblement différent de 5)
undo.save(DiceMemento(dice)) # on sauve dans un memento

undo.restore()
print(dice.get_position()) # vaut 5

{{< /highlight >}}


### UI

Ajoutez à l'UI un bouton undo. 

A chaque fois que l'on clique sur le bouton *roll*, la position du dé doit être sauvée. Cette position doit être restaurée lorsque l'on clique sur le bouton *undo*. De plus, on doit ajouter un message à la listbox qui prévient que la valeur du dé à été undouée.


### Allez plus loin 

Mettez en place un mécanisme de Redo pour vos lancers de dés.



