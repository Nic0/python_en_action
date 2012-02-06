.. _twitter:

Client Twitter et Identi.ca
===========================

On va voir ici, comment envoyer et lire des tweets depuis la console vers Twitter ou
Identi.ca, en passant par le système OAuth pour les deux cas. Les étapes sont
expliqué du début à la fin, et ne requiert pas spécialement de connaissances
importantes en Python.

Pour arriver à nos fins, nous allons utiliser une API, ``python-twitter``. Ce
billet est plus une introduction à cette API que d'avoir un intérêt réel dans
l'application. Surtout dans le cas de Identi.ca où il serait bien plus simple
d'utiliser une authentification http basique au lieu du
système OAuth. Il reste cependant intéressant de voir que Identi.ca propose un
comportement similaire à Twitter dans son API, permettant d'intégrer deux
services avec un quasiment le même code, évitant une réécriture pour
s'adapter à l'un ou l'autre des services.

Enregistrer son application
---------------------------

Pour utiliser OAuth, il faut enregistrer son application, aussi bien pour
Twitter que pour Identi.ca. Rien de bien méchant mais indispensable pour
obtenir *les clés* de votre application.

Twitter
'''''''

Une fois identifié, on se rend sur la page de gestion d'application :
https://dev.twitter.com/apps
On clique sur *Register a new app*, c'est possible qu'il soit écrit en
français chez vous.

Je vous épargne la capture d'écran, nous remplissons le formulaire suivant :

- Application name : Un nom d'application
- Description : Faut vraiment expliqué ? :p
- Application website : C'est le lien qui vient lorsqu'on a la source d'un
  client. Pas super utile si c'est juste un client fait à La Rache mais bon.
- Application type : Important, faut choisir « client ».
- Default access type : C'est ce que twitter à changé récemment, il faut au
  minimum « read & write », mais le « read, write & direct message » peut être
  utile pour avoir accès au messages direct.
- Application icon : bah… un avatar n'a jamais tué personne.

On remplit le captcha, on valide leurs conditions d'utilisation, et nous voilà
avec une application fraîchement créée. Vous obtenez quelques renseignements et
clés, un peu obscures au premier abord, vous ne relevez uniquement les deux
informations suivantes :

