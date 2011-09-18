.. _configparser:

Parser un fichier de configuration
==================================

Il y a de multiples raisons de mettre en place un fichier de configuration pour
une application. Nous allons voir comment faire cela grâce à un module de
python appelé ConfigParser [1]_ qui sert... à parser une configuration de façon
très simple. Il est dans l'esprit de ce langage, de ne pas recréer un module
afin de prendre en compte un fichier de configuration, fonctionnalité très
courante.

Pourquoi un fichier de configuration ?
--------------------------------------

Utiliser un fichier de configuration permet d'obtenir une application plus
souple, si vous faites un choix arbitraire dans votre programme, demandez vous
s'il ne serait pas plus efficace de l'extraire, et de le mettre en tant que
paramètre, modifiable par l'utilisateur. D'une part il est plus agréable de les
manipuler de façon séparé et regrouper, et surtout cela évite qu'un utilisateur
voulant changer une variable le fasse en fouillant dans le code, au risque de
changer un mauvais paramètre par mégarde dans votre code source. 

Présentation du module ConfigParser
-----------------------------------

Puisqu'un module existe pour Python, autant l'utiliser. Un des avantages est
d'avoir la possibilité de commenter vos configuration en commencent par `# (dièse)` ou
`; (point-virgule)` une ligne, celle-ci sera ignoré.  Le fichier suit une structure assez
simple, qu'on retrouve régulièrement, basé sur un système de section, et
d'argument/valeurs. Pour résumer dans un schéma::

    [my_section]
    coin = pan
    answer = 42

On peut de la sorte avoir plusieurs noms de section et ce n'est là que pour donner
une idée basique du module.

Lire dans un fichier de configuration
-------------------------------------

Premier exemple tout simple, nous allons lire dans un fichier quelques
arguments, pour les afficher en retour dans la console. Par commodité, on va
placer le fichier de configuration à l'endroit où sera exécuté le programme. Le
nom du fichier serra ici *conf.rc* mais aucune importance, du moment qu'on s'y
retrouve.

`conf.rc`::

    [user]
    nom = Dupond
    age = 42

Il nous reste plus qu'à écrire le bout de code en python pour interpréter ça.

`config.py`::

    import ConfigParser

    def main ():

        config = ConfigParser.RawConfigParser()
        config.read('conf.rc')

        nom = config.get('user', 'nom')
        age = config.get('user', 'age')

        print 'Bonjour M. %s, vous avez %s ans' % (nom, age)

    if __name__ == '__main__':
        main()

On le rend exécutable::

    chmod +x config.py

Puis on le lance::

    $ ./config.py  
    Bonjour M. Dupond, vous avez 42 ans

Ça fait ce qu'on voulais, mais voici quelques explications::

    import ConfigParser

On importe le module, rien de compliqué ici.

::

    config = ConfigParser.RawConfigParser()

Sûrement la ligne la plus obscure du code, on instancie simplement un objet
(config) de la classe RawConfigParser venant du module ConfigParser. Et c'est
grâce à cet objet que tout va se jouer.

::

    config.read('conf.rc')

Nous indiquons ici quel est le fichier de configuration que nous utilisons.
Pour simplifier l'affaire, le fichier est en relatif (par rapport à l'endroit
d'où est exécuté le programme), et surtout, il n'y a pas de vérification que le
fichier existe, ni qu'il est lu correctement. Je suis sûr que vous trouverez à
le faire de vous même.

::

    nom = config.get('user', 'nom')

C'est là qu'on lit vraiment les variables, un détail à noté, tout comme on peut
le deviner par la suite, la variable age est considéré comme une chaine de
caractère, et non comme un nombre à part entière, dans l'état il ne serait pas
possible d'effectuer des calcules dessus, mais pouvant être convertie facilement
de la sorte::

    age = int(config.get('user', 'age'))

De la sorte, age est maintenant effectivement un entier, et nous pourrions en
faire toute sorte de calculs afin de trouver l'âge du capitaine.

Je ne pense pas que le reste ait besoin de plus d'explication, plusieurs
remarques cependant.

- Le plus important, on remarque qu'il est simple d'interpréter de la sorte un
  fichier de configuration, le tout en une poignée de lignes seulement.
