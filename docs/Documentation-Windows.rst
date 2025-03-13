=====================
Documentation Windows
=====================

Joindre le PC à un domaine LDAP (AD)
=====================================

Prérequis
-----------

- Nom du domaine
- Accès administrateur au domaine


Sous Windows 11, afin de joindre un PC à un domaine Active Directory, il faut se rendre dans les "Paramètres" -> Système -> Informations Système


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/systeme.png



Ici, vous trouverez une section "Liens connexes" dans laquelle se situe "Domaine et groupe de travail".

La fenêtre ci-dessous s'ouvre, dans laquelle il faut cliquer sur "Modifier..."


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/proprietes-systeme.png


Rentrez le nom de domaine voulu, et identifiez-vous avec un compte administrateur du domaine.

Windows Server 2022
=========================


Installer fonctionnalités 
----------------------------

Active Directory DS
^^^^^^^^^^^^^^^^^^^^



.. danger::

    Du moment que votre domaine Active Directory est créé, vous ne pouvez plus changer le nom de votre DC (domain controller).
    



Déplacer partition de récupération
-----------------------------------




I just had this problem, and I followed this guide to resize the drive:

https://thedxt.ca/2023/06/moving-windows-recovery-partition-correctly/

Basically, here are the steps:

Disable the existing Windows Recovery Partition by running reagentc /disable

Use diskpart to remove the recovery partition

list disk

select disk # where # is the disk needing the recovery partition removed

list partition

select partition # where # is the recovery partition

delete partition override to force deletion of the recovery partition

Expand the disk using Disk Management, leaving ~1024 MB at the end of the drive for recreating the recovery partition

Create New Simple Volume for Recovery, NTFS, no drive letter

use diskpart to set the recovery partition attributes

list partition

select partition # where # is the new recovery partition

For GPT disks run set id=de94bba4-06d1-4d40-a16a-bfd50179d6ac & gpt attributes=0x8000000000000001

For MBR disks, run set id=27

Re-enable the recovery partition by running reagentc /enable

Tools & Composants Windows
============================

Console MMC
-------------

La console MMC est une console graphique regroupant tous les outils, gestionnaire, services présents sur Windows.

Elle est donc un must-have pour les administrateurs systèmes Windows.

Vous pouvez l'ouvrir en tapant "mmc" dans le menu "Démarrer" ou en rentrant "mmc.exe" dans l'explorateur de fichiers. 

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/mmc.png



Lorsque nous débarquons la 1ère fois sur notre console, seul un dossier nommé "Racine de la console" est présent.

Pour avoir accès aux outils dont vous nécessitez, il faudra cliquer sur "Fichier" et ensuite "Ajouter/Supprimer un composant logiciel enfichable".


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/mmc-new.png



Avec l'interface ci-dessous, vous serez en mesure d'intégrer les outils désirés à la console mmc.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/mmc-tool.png



Windows Defender
---------------------


Windows Defender est un composant fondamental de Windows afin de garantir l'intégrité et la sécurité de l'OS ainsi que de l'utilisateur.

Cependant il peut s'avérer qu'il bloque des logiciels, téléchargements de pilotes etc... qui nous sont utiles en tant qu'admin ou simplement en tant qu'utilisateur final.


Si Windows Defender SmartScreen bloque l'éxecution d'un .exe, vous pouvez le débloquer facilement depuis "Propriétés" en faisant clique-droit sur le fichier concerné, et "Débloquer".

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/unblock.png


Partage réseau
----------------------------

Si le partage réseau du PC est activé, il est possible d'accéder au répertoire C: de ce dernier de la manière suivante :abbr:

.. code-block:: console

    \\<HOSTNAME>\c$


Office 365
-------------------

Command-line switches
^^^^^^^^^^^^^^^^^^^^^^^^

.. tip::
    Toutes les command-line switches pour Office sont disponibles `ici <https://support.microsoft.com/en-us/office/command-line-switches-for-microsoft-office-products-079164cd-4ef5-4178-b235-441737deb3a6#Category=Outlook>`_


Outlook
^^^^^^^^^^^^

Troubleshooting
~~~~~~~~~~~~~~~~~~


**Liste de commandes pour débugger Outlook**

.. tabs::

   .. tab:: Safemode

      Windows + R (outlook.exe /safe) -> Lance Outlook en safemode sans configuration personnalisée

      .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/execute-outlook-safemode.png


   .. tab:: Cleanviews

      Windows + R (outlook.exe /cleanviews) -> Lance Outlook en rétablissant les réglages d'affichage par défaut. Attention, cette action supprime les profils d'affichage personnalisés.

       .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/execute-outlook-cleanviews.png

       .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/outlook-display-settings.png

     





SYSPREP
------------------

Cette section est basée sur l'article d'IT-Connect.fr sur ce sujet.


.. admonition:: Lien vers l'article

    `SYSPREP par IT-Connect.fr <https://www.it-connect.fr/effectuer-sysprep-windows-11-24h2/>`_


