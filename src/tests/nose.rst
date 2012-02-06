.. _nose:

Introduction à Nose
===================

Pour le moment, on a vu quelques bases du module Python unittest. Dans ce
billet, on va voir comment faciliter l’exécution d'une suite de tests, même
hétérogène, et plus encore.

Nose, s'intègre avec unittest et doctest, mais propose également
sa propre syntaxe simplifiée pour écrire des tests. Dans ce billet, on prendra
la même fonction aillant servis au dernier billet, c'est à dire une fonction
``add()``, additionnant simplement deux nombres entre eux.

Installation
------------

Comme souvent, plusieurs façon d'installer Nose, pour Arch Linux par exemple,
on peut installer l'un des deux::

    yaourt -S python2-nose
    yaourt -S python-nose

D'une façon plus générale, on peut utiliser l'outil ``easy_install`` ou ``pip``::

    sudo pip install nose

Utilisation de base
-------------------

Reprenons où nous en étions la dernière fois, mais en séparant les fichiers
sources et les fichiers tests, selon la structure suivante :

::

    $ tree
    .
    |── src
    │    ── chiffres.py
     ── tests
         ── test_chiffres.py

    2 directories, 2 files</code>

Avec le contenu des fichiers:

`src/chiffres.py`::

    def add (a, b):
        if isinstance(a, basestring) or isinstance(b, basestring):
            raise ValueError
        return a+b

`tests/test_chiffres.py`::

    import unittest
    from chiffres import add

    class TestChiffres(unittest.TestCase):

        def test_addition(self):
            result = add(36, 6)
            self.assertEqual(result, 42)

        def test_add_string(self):
            self.assertRaises(ValueError, add, 'coin', 'pan')

        def test_add_with_float(self):
            result = add(1.01, 2.001)
            self.assertEqual(result, 3.011)

    if __name__ == '__main__':
        unittest.main()

Nose, contrairement à unittest, est prévu nativement pour parcourir les
fichiers et arborescences afin de découvrir les différents tests.  Le code est
dans l'état laissé dans le dernier billet. Regardons le résultat de nose, placé
vous dans la racine du projet, et entrez la commande::

    $ nosetests
    ...
    ----------------------------------------------------------------------
    Ran 3 tests in 0.016s

    OK

On obtient ce qu'on avait avant.

Fichier de configuration
------------------------

Un avantage de nose, c'est qu'il est possible de le faire fonctionner avec un
fichier de configuration (``~/.noserc``) permettant d'enregistrer les
préférences.

On peut ainsi rajouter de la verbosité comme paramètre de défaut, mais
également inclure les doctests. De façon plus général, chaque argument dans la
liste obtenu avec ``nosetests -h`` peut être rajouté dans votre .noserc.
Prenons un exemple::

    [nosetests]
    verbosity=3
    with-doctest=1
    doctest-extention=txt

De cette façon, je n'ai plus à m'occuper de la verbosité, elle est
automatiquement incluse, on va également rajouter un fichier .txt, contenant un
test tout simple en doctest, pour voir qu'il est bien inclue directement.

`tests/test_doctest_chiffres.txt`::

    >>> from chiffres import add
    >>> add(2,5)
    7

Puis, on exécute la suite de test avec la commande ``nosetests``::

    $ nosetests
    test_add_string (test_chiffres.TestChiffres) ... ok
    test_add_with_float (test_chiffres.TestChiffres) ... ok
    test_addition (test_chiffres.TestChiffres) ... ok
    Doctest: test_doctest_chiffres.txt ... ok

    ----------------------------------------------------------------------
    Ran 4 tests in 0.032s

    OK

Les trois tests venant de unitest, auxquels on rajoute doctest, le compte y est.

Framework de test spécifique à Nose
-----------------------------------

Bien que Nose soit compatible avec l'usage de unittest, il comporte également
une version un peu allégé, mais pas moins complète de test. Incluant l'usage de
``setUp()`` et ``tearDown()``, permettant de donner des directives avant et
après le tests, souvent utile à la connexion de base de donnée par exemple. Les
tests n'ont pas besoin d'hériter de unittest.Test.Case, et peut être de simples
fonctions.

Dans cette exemple, on va reproduire dans un nouveau fichier, les tests déjà
écrit avec unittest.

`tests/test_nose_chiffres.py`::

    from chiffres import add
    from nose.tools import raises

    def test_add_int():
        assert add(3, 4) == 7

    def test_add_float():
        assert add(2.01, 1.01) - 3.02 < 0.001

    @raises(ValueError)
    def test_add_chaine():
        add('coin', 'pan')

La première chose qu'on remarque, c'est la légèreté de la syntaxe, l'absence de
classe (bien qu'on aurait pu) et l'usage *d'assert*.

Pour le test de float, je ne crois pas avoir trouvé un almostEqual, j'ai donc
mis le code suivant qui revient un peu près au même donc::

    assert add(2.01, 1.01) - 3.02 < 0.001

L'autre différence notable est pour la gestion de l'exception, qui se fait
maintenant par un décorateur, et l'import correspondant (ne pas l'oublier)::

    @raises(ValueError)

On relance nosetests comme suit::

    $ nosetests
    test_add_string (test_chiffres.TestChiffres) ... ok
    test_add_with_float (test_chiffres.TestChiffres) ... ok
    test_addition (test_chiffres.TestChiffres) ... ok
    Doctest: test_doctest_chiffres.txt ... ok
    test_nose_chiffres.test_add_int ... ok
    test_nose_chiffres.test_add_float ... ok
    test_nose_chiffres.test_add_chaine ... ok

    ----------------------------------------------------------------------
    Ran 7 tests in 0.036s

    OK

