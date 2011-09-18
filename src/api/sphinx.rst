.. _sphinx-doc:

Générateur de documentation Sphinx
==================================

Lorsqu'on utilise un projet ou une bibliothèque, il est souvent agréable de
générer la documentation localement, plusieurs raisons à ça.

- Temps de réaction aux chargement de pages plus rapide.
- Pas besoin de connexion Internet pour consulter la documentation, pouvant
  être utile à certain (train, déplacement, Internet coupé par Hadœpi...)
- Pas de « mince le site est down... »
- Pour les petit projet, la documentation sur le site ne correspond pas
  forcément à l'actuel car le développeur ne prend pas forcément le temps de
  mettre à jour. Et je pense qu'on est plus d'un à avoir le reflex de comparer
  le numéro de version entre le site et l'api réellement utilisé.

Par chance, les projets basé sur Python utilises presque tous un même outil
pour générer la documentation, `Sphinx`_ [1]_. Consistant à parser des fichiers
en ReStructuredText (extention .rst). Spinx étant lui même écrit en Python, et
s'appuyant sur pigments pour la coloration syntaxique.

Pour Arch Linux, Sphinx est disponible dans [community], il est également
disponible pour Ubuntu et Debian, et très certainement pour toutes autres
distributions tant ce projet est un classique. Il est par ailleurs utilisé pour
générer la documentation en ligne officielle de Python.

::

    pacman -S python-sphinx
    apt-get install python-sphinx

Le fonctionnement de sphinx est simple, un utilitaire règle les détails en
écrivant un fichier ``Makefile``, pour lequel il suffira d'utiliser avec un make.
Le fichier ``Makefile`` peut être générer avec la commande
``sphinx-quickstart`` par exemple.

Par exemple, prenons Lettuce comme projet, un projet pour les tests unitaires,
qui sera vu par la suite. C'est un projet permettant de gérer les tests, qui
sera vu un peu plus en détail dans la partie tests unitaires.

::

    $ git clone git://github.com/gabrielfalcao/lettuce.git
    $ cd lettuce/docs
    $ make html
    rm -rf _build/*
    sphinx-build -b html -d _build/doctrees   . _build/html
    Making output directory...
    Running Sphinx v1.0.7
    loading pickled environment... not yet created
    building [html]: targets for 17 source files that are out of date
    updating environment: 17 added, 0 changed, 0 removed
    reading sources... [100%] tutorial/tables                                                                                      
    looking for now-outdated files... none found
    pickling environment... done
    checking consistency... /home/nicolas/test/lettuce/docs/tutorial/django.rst::
    WARNING: document isn't included in any toctree
    done
    preparing documents... done
    writing output... [100%] tutorial/tables                                                                                       
    writing additional files... genindex search
    copying images... [100%] tutorial/flow.png                                                                                     
    copying static files... done
    dumping search index... done
    dumping object inventory... done
    build succeeded, 1 warning.

    Build finished. The HTML pages are in _build/html.

Comme indiqué, le résultat se trouve dans ``_build/html`` dont il suffit de
lancer ``index.html``.

Il est donc possible de générer rapidement la documentation avec un simple::

    make html

D'autre format sont supporté, notamment le PDF. Cette opération passe par la
génération d'un document LaTeX.

::

    make latex
    make latexpdf

Pour anecdote, le présent document est généré en utilisant Sphinx et le make
latexpdf.

Pour générer le fichier Makefile, Sphinx fournit un utilitaire en ligne de
commande, permettant, une fois quelques questions basique répondu, de générer
le Makefile. Heureusement les questions sont assez simple, et les valeurs par
défaut sont souvent suffisante.

::

    sphinx-quickstart

Ce billet est une très courte introduction de ce que Sphinx peut faire. Cet
outil vaut à être connu.

.. _`Sphinx`: http://sphinx.pocoo.org/
.. [1] http://sphinx.pocoo.org/
