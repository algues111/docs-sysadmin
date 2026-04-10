=========================================
Service DFS Namespace sous Windows Server
=========================================

.. contents:: Table des matières
   :depth: 3
   :local:

.. sectnum::


Vue d'ensemble
==============

Qu'est-ce que le service DFS Namespace ?
----------------------------------------

Le service **DFS Namespace** (Distributed File System Namespace, ou DFS-N) est
un rôle Windows Server permettant de regrouper des dossiers partagés situés sur
différents serveurs sous une arborescence logique unique. Les utilisateurs
accèdent à un chemin UNC stable et cohérent, indépendamment de l'emplacement
physique des données.

Un chemin UNC DFS typique ressemble à ceci ::

   \\contoso.com\Partage\Documents
   \\contoso.com\Partage\Logiciels

Lorsqu'un client se connecte à ce chemin, le serveur de namespace le redirige
de manière transparente vers le partage réseau sous-jacent, en fonction de la
disponibilité et du coût de site.

.. note::

   Ce guide couvre uniquement **DFS Namespace (DFS-N)**. La réplication DFS
   (DFS-R), qui synchronise les données entre serveurs, est un service de rôle
   distinct et **n'est pas abordée ici**.

Avantages principaux
--------------------

- **Accès simplifié** — Les utilisateurs utilisent un chemin UNC unique et
  stable, quelle que soit la localisation physique du serveur.
- **Transparence de localisation** — Les serveurs peuvent être déplacés ou
  renommés sans impacter le chemin d'accès des utilisateurs.
- **Haute disponibilité** — Plusieurs serveurs de namespace peuvent servir le
  même espace de noms, éliminant ainsi le point de défaillance unique.
- **Scalabilité** — Des centaines de cibles de dossiers peuvent être agrégées
  sous un seul namespace.

Types de namespaces
-------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Namespace basé sur le domaine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Stocké dans **Active Directory Domain Services (AD DS)**.
- Le chemin racine suit le format ``\\domaine.com\namespace``.
- Recommandé pour la grande majorité des environnements de production.
- Supporte plusieurs serveurs de namespace pour la redondance.
- Requiert AD DS et un serveur joint au domaine.
- Supporte jusqu'à **50 000 dossiers avec cibles** en mode Windows Server 2008.

************************************
Namespace autonome (Stand-Alone)
************************************

- Stocké localement sur un seul serveur.
- Le chemin racine suit le format ``\\nomserveur\namespace``.
- Ne nécessite pas AD DS.
- Adapté aux groupes de travail, environnements DMZ ou scénarios simples.
- Limité à un seul serveur de namespace ; pas de redondance intégrée.

.. tip::

   Microsoft recommande l'utilisation de **namespaces basés sur le domaine**
   avec le mode Windows Server 2008 activé pour tout nouveau déploiement en
   production.


Prérequis
=========

Exigences logicielles et de rôle
---------------------------------

Avant de déployer DFS Namespace, vérifiez les points suivants :

- **Système d'exploitation** : Windows Server 2012 R2 ou version ultérieure
  (Windows Server 2019 / 2022 recommandé).
- **Appartenance au domaine** : Obligatoire pour les namespaces basés sur le
  domaine.
- **Service de rôle** : ``Espaces de noms DFS`` installé (inclus dans le rôle
  ``Services de fichiers et de stockage``).
- **Outils de gestion DFS** : Installés sur le serveur ou sur un poste
  d'administration.

Droits et permissions nécessaires
----------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Pour la création d'un namespace
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Appartenance au groupe **Administrateurs locaux** sur chaque serveur de
  namespace.
- Appartenance au groupe **Admins du domaine**, ou permissions déléguées sur
  le partage ``SYSVOL`` et le conteneur AD DS correspondant pour les namespaces
  basés sur le domaine.

****************************************************
Pour la gestion des cibles de dossiers
****************************************************

- Permissions **Lecture/Écriture** sur les dossiers partagés sous-jacents
  utilisés comme cibles.