Généralités
^^^^^^^^^^^^^^^^^^^^

Le SYSPREP est une fonctionnalité inclue nativement dans Windows 10 & 11. Elle permet de préparer une machine Windows avant de la dupliquer ou de la déployer sur d'autres ordinateurs.

Grâce à cette fonctionnalité, nous n'avons pas à nous soucier de quelconque conflit de nom, SID ou paramètre spécifique à un user car le SYSPREP va donc effectuer plusieurs actions importantes, dont :

- Réinitialisation du SID : il génère un nouvel identifiant de sécurité pour éviter des conflits entre machines.
- Activation du mode OOBE (Out-Of-Box Experience) : ce mode fait en sorte que, lors du premier démarrage de la machine clonée, Windows affiche un assistant pour personnaliser les paramètres (langue, fuseau horaire, etc.).
- Généralisation de l’image : SYSPREP rend l’image Windows générique pour qu’elle puisse être utilisée sur différents matériels.

Étapes
^^^^^^^^^^^^^^^^

Voici les étapes pour préparer Windows avec SYSPREP.


Disable Bitlocker
~~~~~~~~~~~~~~~~~~~~~~


.. code-block:: console

    Disable-BitLocker -MountPoint "C:"

Pour suivre la progression de la tâche, la commande ci-dessous est disponible :abbr:

.. code-block:: console

    Get-BitLockerVolume | Select MountPoint, VolumeStatus


Lorsque le processus sera terminé, le retour de commande indiquera la colonne "VolumeStatus" en "FullyDecrypted".



Lancement de SYSPREP
~~~~~~~~~~~~~~~~~~~~~~~~~~


1. Depuis l'Explorateur de fichiers de Windows, accédez à l'emplacement suivant : C:\Windows\System32\Sysprep.

2. Lancez l'application "sysprep"

3. Choisissez le mode "Entrer en mode OOBE (Out-of-Box Experience)" et cochez l'option "Généraliser" juste en dessous. Ce mode prépare le système pour avoir la première expérience de démarrage, tandis que l'option cochée va permettre de générer un SID et "supprimer" les dépendances liées au matériel.

4. Choisissez "Arrêter le système" comme option d'extinction.

5. Cliquez sur "OK" pour lancer l'opération. Vous devez patienter. Cette opération peut être assez longue, au moins une dizaine de minutes. Quand ce sera terminé, vous le saurez, car la machine sera éteinte.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/Executer-un-SYSPREP-sur-Windows-11-24H2.png



Suite
~~~~~~~~~~~~~~~~~~~~~~

- Extraire l'image au format WIN pour la déployer avec WDS

OU

- Dupliquer le fichier de disque dur VHDX si c'est une VM

Softwares utiles
====================


Voici une petite liste de logiciels plus ou moins utiles pour Windows.




Best practices
==================


Active Directory Directory Services (AD DS)
---------------------------------------------


PingCastle
^^^^^^^^^^^^^

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/PC_Logo.png



PingCastle est un outil d'audit de sécurité pour domaine Active Directory.

Il permet de générer un rapport html sur lequel se baser pour améliorer et optimser la sécurité du domaine.

:download:`exemple de rapport HTML pour AD fraîchement créé <source/other/ad_hc_srvds.lab.html>`




Autoriser seulement NTLMv2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/ntlm2-only.png



ms-DS-MachineAccountQuota set to 0
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Par défault, le paramètre ms-DS-MachineAccountQuota a une valeur de 10.

Ce qui veut dire que n'importe quel utilisateur authentifié auprès du domaine peut ajouter jusqu'à 10 clients sur le domaine.

Pour changer cet attribut : **Utilisateurs et Ordinateurs Active Directory --> Affichage : Fonctionnalités Avancées --> Propriétés du domaine en question --> Éditeur d'attributs --> Cherhcer ms-DS-MachineAccountQuota et remplacer "10" par "0"**


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/msdsmachine.png


.. seealso::
    https://sid-500.com/2017/09/09/securing-active-directory-who-can-add-computers-to-the-domain-only-the-domain-admin-are-you-sure/



Avoid unexpected schema modifications which could result in domain rebuild
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ce renforcement permet d'éviter des risques de modifications de schémas impromptues.

Il consiste à retirer tous les utilisateurs du groupe "Administrateurs du schéma".

Cela est réversible.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/schema-admins.png



Check if the LAPS tool to handle the native local admnistrator passwords is installed
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Le but est d'être certain qu'une politique de mots de passe est définie pour le compte administrateur local. 

Il est donc nécessaire d'installer ce package depuis le `site officiel de Microsoft. <https://www.it-connect.fr/chapitres/installation-de-laps-sur-un-controleur-de-domaine/>`_ 


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/laps-install.png


Lancez l'installateur .msi, et sélectionner les packages comme suit :

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/laps-packages.png


.. note::
    AdmPwd GPO Extension n'est pas nécessaire sur un contrôleur de domaine.
    En fait, le composant "AdmPwd GPO Extension" doit être déployé sur l'ensemble des machines à gérer via LAPS


