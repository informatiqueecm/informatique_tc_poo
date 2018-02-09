+++
title = "1 - Composition et agrégation, corrigé"
weight = 1
+++


{{<note>}}
Le but de cette séance est de consolider les concepts fondamentaux de classe et d'objet et d'ajouter les notions de
composition, d'agrégation et d'attributs de classe. Vous (re)découvrirez aussi les tests unitaires.
{{</note>}}


## Éléments de cours




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

{{< highlight python >}}
def test_ajoute_0():
    ajoute_0 = ajoute(0)
    assert ajoute_0(0) == 0

def test_ajoute_different():
	assert ajoute(41)(1) == ajoute(1)(41) == 42
{{< /highlight >}}

Question piège. Qu'est censé rendre `ajoute("truc")("bidule")` ? Si on est dans le mode de pensée python, on utilise la concaténation de chaînes de caractères et donc c'est censé rendre "trucbidule". Et c'est le cas avec la proposition de code suivant (attention, pour les chaînes de caractères, `+` n'est pas commutatif...): 


{{< highlight python >}}
def ajoute(valeur_a_ajouter):
	def ajoute(valeur):
		return valeur_a_ajouter + valeur
	return ajoute
{{< /highlight >}}


Bien faire les namespaces et les noms pour comprendre comment tout ça marche pour de vrai (ici le `ajoute` local masque le `ajoute` du namespace qui possède le nom de la fonction `ajoute` du dessus).

## TP

{{<highlight python>}}

class GreenCarpet:
  def __init__(self):
    self.dices = [Dice() for i in range(5)]

  def get_dices(self):
    return self.dices

  def roll(self):
    for dice in self.dices:
      dice.roll()

  def get_dice_by_id(self, item):
    return self.dices[item]

  def __getitem__(self, item):
    return self.dices[item]

{{</highlight>}}

{{<highlight python>}}


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

{{</highlight>}}



