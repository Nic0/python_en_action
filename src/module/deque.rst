.. _deque:

Piles et Files avec Deque
=========================

Introduction
------------

En programmation, les `piles`_ [1]_ et `files`_ [2]_, aka LIFO (last in, first
out) et FIFO (first in, first out) sont des incontournables. Il s'agit de
stocker des données, et surtout de l'ordre dans lequel on récupère celle-ci.
Pour information, j'avais écrit `Exemple de file avec deux pointeurs en langage
C`_ [3]_ qui est un exemple de liste chaînée utilisée comme une file en C.

Comme Python est un langage de haut niveau, il offre une façon simple de mettre en
place l'un ou l'autre. On va voir qu'il correspond à une classe du module
`collections`.

**Pourquoi ne pas utiliser simplement une liste ?**

Il est possible d'obtenir le même comportement avec une liste, surtout qu'elle
offre un peu près les mêmes méthodes (pop), cependant, *deque* est spécialement
prévu pour cette effet, et est donc optimisé dans ce sens. Il est donc
préférable d'utiliser *deque* si l'on sait qu'on l'utilise comme fifo ou lifo.

Utilisation
-----------

Rien de bien compliqué, regardons cette suite de commande effectuée directement
avec l'interpréteur.

- Fonctionnement comme pile (lifo)

::

    >>> from collections import deque
    >>> pile = deque([1, 2, 3, 4, 5])
    >>> from collections import deque
    >>> pile = deque([1, 2, 3, 4, 5])
    >>> pile.pop()
    5
    >>> pile.pop()
    4
    >>> pile.append(6)
    >>> pile
    deque([1, 2, 3, 6])

- Fonctionnement comme file (fifo)

::

    >>> from collections import deque
    >>> pile = deque([1, 2, 3, 4, 5])
    >>> pile.popleft()
    1
    >>> pile.popleft()
    2
    >>> pile.append(6)
    >>> pile
    deque([3, 4, 5, 6])

**Et après ?**

Le module vient avec d'autres méthodes pouvant être utile dans ce genre de cas
tel que par exemple :

* appendleft()
* clear()
* extend()
* reverse()
* rotate(n)

Pour plus de détails sur les possibilités, lisez `la documentation officiel sur
ce module`_ [4]_.

Ce module est assez simple à prendre en main, mais toujours utile si on sait
qu'on veut mettre en place rapidement une fifo ou lifo. Ce n'est qu'une méthode
parmi d'autre pour obtenir ce que l'on souhaite, mai l'avantage de *deque* est
d'être très simple à l'emploi, tout en étant plus performant. Éventuellement,
pour les plus curieux, l'utilisation de `queue.LifoQueue` pourrait intéresser,
mais l'idée ici était de garder une utilisation simplifié au maximum.

.. _`piles`: http://fr.wikipedia.org/wiki/Pile_(informatique)
.. _`files`: http://fr.wikipedia.org/wiki/File_(structure_de_données)
.. _`Exemple de file avec deux pointeurs en langage C`: http://www.nicosphere.net/exemple-de-file-avec-deux-pointeurs-en-langage-c-1752/
.. _`la documentation officiel sur ce module`: http://docs.python.org/py3k/library/collections.html#collections.deque

.. [1] http://fr.wikipedia.org/wiki/Pile_(informatique)
.. [2] http://fr.wikipedia.org/wiki/File_(structure_de_données)
.. [3] http://www.nicosphere.net/exemple-de-file-avec-deux-pointeurs-en-langage-c-1752/
.. [4] http://docs.python.org/py3k/library/collections.html#collections.deque
