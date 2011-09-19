================
Python en Action
================

**Préface**

Ce document est issue principalement de billets pris sur mon blog, revu pour
l'occasion. Aillant depuis quelques temps cumulé des suites de petits tutoriels
un peu sous forme de recettes, je pense qu'il pouvait être intéressant de
les réunir sous un format PDF. Ce format s'y prêtait plutôt bien, du fait
d'avoir traité quelques thèmes sous différents aspects, notamment avec la
partie Ncurses et Tests Unitaires. Le style est certainement plus celui
utilisé pour un blog, bien que j'en ai apporté quelques modifications pour
garder une cohérence.

Le document est sous licence libre `Créative Commun CC-BY-SA` [1]_ et puisqu'un
PDF ne serait pas vraiment considéré comme libre sans les sources, elles
peuvent être téléchargées sur `Github`_ [2]_. Vous avez le droit de
le télécharger, le redistribuer, le modifier et même de l'imprimer pour le lire
dans les toilettes. Merci de copier ce document ! Je demande juste d'être 
cité, Nicolas Paris, et de faire un lien vers mon blog,
http://www.nicosphere.net.

Vous pouvez me contacter via email: nicolas.caen@gmail.com

La version de Python utilisé est généralement la 2.7, parfois la 3.2. J'ai
généralement fait mon possible pour que ce soit clair. Une précision tout de
même, étant sous Arch Linux, Python par défaut est le 3.2.x, mais j'ai essayé
autant que possible de garder dans le texte Python 2.7 comme celui de défaut,
pour éviter les confusions de ceux ne connaissant pas cette spécificité d'Arch
Linux.

J'espère que ce document trouvera lecteurs, bien que ce soit mon premier
"ebook".

-- Nicolas Paris

.. _`Github`: https://github.com/Nic0/python_en_action
 
.. [1] http://creativecommons.org/licenses/by-sa/2.0/fr/
.. [2] https://github.com/Nic0/python_en_action

Utilisation de Modules
----------------------

.. toctree::
    :maxdepth: 3

    src/module/documentation
    src/module/deque
    src/module/log
    src/module/urllib
    src/module/configparser

Python et Ncurses
-----------------

.. toctree::
    :maxdepth: 2

    src/curses/transparency
    src/curses/window
    src/curses/menu
    src/curses/scroller
    src/curses/scripts

Utilisation d'API
-----------------

.. toctree::
    :maxdepth: 2

    src/api/sphinx
    src/api/twitter
    src/api/googl
    src/api/requests
    src/api/scrapy
    
Les Tests Unitaires
-------------------

.. toctree::
    :maxdepth: 2

    src/tests/unittest
    src/tests/nose
    src/tests/lettuce
    src/tests/coverage
    src/tests/watchr
