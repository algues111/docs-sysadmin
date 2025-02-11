======================
Documentation MS365
======================

Création de tenant
====================





Microsoft 365
================


Gestion des utilisateurs et groupes
--------------------------------------


Achat de licences
------------------------



Sharepoint
---------------------

PowerAutomate
-------------------

Exchange
--------------------

.. note::

    Si vous hébergez un serveur Exchange On Premise, lors de la connexion à ce serveur via Outlook, ce dernier passera tout de même par les serveurs de Microsoft.
    Il faut donc faire attention quant aux restrictions par pays pour ne pas bloquer des serverzs Microsoft légitimes !

Defender
-------------------

Alerts & Rules
------------------

Depuis Microsoft Defender, il ets possible de créer des alertes suivant certaines conditions.


Permissions de boîte aux lettres ajoutées 
---------------------------------------------

Plusieurs étapes sont nécessaires pour configurer une alerte sur Microsoft Defender.

Tout d'abord, dans la section "E-mail et collaboration" du menu gauche, cliquer sur "Stratégies et règles".

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/menu.png

Une nouvelle fenêtre apparaît dans laquelle nous pouvons sélectionner 3 catégories de stratégies ou alrtes.
Il faut cliquer sur "Stratégie d'alerte".

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/strat-regles.png

Toutes les règles déjà créées et activées par Microsoft sont désormais affichées.
Pour en créer une nouvelle, cliquer sur "+ Nouvelle stratégie d'alerte".

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/alerts.png


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create1.png
.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create2.png
.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create3.png
.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create4.png



.. tabs::

   .. tab:: SquareX

      SquareX est une extension s'ajoutant à votre navigateur vous permettant de sandboxer un site que vous visitez, un fichier ou même un mail que vous pouvez recevoir sur une adresse temporaire.
      Rendez-vous sur https://sqrx.com/ pour la télécharger !

      .. image:: https://raw.githubusercontent.com/algues111/docs-cfc/main/docs/source/images/M233/sqrx.png


   .. tab:: VirusTotal

      `VirusTotal <https://www.virustotal.com/gui/home/upload>`_ est un outil 100% gratuit permettant de scanner des URL, des fichiers, des hashs/checksums, des domaines et adresses IP.
      Ses analyses sont basés sur plus de 70 anti-virus connus du marché de la cybersécurité et vous offre en plus de cela un score de communauté.

      Accueil du site :

      .. image:: https://raw.githubusercontent.com/algues111/docs-cfc/main/docs/source/images/M233/virustotal.png

      Regroupant toutes ces informations, il est bien plus facile de savoir si tel site ou tel fichier est malveillant.

      L'entreprise offre aussi des applications de bureau pour Mac, Linux et Windows ainsi que des services payant pour du threat hunting et des graphs !

      Ci-dessous une démonstration d'un scan de site malveillant (ici phishing).

       .. image:: https://raw.githubusercontent.com/algues111/docs-cfc/main/docs/source/images/M233/virustotal-malurl.png
     


   .. tab:: iBarry

     `iBarry <https://www.ibarry.ch/fr/controles-de-securite/>`_ centralise des fonctionnalités similaires et complémentaires à VirusTotal, il permet de :

         - Vérifier un site web
         - Vérifier si une adresse mail a été compromis via des fuites de données (vérification faite par Have I Been Powned)
         - Vérifier si son IP publique est potentiellement sujette à des attaques (iBarry effectue des tests de ports)

     Le site propose aussi dvers logiciels de sécurité dont l'antivirus de Sophos ainsi que Qualys pour la veille des logiciels.

     .. image:: https://raw.githubusercontent.com/algues111/docs-cfc/main/docs/source/images/M233/ibarry.png
