.. _menu:

Menu navigable
==============

Ce que l'on veut
----------------

Deux scripts sont proposés dans ce chapitre, le second en est une extension.

- Un menu complet, dans lequel nous pouvons nous déplacer à l'aide de flèche,
  et sélectionner l'un des éléments. L'élément du menu sera mis en surbrillance
  en choisissant une couleur différente (ici, il sera rouge). Il faudra
  s'assurer qu'on ne dépasse pas les limites du menu.
- Au lieu d'afficher tout le menu, seuls quelques éléments seront affichés, mais
  la liste doit être navigable entièrement, on s'attend donc à voir apparaitre
  les éléments qui n'étaient pas affichés tout en se déplacent dans la liste. La
  navigation sera naturelle.

Menu entièrement affiché
------------------------

::

    import curses
    import curses.wrapper

    class Menu(object):

        menu = [
            'item 0', 'item 1', 'item 2', 'item 3',
            'item 4', 'item 5', 'item 6', 'item 7',
        ]

        def __init__(self, scr):
            self.scr = scr
            self.init_curses()
            self.item = { 'current': 0 }
            self.draw_menu()
            self.handle_keybinding()

        def init_curses(self):
            curses.curs_set(0)
            curses.use_default_colors()
            curses.init_pair(1, curses.COLOR_RED, -1)

        def draw_menu(self):
            i = 2
            for element in self.menu:
                if element == self.get_current_item():
                    self.scr.addstr(i, 2, element, curses.color_pair(1))
                else:
                    self.scr.addstr(i, 2, element)
                i += 1
            self.scr.refresh()

        def get_current_item(self):
            return self.menu[self.item['current']]

        def navigate_up(self):
            if self.item['current'] > 0:
                self.item['current'] -= 1

        def navigate_down(self):
            if self.item['current'] < len(self.menu) -1:
                self.item['current'] += 1

        def show_result(self):
            win = self.scr.subwin(5, 40, 2, 10)
            win.border(0)
            win.addstr(2, 2, 'Vous avez selectionné %s'
                       % self.menu[self.item['current']])
            win.refresh()
            win.getch()
            win.erase()

        def handle_keybinding(self):
            while True:
                ch = self.scr.getch()
                if ch == curses.KEY_UP:
                    self.navigate_up()
                elif ch == curses.KEY_DOWN:
                    self.navigate_down()
                elif ch == curses.KEY_RIGHT:
                    self.show_result()
                elif ch == ord('q'):
                    break
                self.draw_menu()

    if __name__ == '__main__':
        curses.wrapper(Menu)

Si vous avez bien suivi le précédent chapitre, celui-ci doit aller tout seul.
Puisqu'il n'y a pas beaucoup de nouveautés.

Le choix de `self.item = { 'current': 0 }` est surtout dû à la seconde partie
qu'on va voir après, il n'est effectivement pas très justifié ici de prendre un
dictionnaire pour un seul élément.

Ce que l'on souhaite surtout, c'est garder une indication, un pointeur, sur
l'élément courant du menu, pour savoir où on en est dans le menu. Il faut
également s'assurer qu'on n'a pas dépassé le début ou la fin du menu, au quel
cas, il faudrait empêcher d'aller plus loin.

Menu déroulant
--------------

::

    import curses
    import curses.wrapper

    class Menu(object):

        menu = [
            'item 0', 'item 1', 'item 2', 'item 3',
            'item 4', 'item 5', 'item 6', 'item 7',
        ]

        def __init__(self, scr):
            self.scr = scr
            self.init_curses()
            self.item = {
                    'current': 0,
                    'first':   0,
                    'show':    5,
                  }
            self.draw_menu()
            self.handle_keybinding()

        def init_curses(self):
            curses.curs_set(0)
            curses.use_default_colors()
            curses.init_pair(1, curses.COLOR_RED, -1)

        def draw_menu(self):

            first = self.item['first']
            last = self.item['first'] + self.item['show']
            menu = self.menu[first:last]

            i = 2
            for element in menu:
                if element == self.get_current_item():
                    self.scr.addstr(i, 2, element, curses.color_pair(1))
                else:
                    self.scr.addstr(i, 2, element)
                i += 1
            self.scr.refresh()

        def get_current_item(self):
            return self.menu[self.item['current']]

        def navigate_up(self):
            if self.item['current'] > 0:
                self.item['current'] -= 1
                if self.item['current'] < self.item['first']:
                    self.item['first'] -= 1

        def navigate_down(self):
            if self.item['current'] < len(self.menu) -1:
                self.item['current'] += 1
                if self.item['current'] >= self.item['show'] + self.item['first']:
                    self.item['first'] += 1

        def show_result(self):
            win = self.scr.subwin(5, 40, 2, 10)
            win.border(0)
            win.addstr(2, 2, 'Vous avez selectionné %s'
                       % self.menu[self.item['current']])
            win.refresh()
            win.getch()
            win.erase()

        def handle_keybinding(self):
            while True:
                ch = self.scr.getch()
                if ch == curses.KEY_UP:
                    self.navigate_up()
                elif ch == curses.KEY_DOWN:
                    self.navigate_down()
                elif ch == curses.KEY_RIGHT:
                    self.show_result()
                elif ch == ord('q'):
                    break
                self.draw_menu()

    if __name__ == '__main__':
        curses.wrapper(Menu)

La particularité est surtout de ne pas afficher tout le menu, il est ainsi
découpé avant d'être affiché, ce n'était pas la seul possibilité d'aborder le
problème.

::

    first = self.item['first']
    last = self.item['first'] + self.item['show']
    menu = self.menu[first:last]

L'autre point où il faut faire attention, c'est de bien s'assurer que les méthodes de
navigation fassent ce qu'on attend. Par commodité, j'ai choisi un petit
dictionnaire ``item`` pour repérer certains emplacements clé du menu, comme le
nombre d'éléments affichés (show).
