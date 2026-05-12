================
Active Directory
================

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



Rôles FSMO
==================

- Schema Master :  contrôle toutes les mises à jour et les modifications apportées au schéma. Pour mettre à jour le schéma d’une forêt, vous devez avoir accès au contrôleur de schéma. Il ne peut y avoir qu’un seul contrôleur de schéma dans toute la forêt.
- Domain Naming Master : contrôle l’ajout ou la suppression de domaines dans la forêt. Il ne peut y avoir qu’un seul maître d’opérations des noms de domaine dans toute la forêt.
- Infrastructure Master :  l’infrastructure est responsable de la mise à jour des références des objets de son domaine vers des objets d’autres domaines. À tout moment, il ne peut y avoir qu’un seul contrôleur de domaine agissant en tant que maître d’infrastructure dans chaque domaine.
- RID Master : responsable du traitement des demandes de pool RID provenant de tous les contrôleurs de domaine d’un domaine particulier. À tout moment, il ne peut y avoir qu’un seul contrôleur de domaine agissant en tant que maître RID dans le domaine.
- PDC Emulator : l’émulateur PDC est un contrôleur de domaine qui se présente comme le contrôleur de domaine principal (PDC) pour les stations de travail, les serveurs membres et les contrôleurs de domaine qui exécutent des versions antérieures de Windows. 


.. danger::

    Du moment que votre domaine Active Directory est créé, vous ne pouvez plus changer le nom de votre DC (domain controller).
    
Utilisateurs
-----------------


Profil
^^^^^^^^^^^^


Au sein du profil de l'utilisateur, il est possible de créer un dossier personnel sur un partage de fichiers grâce à un chemin de ce type :

\\labo.local\Shared\Employes\Personnel\%username%

Cela créera le dossier automatiquement avec les autorisations propres à l'utilisateur en question.

GPMC
-----------

La GPMC (Group Policy Management Console), vient de paire avec l'installation du rôle AD DS.

Avec cette console, vous pouvez configurer des stratégies de groupe (GPO) pour faciliter la gestion du parc informatique et des utilisateurs en fonction de vos besoins.

Par exemple grâce à celles-ci, il est possible de configurer le mappage de lecteurs réseau pour une OU spécifique, de limiter certains protocoles réseau (désactiver IPv6 par exemple), forcer le DNSSEC...


Il existe 2 types de GPO :

- GPO utilisateur 
- GPO ordinateur