.. _request:

Request, HTTP pour humains
==========================

Urllib est le module par défaut pour gérer les requetes HTTP avec Python. `Le
cookbook`_ [1]_ du site officiel donne un petit aperçu de l'usage d'urllib2.
Certains détails peuvent surprendre aux premières utilisations. Ce qu'on aime
avec Python, c'est avant tout de pouvoir penser à l'application, plutôt qu'au
code lui même.

`Requests`_ [2]_ se veut être un petit module répondant à cette problématique,
« HTTP for humains » comme titre le site. On va voir quelques exemples, qui
seront certainement plus marquant lorsque les besoins se complexifient.

.. warning::

    Requests n'existe pour le moment que pour Python 2.x, il n'a pas encore été
    porté pour la version 3. C'est donc un choix à faire.

L'installation est on ne peut plus classique avec pip ou autre installeur de
Python.

::

    sudo pip install requests

Lire une page
-------------

Le premier exemple du how-to officiel est::

    import urllib2

    req = urllib2.Request('http://www.voidspace.org.uk')
    response = urllib2.urlopen(req)
    page = response.read()

Il pourrait être écrit avec Requests comme suit::

    import requests

    req = requests.get('http://www.nicosphere.net')
    page = req.content

Remplir un formulaire
----------------------

Un autre exemple tiré du site officiel, dont l'objet ici est de remplir un
formulaire.

::

    import urllib
    import urllib2

    url = 'http://www.someserver.com/cgi-bin/register.cgi'
    values = {'name' : 'Michael Foord',
              'location' : 'Northampton',
              'language' : 'Python' }

    data = urllib.urlencode(values)
    req = urllib2.Request(url, data)
    response = urllib2.urlopen(req)
    page = response.read()

Et l'équivalant avec Requests::

    import requests

    url= 'http://wathever.url'
    values = {'name': 'Nicolas',
              'location': 'Somewhere',
              'language': 'Python'}

    req = requests.post(url, values)
    page = req.content

Quelques remarques qu'on peut déjà faire.

- Pas besoin d'importer urllib _et_ urllib2.
- Pas besoin d'encoder les données avant, requests le fait pour vous.
- On reconnait get, post, mais d'autre méthode *RESTful* sont disponible tel que
  put et delete.
- Moins à écrire, et un code plus instinctif.

Authentification basique
------------------------

Le dernier exemple, gérant l'authentification::

    # create a password manager
    password_mgr = urllib2.HTTPPasswordMgrWithDefaultRealm()

    # Add the username and password.
    # If we knew the realm, we could use it instead of None.
    top_level_url = "http://example.com/foo/"
    password_mgr.add_password(None, top_level_url, username, password)

    handler = urllib2.HTTPBasicAuthHandler(password_mgr)

    # create "opener" (OpenerDirector instance)
    opener = urllib2.build_opener(handler)

    # use the opener to fetch a URL
    opener.open(a_url)

    # Install the opener.
    # Now all calls to urllib2.urlopen use our opener.
    urllib2.install_opener(opener)

Un peu verbeux pour une simple authentification basique... Mais c'est l'exemple
officiel encore une fois, j'ai laissé les commentaires du code pour plus de
« lisibilité ». Maintenant, ce même code écrit avec Requests, exemple pris sur
leur site.

::

    >>> r = requests.get('https://api.github.com', auth=('user', 'pass'))
    >>> r.status_code
    200

Comme qui dirait... « y a pas photo ! »

Il est intéressant de regarder la documentation, par exemple de 'get', afin de
voir ce qui est supporté, et là on voit que la bibliothèque se charge de
redirection, des sessions/cookies (CookieJar), timeout, proxies, et tout ce dont on a
normalement besoin.

::

    requests.get(url, params=None, headers=None, cookies=None,
                 auth=None, timeout=None, proxies=None)

Conclusion
----------

Voilà un petit survole de Requests, et bien que je n'ai pas encore eu tellement
l'occasion de l'utiliser, c'est très certainement un module que je vais garder
sous le coude. Pour des scripts jetables, ou des petites applications
personnelles, il me semble évident que ça peut être un gain de temps et de
confort. Pour ce qui est de son utilisation pour une application redistribuée,
je comprends qu'on puisse préférer l'utilisation d'un module *core* tel que
urllib, cependant, avec un usage de setup.py pour redistribuer, les dépendances
sont installées très facilement sans actions supplémentaires de l'utilisateur,
pourquoi pas utiliser Requests donc.

.. _`Le cookbook`: http://docs.python.org/howto/urllib2.html
.. _`Requests`: http://docs.python-requests.org/en/latest/index.html

.. [1] http://docs.python.org/howto/urllib2.html
.. [2] http://docs.python-requests.org/en/latest/index.html
