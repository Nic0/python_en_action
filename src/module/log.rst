.. _log:

Tenir un fichier de log
=======================

Il est souvent utile de tenir un fichier de log dans plusieurs situations,
comme archiver différentes erreurs, ou même pour les débugs. Python fournit
nativement `un module`_ [1]_ très bien fait et complet pour faire cela, avec
une redirection de l'information soit en console, soit dans un fichier, et
permettant plusieurs niveaux d'importance de logs, du message de débug aux
erreurs critiques.

Ce module permet d'aller bien plus loin de ce que j'en aurais besoin, comme un
serveur de log pour centraliser les logs de clients, des logs tournants ou
encore l'envoi de journaux par SMTP (mail). Il n'est pas question ici de tout
voir. Une petite introduction qui permet de tenir simplement un fichier de log,
pour une prise main rapide.

Les niveaux de message
-----------------------

Le module définit plusieurs niveaux d'importance dans les messages, qui sont
dans l'ordre croissant :

- Debug
- Info
- Warning
- Error
- Critical

.. note::
    
    Dans cet exemple, je me suis servi de Python3, je n'ai pas vérifié la
    compatibilité avec Python2, mais son usage doit être très similaire.

Premier essai
-------------

Essayons un script rapide, qui n'a pas vraiment d'intérêt si ce n'est de voir
ce qu'il s'y passe.

::

    import logging

    logging.warning('coin')
    logging.debug('pan!')

On exécute le programme, et on obtient la sortie suivante::

    $ ./logger.py  
    WARNING:root:coin

Plusieurs remarques:

- Seul le warning s'est affiché, s'expliquant par la configuration de défaut
  n'affichant qu'uniquement les messages à partir du seuil warning. On verra
  comment le modifier par la suite.
- La sortie contient le niveau (WARNING), un 'root' un peu obscure, et le
  message, la sortie n'est pas très jolie ni très verbeuse, mais fait ce qu'on
  lui demande.
- Le message est envoyé en console et non pas dans un fichier comme
  nous le souhaitons.

Fichier et niveau de log
------------------------

Regardons pour enregistrer ce message dans un fichier maintenant. Cette
redirection peut être utile par exemple si le programme est écrit en ncurses ou
qu'on ne possède pas vraiment de retour visuel. Mais également pour avoir des
informations sur une plus longue période.

Dans le script précédent, on rajoute avant les messages de warning et debug la
ligne suivante::

    logging.basicConfig(filename="prog.log",level=logging.DEBUG)

On exécute et regarde le fichier *prog.log*, on obtient exactement ce à quoi
l'on s'attendait : les deux messages. Par simplicité, le fichier est renseigné
par un chemin relatif, et se retrouve donc à l'endroit de l'exécution du
programme.  On pourrait mettre un chemin absolu, en concaténant des variables
d'environnement.

Logger les variables
--------------------

Comme il peut être utile de rajouter quelques variables, afin d'avoir un aperçu
au moment du log, on peut le faire en les rajoutant comme argument, comme on le
ferait pour une *string*, comme dans l'exemple suivant::

    import logging

    a = 'coin'
    b = 'pan!'
    logging.warning('a:%s b:%s', a, b)

La sortie sera la suivante::

    $ ./logger.py 
    WARNING:root:a:coin b:pan!

Ajouter l'heure aux messages
----------------------------

Dans un fichier de log, on souhaite généralement avoir à chaque message, une
indication de l'heure, et éventuellement de la date, il est possible de le
faire, une fois pour toute, de la façon suivante::

    import logging
    import time

    logging.basicConfig(
        filename='prog.log',
        level=logging.INFO,
        format='%(asctime)s %(levelname)s - %(message)s',
        datefmt='%d/%m/%Y %H:%M:%S',
        )

    logging.info('Mon programme démarre')
    time.sleep(2)
    logging.warning('Oh, quelque chose ce passe!')
    logging.info('fin du prog')

Nous obtenons dans le fichier de log les informations suivantes::

    16/07/2011 13:33:26 INFO - Mon programme démarre
    16/07/2011 13:33:28 WARNING - Oh, quelque chose ce passe!
    16/07/2011 13:33:28 INFO - fin du prog

La sortie est plus lisible et exploitable.

Fichier de configuration
------------------------

Pour avoir la possibilité de séparer la configuration du logger et le code en
général, il est possible de tenir un fichier spécifique à la configuration, il
supporte deux syntaxes différentes, basées sur ConfigParser ou YAML.

Comme ce module permet un niveau de personnalisation assez avancé, il est normal
que cela se ressente sur la verbosité du fichier de configuration. Pour ma
part, je ne pense pas en avoir besoin dans l'immédiat, donc je vous renvoie sur
la documentation officielle, et notamment ce `how-to`_ [2]_ qui est un bon endroit
pour commencer, et dont ce billet s'en inspire fortement.


.. _`un module`: http://docs.python.org/library/logging.html 
.. _`how-to`: http://docs.python.org/py3k/howto/logging.html


.. [1] http://docs.python.org/library/logging.html 
.. [2] http://docs.python.org/py3k/howto/logging.html