- L'accès **Lecture** minimum est requis sur les cibles pour vérifier leur
  disponibilité.

Considérations réseau
---------------------

- Le port **TCP 445** (SMB) doit être ouvert entre les clients et les serveurs
  de namespace.
- Les ports **TCP/UDP 135** (RPC endpoint mapper) et les ports RPC dynamiques
  doivent être accessibles pour les référencements DFS.
- Le DNS doit résoudre correctement le nom de domaine ainsi que les noms d'hôte
  des serveurs.
- Pour les déploiements multi-sites, configurez correctement **Sites et services
  Active Directory** afin que DFS calcule les coûts de site et fournisse des
  référencements optimaux.


Installation du service de rôle DFS Namespace
=============================================

Installation via le Gestionnaire de serveur (interface graphique)
-----------------------------------------------------------------

#. Ouvrez le **Gestionnaire de serveur**.
#. Cliquez sur **Gérer** → **Ajouter des rôles et des fonctionnalités**.
#. Avancez dans l'assistant jusqu'à la page **Rôles de serveurs**.
#. Développez **Services de fichiers et de stockage** →
   **Services de fichiers et iSCSI**.
#. Cochez **Espaces de noms DFS**.
#. Sur la page **Fonctionnalités**, vérifiez que les **Outils de gestion DFS**
   sont bien sélectionnés (ajoutés automatiquement dans la plupart des cas).
#. Terminez l'assistant et cliquez sur **Installer**.

Installation via PowerShell
---------------------------

Ouvrez une session PowerShell en mode administrateur et exécutez :

.. code-block:: powershell

   Install-WindowsFeature -Name FS-DFS-Namespace, RSAT-DFS-Mgmt-Con `
       -IncludeManagementTools

Vérifiez l'installation :

.. code-block:: powershell

   Get-WindowsFeature -Name FS-DFS-Namespace, RSAT-DFS-Mgmt-Con

Les deux fonctionnalités doivent afficher ``[X]`` (installé).

Vérification du service DFS
----------------------------

.. code-block:: powershell

   Get-Service -Name Dfs | Select-Object Name, Status, StartType

Le service doit être en état **Running** avec un démarrage de type
**Automatic**.

.. note::

   Le service **DFS Namespace** (``Dfs``) doit être **en cours d'exécution**
   sur chaque serveur jouant le rôle de serveur de namespace.


Création d'un namespace DFS
============================

Création via la console de gestion DFS
---------------------------------------

#. Ouvrez **Gestion du système de fichiers distribués** (``dfsmgmt.msc``).
#. Dans le volet gauche, faites un clic droit sur **Espaces de noms** et
   sélectionnez **Nouvel espace de noms…**
#. Saisissez le **nom du serveur** qui hébergera la racine du namespace, puis
   cliquez sur **Suivant**.
#. Saisissez un **nom** pour le namespace (ex. : ``Partage``). Ce nom fait
   partie du chemin UNC. Cliquez sur **Modifier les paramètres…** pour
   configurer les permissions du partage, puis cliquez sur **Suivant**.
#. Choisissez le type de namespace :

   - **Espace de noms basé sur le domaine** *(recommandé)*
   - **Espace de noms autonome**

#. Vérifiez le résumé et cliquez sur **Créer**.

Le namespace ``\\contoso.com\Partage`` est maintenant disponible.

Création via PowerShell
------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Namespace basé sur le domaine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: powershell

   New-DfsnRoot -Path "\\contoso.com\Partage" `
                -TargetPath "\\SRV-FS01\Partage" `
                -Type DomainV2 `
                -Description "Namespace principal de l'entreprise"

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Namespace autonome
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   New-DfsnRoot -Path "\\SRV-FS01\Partage" `
                -TargetPath "\\SRV-FS01\Partage" `
                -Type Standalone `
                -Description "Namespace autonome"

.. tip::

   Le type ``DomainV2`` correspond au **mode Windows Server 2008**. Il est
   fortement recommandé par rapport au type ``Domain`` (mode Windows 2000
   Server) pour tout nouveau déploiement.

Bonnes pratiques lors de la création
-------------------------------------

- Nommez le namespace de façon **claire et fonctionnelle** (ex. :
  ``\\contoso.com\Data``, ``\\contoso.com\Apps``).
- Évitez les espaces et les caractères spéciaux dans le nom du namespace.
- Créez le dossier racine sur un volume dédié avec suffisamment d'espace.
- Documentez la structure du namespace pour faciliter les futures
  maintenances.


Ajout d'un serveur de namespace
================================

Pourquoi ajouter un serveur de namespace supplémentaire ?
----------------------------------------------------------

Pour les **namespaces basés sur le domaine**, il est possible d'ajouter
plusieurs serveurs de namespace afin de garantir la redondance. Si un serveur
devient indisponible, les clients reçoivent automatiquement les référencements
depuis un autre serveur sans interruption de service.

.. important::

   Chaque serveur ajouté comme serveur de namespace doit :

   - Avoir le service de rôle **Espaces de noms DFS** installé.
   - Être **joint au domaine** (pour les namespaces basés sur le domaine).
   - Avoir le service ``DFS`` en cours d'exécution.
   - Disposer d'un partage correspondant à la racine du namespace.

Ajout via la console de gestion DFS
-------------------------------------

#. Ouvrez **Gestion du système de fichiers distribués** (``dfsmgmt.msc``).
#. Dans le volet gauche, développez **Espaces de noms** et sélectionnez le
   namespace cible (ex. : ``\\contoso.com\Partage``).
#. Faites un clic droit sur le namespace et sélectionnez
   **Ajouter un serveur d'espace de noms…**
#. Dans la boîte de dialogue, saisissez le **nom NetBIOS** ou le **FQDN** du
   nouveau serveur (ex. : ``SRV-FS02``).
#. Cliquez sur **OK**.
#. Sélectionnez le namespace, puis cliquez sur l'onglet **Serveurs d'espace
   de noms** pour vérifier que les deux serveurs sont listés et affichent
   le statut **En ligne**.

Ajout via PowerShell
---------------------

.. code-block:: powershell

   # Ajouter un second serveur de namespace
   New-DfsnRootTarget -Path "\\contoso.com\Partage" `
                      -TargetPath "\\SRV-FS02\Partage"

