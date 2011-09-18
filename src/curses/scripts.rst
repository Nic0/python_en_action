.. _scripts:

Les scripts de démo fournis par Python
======================================

Les sources de Python sont fournis avec une poignée de scripts pouvant servir à
apprendre quelques bases. Dans ce billet, nous allons voir ceux
prévu pour le module curses de Python2.7.  Le choix de Python2.7, et non pas
3.2, est du au fait qu'il y a plus d'exemples pour curses dans celui-ci.

Sans plus attendre, on prend les sources de Python, avec quelques commandes
comme suit:

::

    $ wget http://www.python.org/ftp/python/2.7.2/Python-2.7.2.tar.bz2                               
    $ tar xf Python-2.7.2.tar.bz2 
    $ cd Python-2.7.2/Demo/curses 
    $ ls
    life.py*  ncurses.py  rain.py  README  repeat.py*  tclock.py  xmas.py

Ok, on voit quelques scripts disponible dont la curiosité nous pousse à les essayer tous.

Petite précision, si vous prenez Python3.2, les scripts ne se
trouvent plus au même endroit, mais dans `Python-3.2/Tools/demo/`, et on voit
qu'il y en a nettement moins, sauf s'ils sont cachés ailleurs.

Jetons un œil sur le contenu de chacun.

README
------

Fournis un petit récapitulatif, moins complet que dans ce billet
cela dit, et quelques crédits.

ncurses.py
----------

- 273 lignes

Un bon exemple pour montrer l'intérêt du module *curses.panel*,
qui est une forme de fenêtre un peu particulière, car elle permet de gérer le
super-positionnement les unes par rapport aux autres. Le programme positionne
simplement des panels, et passe à un positionnement prédéfini à chaque frappe
de touche.

Une petite remarque sur le script, on y voit deux constantes.
Cependant, elles ne figurent pas sur la documentation, ce
qui m'a plutôt surpris.

* ncurses.COLS
* ncurses.LINES

rain.py
-------

- 93 lignes

Un script un peu plus simple dont le but est d'afficher des dessins
asciiart, dont la suite représente une goutte s’éclatant. Idéal pour les
premiers pas avec Python et ncurses.

tclock.py
---------

- 148 lignes

De conception plus avancé, ce script reproduit une horloge à aiguille, avec
même une trotteuse représenté par le point rouge. Assez amusant.

xmas.py
-------

- 906 lignes

Ce script correspond à une animation d'asciiart, d'où le nombre
important de ligne, il est amusant de le regarder, mais n'apporte peut être pas
énormément pour l'usage ncurses je crois.

life.py
-------

- 216 lignes

Une jolie adaptation du jeu de la vie de Conway en 200 lignes, un grand
classique de la programmation et de la communauté de hacker, dont j'en ai fait
une présentation sur [ce billet][2]. Ce script peut intéresser pour le côté
curses, mais également pour lire une implémentation simple de ce jeu.

repeat.py
---------

- 58 lignes

Le but est de passer en argument une commande, dont l'affichage sera rafraîchis
toute les secondes. On peut essayer par exemple::

    repeat.py uptime


Voilà un petit tour d'horizon des quelques programmes de démonstration que
fournis Python, ils peuvent être utile pour se faire la main. Le but du billet
n'était pas d'en faire une description détaillé, mais surtout de motiver à
aller voir de plus près le code. À vos éditeurs !
