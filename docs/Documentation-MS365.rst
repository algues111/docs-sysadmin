======================
Documentation MS365
======================

Création de tenant
====================





Microsoft 365
================

Entra ID
---------------

Gestion des utilisateurs et groupes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Utilisateurs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~






Différents types de groupes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/microsoft-365-groups.png


+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Group Type                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | When to use?                                                                                                               | Where to create it?                                 |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Microsoft   365 group         | A Microsoft 365 group is the recommended group type. It   includes a group email and shared workspaces, making it ideal for   collaboration. It's similar to distribution groups because it has its own   mailbox, and its members receive email messages sent to the group. However,   it differs from distribution groups in that it allows teams to collaborate by   providing them with a shared workspace for email, conversations, files, and   calendar events.                                                                              | When you want to provide distribution list capabilities   and other collaboration features. The best option for team work. | - Microsoft 365 admin center                        |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Outlook (also known as the Groups app in Outlook) |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Exchange admin center                             |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft Entra admin center                      |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft 365 admin app                           |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Distribution   group          | In Microsoft 365, a distribution group is also known as a   distribution list. Organizations primarily use it for sending notifications   to a group of people. You do so by associating a single email address with   the distribution group. An organization's email system or mail server   distributes messages sent to the group's email address to all members of the   group.                                                                                                                                                                | When you want to distribute messages using the group   only.                                                               | - Microsoft 365 admin center                        |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft 365 admin app                           |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Exchange admin center                             |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Mail-enabled   security group | When you mail-enable a security group, it means the group   has an associated email address, and members of the group can send and   receive emails using that address. This design enables the group to function   as a mailing list or distribution group. Message delivery works the same as   with a distribution group. In other words, an organization's email system or   mail server distributes messages sent to the security group's email address   to all members of the group.                                                         | When you want to use the group for both permissions and   mail distribution.                                               | - Microsoft 365 admin   center                      |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft 365 admin app                           |
|                               |      You can also use a mail-enabled security group to assign group permissions   to various resources, such as SharePoint. For example, by adding a   mail-enabled security group to SharePoint, you can grant the group's members   access permissions to SharePoint sites, libraries, lists, or individual items   within SharePoint. Mail-enabled security groups can't be dynamically managed,   nor can they contain devices.                                                                                                                 |                                                                                                                            | - Exchange admin center                             |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Security   group              | A security group provides a convenient way to manage and   assign permissions to multiple users simultaneously. A security group can   grant access permissions to resources such as OneDrive and SharePoint.                                                                                                                                                                                                                                                                                                                                       | When you only require a group to grant permissions.                                                                        | - Microsoft 365 admin center                        |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft 365 admin app                           |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft Entra admin center                      |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Dynamic   distribution group  | A dynamic distribution group automatically manages its   membership based on predefined criteria or filters. In a traditional   distribution group, users manually manage membership. In a dynamic   distribution group, Microsoft 365 dynamically updates its member list based   on specific attributes or conditions.                                                                                                                                                                                                                            | When you want to have a flexible distribution list that   changes membership automatically.                                | - Exchange admin center                             |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            |                                                     |
|                               |      You create and manage dynamic distribution groups in Exchange Online. With   Exchange Online, administrators can define the criteria or rules for dynamic   distribution groups using PowerShell or the Exchange admin center. They can   base these rules on user attributes such as department, location, job title,   or custom attributes. Microsoft 365 automatically calculates and updates the   membership of the dynamic distribution group based on these rules.                                                                     |                                                                                                                            |                                                     |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| Shared   mailbox              | Shared mailboxes are used when multiple people need   access to the same mailbox, such as a company information or support email   address, reception desk, or other function that might be shared by multiple   people. Users with permissions to the group mailbox can send as or send on   behalf of the mailbox email address if the administrator has given that user   permissions to do that. This feature is useful for help and support mailboxes   because users can send emails from "Contoso Support" or "Building   A Reception Desk." | When multiple people need to access the same mailbox,   such as a support email address.                                   | - Microsoft 365 admin center                        |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft 365 admin app                           |
|                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                            | - Microsoft Entra admin center                      |
+-------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+



Conditional Access Policies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. important::
   
   Afin de pouvoir utiliser les Conditional Access Policies, il est d'abord nécessaire de désactiver les Security Defaults du tenant.

   Entra ID > Overview > Properties > Security Defaults

   .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/security-defaults.png






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


Limitations
^^^^^^^^^^^^^^^^^^^^^^^^^^





Teams
----------

Importation utilisateurs (bulk)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Canal privé
~~~~~~~~~~~~~~~~~~

Pour importer des utilisateurs dans un canal privé en mode bulk, il ets nécessaire de posséder un fichier csv formaté de la sorte :

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MS365/csv-private-channel.png


.. code-block:: console

   Install-Module -Name MicrosoftTeams

   Connect-MicrosoftTeams

   Import-Csv -Path "C:\Users\Aleg\Downloads\asasas.csv" | foreach{Add-TeamChannelUser -GroupId 449f4444-4444-4444-b4d5-5b1f08aff324 -DisplayName "Terrain" -user $_.email}





PowerAutomate
-------------------



Exchange
--------------------

.. note::

    Si vous hébergez un serveur Exchange On Premise, lors de la connexion à ce serveur via Outlook, ce dernier passera tout de même par les serveurs de Microsoft.
    Il faut donc faire attention quant aux restrictions par pays pour ne pas bloquer des serverzs Microsoft légitimes !


Exchange Online
^^^^^^^^^^^^^^^^^^^^^^ 


Exchange On-Premises
^^^^^^^^^^^^^^^^^^^^^^^^^^



Build number and release dates
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Disponibles sur le `site de Microsoft <https://learn.microsoft.com/en-us/exchange/new-features/build-numbers-and-release-dates>`_

Commandes utiles
~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Scripts utiles
~~~~~~~~~~~~~~~~


.. note::
   
   `Scripts <https://microsoft.github.io/CSS-Exchange/>`_ fournis par Microsoft.




Limites globales de transport
***************************************

.. code-block:: powershell

   C:\>Get-TransportConfig | fl MaxReceiveSize,MaxSendSize

Cette commande permet de récupérer les limites globales du serveur pour les tailles d'envoi et de réception d'emails

Par défaut, cette limite est 



Migration 2016 vers 2019
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
   Ce chapitre sera basé principalement sur le `KB 0001472 <https://www.petenetlive.com/kb/article/0001472/>`_ de Petenetlive.



Configuration relais SMTP Exchange 2016 (et versions ultérieures)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
   Ce chapitre sera basé principalement sur `cet article <https://www.samuraj-cz.com/en/article/exchange-server-2016-mail-flow-mail-routing-and-connectors/>`_ de Samuraj-cz.com.





Defender
-------------------

Alerts & Rules
^^^^^^^^^^^^^^^^

Depuis Microsoft Defender, il ets possible de créer des alertes suivant certaines conditions.


Permissions de boîte aux lettres ajoutées 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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







Purview
---------

Purview regroupe un ensemble de services permettant la protection, sécurisation, l'audit, et la récupération des données présentes dans 365 mais aussi dans les connecteurs externes.


Insider Risk Management
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

