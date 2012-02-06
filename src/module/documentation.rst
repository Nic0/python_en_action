.. _documentation:

Consulter la documentation en local
===================================

L'idée est de lire la documentation de tous les modules dans un navigateur,
qu'ils soient natifs à Python ou installés implicitement, le tout en local.
Plusieurs avantages à ça :

- Avoir toujours la documentation correspondante à la version installée
- Naviguer rapidement entre les modules tiers, installé par l'utilisateur

C'est une façon assez classique de procéder, qu'on retrouve dans certains
langages. J'en écrivais un billet `pour ruby`_ [1]_ de ce même procédé. On
retrouve également pour Perl, `Perldoc-server`_ [2]_.

Cependant, je le trouve vraiment moins bien fait que celui de Ruby, qui a pour
avantage d'être bien plus facilement navigable, la possibilité d'afficher le
code correspondant (vraiment utile). Sans parler de l'esthétisme global bien
meilleur. Il est dommage qu'il ne soit pas un peu plus abouti que ça.

Le fonctionnement est très simple, on lance le serveur comme suit::

    $ pydoc -p 1337
    Server ready at http://localhost:1337/
    Server commands: [b]rowser, [q]uit
    server>

Il vous suffit à présent de se rendre sur http://localhost:1337 et de naviguer
entre les différents modules affichés.

Le choix du port est arbitraire.

.. _`pour ruby`: http://www.nicosphere.net/lire-la-documentation-rdoc-des-gem-avec-un-navigateur-2239/
.. _`Perldoc-server`: http://search.cpan.org/dist/Perldoc-Server/

.. [1] http://www.nicosphere.net/lire-la-documentation-rdoc-des-gem-avec-un-navigateur-2239/
.. [2] http://search.cpan.org/dist/Perldoc-Server/
