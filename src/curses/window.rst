.. _window:

Déplacement de fenêtre
======================

Petit exercice, pour une approche de la bibliothèque curses en douceur.  Le
but, tout simple, est de créer une petite fenêtre à l'intérieur de la console,
cette fenêtre est visualisable par un carré (4x6), et de la déplacer dans sa
console à l'aide des flèches directionnel. Même si l'émerveillement de voir
apparaître une fenêtre reste limité, ce n'est pas moins un bon exercice afin de
voir quelques fonctions propre à ncurses.

L'approche est organisé étape par étape, avec en fin de chapitre le code complet
de l'application.

Obtenir une petite fenêtre
--------------------------

Dans un premier temps, on va afficher une petite fenêtre et... c'est tout.

::

    import curses
    import curses.wrapper

    def main(scr):
        curses.use_default_colors()
        curses.curs_set(0)
        win = scr.subwin(4, 6, 3, 3)
        win.border(0)
        win.refresh()
        win.getch()

    if __name__ == '__main__':
        curses.wrapper(main)

On rend le tout exécutable avec un chmod +x, puis on regarde le résultat. Une
fenêtre s'affiche, nous sommes émerveillé, on appuis sur une touche, le
programme quitte. Mais déjà quelques explications s'impose, en commençant par
l'appel du ``main()`` qui ne se fait pas comme on a l'habitude de voir.

::

    if __name__ == '__main__':
        curses.wrapper(main)

``curses.wrapper`` est une fonctionnalité de python, permettant d'obtenir
l'initialisation rapide de curses, elle prend en argument une fonction, alors
on a l'habitude de mettre des variables en argument, mais ce n'est pas grave,
on met la fonction ``main`` en argument, il faut noter qu'il n'y a pas besoin des
parenthèses dans ce cas, puisqu'on passe la référence de cette fonction, et non
un message d'exécution de celle ci.

L'autre important avantage d'utiliser le wrapper, c'est de vous laisser la
console dans un état *normal* lorsque vous quittez ou que le programme plante,
sinon, il vous le laisse dans un état qui vous obligerez quasiment à fermer
votre console, car l'affichage y serait très pénible.

La fonction qui est appelé reçois en argument l'écran initialisé, d'où le ``scr``
un peu inattendu dans la fonction de main::

    def main(scr):

Passé ce détail, le reste est assez intuitif.

On initialise la transparence, comme je l'expliquais dans le chapitre
précédent, et on désactive le curseur, car pour dessiner une petite fenêtre,
nous n'en avons pas vraiment besoin.

::

    curses.use_default_colors()
        curses.curs_set(0)

Vient ensuite la création de la fenêtre avec une méthode ``subwin`` attaché à
l'écran ``scr``::

    src.submin(taille_y, taille_x, position_y, position_x)
    win = scr.subwin(4, 6, 3, 3)
    # On y affecte des bordures pour bien voir la fenêtre
    win.border(0)
    # On rafraîchis pour l'affichage.
    win.refresh()
    # On demande de saisir une touche, histoire d'avoir le temps
    # de voir le résultat.
    win.getch()

Faire bouger la fenêtre
-----------------------

Comme le but étant de faire bouger un peu la fenêtre, voyons comment faire
évoluer le code pour cela. Par commodité, je vais bouger le ``main`` dans
sa propre classe. Jetons directement un œil au code.

::

    import curses
    import curses.wrapper

    class MovingWindow(object):

        def __init__(self, scr):
            self.scr = scr
            self.pos = [3, 3]
            self.size = [4, 6]
            self.init_curses_mode()
            self.draw_window()
            self.handle_key_stroke()

        def init_curses_mode(self):
            curses.use_default_colors()
            curses.noecho()
            curses.curs_set(0)

        def draw_window(self):
            self.scr.erase()
            self.win = self.scr.subwin(self.size[0], self.size[1],
                                       self.pos[0], self.pos[1])
            self.win.border(0)
            self.win.refresh

        def handle_key_stroke(self):
            while True:
                ch = self.scr.getch()
                if ch == curses.KEY_DOWN:
                    self.pos[0] += 1
                elif ch == curses.KEY_UP:
                    self.pos[0] -= 1
                elif ch == curses.KEY_LEFT:
                    self.pos[1] -= 1
                elif ch == curses.KEY_RIGHT:
                    self.pos[1] += 1
                elif ch == ord('q'):
                    break
                self.draw_window()

    if __name__ == '__main__':
        curses.wrapper(MovingWindow)

