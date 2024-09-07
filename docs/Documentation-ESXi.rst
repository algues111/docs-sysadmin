====================
Documentation ESXi
====================


Upgrade 6.7 -> 8.0.2B (sur DL380 Gen9)
=======================================

Phase 1 : Préparation
------------------------

https://www.stephenwagner.com/2023/07/24/esxi-8-0-hpe-proliant-dl360p-gen8/

Préparer des backups des différentes VMs tournant sur l'esxi au préalable.
(Stockage > Datastore > Télécharger les dossiers de chaque VM ou via VEEAM)

Accéder à l'ESXi, activer le mode maintenance et SSH

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/1-entrer-en-mode-de-maintenance.webp


Télécharger dans le datastore votre nouveau bundle de VIBs pour ESXi 8.0.2B (ici HPE)
Telecharger l'iso complet de esxi 8.0.2 via le lien donné (soit le ALL soit custom, ici HPE)

Phase 2 : Mise à jour
------------------------

Créer une clé bootable de l'iso
Insérer la clé dans l'esxi
F11 au boot
Selectionner Generic USB
Attendre le boot
Accepter l'installation d'ESXi 8
Choisir le bon support de stockage
Selectionner Upgrade OU installer ESXI8 si vous voulez réinstaller le tout

Erreur : conflits de VIBs ou checksum invalide etc...
Désinstaller les VIBs conflicutels et invalides de la manière suivante :

- rebooter l'esxi normalement
- y accéder via ssh

Liste des commandes :

.. code-block:: console

    esxcli software vib list  
    esxcli software remove -n <vib-name>


Then install the new VIBs bundle :

.. code-block:: console

    esxcli software vib install -d /vmfs/volumes/datastore-name/folder-name/HPE-801.0.0.11.3.1.1-Jun2023-Addon-depot.zip -f

The parameter -f is not always necessary, but it does when you dont have the AcceptanceLevel "Community"

https://github.com/lamw/ghettoVCB/issues/64

esxcli software acceptance set –level=CommunitySupported

https://www.vladan.fr/how-to-understand-vmware-acceptance-levels-for-hosts-and-vibs/

Reessayer commande install

Desormais, un retour de commande expliquant que les paquets VIBs ont été installé avec succès apparaitra.

Reboot la machine

certificat openssl defaillant ;

recréer un :

https://blogs.vmware.com/cloud-foundation/2020/04/14/replacing-vmware-esxi-ssl-certificate-in-vmware-cloud-foundation/

Licences :

https://gist.github.com/ayebrian/646775424393c9a35fb8257f44df1c8b

Installation VCenter Server

Télécharger iso correspondant à la version de l'ESXi (ici 8)

.. warning::

    Vérifier le checksum.
    Si ce dernier n'est pas bon, votre installation plantera à 19% en indiquant une erreur de connection réseau.


Interface ESXi 8.0 :

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/customize-esxi-host-client-theme-0.webp 
*Source : williamlam.com*


.. seealso:: 
    Si vous voulez personnaliser l'interface ESXi, je vous invite à vous rediriger vers cette page :
    https://williamlam.com/2022/10/quick-tip-accessing-new-custom-theme-editor-for-esxi-8-0-host-client.html



Simple updates on legacy ESXi hosts
======================================

Si votre ESXi est hébergé sur un hôte avec des caractéristiques hardwares non officiellement supportées par VMWare, notamment pour le CPU, il faudra effectuer quelques manipulations pour remédier à la problématique.

https://williamlam.com/2022/10/using-vsphere-lifecycle-manager-vlcm-to-remediate-nested-esxi-host-with-cpu-on-the-host-is-not-supported.html

Tout d'adbord, il est impératif de sauvegarder toute donnée sensible et essentielle à votre organisation.
Pour cela, vous pouvez exporter les VM, les sauvegarder avec VEEAM Backups etc...

Prérequis :

- Connexion Internet pour télécharger le bundle offline
- Compte Broadcom pour accéder aux ressources


Custom ISO download
---------------------

Si vous possédez un serveur HPE, DELL, ou provenant de tout autre fabricant disposant d'iso ESXi personnalisé, vous ne les trouverez plus sur leurs propres sites, mais sur celui de Broadcom, étant donné que les licences gratuites de VMWare ESXi ont été abandonnées par ces derniers.


.. note::
    
    Vous trouverez les ISO VMWare seulement depuis le lien ci-dessous.
    
    https://support.broadcom.com/group/ecx/downloads


My Downloads --> VMWare VSphere --> VMware vSphere - Standard 8.0 --> Custom ISOs


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/broadcom-dl.png



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vsphere-dl.png



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vsphere-dl1.png


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vsphere-dl2.png


.. seealso::
    https://pio.nz/2023/01/05/keeping-esxi-up-to-date-on-obsolete-hw/
    https://infra.engineer/miscellaneous/71-vmware-upgrade-esxi-host-with-esxcli
    https://www.vinchin.com/vm-tips/best-practice-to-backup-and-restore-vmware-vcenter.html



VEEAM BACKUP & REPLICATION
==============================

Veeam Backup & Replication est une solution de sauvegardes / backups

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-website.png



