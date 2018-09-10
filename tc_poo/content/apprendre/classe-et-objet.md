+++
title = "1 - Classes et objets"
weight = 1
+++


## Introduction

Dans cette séance nous allons voir les notions :

  - de classe
  - d'objets
  - d'attributs
  - de méthode


## Les bases

> [Le sujet de td](/ressources/TD_1_impression.pdf)


## PyCharm

{{<note>}}
Si ce n'est pas votre premier cours d'informatique à l'école centrale marseille et que vous avez déjà suivi la partie Algorithmes par exemple, vous êtes censés : (i) connaître PyCharm, (ii) programmer en python. Si vous avez répondu OUI aux deux questions précédentes vous pouvez aller à la partie Dice. Sinon faites ce qui suit.
{{</note>}}

On vous demande donc d'aller sur le [site des tutos](https://informatique.centrale-marseille.fr/tutos) pour :

  - se rappeler les [bases de python](https://informatique.centrale-marseille.fr/tutos/post/python-bases.html)
  - utiliser [Pycharm](https://informatique.centrale-marseille.fr/tutos/post/utilisation-pycharm-bases.html)


## Dice
### Un dé tout simple

Implémentez la première version de la classe `Dice` vue dans le TD1 dans un fichier nommé `dice.py`. Elle doit être capable de :

  - créer un objet sans paramètre
  - créer un objet avec une position initiale
  - connaître et changer la position du dé
  - lancer le dé

Pour vérifier que votre code fait ce qu'on lui demande, vous devez créer un fichier de tests `essai_dice.py` pour
essayer votre classe. Prenez le temps d'essayer :

  - de créer un objet sans argument et de vérifier dans quelle position il est,
  - de créer un objet en choisissant sa position et de vérifier sa position,
  - de modifier la position d'un dé créé et de vérifier qu'elle a bien été changée,
  - de lancer le dé et de vérifier que sa position est toujours cohérente avec le nombre de faces.

### Un dé pipé

Nous allons maintenant améliorer notre dé avec la deuxième version de la classe vue en TD : le dé pipé. Il faudra donc ajouter un nouvel attribut permettant de renseigner les probabilités de chaque face. On ajoutera ces probabilités en paramètre optionnel à l'initialisation du dé et sous forme de *setter/getter*.


Pour l'initialisation, ajoutez cet attribut et essayez de créer un nouveau dé en utilisant ce paramètre.

Modifiez la méthode `roll` pour prendre en compte les probabilités. Pour lancer le dé au hasard en respectant les probabilités voulues, vous pouvez utiliser `numpy.random.choice`. Regardez la [doc](https://docs.scipy.org/doc/numpy-dev/reference/generated/numpy.random.choice.html) pour savoir comment l'utiliser et avec quels paramètres.

{{<note>}}
Pour utiliser numpy, vous devez l'importer en haut de votre fichier avec `import numpy`.
{{</note>}}

Essayez les nouvelles fonctionnalités:

  - créer un dé avec une distribution de probabilités particulière,
  - changer la distribution de probabilités d'un dé,
  - lancer un dé pipé en vérifiant qu'il suit (à peu près) la loi de probabilités donnée.




## Pour aller plus loin

Python dispose de méthodes spéciales qui peuvent être invoquées en utilisant une syntaxe particulière (par exemple les
opérations arithmétiques `+, -, *, \` entre objets que nous avons créés nous-mêmes). Vous en trouverez une liste
exhaustive sur la [doc officielle](https://docs.python.org/3/reference/datamodel.html#special-method-names). Nous allons
en utiliser ici quelques-unes sur notre classe. Ces méthodes se présentent toujours sous la forme `__nom_de_la_methode__`.


### Dice
Essayez de taper :

{{<highlight python>}}
d = Dice()
print(d)
{{</highlight>}}

Vous devriez obtenir quelque chose comme :

{{<highlight python>}}
<dice.Dice object at 0x7f40e518a668>
{{</highlight>}}

Le `print` appelle la méthode `__str__` de notre classe. Nous ne l'avons pas définie, c'est donc la méthode par défaut de tous les objets python qui est appelée. Comme vous le constatez, elle n'est pas très intéressante pour nous. Il faut donc la définir dans notre classe.

Définissez une méthode permettant d'écrire le dé comme une chaîne de caractères sous la forme `"Dice(position=1,
probabilities=[1, 0, 0, 0, 0, 0])"` et vérifiez qu'elle fonctionne.

On voudrait également pouvoir vérifier si un dé a fait un meilleur lancer qu'un autre en faisant tout simplement :

{{<highlight python>}}
d1 = Dice(1)
d2 = Dice(6)
d1 < d2
{{</highlight>}}

Si vous testez ça avec votre code tel qu'il est, vous obtiendrez :

{{<highlight python>}}
TypeError: '<' not supported between instances of 'Dice' and 'Dice'
{{</highlight>}}

Python vous explique qu'il ne connaît pas l'opérateur `<` pour les objets de notre classe. Pour pouvoir utiliser
directement les opérateurs `<` et `>`, il faut définir respectivement les méthodes `__lt__(self, other)` et
`__gt__(self, other)`. On pourra aussi ajouter `__eq__(self, other)` pour tester l'égalité.

Ajoutez les méthodes `__lt__`, `__gt__` et `__eq__` et testez les.