Explications
------------

Dans un premier temps, nous n'appelons plus la fonction ``main``, mais nous
initialisons un objet de la classe ``MovingWindow``.

::

    curses.wrapper(MovingWindow)

Nous créons des attributs, pour avoir la taille (facultatif), mais surtout la
position courante de la fenêtre à afficher, ce qui correspond dans le ``__init__``
aux lignes suivantes::

    self.pos = [3, 3]
    self.size = [4, 6]

Les trois lignes suivantes ne sont que des appels à d'autre méthode de la
classe.

On initialise quelques éléments de ncurses::

    def init_curses_mode(self):
        # Toujours les couleurs transarante
        curses.use_default_colors()
        # On s'assure de ne rien afficher si on écrit
        curses.noecho()
        # On désactive le curseur
        curses.curs_set(0)

La méthode permettant d'afficher la fenêtre n'est pas bien plus compliqué.

::

    def draw_window(self):
        # On efface ce qu'on avait
        self.scr.erase()
        # On créer une nouvelle fenêtre, avec la position et taille
        # indiqué par les attributs
        self.win = self.scr.subwin(self.size[0], self.size[1], self.pos[0], self.pos[1])
        # On remets une bordure
        self.win.border(0)
        # Enfin, on affiche le résultat
        self.win.refresh

La dernière méthode ``handle_key_stroke`` gère les touches, et son fonctionnement
est plutôt simple, ``curses.KEY_UP`` par exemple désigne la touche du haut.
Lorsqu'une des flèches est appuyé, on change les attributs de position en
fonction. En fin de boucle, on affiche le résultat obtenu.

Il est a noter, la ligne suivante::

    elif ch == ord('q'):

On devine facilement qu'il sert à quitter l'application, mais le ``ord`` est
utile pour convertir la lettre en son équivalant numérique, car les touches
saisis sont des chars.

On lance le programme, on joue un peu avec, la fenêtre ce déplace, on est
content... jusqu'à ce que... la fenêtre sort de la console, en faisant planter
le programme. Nous savons ce qu'il nous reste à faire alors, nous assuré que
cette fenêtre ne sorte pas de la console.

Script au complet
-----------------

::

    import curses
    import curses.wrapper

    class MovingWindow(object):

        def __init__(self, scr):
            self.scr = scr
            self.pos = [3, 3]
            self.size = [4, 6]
            self.maxyx = []
            self.init_curses_mode()
            self.draw_window()
            self.handle_key_stroke()

        def init_curses_mode(self):
            curses.use_default_colors()
            curses.noecho()
            curses.curs_set(0)
            self.maxyx = self.scr.getmaxyx()

        def draw_window(self):
            self.scr.erase()
            self.win = self.scr.subwin(self.size[0], self.size[1],
                                       self.pos[0], self.pos[1])
            self.win.border(0)
            self.win.refresh

        def move_down(self):
            if self.pos[0] + self.size[0] < self.maxyx[0]:
                self.pos[0] += 1

        def move_up(self):
            if self.pos[0] > 0:
                self.pos[0] -= 1

        def move_left(self):
            if self.pos[1] > 0:
                self.pos[1] -= 1

        def move_right(self):
            if self.pos[1] + self.size[1] < self.maxyx[1]:
                self.pos[1] += 1

        def handle_key_stroke(self):
            while True:
                ch = self.scr.getch()
                if ch == curses.KEY_DOWN:
                    self.move_down()
                elif ch == curses.KEY_UP:
                    self.move_up()
                elif ch == curses.KEY_LEFT:
                    self.move_left()
                elif ch == curses.KEY_RIGHT:
                    self.move_right()
                elif ch == ord('q'):
                    break
                self.draw_window()

    if __name__ == '__main__':
        curses.wrapper(MovingWindow)

Il n'y a pas énormément de changement, et corresponde à la gestion de la taille
maximal de l'écran. On remarque dans un premier temps, que j'en ai profiter
pour créer autant de méthodes que de mouvement, permettant de gagner en
lisibilité un peu.

La ligne suivante, va retourner la taille de la console dans une turple::

    self.maxyx = self.scr.getmaxyx()

Tout le reste n'est qu'un petit peu de calcul et de logique pour s'assurer que
la fenêtre ne sorte pas.

On pourrait très bien essayer quatre touches qui aurait pour effet d'agrandir la fenêtre par l'un des côtés, toujours en s'assurant de l'espace disponible.