Voici l'utilité des différents outils de gestion :

- Fat client UI : outil graphique pour la gestion de LAPS
- PowerShell module : commandes PowerShell pour LAPS
- GPO Editor templates : modèle ADMX de LAPS

Après avoir terminé l'installation graphique de LAPS, il est nécessaire d'importer ses modules et de modifier le schéma de l'Active Directory.

Pour savoir quel poste est maître du schéma, exécutez en Powershell

.. code-block:: console

    Get-ADForest | Select-Object Name, SchemaMaster

Pour importer les modules, exécutez toujours en Powershell :

.. code-block:: console

    Import-Module AdmPwd.PS
    Update-AdmPwdADSchema



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/laps-modules.png


J'en suis à B. Attribuer les droits d'écriture aux machines dans l'article


.. seealso::
    
    `L'article d'IT-Connect.fr sur LAPS.<https://www.it-connect.fr/chapitres/installation-de-laps-sur-un-controleur-de-domaine>`_


Empêcher la délégation de compte pour les utilisateurs admin
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Afin d'éviter des élévations de privilèges non souhaitées et limiter les risques, il est préférable de marquer les utilisateurs ayant des privilièges importants comme "sensibles".



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/account-protected.png


.. warning::
    Si vous marquez un compte comme protégé dans l'AD, cela empechêra sa connexion sur une session hors-ligne (hors-réseau du DC)





Disable IPv6 on Windows
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Depuis la faille de sécurité de Windows liée à IPv6 (CVE-2024-38063), il est conseillé de désactiver ce protocole s'il n'est pas utilisé dans l'organisation.

Nativement, il n'y pas de GPO pour gérer l'intégration d'IPv6 à Windows, mais la communauté a créé un fichier .amdx paliant à cela.


.. note::
    Téléchargez-le :download:`ici <source/other/IPv6Configuration.zip>`





Check disabled accounts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^




Windows
-----------------------


Après une fresh install, il est important de vérifier un nombre relativement important de paramètres afin que l'OS soit le plus optimisé et sécurisé possible.



Langues
^^^^^^^^^^^^^^^^^^^^

En Suisse, le formatage des nombres est différents de la France. Il est donc nécessaire de bien vérifier si le "." est bien le symbole décimal à la place de la ",".

Sans cela, les logiciels de facturations seront soumis à des bugs et erreurs.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/decimal.png


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/decimal1.png


Microsoft Entra Connect
=====================================

Si vous souhaitez joindre votre domaine AD à votre tenant MS365, il faudra installer un agent de synchronisation directement sur un contrôleur de domaine.


.. note::
    Si vous avez créer un domaine AD avec un DNS de type non routable (extensions en .lan, .local etc...), il faudrait rajouter un UPN alternatif (lui étant routable) pour votre AD.



Ajouter un UPN Alternatif
----------------------------

Pour ajouter un suffixe UPN, vous pouvez le faire de 2 manières différentes :

- Via Powershell
- Via la console Active Directory Domains and Trusts

Powershell
^^^^^^^^^^^^

Lister les UPN :

.. code-block:: console

    Get-ADForest | Format-List UPNSuffixes 


Ajouter un UPN :

.. code-block:: console

    Get-ADForest | Set-ADForest -UPNSuffixes @{add="mydomain.com"}


Relistez les UPN pour vérifier le succès de l'ajout.



Console Active Directory Domains and Trusts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/upn-add.png



Après avoir rajouter notre nouveau domaine routable (utilisé pour le tenant MS365), nous pouvons désormais lancer l'agent.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/accueil-ec.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/azure-connect-ec.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/login-ec.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/ad-connect-ec.png 




Si vous rencontrez une erreur contenant "Creation of connector ********.onmicrosoft.com - AAD failed. This may be due to replication delay", il sera nécessaire de renforcer l'utilisation de TLS1.2 sur votre contrôleur de domaine.

https://answers.microsoft.com/en-us/msoffice/forum/all/creation-of-connector-onmicrosoftcom-aad-failed/0c1aaba0-a034-4e96-bd68-de602a39a5b5

https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/reference-connect-tls-enforcement


Exchange Online
======================

Désactiver l'automapping
----------------------------------


.. code-block:: console

    Add-MailboxPermission -Identity <MailboxIdentity> -User <UserIdentity> -AccessRights FullAccess -AutoMapping $false


.. note::

    https://learn.microsoft.com/en-us/outlook/troubleshoot/profiles-and-accounts/remove-automapping-for-shared-mailbox



Send as permissions
-------------------------

.. code-block:: console
    
    Add-RecipientPermission | Remove-RecipientPermission> -Identity <MailboxOrGroupIdentity> -Trustee <DelegateIdentity> -AccessRights SendAs

.. note::

    https://learn.microsoft.com/en-us/exchange/recipients-in-exchange-online/manage-permissions-for-recipients