- Il n'y a aucune gestion d'erreur ici, s'il manque un argument, ou même la
  section, le programme va planter lamentablement... Mais nous allons voir ça
  maintenant.

Gestion d'erreurs
-----------------

Nous allons voir deux cas ici.

try/except block
''''''''''''''''

::

    import sys
    import ConfigParser

    def main ():

        config = ConfigParser.RawConfigParser()
        config.read('conf.rc')
        try:
            nom = config.get('user', 'nom')
            age = config.get('user', 'age')
        except ConfigParser.Error, err:
            print 'Oops, une erreur dans votre fichier de conf (%s)' % err
            sys.exit(1)

        print 'Bonjour M. %s, vous avez %s ans' % (nom, age)

    if __name__ == '__main__':
        main()

Et on essaye avec un fichier de config erroné suivant::

    [user]
    nom = Dupond

On exécute, et regarde la sortie::

    $ ./config.py  

Oops, une erreur dans votre fichier de conf (No option 'age' in section:
'user')

has_section, has_option
'''''''''''''''''''''''

Le module viens avec deux méthodes permettant de vérifier la présence de
section ou d'option, on peut donc s'en servir, avec quelques choses ressemblant
à ça par::

    if config.has_option('user', 'nom'):
        nom = config.get('user', 'nom')
    else:
        nom = 'Default_name'
    if config.has_option('user', 'age'):
        age = config.get('user', 'age')
    else:
        age = '42'

On affecte également des valeurs par défaut si une option n'est pas trouvé, on
peut noté également, que la gestion d'erreur sur la section n'est pas faite
ici, uniquement les options.

Écrire dans un fichier de configuration
---------------------------------------

Jusqu'ici, nous avons vu comment lire les données d'un fichier de
configuration. Pendant qu'on y est, autant jeter un œil sur la façon d'écrire,
et donc sauvegarder, une configuration. Dans la lignée de ce qui à déjà était
fait, même nom de fichier, même section et options.

Dans un premier temps, on supprime le fichier de configuration `conf.rc` si
vous l'aviez gardé depuis l'exercice plus haut, et on écrit dans `config.py` le
code suivant::

    import ConfigParser

    def main ():

        config = ConfigParser.RawConfigParser()

        config.add_section('user')
        config.set('user', 'nom', 'Dupond')
        config.set('user', 'age', '42')

        with open('conf.rc', 'wb') as conf_file:
            config.write(conf_file)

    if __name__ == '__main__':
        main()

On rend exécutable avec `chmod +x config.py`, puis on exécute le script, et on
observe le résultat en ouvrant le fichier conf.rc, il contient exactement ce
qu'on attendait.

Pour les explications, plus courte cette fois ci. On vois qu'on rajoute la
section avec la méthode `add_section`, pour lequel on affecte les options avec
la méthode `set` qui prends trois arguments::

    config.set(section, options, valeur)

L'utilisation de `with open` pour la lecture ou l'écriture de fichier à
l'avantage de ne pas avoir besoin de le refermer, et celà quoi qu'il arrive,
même si l'écriture est defectueuse. Cette façon de procédé est privilégié.

Sauvegarder un dictionnaire comme configuration
-----------------------------------------------

Imaginons que nous voulons sauvegarder un dictionnaire qui nous sert de
configuration dans un fichier, on peut donc effectuer de la sorte::

    import ConfigParser

    def main ():

        config = ConfigParser.RawConfigParser()
        params = {
                'Linux': 'Torvalds',
                'GNU': 'RMS',
                'answer': '42',
            }
        config.add_section('params')
        for arg in params:
            config.set('params', arg, params[arg])

        with open('conf.rc', 'wb') as conf_file:
            config.write(conf_file)

    if __name__ == '__main__':
        main()

On exécute, et regarde le résultat obtenu, et c'est ce que nous voulions, un
fichier contenant ce dictionnaire et sous forme `option = valeur`.

Voilà, cette introduction au module ConfigParser [1]_ touche à sa fin, C'est un
module qui n'est pas compliqué à prendre en main, il est conseillé de lire la
documentation fournis pour plus amples détails. En espérant motiver certain à
utiliser un fichier de configuration plutôt que d'écrire « en dur » les
variables directement dans le fichier source.

.. _`ConfigParser`: http://docs.python.org/library/configparser.html
.. [1] http://docs.python.org/library/configparser.html
