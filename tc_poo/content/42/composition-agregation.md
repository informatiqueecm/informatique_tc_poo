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



