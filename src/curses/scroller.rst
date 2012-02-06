.. _scroller:

Visualiser et scroller un fichier
=================================

On va essayer de reproduire sommairement le comportement de la
commande **less**, qui permet de naviguer en scrollant dans un fichier, et de
quitter, même si la commande permet plus que ça, c'est du moins la
fonctionnalité qu'on cherche ici à reproduire. En bonus, nous afficherons le
numéro de ligne.

L'utilité de ce script en est réduite à l'intérêt d'apprendre à le faire, ni
plus, ni moins. Le fichier qu'on cherche à visualiser sera renseigné en ligne de
commande, préparez un fichier de quelques centaines de lignes qui servira de
fichier test.

Les touches sont: 'j' et 'k' pour ce déplacer dans le fichier, et 'q' pour
quitter.

.. note::

    J'ai utilisé ici **Python 3** pour écrire le script, bien que ça ne fasse que
    peu de différence, il est utile de changer quelques détails, pour Python2.
    Il est cependant intéressant ici de diversifier un peu les exemples.

Idée générale
-------------

Nous allons utiliser deux fenêtres, ou cadre pour faire cela.

- *scr* : la console, utilisée de façon classique comme dans les derniers billet,
  mais qui n'aura que peu d'utilité ici.
- *pad* : une fenêtre de taille supérieure à la console, contenant la totalité du
  fichier texte à afficher et scroller.

Pour comprendre le fonctionnement du ``pad``, imaginons que j'ai un petit cadre
(la console) derrière lequel je place une grande feuille (pad) bien plus grand
que le cadre, si je déplace la feuille, tout en ne regardant que le cadre,
j'aurais l'impression que le contenu dans le cadre défile, et c'est un
peu ce comportement qui est reproduit ici. Le texte reste fixe par rapport au
*pad*, mais l'impression lorsqu'on bouge le *pad* par rapport à la console
(window), c'est d'avoir un texte qui défile.

Voici le script entier dans un premiers temps, sur lequel j'ai placé des
commentaires tout du long pour la compréhension, je reviens tout de même sur le
*pad* à la suite de ce billet.

Le script complet
-----------------

::

    import sys
    import curses
    import curses.wrapper

    class Scroll(object):

        def __init__(self, scr, filename):
            self.scr = scr
            self.filename = filename
            # On garde en mémoire la première ligne à afficher
            self.first_line = 0

            self.run_scroller()

        def run_scroller(self):
            self.init_curses()
            self.init_file()
            self.init_pad()
            self.key_handler()

        def init_curses(self):
            '''Quelques banalités pour le fonctionnement de curses
            Voir les billets précédents s'il y a des doutes dessus
            '''
            curses.use_default_colors()
            curses.curs_set(0)
            self.maxyx = self.scr.getmaxyx()

        def init_file(self):
            '''On ouvre le fichier, en gardant son contenu, sous forme de tableau
            avec une ligne par entrée, on compte également le nomble de lignes
            afin de savoir la hauteur utile pour le "pad"
            '''
            f = open(self.filename, 'r')
            self.content = f.readlines()
            self.count = len(self.content)+1
            f.close()

        def init_pad(self):
            '''On crée le pad, dans lequel on affiche ligne par ligne le contenu
            du fichier, avec son numéro de ligne. Le pad est finalement affiché
            avec l'appel à la méthode refresh_pad qu'on va voir en dessous.
            '''
            self.pad = curses.newpad(self.count, self.maxyx[1])

            for i, line in enumerate(self.content):
                self.pad.addstr(i, 0, '{0:3} {1}'.format(i+1, line))

            self.refresh_pad()

        def refresh_pad(self):
            '''Le plus gros du concept est ici, voir le billet pour plus
            d'explication
            '''
            self.pad.refresh(self.first_line, 0, 0, 0,
                    self.maxyx[0]-1, self.maxyx[1]-1)

        def key_handler(self):
            '''Une boucle classique, afin d'interpréter les touches'''
            while True:
                ch = self.pad.getch()
                if ch == ord('j'):
                    self.scroll_down()
                elif ch == ord('k'):
                    self.scroll_up()
                elif ch == ord('q'):
                    break
                self.refresh_pad()

        def scroll_down(self):
            '''On scroll, tout en s'assurant de l'affichage restant'''
            if self.maxyx[0] + self.first_line < self.count:
                self.first_line += 1

        def scroll_up(self):
            '''On scroll, en s'assurant qu'on est pas déjà en début
            de fichier.'''
            if self.first_line > 0:
                self.first_line -= 1

    if __name__ == '__main__':

        try:
            # On essaie de lire l'argument fournis en console
            # correspondant au nom du fichier
            filename = sys.argv[1]
        except IndexError as e:
            # Si aucun argument est trouvé, on affiche l'usage
            print('Erreur: {}'.format(e))
            print('Usage:  {} filename'.format(sys.argv[0]))
            sys.exit(0)
        # On appelle la classe avec le wrapper curses.
        curses.wrapper(Scroll, filename)

Quelques informations supplémentaires
-------------------------------------

Comme promis, je reviens sur la méthode suivante::

    def refresh_pad(self):
        self.pad.refresh(self.first_line, 0, 0, 0,
                self.maxyx[0]-1, self.maxyx[1]-1)

Pour la signification des arguments, en les prenant deux par deux:

* coordonnées y, x de départ sur le pad. Pour reprendre l'exemple plus haut de
  la feuille, ces coordonnées précisent à quel point je vais commencer à afficher
  le contenu. Si j'ai par exemple 2, 10, ça signifie que je commence à afficher
  à la ligne 2 et à partir du 10ème caractère, Je ne sais pas encore où je
  vais afficher, ni quel quantité, mais je connais le commencement de mon
  contenu. Dans l'exemple, on veux bien sûr afficher dès le 1er caractère et
  commencer à la première ligne, pour scroller, il suffira d'incrémenter la
  première ligne.
* coordonnées y, x de départ sur la console. Puisque je n'affiche qu'une partie,
  je décide donc de commencer l'affichage tout en haut à gauche de ma console,
  c'est à dire 0, 0.
* coordonnées y, x de fin sur la console. Pour avoir notre cadre, il faut le
  début (voir ci-dessus) et la fin, correspondant au coin inférieur droit de la
  console, obtenu grâce à la fonction ``scr.getmaxyx()``.

Je pense qu'avec les explications, le système de scroll est plus clair, on peut
noter que le script est assez simplifié, qu'il manque par exemple la gestion de
redimension de la console.
