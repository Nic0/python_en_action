.. _unittest-ch:

Unittest
========

Introduction
------------

Quelques mots pour les personnes n'aillant jamais écrit de tests. Il y a
beaucoup d'avantages à en écrire, par exemple être plus confient lors
d'amélioration de code existant, être sûr de ne rien « casser ». Il s'agit bien
souvent (pour les tests unitaires du moins) de tester une petite partie d'un
code, afin de s'assurer qu'on obtient les valeurs auxquelles on s'attendait. Ce
billet n'est pas là pour faire une introduction sur les avantages de tester son code,
mais sachez que c'est une pratique indispensable et courante pour tout code.

Nous allons utiliser ici un module, `unittest`_ [1]_ qui est disponible
directement avec Python. J'aurais pu commencer avec `doctest`_ [2]_, également
un module natif à Python, permettant d'écrire les tests directement sous forme
de commentaire, pour les personnes intéressées, la documentation officiel est
certainement un bon endroit pour commencer. Ce billet n'est qu'un rapide aperçu
de unittest, et ne se veux pas d'être complet.

Le code
-------

Commençons par un exemple _très_ simple. En créant une fonction `add()` qui...
additionne deux chiffres !

Créez un répertoire de test, dans lequel on crée la fonction suivante dans un
fichier `chiffres.py` ::

    def add (a, b):
        return a+b

On écrit maintenant le test correspondant ::

    import unittest
    from chiffres import add

    class TestChiffres(unittest.TestCase):

        def test_addition(self):
            result = add(36, 6)
            self.assertEqual(result, 42)

    if __name__ == '__main__':
        unittest.main()

Explications
------------

On import le module pour unittest, ainsi que la fonction qu'on a crée::

    import unittest
    from chiffres import add

On crée une classe qui doit commencer par Test, et héritant de
unittest.TestCase, correspondant à la ligne suivante::

    class TestChiffres(unittest.TestCase):

La fonction suivante est celle qui est utilisé pour le test, et doit commencer
par ``test_`` pour qu'elle soit pris en compte. Le plus important, c'est de
vérifier la valeur que la fonction retourne avec celle auquel on s'attend, et
c'est ce que fais la ligne suivante ::

    self.assertEqual(result, 42)

La dernière partie, correspond à l'appel de la class par unittest::

    if __name__ == '__main__':
        unittest.main()

Content de notre premier test, on fait un essai::

    $ python test_chiffres.py 
    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.000s

    OK

Maintenant, on veut s'assurer que la fonction lève une exception si donne comme
argument une chaine de caractères, on écrit donc le test::

    import unittest
    from chiffres import add

    class TestChiffres(unittest.TestCase):

        def test_addition(self):
            result = add(36, 6)
            self.assertEqual(result, 42)

        def test_add_string(self):
            self.assertRaises(ValueError, add, 'coin', 'pan')

    if __name__ == '__main__':
        unittest.main()

On utilise assertRaises, avec comme argument, l'exception attendu, la fonction
et une liste d'argument fournis à la fonction. On essaye le test::

    $ python test_chiffres.py
    F.
    ======================================================================
    FAIL: test_add_string (__main__.TestChiffres)
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "test_chiffres.py", line 13, in test_add_string
        self.assertRaises(ValueError, add, 'coin', 'pan')
    AssertionError: ValueError not raised by add

    ----------------------------------------------------------------------
    Ran 2 tests in 0.001s

    FAILED (failures=1)

Le test échoue, on s'y attendait un peu, puisque le résultat retourné est la
concaténation des deux chaines, c'est à dire 'coinpan'.

On écrit un bout de code qui lèvera une erreur en cas de chaines passé en
argument, comme suit::

    def add (a, b):
        return int(a)+int(b)

On execute maintenant le test::

    $ python test_chiffres.py
    ..
    ----------------------------------------------------------------------
    Ran 2 tests in 0.000s

    OK

Très bien, c'est ce qu'on voulait. Mais que ce passe-t-il si on envoie comme
argument des floats (nombre à virgules) ?

Écrivont le test, qu'on rajoute à la suite, et regardons ::

    def test_add_with_float(self):
        result = add(1.01, 2.001)
        self.assertEqual(result, 3.011)</code>


::

    $ python test_chiffres.py
    .F.
    ======================================================================
    FAIL: test_add_with_float (__main__.TestChiffres)
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "test_chiffres.py", line 17, in test_add_with_float
        self.assertEqual(result, 3.011)
    AssertionError: 3 != 3.011

    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    FAILED (failures=1)</code>

La ligne qui nous renseigne ici est surtout la suivante::

    AssertionError: 3 != 3.011

Et effectivement int(a)+int(b) ne retournera pas de float, changeons le code
pour que le test passe maintenant avec succès::

    def add (a, b):
        if isinstance(a, basestring) or isinstance(b, basestring):
            raise ValueError
        return a+b</code>

.. note::

    basestring, qui ici nous permet de comparer l'objet à une chaîne de
    caractère, est spécifique à Python 2, puisque dans la version 3, elles ne
    sont plus géré de la même manière.

On exécute une dernière fois le test::

    $ python test_chiffres.py   
    ...
    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    OK

Voilà, grâce au test, on sait que la fonction à un comportement auquel on
s’attend, on pourrait essayer de faire quelques modification, mais avec les
tests, on s'assure que son comportement ne change pas de façon inattendu.

Note: Pour les floats et à cause du caractère parfois approximatif de leur
résultat, il est sûrement préférable d'utiliser ``assertAlmostEqual`` au lieu de
``assertEqual``

Précisions
----------

Verbosité
'''''''''

On peut rajouter de la verbosité pour les tests, avec l'une des deux méthodes
suivante:

- Directement en ligne de commande

::
    $ python -m unittest -v test_chiffres.py
    test_add_string (test_chiffres.TestChiffres) ... ok
    test_add_with_float (test_chiffres.TestChiffres) ... ok
    test_addition (test_chiffres.TestChiffres) ... ok

    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s

    OK

- Directement dans le code

En remplacent le unittest.main() comme suit::

    if __name__ == '__main__':
        suite = unittest.TestLoader().loadTestsFromTestCase(TestChiffres)
        unittest.TextTestRunner(verbosity=2).run(suite)</code>

Liste des asserts
'''''''''''''''''

On a vu jusqu'ici assertEqual et assertRaises, il en existe bien d'autre, dont
voici la liste.

- assertEqual  
- assertNotEqual  
- assertTrue
- assertFalse
- assertIs
- assertIsNot
- assertTruetIsNone
- assertIsNotNone
- assertIsNottIn
- assertNotIn
- assertIsInstance
- assertNotIsInstance
- assertRaises
- assertRaisesRegex
- assertWarns
- assertWarnsRegex

Attention cependant à la compatibilité entre les versions de Python, pas mal de
nouveautés on été introduite dans Python2.7 par exemple, ou 3.2.
Pour aller plus loin, il est préférable de se référer à la documentation
officiel.

Discover
''''''''

Ce module vient maintenant avec une nouvelle option, 'discover', permettant
d'automatiser la recherche des différents fichier de test, je ne m'étendrais
pas sur ce détail, et on verra pourquoi dans le prochain billet.

::

    $ python -m unittest discover
    ...
    ----------------------------------------------------------------------
    Ran 3 tests in 0.000s

    OK

.. _`unittest`: http://docs.python.org/library/unittest.html
.. _`doctest`: http://docs.python.org/library/doctest.html

.. [1] http://docs.python.org/library/unittest.html
.. [2] http://docs.python.org/library/doctest.html
