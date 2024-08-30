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

Il permet de générer un rapport sur lequel se baser pour améliorer et optimser la sécurité du domaine.


Autoriser seulement NTLMv2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/ntlmv2-only.png



ms-DS-MachineAccountQuota set to 0
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Par défault, le paramètre ms-DS-MachineAccountQuota a une valeur de 10.

Ce qui veut dire que n'importe quel utilisateur authentifié auprès du domaine peut ajouter jusqu'à 10 clients sur le domaine.

Pour changer cet attribut : Utilisateurs et Ordinateurs Active Directory --> Affichage : Fonctionnalités Avancées --> Propriétés du domaine en question --> Éditeur d'attributs --> Cherhcer ms-DS-MachineAccountQuota et remplacer "10" par "0"



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Windows/msdsmachine.png