Vérification des serveurs de namespace :

.. code-block:: powershell

   Get-DfsnRootTarget -Path "\\contoso.com\Partage"

Les deux cibles ``\\SRV-FS01\Partage`` et ``\\SRV-FS02\Partage``
doivent être listées.

Prérequis sur le nouveau serveur
----------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Création du dossier et du partage sur le nouveau serveur
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: powershell

   # Créer le dossier racine
   New-Item -Path "C:\DFSRoots\Partage" -ItemType Directory

   # Créer le partage SMB
   New-SmbShare -Name "Partage" `
                -Path "C:\DFSRoots\Partage" `
                -FullAccess "Tout le monde"

.. note::

   Le partage sous-jacent doit exister sur le nouveau serveur **avant** de
   l'ajouter comme serveur de namespace dans la console DFS.


Déplacement du service DFS Namespace vers un autre serveur
==========================================================

Contexte et cas d'usage
------------------------

Il peut être nécessaire de déplacer le service DFS Namespace d'un serveur à un
autre dans les situations suivantes :

- Remplacement ou décommissionnement d'un serveur.
- Migration vers un nouveau matériel ou vers une infrastructure virtualisée.
- Consolidation des rôles serveurs.

.. warning::

   Si vous supprimez le serveur hébergeant le namespace **sans** l'avoir
   préalablement transféré sur un autre serveur, le service DFS cessera de
   référencer les clients et les accès aux partages seront interrompus.

Procédure de déplacement (source : Falcon IT Services)
-------------------------------------------------------

La procédure se déroule en trois étapes principales :

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Étape 1 — Installer DFS Namespace sur le nouveau serveur
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Installez le service de rôle **Espaces de noms DFS** sur le serveur de
destination (voir section `Installation du service de rôle DFS Namespace`_).

