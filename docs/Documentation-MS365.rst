======================
Documentation MS365
======================

Création de tenant
====================





Microsoft 365
================


Gestion des utilisateurs et groupes
--------------------------------------

Différents types de groupes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/microsoft-365-groups.png


+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Group Type                | Description                                                                                                                                                                                                                                                | When to use?                                                                                   | Where to create it?                       |
+===========================+============================================================================================================================================================================================================================================================+===============================================================================================+===========================================+
| Microsoft 365 group       | A Microsoft 365 group is the recommended group type. It includes a group email and shared workspaces, making it ideal for collaboration. It's similar to distribution groups because it has its own mailbox, and its members receive email... | When you want to provide distribution list capabilities and other collaboration features. The best option for team work. | - Microsoft 365 admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Distribution group        | In Microsoft 365, a distribution group is also known as a distribution list. Organizations primarily use it for sending notifications to a group of people. You do so by associating a single email address with the distribution group. | When you want to distribute messages using the group only. | - Microsoft 365 admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Mail-enabled security group | When you mail-enable a security group, it means the group has an associated email address, and members of the group can send and receive emails using that address. This design enables the group to function as a mailing list or distribution group. | When you want to use the group for both permissions and mail distribution. | - Microsoft 365 admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Security group | A security group provides a convenient way to manage and assign permissions to multiple users simultaneously. A security group can grant access permissions to resources such as OneDrive and SharePoint. | When you only require a group to grant permissions. | - Microsoft 365 admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Dynamic distribution group | A dynamic distribution group automatically manages its membership based on predefined criteria or filters. In a traditional distribution group, users manually manage membership. | When you want to have a flexible distribution list that changes membership automatically. | - Exchange admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+
| Shared mailbox | Shared mailboxes are used when multiple people need access to the same mailbox, such as a company information or support email address, reception desk, or other function that might be shared by multiple people. | When multiple people need to access the same mailbox, such as a support email address. | - Microsoft 365 admin center |
+---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------+-------------------------------------------+


Export des dernières connexions des utilisateurs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Via MSGraph
~~~~~~~~~~~~


.. code-block:: console

   Connect-MgGraph -Scopes Directory.Read.All,AuditLog.Read.All
   Get-MgUser -All -Property 'UserPrincipalName', 'SignInActivity' | Select-Object UserPrincipalName, @{N='LastSignInDate';E={$_.SignInActivity.LastSignInDateTime}} | Export-Csv -Path C:\\Windows\\temp\\LastLogininfo.csv -NoTypeInformation


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