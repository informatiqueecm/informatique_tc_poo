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
    self.set_vie(self.get_vie() - personnage.get_attaque())

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


On peut mettre le code s'ils en ont besoin mais a priori rien de très difficile s'ils ont compris ce qui se passe avant. Il faut :

  - ajouter une méthode `lancer_sort`
  - ajouter un paramètre et son attribut associé `attaque_magique` au constructeur
  - ajouter une méthode `se_faire_toucher_par_un_sort(magicien)` avec un paramètre de type Magicien dans la classe Personnage.

On mentionnera aussi le fait que l'héritage est utile quand on veut utiliser des classes définies dans un module quelconque et la mettre un peu à notre sauce.


## TP

### Les bases de l'héritage

Il faut insister sur l'appel du `super().__init__()` dans le constructeur et bien l'expliquer. Passez bien le temps
qu'il faut sur cette introduction en insistant toujours sur  les namespaces pour qu'ils comprennent bien d'où viennent
les choses et que rien ne paraissent magique.

### StatDice

On réécrit uniquement la fonction `set_position` car on l'utilise dans le `roll` de `Dice` (attention si certain·e·s étudiant·e·s utilisent leur propre code de la classe, ils n'avaient peut-être pas fait le `roll` en utilisant le `set_position` et leur dé ne conservera donc pas les statistiques). Veillez à bien expliquer le `super().set_position(new_position)` pour qu'ils comprennent bien comment réutiliser les choses écrites dans la classe mère même si on définit une méthode du même nom.

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



## PlantUML

Pour https://www.planttext.com/

{{<highlight uml>}}



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

{{</highlight>}}