Vérifiez que le service ``Dfs`` est bien démarré :

.. code-block:: powershell

   Get-Service -Name Dfs

*************************************************************
Étape 2 — Ajouter le nouveau serveur comme serveur de namespace
*************************************************************

#. Ouvrez **Gestion du système de fichiers distribués** (``dfsmgmt.msc``).
#. Faites un clic droit sur le namespace concerné.
#. Sélectionnez **Ajouter un serveur d'espace de noms…**
#. Saisissez le **nom NetBIOS** du nouveau serveur de namespace.
#. Cliquez sur **OK**.
#. Vérifiez dans l'onglet **Serveurs d'espace de noms** que les deux serveurs
   sont bien listés et affichent le statut **En ligne**.

Via PowerShell :

.. code-block:: powershell

   New-DfsnRootTarget -Path "\\contoso.com\Partage" `
                      -TargetPath "\\NOUVEAU-SRV\Partage"

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Étape 3 — Supprimer l'ancien serveur de namespace
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Une fois le nouveau serveur de namespace confirmé comme opérationnel :

#. Dans la console DFS Management, sélectionnez l'onglet
   **Serveurs d'espace de noms**.
#. Faites un clic droit sur l'**ancien serveur** et sélectionnez
   **Supprimer le serveur d'espace de noms**.
#. Confirmez la suppression.

Via PowerShell :

.. code-block:: powershell

   Remove-DfsnRootTarget -Path "\\contoso.com\Partage" `
                         -TargetPath "\\ANCIEN-SRV\Partage" `
                         -Confirm:$false

.. tip::

   Après le déplacement, testez l'accès depuis un poste client en naviguant
   vers ``\\contoso.com\Partage`` pour confirmer que les référencements
   fonctionnent correctement depuis le nouveau serveur.


Création de dossiers de namespace (liens DFS)
=============================================

Qu'est-ce qu'un dossier de namespace ?
---------------------------------------

Un **dossier de namespace** (également appelé lien DFS) est un dossier virtuel
au sein du namespace qui pointe vers une ou plusieurs **cibles de dossier**
(partages réseau réels sur des serveurs de fichiers). Du point de vue de
l'utilisateur, il apparaît comme un simple sous-dossier du namespace.

Exemple de structure ::

   \\contoso.com\Partage\Documents   →  \\SRV-FS01\Documents$
   \\contoso.com\Partage\Logiciels   →  \\SRV-FS02\Logiciels$
   \\contoso.com\Partage\RH          →  \\SRV-FS03\RH$

Ajout d'un dossier via la console de gestion DFS
-------------------------------------------------

#. Ouvrez **Gestion du système de fichiers distribués** (``dfsmgmt.msc``).
#. Développez **Espaces de noms** et sélectionnez le namespace cible.
#. Faites un clic droit sur le namespace et sélectionnez
   **Nouveau dossier…**
#. Saisissez un **Nom** pour le dossier (ex. : ``Documents``).
#. Cliquez sur **Ajouter…** pour ajouter une ou plusieurs cibles de dossier.
#. Dans la boîte de dialogue **Ajouter une cible de dossier**, saisissez le
   chemin UNC du partage réseau (ex. : ``\\SRV-FS01\Documents$``).
#. Cliquez sur **OK** pour valider.

Ajout d'un dossier via PowerShell
----------------------------------

^^^^^^^^^^^^^^^^^^^^^^^
Créer un dossier simple
^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: powershell

   New-DfsnFolder -Path "\\contoso.com\Partage\Documents" `
                  -TargetPath "\\SRV-FS01\Documents$" `
                  -Description "Dossier des documents d'entreprise"

*************************************
Ajouter plusieurs cibles à un dossier
*************************************

.. code-block:: powershell

   # Ajouter une première cible
   New-DfsnFolder -Path "\\contoso.com\Partage\Logiciels" `
                  -TargetPath "\\SRV-FS01\Logiciels$"

   # Ajouter une seconde cible au même dossier
   New-DfsnFolderTarget -Path "\\contoso.com\Partage\Logiciels" `
                        -TargetPath "\\SRV-FS02\Logiciels$"

Lister les dossiers et cibles existants
----------------------------------------

.. code-block:: powershell

   # Lister tous les dossiers d'un namespace
   Get-DfsnFolder -Path "\\contoso.com\Partage\*"

   # Lister les cibles d'un dossier spécifique
   Get-DfsnFolderTarget -Path "\\contoso.com\Partage\Documents"

Suppression d'un dossier ou d'une cible
-----------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Supprimer une cible de dossier
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: powershell

   Remove-DfsnFolderTarget -Path "\\contoso.com\Partage\Documents" `
                           -TargetPath "\\SRV-FS01\Documents$" `
                           -Confirm:$false

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Supprimer un dossier entier
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   Remove-DfsnFolder -Path "\\contoso.com\Partage\Documents" `
                     -Confirm:$false

