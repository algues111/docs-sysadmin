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

Active Directory
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

Tools Windows
===============

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

Il est donc nécessaire d'installer ce package depuis le `site officiel de Microsoft.<https://www.it-connect.fr/chapitres/installation-de-laps-sur-un-controleur-de-domaine/>`_ 


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
    `L'article d'IT-Connect.fr sur LAPS.<https://www.it-connect.fr/chapitres/installation-de-laps-sur-un-controleur-de-domaine/>`_



Disable IPv6 on Windows
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Depuis la faille de sécurité de Windows liée à IPv6 (CVE-2024-38063), il est conseillé de désactiver ce protocole s'il n'est pas utilisé dans l'organisation.

Nativement, il n'y pas de GPO pour gérer l'intégration d'IPv6 à Windows, mais la communauté a créé un fichier .amdx paliant à cela.


.. note::
    Téléchargez-le :download:`ici <source/other/IPv6Configuration.zip>`





Check disabled accounts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



