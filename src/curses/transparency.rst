.. _transparency:

Transparence avec Ncurses
=========================

J'ai recherché sur le net comment avoir la transparence avec Python et le
module curse, mais l'information n'était pas très clair, ni dans la
documentation officiel, ni sur le web. C'est en lisant le code des autres que
j'ai trouvé.

Donc oui, il est possible d'avoir la transparence avec le module curses de
défaut de Python. Il fonctionne avec les versions 2.7.x et 3.2.x.

Attention de ne pas appeler votre fichier curses.py, comme lorsqu'on fait des
essais, on a tendance à appeler le fichier avec un nom parlant, mais cela
interférerai avec l'import du module curses.

Cet exemple est également un bon point de départ pour afficher un mot avec
ncurses.

Le code
-------

Un exemple complet, qui affiche un gros ``Hello World`` en rouge, et avec fond
transparent::

    import curses

    def main():

        scr = curses.initscr()
        curses.start_color()
        if curses.can_change_color():
            curses.use_default_colors()
            background = -1
        else:
            background = curses.COLOR_BLACK

        curses.init_pair(1, curses.COLOR_RED, background)
        scr.addstr(2, 2, 'Hello World', curses.color_pair(1))
        scr.getch()

    if __name__ == '__main__':
        main()

Quelques explications
---------------------

::

    if curses.can_change_color():
        curses.use_default_colors()

La première ligne s'assure que le terminal est capable d'afficher la
transparence, et la seconde l'active, en assignant la valeur ``-1`` à la
variable background. Si le terminal ne supporte pas la transparence, on
remplace par un fond noir.