.. warning::

   La suppression d'un dossier de namespace ne supprime **pas** les données
   sur le serveur de fichiers sous-jacent. Seul le lien virtuel dans le
   namespace est supprimé.


Fonctionnalités avancées
========================

Énumération basée sur l'accès (ABE)
-------------------------------------

L'**énumération basée sur l'accès** (Access-Based Enumeration) masque
automatiquement les dossiers auxquels un utilisateur n'a pas accès. Cela
améliore la sécurité et la clarté pour les utilisateurs finaux.

Activation via PowerShell :

.. code-block:: powershell

   Set-DfsnRoot -Path "\\contoso.com\Partage" `
                -EnableAccessBasedEnumeration $true

Activation via la console DFS :

#. Clic droit sur le namespace → **Propriétés**.
#. Onglet **Avancé**.
#. Cochez **Activer l'énumération basée sur l'accès**.

Référencement et priorité des cibles
--------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Ordre de référencement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

DFS utilise les **coûts de site AD DS** pour déterminer quelle cible proposer
en priorité à un client. Les cibles situées sur le même site que le client
sont préférées.

Il est possible de définir manuellement la priorité d'une cible :

.. code-block:: powershell

   Set-DfsnFolderTarget -Path "\\contoso.com\Partage\Documents" `
                        -TargetPath "\\SRV-FS01\Documents$" `
                        -ReferralPriorityClass GlobalHigh

Les valeurs possibles pour ``ReferralPriorityClass`` sont :

- ``GlobalHigh`` — Priorité la plus haute, quel que soit le site.
- ``SiteCostHigh`` — Haute priorité parmi les cibles de même coût de site.
- ``SiteCostNormal`` — Priorité normale (valeur par défaut).
- ``SiteCostLow`` — Basse priorité parmi les cibles de même coût de site.
- ``GlobalLow`` — Priorité la plus basse, quel que soit le site.

Durée de vie du cache de référencement (TTL)
---------------------------------------------

La durée de vie (TTL) du cache de référencement détermine combien de temps
un client met en cache la liste des cibles avant de re-consulter le serveur de
namespace. La valeur par défaut est **300 secondes** (5 minutes).

.. code-block:: powershell

   # Modifier le TTL du namespace racine (en secondes)
   Set-DfsnRoot -Path "\\contoso.com\Partage" `
                -TimeToLiveSec 600

   # Modifier le TTL d'un dossier spécifique
   Set-DfsnFolder -Path "\\contoso.com\Partage\Documents" `
                  -TimeToLiveSec 300

.. tip::

   Diminuez le TTL lors d'une migration pour que les clients basculent plus
   rapidement vers la nouvelle cible. Remettez une valeur plus élevée après
   la migration pour réduire la charge sur les serveurs de namespace.


Dépannage courant
=================

Le namespace n'est pas accessible depuis les clients
-----------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Vérifications à effectuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. Vérifiez que le service ``Dfs`` est démarré sur le serveur de namespace :

   .. code-block:: powershell

      Get-Service -Name Dfs

#. Vérifiez la résolution DNS du nom de domaine et du serveur :

   .. code-block:: powershell

      Resolve-DnsName contoso.com
      Resolve-DnsName SRV-FS01

#. Vérifiez que le partage racine est accessible :

   .. code-block:: powershell

      Test-Path "\\SRV-FS01\Partage"

#. Vérifiez les règles de pare-feu (TCP 445, TCP/UDP 135).

#. Consultez l'**Observateur d'événements** → **Journaux des applications et
   des services** → **DFS Replication** (et journaux système) pour détecter
   des erreurs.

Le statut d'un serveur de namespace est « Hors ligne »
-------------------------------------------------------

- Vérifiez que le service ``Dfs`` est démarré sur ce serveur.
- Vérifiez que le partage racine existe et est accessible.
- Vérifiez la connectivité réseau entre le serveur de gestion et le serveur
  de namespace concerné.

.. code-block:: powershell

   # Vérifier les cibles de la racine et leur état
   Get-DfsnRootTarget -Path "\\contoso.com\Partage"

Les utilisateurs sont redirigés vers une cible incorrecte
----------------------------------------------------------

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Vérification de la configuration des sites AD DS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Assurez-vous que les sous-réseaux IP des clients et des serveurs sont
correctement associés aux sites AD DS dans **Sites et services Active
Directory**. Une mauvaise configuration des sites entraîne des référencements
non optimaux.

.. code-block:: powershell

   # Afficher les sites AD DS
   Get-ADReplicationSite -Filter *

   # Afficher les sous-réseaux AD DS
   Get-ADReplicationSubnet -Filter *


Récapitulatif des commandes PowerShell essentielles
====================================================

.. list-table:: Commandes PowerShell DFS Namespace
   :header-rows: 1
   :widths: 45 55

   * - Commande
     - Description
   * - ``New-DfsnRoot``
     - Créer un nouveau namespace DFS
   * - ``Get-DfsnRoot``
     - Lister les namespaces existants
   * - ``Remove-DfsnRoot``
     - Supprimer un namespace
   * - ``New-DfsnRootTarget``
     - Ajouter un serveur de namespace
   * - ``Get-DfsnRootTarget``
     - Lister les serveurs de namespace
   * - ``Remove-DfsnRootTarget``
     - Supprimer un serveur de namespace
   * - ``New-DfsnFolder``
     - Créer un dossier de namespace
   * - ``Get-DfsnFolder``
     - Lister les dossiers d'un namespace
   * - ``Remove-DfsnFolder``
     - Supprimer un dossier de namespace
   * - ``New-DfsnFolderTarget``
     - Ajouter une cible à un dossier
   * - ``Get-DfsnFolderTarget``
     - Lister les cibles d'un dossier
   * - ``Remove-DfsnFolderTarget``
     - Supprimer une cible de dossier
   * - ``Set-DfsnRoot``
     - Modifier les propriétés d'un namespace
   * - ``Set-DfsnFolder``
     - Modifier les propriétés d'un dossier
   * - ``Set-DfsnFolderTarget``
     - Modifier les propriétés d'une cible


Références
==========

- Microsoft Learn — `DFS Namespaces overview
  <https://learn.microsoft.com/en-us/windows-server/storage/dfs-namespaces/dfs-overview>`_
- Microsoft Learn — `DFS Namespaces PowerShell reference
  <https://learn.microsoft.com/en-us/powershell/module/dfsn/>`_
- Microsoft Learn — `Checklist: Deploy DFS Namespaces
  <https://learn.microsoft.com/en-us/windows-server/storage/dfs-namespaces/checklist-deploy-dfs-namespaces>`_
- Falcon IT Services — `Move the DFS Namespace to another server
  <https://falconitservices.com/move-the-dfs-namespace-to-another-server/>`_
- Microsoft Tech Community — Best practices for DFS Namespace design
- Microsoft Q&A — DFS Namespace troubleshooting forums