Sauvegardes
--------------

Mais tout d'abord, regardons les différents types de sauvegardes.


Sauvegarde complète
^^^^^^^^^^^^^^^^^^^^^^^^

La sauvegarde complète consiste à copier l'intégralité des données d'un système, incluant le système d'exploitation, les applications, les paramètres et les données utilisateur. 

Avantages :
~~~~~~~~~~~~~~~~
- Toutes les données sont sauvegardées
- Restauration facile et rapide

Inconvénients :
~~~~~~~~~~~~~~~~
- Prend beaucoup de temps à réaliser
- Utilise beaucoup d'espace de stockage

Elle est généralement effectuée de façon périodique, par exemple hebdomadairement, et sert souvent de base pour d'autres types de sauvegardes.

Sauvegarde incrémentielle
^^^^^^^^^^^^^^^^^^^^^^^^^^

La sauvegarde incrémentielle ne sauvegarde que les données qui ont été modifiées depuis la dernière sauvegarde, qu'elle soit complète ou incrémentielle.

Avantages :
~~~~~~~~~~~~~~~~
- Rapide à effectuer
- Utilise peu d'espace de stockage

Inconvénients :
~~~~~~~~~~~~~~~~
- La restauration peut être complexe, nécessitant la dernière sauvegarde complète et toutes les sauvegardes incrémentielles suivantes

Sauvegarde différentielle
^^^^^^^^^^^^^^^^^^^^^^^^^^

La sauvegarde différentielle copie tous les fichiers modifiés depuis la dernière sauvegarde complète.

Avantages :
~~~~~~~~~~~~~~~~
- Plus rapide qu'une sauvegarde complète
- Restauration plus simple que la sauvegarde incrémentielle

Inconvénients :
~~~~~~~~~~~~~~~~
- Utilise plus d'espace que la sauvegarde incrémentielle
- Moins rapide que la sauvegarde incrémentielle

Sauvegarde miroir
^^^^^^^^^^^^^^^^^^^^^^

La sauvegarde miroir crée une copie exacte des données source. Elle est souvent utilisée dans des environnements nécessitant une disponibilité élevée des données.

Choix du type de sauvegarde
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Le choix du type de sauvegarde dépend de plusieurs facteurs, notamment :
- Le volume de données à sauvegarder
- La fréquence de modification des données
- Le temps disponible pour effectuer les sauvegardes
- L'espace de stockage disponible
- Les besoins en termes de rapidité de restauration

Une stratégie courante consiste à combiner plusieurs types de sauvegardes, par exemple en effectuant une sauvegarde complète hebdomadaire, complétée par des sauvegardes incrémentielles ou différentielles quotidiennes.



.. tip::
    https://www.veeam.com/fr/products/free/backup-recovery.html?wvideo=z5ezmykjpu

Vous pouvez installer Veeam Backup Recovery And Replication gratuitement avec la community edition.



Installation
------------------

Pour installer Veeam Backup & Replication, il est tout d'abord nécessaire d'installer le l'image du logiciel sur le `site officiel.<https://www.veeam.com/fr/products/free/backup-recovery.html?wvideo=z5ezmykjpu>`_

.. tip::
    Cliquer sur "Testez gratuitement" vous demandera de remplir un formulaire nécessaire au téléchargement du soft.

    Des vidéos explicatives sont aussi disponibles en bas de la page.


Après avoir téléchargé l'iso de 11Go environ, vous pourrez l'ouvrir et cliquer sur "setup".

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/iso-veeam.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/setupexeveeam.png
   

Après l'éxecution du setup, vous aurez plusieurs options d'installation.

Dans notre cas, nous installons Veeam Backup and Replication.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-install-options.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-installationpng


Après que l'installation soit terminée, vous pourrez lancer la console Veeam qui affichera une fenêtre de connexion.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-connection.png




Configuration
--------------------------


Afin de pouvoir créer des backups et les restaurer, il est nécessaire de configurer quelques paramètres.


Tout d'abord, lorsque Veeam est lancé, cliquez sur **Backup Infrastructure** en bas à gauche de la page, cliquez sur **Managed Servers** puis sur **Add Server** en haut à gauche de la page.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-backup-infr.png


A la suite de ces étapes, le logiciel vous demandera de choisir le serveur que vous souhaitez joindre à votre infrastructure de backup.

Ici, nous choisissons VMWare vSphere, puis vSphere.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-addserver.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-addserver-vsphere.png


.. note::
    Comme il est précisé dans le menu d'ajout de serveur, il est préférable d'ajouter vCenter Server si votre ESXi est géré via cette instance.
    Cela facilitera notamment la gestion des permissions de VEEAM sur les VMs de l'ESXi.


Rentrer l'IP ou le nom DNS du serveur vCenter.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-addserver-ip.png


Rentrer les credentials de votre SSO vSphere.

.. important::
    Il est important de renseigner les login de la manière suivante :

    **<vsphere-sso.domain> \ <username>**


.. note::
    Si le port https n'est pas le 443 sur votre serveur, il est nécessaire de le renseigner dans la fenêtre.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/veeam-addserver-id.png