Comme prévu, nosetests a parcouru l'arborescence pour trouver les trois
fichiers de tests (test_chiffres.py, test_doctest_chiffres.txt et
test_nose_chiffres.py), dans ce cas les tests passent tous, mais on peut aisément
à partir de là jouer un peu avec, pour voir son comportement. On note
aussi que nosetests ne se soucit pas de passer d'un format (unittest, doctest)
à son propre format de tests, le tout à la volée.

Comme indiqué plus haut, il est possible d'utiliser un setUp() et tearDown(),
c'est à dire d'appeler une fonction avant et après l’exécution de chaque tests,
utile lors de connexion à une base de donnée par exemple, le tout ce fait avec
un décorateur (c'est pas la seul possibilité) voici l'exemple tiré de la
documentation officiel.

::

    @with_setup(setup, teardown)
    def test_something():
        " ... "

Il faut bien entendu rajouter les deux fonctions correspondantes.

Pinocchio, plugin pour Nose
---------------------------

`Pinocchio`_ [3]_ est un plugin pour utiliser nose d'une façon plus proche de
`RSpec`_ [5]_.  L'idée est de rendre les tests un peu plus parlant, un peu de la façon
dont on procède pour le BDD (Behavior Driven Development), il est préférable de se
reporter à quelques documentations à ce sujet si vous n'êtes pas familier avec
ce terme. Pinocchio ne semble pas fonctionner avec Python3, ou du moins, je
n'ai pas réussi.

Installons le plugin::

    sudo pip install pinocchio

Créons un troisième fichier de tests avec des tests plus « parlant »::

    from chiffres import add
    from nose.tools import raises

    def test_should_add_two_integer():
        assert add(3, 4) == 7

    def test_should_add_two_float():
        assert add(2.01, 1.01) - 3.02 < 0.001

    @raises(ValueError)
    def test_should_raise_an_exception_with_two_string():
        add('coin', 'pan')</code>

Deux possibilités, soit on utilise la commande suivante::

    nosetests --with-spec --spec-color --spec-doctests

Ou plus simplement, on rajoute dans son fichier de configuration comme suit::

    [nosetests]
    verbosity=3
    with-doctest=1
    doctest-extension=txt
    with-spec=1
    spec-color=1
    spec-doctests=1

On exécute maintenant (dans l'exemple, je force l'usage pour python 2.7,
n'aillant pas réussi l'autre, et que Arch Linux fonctionne avec Python 3.x par
défaut)

::

    $ nosetests-2.7

    Chiffres
    - add string
    - add with float
    - addition

    test_doctest_chiffres.txt
    - add(2,5) returns 7

    Nose chiffres
    - add int
    - add float
    - add chaine

    Spec chiffres
    - should add two integer
    - should add two float
    - should raise an exception with two string

    ----------------------------------------------------------------------
    Ran 10 tests in 0.040s

    OK

Bien qu'il n'apparaît pas ici, la sortie différencie les erreurs des bons
tests avec les couleurs rouge/vert, et rajoutant un gros (ERROR) si besoin.

L'important à remarquer, c'est la lisibilité des tests pour spec, dont il est
conseillé de faire des phrases qui ont un sens. L'exemple n'est certainement
pas le plus parlant pour ce genre de tests, mais afin d'en garder une
continuité, j'ai préféré garder toujours le même exemple.

Pour donner un exemple de sortie comportant une erreur::

    $ nosetests-2.7

    Chiffres
    - add string
    - add with float
    - addition

    test_doctest_chiffres.txt
    - add(2,5) returns 7

    Nose chiffres
    - add int
    - add float
    - add chaine

    Spec chiffres
    - should add two integer
    - should add two float
    - should raise an exception with two string (ERROR)

    ======================================================================
    ERROR: test_spec_chiffres.test_should_raise_an_exception_with_two_string
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "/usr/lib/python2.7/site-packages/nose-1.0.0-py2.7.egg/nose/case.py",
      line 187, in runTest
        self.test(*self.arg)
      File "/usr/lib/python2.7/site-packages/nose-1.0.0-py2.7.egg/nose/tools.py",
      line 80, in newfunc
        func(*arg, **kw)
      File "/home/nicolas/exo/chiffres/tests/test_spec_chiffres.py", line 15, in test_should_raise_an_exception_with_two_string
        add('coin', 'pan')
      File "/home/nicolas/exo/chiffres/src/chiffres.py", line 5, in add
        raise ValueError
    ValueError

    ----------------------------------------------------------------------
    Ran 10 tests in 0.041s

    FAILED (errors=1)

Pinocchio peut répondre à un besoin de faire des tests un peu différents, si le
plugin fait ce qu'on lui demande, il ne semble pas être très activement
maintenu. La raison est certainement que ce plugin ne correspond plus vraiment
à un besoin, et que l'approche vu dans le prochain chapitre est plus pertinante
pour ce type de développement. On y verra deux projets bien plus à jour et
maintenu. Pour aller plus loin avec ce plugin, [une documentation][4] est à
disposition.

Conclusion
----------

Ce billet devrait faire découvrir un outil fort pratique pour effectuer des
tests unitaires avec Python, et nous en avons vu qu'une petite partie de ses
capacités. Il vient avec beaucoup de fonctionnalités et des plugins tiers. Les
Plugins nativement prévu pour Nose sont compatible avec Python3, ce qui n'est
pas toujours le cas avec les plugins tiers.


.. _`Pinocchio`: https://github.com/infrared/pinocchio
.. _`une documentation`: http://darcs.idyll.org/~t/projects/pinocchio/doc/
.. _`RSpec`: http://en.wikipedia.org/wiki/RSpec

.. [3] https://github.com/infrared/pinocchio
.. [4] http://darcs.idyll.org/~t/projects/pinocchio/doc/
.. [5] http://en.wikipedia.org/wiki/RSpec