- Consumer key (Clé de l'utilisateur)
- Consumer secret (Secret de l’utilisateur)

Identi.ca
'''''''''

Le principe est similaire, on s'identifie à son compte Identi.ca, puis on
enregistre son application sur le lien suivant :
http://identi.ca/settings/oauthapps

On clique sur *Register a new application*, puis on remplit également le petit
formulaire, qui est similaire à celui de Twitter, pour lequel je ne reprendrai
que les détails qui peuvent laisser un doute :

- Callback url : Laissez vide,
- Navigateur/Bureau : Vous choisissez Bureau.
- Lecture-écriture : vous souhaitez pouvoir écrire.

Une fois validé, il vous affiche une liste de vos applications enregistrées,
cliquez sur le nom de celle que vous venez de valider pour obtenir les éléments
recherchés.

- Clé de l’utilisateur
- Secret de l’utilisateur

Authentification du client
--------------------------

Dans la première étape, nous avons obtenu les clés spécifique à l'application,
maintenant, il faut obtenir les clés pour un utilisateur, liées à cette
application, et l'autoriser à accéder à son compte.

Comme nous voulons aller au plus simple, et qu'il est pratique courante de
réutiliser un code de quelqu'un d'autre lorsque celui-ci est libre. On utilise
l'utilitaire fournis avec l'API python-twitter.

On télécharge la version de python-twitter (0.8.2, la dernière), on décompresse
l'archive, et rentre dans le répertoire::

    wget http://python-twitter.googlecode.com/files/python-twitter-0.8.2.tar.gz
    tar xvf python-twitter-0.8.2.tar.gz
    cd python-twitter-0.8.2

Le fichier important à ce niveau est ``get_access_token.py`` puisqu'il permet
d'obtenir les clés. Je ne copie-colle pas tout le code, mais vous devez
rechercher les deux paramètres suivant à la ligne 34 et 35::

    consumer_key    = None
    consumer_secret = None

En le remplaçant par les valeurs obtenu plus haut, par exemple::

    consumer_key    = 'vIxy85s7r2jOBmr7m7bQ'
    consumer_secret = 'PyLzYa3WLMqv6xziFAiOqMlQlSxP9vXyXsTemqyB7c'

L'application vous donne un lien à visiter, un code à rentrer, puis vous
fournit vos deux codes.

::

    $ python2.7 get_access_token.py
    Requesting temp token from Twitter

    Please visit this Twitter page and retrieve the pincode to be used
    in the next step to obtaining an Authentication Token:

    https://api.twitter.com/oauth/authorize?oauth_token=c5X[...]31Y

    Pincode? 4242424

    Generating and signing request for an access token

    Your Twitter Access Token key: 1232[...]qH0y43
              Access Token secret: HHzs[...]IyoJ

Un jeu d'enfant, vous notez vos token obtenu.

Identi.ca
'''''''''

Pour adapter à Identi.ca, il n'y a besoin que de peu de changement, l'idée est
de remplacer ``https://api.twitter.com/`` par ``https://identi.ca/api/`` ou par
toute autre URL répondant à vos besoins.

hint: ``:%s/api.twitter.com/identi.ca\/api/g``

**Attention** Il y a une différence notable entre les deux, sur le
``REQUEST_TOKEN_URL``, il faut rajouter à la fin ``?oauth_callback=oob``. Je ne
suis plus certain d'où vient cette information, mais il ne fonctionnera pas
sans, alors que c'est sans problème pour Twitter.

Pour obtenir::

    REQUEST_TOKEN_URL =
        'https://identi.ca/api/oauth/request_token?oauth_callback=oob'
    ACCESS_TOKEN_URL  = 'https://identi.ca/api/oauth/access_token'
    AUTHORIZATION_URL = 'https://identi.ca/api/oauth/authorize'
    SIGNIN_URL        = 'https://identi.ca/api/oauth/authenticate'

N'oubliez pas de remplir ``consumer_key`` et ``consumer_secret`` comme pour
Twitter. La démarche y est similaire.

L'application
-------------

La partie fastidieuse étant passée, les clés pour le bon déroulement obtenu,
place enfin à ce qui nous intéresse, le code !

On veut quoi ?

- Appeler mon application, avec un argument, on va mettre -s comme send, puis
  le tweet à envoyer directement dans la ligne de commande.
- L'application doit être correctement authentifiée au service voulu.
- Utiliser python-twitter comme API pour gérer l'authentification et l'envoi.
- Un nom pour l'application, va pour... Clitter... Bon un peu pourri, mais je
  vais faire avec :þ (Clitter pour CLI, Commande Line Interface, je préfère
  préciser, on sait jamais)

Dans un premier temps, on veut passer correctement l'argument -s. J'utilise
dans l'exemple getopt, qui est un parser ressemblant à celui de C, mais on peut
utiliser également argparse, qui est moins verbeux à mettre en place.

Je reprends un peu l'exemple pris sur `la page de documentation de getopt`_ [1]_,
on fait un premier essai, voir si l'argument est bien pris en compte.

`clitter.py`::

    import getopt, sys

    def main():
        try:
            opts, args = getopt.getopt(sys.argv[1:], "s:", ["send="])
        except getopt.GetoptError, err:
            print str(err) # will print something like "option -a not recognized"
            sys.exit(2)
        for o, a in opts:
            if o in ("-s", "--send"):
                tweet = a
                print a
            else:
                assert False, "unhandled option"

    if __name__ == "__main__":
        main()

Ce qu'on veut ici, c'est uniquement avoir en sortie le tweet entrée, on essaie::

    python2.7 clitter.py -s 'mon tout premier test'

On a ce quoi l'on s'attendait en retour en console, le « mon tout premier test »,
très bien, passons aux choses sérieuses.

Il nous reste plus qu'à rajouter l'authentification, et l'envoi sur twitter,
ce n'est pas bien compliqué maintenant.

On va créer un répertoire dans lequel on place :
- ``__init__.py`` avec rien dedans
- ``clitter.py`` correspondant à notre script
- ``twitter.py`` l'api qu'on a téléchargé tout à l'heure, là où se trouvait get_access_token.py, il nous suffit de le déplacer dans le répertoire.

Voici le code permettant d'envoyer un tweet::

    import getopt, sys
    import twitter

    def main():
        try:
            opts, args = getopt.getopt(sys.argv[1:], "s:", ["send="])
        except getopt.GetoptError, err:
            print str(err) # will print something like "option -a not recognized"
            sys.exit(2)
        for o, a in opts:
            if o in ("-s", "--send"):
                tweet = a
            else:
                assert False, "unhandled option"

        consumer_key = 'vIxy85s[...]7m7bQ'
        consumer_secret = 'PyLzYa[...]9vXyXsTemqyB7c'
        oauth_token = 'zCNVC[...]hxgI5m5'
        oauth_token_secret = '3Q4tL3U[...]FyHvWCh'

        api = twitter.Api(consumer_key, consumer_secret,
                          oauth_token, oauth_token_secret)

        api.PostUpdate(tweet)

    if __name__ == "__main__":
        main()


La première chose, c'est d'importer le module twitter::

    import twitter

Puis on renseigne les quatre clefs pour s'identifier, que j'ai un peu
raccourci ici.


.. note::

    Lorsqu'on distribue une application, on fournit la clé
    `consumer_secret`, c'est un peu déroutant au début, on se dit, hmm, si c'est
    secret, pourquoi le diffuser, bref, peut-être que le nom est trompeur, mais par
    exemple, pour le programme `hotot`, elles sont dans le fichier `config.py` à la
    ligne 76/77, et on retrouve bien également la `consumer_secret`, c'est normal,
    rien d'inquiétant.

Vient ensuite l'authentification proprement dite, avec un double rôle,
instancier l'objet de l'api, et s'identifier à celle-ci::

    api = twitter.Api(consumer_key, consumer_secret,
                      oauth_token, oauth_token_secret)

Enfin, on envoie le tweet grâce à cette api tout simplement::

    api.PostUpdate(tweet)

Et c'est aussi simple que ça !

Identi.ca
'''''''''

Comme je disais en introduction, l'intérêt d'utiliser Oauth  et python-twitter
pour Identi.ca, c'est de pouvoir utiliser un même code sans quasiment rien
changer. La seul différence sera d'indiquer l'URL lors de l'authentification,
comme dans cette exemple::

    url = "https://identi.ca/api"
    api = twitter.Api(consumer_key, consumer_secret,
                      oauth_token, oauth_token_secret,
                      base_url=url)

Et c'est tout, pour envoyer un tweet, le code est exactement le même.

Bonus, lire ses tweets
''''''''''''''''''''''

Comme il est assez facile d'envoyer un tweet, qu'on a finalement pas vu grand
chose de python-twitter, compliquons un peu les choses, en essayant de lire ses
tweets en console. Une bonne occasion de réorganiser un peu le code dans la
foulée.

La méthode utilisé pour lire ses tweets est::

    api.GetFriendsTimeline(retweets=True)

On récupère la timeline de nos « amis », et on active les retweets.
On veut appeler cette fonction lorsqu'on utilisera l'option ``-r`` pour read, en
console.

Voici le code final de notre petite application (à adapter comme vu plus haut
pour Identi.ca)

::

    import getopt, sys
    import twitter

    def authentication():

        consumer_key = '2jO[...]Bm'
        consumer_secret = 'PyLzYa[...]3WLM'
        oauth_token = 'tsr[...]ds5'
        oauth_token_secret = 'PSd3[...]tSt'

        return twitter.Api(consumer_key, consumer_secret,
                          oauth_token, oauth_token_secret)

    def send(tweet):
        api = authentication()
        api.postUpdate(tweet)

    def read():
        api = authentication()
        statuses = api.GetFriendsTimeline(retweets=True)

        for status in statuses:
            print "%s:\t%s" % (status.user.screen_name, status.text)

    def main():
        try:
            opts, args = getopt.getopt(sys.argv[1:], "rs:", ["send="])
        except getopt.GetoptError, err:
            print str(err) # will print something like "option -a not recognized"
            sys.exit(2)
        for o, a in opts:
            if o in ("-s", "--send"):
                tweet = a
                send(tweet)
            elif o in ("-r", "--read"):
                read()
            else:
                assert False, "unhandled option"

    if __name__ == "__main__":
        main()

Pas besoin de plus de code que ça ! Quelques explications encore. On retrouve
nos tweets avec le `GetFriendsTimeline`, auquel on rajoute les retweets dans la
timeline, sinon vous ne verriez pas les retweets de vos follower, ce qui est
généralement le comportement par défaut des applications twitter.

::

    statuses = api.GetFriendsTimeline(retweets=True)

On obtient une liste, sur lequel on va lire le pseudo et le tweet en accédant
directement au attribut de l'objet Status.

::

    for status in statuses:
        print "%s:\t%s" % (status.user.screen_name, status.text)

Et on appelle le script::

    python2.7 clitter.py -r

Et voilà !

Conclusion
----------

Grâce à ce script d'une cinquantaine de lignes tout mouillé, vous pouvez lire
et envoyer des tweets directement en console. C'est l'avantage de s'appuyer sur
une bibliothèque, même si au début cela peut faire peur de s'y plonger. Il ne
faut pas hésiter à ouvrir le code, lire la documentation tout au long de l'API
pour en apprendre plus.

.. _`la page de documentation de getopt`: http://docs.python.org/library/getopt.html
.. [1] http://docs.python.org/library/getopt.html
