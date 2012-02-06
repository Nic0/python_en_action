.. _lettuce-ch:

Behavior Driven Developpment avec Lettuce
=========================================

`Lettuce`_ [3]_ est principalement un portage sur Python de `Cucumber`_ [1]_.
Cucumber a été écrit par la communauté de Ruby. Il permet une approche des
tests et du développement de façon "BDD" Behavior Driven Developpement. Cet
outil à été porté pour PHP (`Behat`_ [2]_) et pour Python. C'est une pratique
courante, comportant son lot d'adeptes et de récalcitrants...

Python dispose de deux outils semblable, `Lettuce`_ [3]_ et `Freshen`_ [4]_. Les deux
projets sont actif et fonctionnel. Lettuce est un standalone, tandis que
Freshen est un plugin de Nose (voir mes deux précédents articles consacrés à
Nose). Bien que l'utilisation est peut-être plus courante pour un développement
web (Django par exemple) il est possible de s'en servir en tout autre
contexte.

L'objet, est d'écrire un "scénario" compréhensible par n'importe qui,
correspondant à un comportement voulu d'une fonctionnalité, de s'assurer que le
test échoue, puis on écrit le code correspondant, et pour finir, on s'assure
que le test passe avec succès. Ce billet n'est qu'une approche rapide, tiré
principalement de l'exemple de la documentation.

Installation et doc de Lettuce
------------------------------

L'installation est toujours simplifié avec `pip` (pip-2.7 pour ArchLinux)

::
    sudo pip install lettuce

La documentation du site n'étant pas des plus à jour, il est plus sage de la
générer localement comme suit, comme vu dans la partie API/documentation avec Sphinx::

    git clone https://github.com/gabrielfalcao/lettuce.git
    cd lettuce/docs && make html
    firefox _build/html/index.html

Exemple basique
---------------

L'exemple veut tester une fonction simple, factoriel.

Voici la structure avec lequel on part

::

    .
    |── src
     ── tests
        ── features


Un peu déroutant à la première approche, mais les tests se rangent dans `features`.

L'important à comprendre, c'est qu'on va se retrouver avec deux types de fichiers.

1. \*.feature: Sont les scénarios rédigé dans un anglais compréhensible par
tous, décrivant une suite de comportements et de résultats attendus.
2. step.py: Le code servant à interpréter les scénarios.

.. note::

    Il existe une coloration pour les features avec Vim, celui-ci le
    reconnais comme syntaxe de Cucumber. Emacs et tout autre éditeur doit
    certainement en faire de même.

Le code suivant, est là surtout pour donner une idée de la syntaxe utilisée pour
lettuce, et du résultat obtenu. La documentation, que vous pouvez générer en
local comme vu plus haut, donne un bon exemple de petit tutoriel reprenant un
peu plus en détail.

Pour mieux visualiser, voici les répertoires et fichiers après écriture du code::

    $ tree
    .
    |── __init__.py
    |── src
    │   |── fact.py
    │    ── __init__.py
     ── tests
        |── features
        │   |── __init__.py
        │   |── steps.py
        │    ── zero.feature
         ── __init__.py

    3 directories, 7 files

`tests/features/zero.feature`::

    Feature: Compute factorial
        In order to play with Lettuce
        As beginners
        We'll implement factorial

        Scenario: Factorial of 0
            Given I have the number 0
            When I compute its factorial
            Then I see the number 1

        Scenario: Factorial of 1
            Given I have the number 1
            When I compute its factorial
            Then I see the number 1

        Scenario: Factorial of 2
            Given I have the number 2
            When I compute its factorial
            Then I see the number 2

          Scenario: Factorial of 3
            Given I have the number 3
            When I compute its factorial
            Then I see the number 6

          Scenario: Factorial of 4
            Given I have the number 4
            When I compute its factorial
            Then I see the number 24

`tests/features/steps.py`::

    from lettuce import *
    import sys
    sys.path.append('../src/')
    from fact import factorial

    @step('I have the number (\d+)')
    def have_the_number(step, number):
        world.number = int(number)

    @step('I compute its factorial')
    def compute_its_factorial(step):
        world.number = factorial(world.number)

    @step('I see the number (\d+)')
    def check_number(step, expected):
        expected = int(expected)
        assert world.number == expected, \
            "Got %d" % world.number

`src/fact.py`::

    def factorial(number):
        number = int(number)
        if (number == 0) or (number == 1):
            return 1
        else:
            return number*factorial(number-1)

Pour exécuter les tests, on se place dans tests, puis on exécute `lettuce`.

::

    $ lettuce

    Feature: Compute factorial       # features/zero.feature:1
      In order to play with Lettuce  # features/zero.feature:2
      As beginners                   # features/zero.feature:3
      We'll implement factorial      # features/zero.feature:4

      Scenario: Factorial of 0       # features/zero.feature:6
        Given I have the number 0    # features/steps.py:7
        When I compute its factorial # features/steps.py:11
        Then I see the number 1      # features/steps.py:15

      Scenario: Factorial of 1       # features/zero.feature:11
        Given I have the number 1    # features/steps.py:7
        When I compute its factorial # features/steps.py:11
        Then I see the number 1      # features/steps.py:15

      Scenario: Factorial of 2       # features/zero.feature:16
        Given I have the number 2    # features/steps.py:7
        When I compute its factorial # features/steps.py:11
        Then I see the number 2      # features/steps.py:15

      Scenario: Factorial of 3       # features/zero.feature:21
        Given I have the number 3    # features/steps.py:7
        When I compute its factorial # features/steps.py:11
        Then I see the number 6      # features/steps.py:15

      Scenario: Factorial of 4       # features/zero.feature:26
        Given I have the number 4    # features/steps.py:7
        When I compute its factorial # features/steps.py:11
        Then I see the number 24     # features/steps.py:15

    1 feature (1 passed)
    5 scenarios (5 passed)
    15 steps (15 passed)

La sortie se fait en couleur bien entendu.

Conclusion
----------

Encore une fois, l'important ici est d'écrire les tests avant d'en écrire le
code, de s'assurer qu'il ne passe pas, et ensuite d'écrire le code
correspondant afin que le test passe, principe du BDD.

Je suis aller assez vite sur le fonctionnement car je pense que si le rapide
aperçu vous intrigue, vous aurez de toute façon bien assez envie d'attaquer leur
tutoriel, et creuser les quelques pistes dont le billet était sujet, avant tout
la présentation d'un outil, que l'explication de son fonctionnement.

.. _`Lettuce`: https://github.com/gabrielfalcao/lettuce
.. _`Cucumber`: http://cukes.info/
.. _`Behat`: http://behat.org/
.. _`Freshen`: https://github.com/rlisagor/freshen

.. [1] http://cukes.info/
.. [2] http://behat.org/
.. [3] https://github.com/gabrielfalcao/lettuce
.. [4] https://github.com/rlisagor/freshen
