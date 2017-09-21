+++
title = "1 - Classes et objets, corrigé"
weight = 1
+++

## TD


## TP
### Dice et GreenCarpet

{{<highlight python >}}
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
    self.position = numpy.random.choice([i for i in range(1, self.NUMBER_FACES + 1)], p=self.probabilities)

  def __str__(self):
    return "Dice(position=" + str(self.position) + ", probabilities=" + str(self.probabilities)

  def __eq__(self, other):
    return self.position == other.position and self.probabilities == other.probabilities

  def __lt__(self, other):
    return self.position < other.position

  def __gt__(self, other):
    return self.position > other.position


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

### Tests

{{<highlight python>}}
from dice import *


def test_dice_creation_no_argument():
    dice = Dice()
    assert dice.get_position() == 1
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


def test_dice_creation_initial_position():
    dice = Dice(position=5)
    assert dice.get_position() == 5
    assert dice.probabilities == [1/6, 1/6, 1/6, 1/6, 1/6, 1/6]


def test_str():
    dice = Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0])
    assert str(dice) == "Dice(position=1, probabilities=[1, 0, 0, 0, 0, 0]"


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


def test_eq():
    dice = Dice(position=1)
    different_dice = Dice(position=2)
    equal_dice = Dice(position=1)
    assert dice != different_dice
    assert dice == equal_dice


def test_lt():
    small_dice = Dice(position=1)
    large_dice = Dice(position=2)
    assert small_dice < large_dice


def test_gt():
    small_dice = Dice(position=1)
    large_dice = Dice(position=2)
    assert large_dice > small_dice


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