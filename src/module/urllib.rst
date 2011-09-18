.. _urllib:

Script concret pour Urllib
==========================

Je l'accorde que ce script à été fait un peu à `La Rache`_ [1]_ un samedi soir
et sur un coup de tête, le titre original était d'ailleurs *Le script idiot du
dimanche*, titre honteusement repompé de PC INpact (les liens idiots du
dimanche) mais qui était de circonstance. Je pense qu'il peut faire une petite
introduction à Urllib, tout en restant amusant, chose importante lorsqu'on
apprends, et surtout de façon auto-didacte.

Mais la question qu'on peut se poser est :

**Mais que fait ce script ?!**

Vous connaissez peut être le site downforeveryoneorjustme.com ? Il permet de
s'assurer que pour un site qui vous est inaccessible, qu'il ne s'agisse pas d'un
problème venant de chez vous. L'intérêt est de passer par un autre site, afin
de s'assurer que le problème rencontrer n'est pas entre le premier site et soi.
Ce script est tout simplement un front-end de ce site, dont l'usage en console
est on ne peu plus simple::

    $ ./downforeveryoneorjustme http://www.nicosphere.net
    It's just you. http://www.nicosphere.net is up.

De plus, l'affichage se fait en couleur, rouge si c'est down, et vert si le
site est fonctionnel (j'ai hésité longuement sur le choix des couleurs).

Vous pouvez cloner le dépôt git fait à cette occasion. Si si, j'ai fais un
dépôt rien que pour ça…

.. note::

    Je n'ai testé le script qu'avec **Python 2.7**, et il faudra
    certainement adapter la partie avec urllib, comme il y a quelques changement
    entre les deux versions majeurs de Python.

::

    git clone git://github.com/Nic0/DownForEveryoneOrJustMe.git

Mais jetons un œil au script tout de même.

::

    import re
    import sys
    import urllib

    class DownForEveryone(object):

        base_url = 'http://www.downforeveryoneorjustme.com/'

        def __init__(self):
            self.parse_url()
            self.is_url()
            self.get_answer(
                urllib.urlopen(self.base_url+self.url).read()
            )

        def parse_url(self):
            try:
                self.url = sys.argv[1]
            except IndexError:
                self.usage()

        def is_url(self):
            url_regex = 
            'http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\(\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+'
            resp = re.findall(url_regex, self.url)
            if len(resp) != 1:
                print 'The argument does not seems to be an URL'
                self.usage()

        def get_answer(self, response):
            up = 'It\'s just you.'
            down ='It\'s not just you!'
            if up in response:
                print '\033[1;32m{} {} {}\033[1;m'.format(up, self.url, 'is up.')
            elif down in response:
                print '\033[1;31m{} {} {}\033[1;m'.format(
                    down, self.url, 'looks down from here.')
            else:
                print 'Error to find the answer'

        def usage(self):
            print 'Usage:'
            print ' {} http://www.exemple.org'.format(sys.argv[0])
            sys.exit(0)

    if __name__ == '__main__':
        DownForEveryone()

Il n'y a pas beaucoup à dire sur le fonctionnement simpliste du script.
L'argument passé en commande est vérifié, voir s'il existe, que c'est bien un
lien, puis on récupère la page demandé, sur lequel on vérifie simplement la
présence d'une petite phrase clé pour l'un ou l'autre des évènements. Puis on
affiche le résultat avec de belles couleurs. Et c'est tout !

Le script méritait donc bien le titre de ce billet.

Il y aurait bien sûr beaucoup d'amélioration à effectuer ou même des
simplification, mais je le laisse tel quel pour montrer qu'en vraiment peut de
temps, et sans connaitre très bien Python, on peut arriver à un résultat
amusant et concret.

.. _`La Rache`: http://www.nicosphere.net/utilisez-la-methode-la-rache-pour-vos-projets-1363/
.. [1] http://www.nicosphere.net/utilisez-la-methode-la-rache-pour-vos-projets-1363/
