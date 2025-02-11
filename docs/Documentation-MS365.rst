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






.. tabs::

   .. tab:: Etape 1

      - Creer un nom de règle
      - Ajouter une description (optionnelle mais best practice)
      - Gravité
      - Catégorie

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create1.png


   .. tab:: Etape 2

      - Choisir le déclencheur de l'alerte. Ici "Autorisation de boître aux lettres octroyée"
      - Comment l'alerte se déclenche-t-elle ? (déclenchement avec volume, dès que l'activité correspond etc...). Ici "dès que l'activité correspond".

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create2.png


   .. tab:: Etape 3

      - Choisir quel est le destinataire de l'alerte et si l'on veut qu'elle soit envoyée par mail.
      - Nombre de notifications maximum par jour.

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create3.png
     
   .. tab:: Etape 4

      - Vérification de la configuration de l'alerte
      - Choisir de l'activer dès maintenant ou plus tard. (Ici, choisir maintenant).

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/create4.png