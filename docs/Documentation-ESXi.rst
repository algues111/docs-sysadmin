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



Après avoir téléchargé le bundle custom .zip, transférez le dans le datastore de votre hôte ou dans un volume connecté à ce dernier.

Activez SSH et connectez-vous.


.. code-block:: console

    [root@esx2:/vmfs/volumes/62c80b1d-db652382-0617-000af7389070/downloads] esxcli software vib update --depot=/vmfs/volumes/62c80b1d-db652382-0617-000af7389070/downloads/VMware-ESXi-8.02b-20842708-depot.zip


L'installation des vib peut prendre du temps. Si cette dernière a été effectuée avec succès, vous aurez cette output dans la CLI :


.. code-block:: console

   Installation Result
   Message: The update completed successfully, but the system needs to be rebooted for the changes to be effective.
   Reboot Required: true
   VIBs Installed: VMW_bootbank_ntg3_4.1.8.0-4vmw.703.0.65.20842708, VMware_bootbank_bmcal_7.0.3-0.65.20842708, VMware_bootbank_cpu-microcode_7.0.3-0.65.20842708, VMware_bootbank_crx_7.0.3-0.65.20842708, VMware_bootbank_esx-base_7.0.3-0.65.20842708, VMware_bootbank_esx-dvfilter-generic-fastpath_7.0.3-0.65.20842708, VMware_bootbank_esx-ui_2.1.1-20188605, VMware_bootbank_esx-update_7.0.3-0.65.20842708, VMware_bootbank_esx-xserver_7.0.3-0.65.20842708, VMware_bootbank_esxio-combiner_7.0.3-0.65.20842708, VMware_bootbank_gc_7.0.3-0.65.20842708, VMware_bootbank_loadesx_7.0.3-0.65.20842708, VMware_bootbank_native-misc-drivers_7.0.3-0.65.20842708, VMware_bootbank_trx_7.0.3-0.65.20842708, VMware_bootbank_vdfs_7.0.3-0.65.20842708, VMware_bootbank_vsan_7.0.3-0.65.20842708, VMware_bootbank_vsanhealth_7.0.3-0.65.20842708, VMware_locker_tools-light_12.1.0.20219665-20841705
   VIBs Removed: VMW_bootbank_ntg3_4.1.7.0-0vmw.703.0.20.19193900, VMware_bootbank_bmcal_7.0.3-0.55.20328353, VMware_bootbank_cpu-microcode_7.0.3-0.55.20328353, VMware_bootbank_crx_7.0.3-0.55.20328353, VMware_bootbank_esx-base_7.0.3-0.55.20328353, VMware_bootbank_esx-dvfilter-generic-fastpath_7.0.3-0.55.20328353, VMware_bootbank_esx-ui_1.43.8-19798623, VMware_bootbank_esx-update_7.0.3-0.55.20328353, VMware_bootbank_esx-xserver_7.0.3-0.55.20328353, VMware_bootbank_esxio-combiner_7.0.3-0.55.20328353, VMware_bootbank_gc_7.0.3-0.55.20328353, VMware_bootbank_loadesx_7.0.3-0.55.20328353, VMware_bootbank_native-misc-drivers_7.0.3-0.55.20328353, VMware_bootbank_trx_7.0.3-0.55.20328353, VMware_bootbank_vdfs_7.0.3-0.55.20328353, VMware_bootbank_vsan_7.0.3-0.55.20328353, VMware_bootbank_vsanhealth_7.0.3-0.55.20328353, VMware_locker_tools-light_12.0.0.19345655-20036586
   VIBs Skipped: VMW_bootbank_atlantic_1.0.3.0-8vmw.703.0.20.19193900, VMW_bootbank_bnxtnet_216.0.50.0-44vmw.703.0.50.20036589, VMW_bootbank_bnxtroce_216.0.58.0-23vmw.703.0.50.20036589, VMW_bootbank_brcmfcoe_12.0.1500.2-3vmw.703.0.20.19193900, VMW_bootbank_elxiscsi_12.0.1200.0-9vmw.703.0.20.19193900, VMW_bootbank_elxnet_12.0.1250.0-5vmw.703.0.20.19193900, VMW_bootbank_i40en_1.11.1.31-1vmw.703.0.20.19193900, VMW_bootbank_iavmd_2.7.0.1157-2vmw.703.0.20.19193900, VMW_bootbank_icen_1.4.1.20-1vmw.703.0.50.20036589, VMW_bootbank_igbn_1.4.11.2-1vmw.703.0.20.19193900, VMW_bootbank_ionic-en_16.0.0-16vmw.703.0.20.19193900, VMW_bootbank_irdman_1.3.1.22-1vmw.703.0.50.20036589, VMW_bootbank_iser_1.1.0.1-1vmw.703.0.50.20036589, VMW_bootbank_ixgben_1.7.1.35-1vmw.703.0.20.19193900, VMW_bootbank_lpfc_14.0.169.26-5vmw.703.0.50.20036589, VMW_bootbank_lpnic_11.4.62.0-1vmw.703.0.20.19193900, VMW_bootbank_lsi-mr3_7.718.02.00-1vmw.703.0.20.19193900, VMW_bootbank_lsi-msgpt2_20.00.06.00-4vmw.703.0.20.19193900, VMW_bootbank_lsi-msgpt35_19.00.02.00-1vmw.703.0.20.19193900, VMW_bootbank_lsi-msgpt3_17.00.12.00-1vmw.703.0.20.19193900, VMW_bootbank_mtip32xx-native_3.9.8-1vmw.703.0.20.19193900, VMW_bootbank_ne1000_0.9.0-1vmw.703.0.50.20036589, VMW_bootbank_nenic_1.0.33.0-1vmw.703.0.20.19193900, VMW_bootbank_nfnic_4.0.0.70-1vmw.703.0.20.19193900, VMW_bootbank_nhpsa_70.0051.0.100-4vmw.703.0.20.19193900, VMW_bootbank_nmlx4-core_3.19.16.8-2vmw.703.0.20.19193900, VMW_bootbank_nmlx4-en_3.19.16.8-2vmw.703.0.20.19193900, VMW_bootbank_nmlx4-rdma_3.19.16.8-2vmw.703.0.20.19193900, VMW_bootbank_nmlx5-core_4.19.16.11-1vmw.703.0.20.19193900, VMW_bootbank_nmlx5-rdma_4.19.16.11-1vmw.703.0.20.19193900, VMW_bootbank_nvme-pcie_1.2.3.16-1vmw.703.0.20.19193900, VMW_bootbank_nvmerdma_1.0.3.5-1vmw.703.0.20.19193900, VMW_bootbank_nvmetcp_1.0.0.1-1vmw.703.0.35.19482537, VMW_bootbank_nvmxnet3-ens_2.0.0.22-1vmw.703.0.20.19193900, VMW_bootbank_nvmxnet3_2.0.0.30-1vmw.703.0.20.19193900, VMW_bootbank_pvscsi_0.1-4vmw.703.0.20.19193900, VMW_bootbank_qcnic_1.0.15.0-14vmw.703.0.20.19193900, VMW_bootbank_qedentv_3.40.5.53-22vmw.703.0.20.19193900, VMW_bootbank_qedrntv_3.40.5.53-18vmw.703.0.20.19193900, VMW_bootbank_qfle3_1.0.67.0-22vmw.703.0.20.19193900, VMW_bootbank_qfle3f_1.0.51.0-22vmw.703.0.20.19193900, VMW_bootbank_qfle3i_1.0.15.0-15vmw.703.0.20.19193900, VMW_bootbank_qflge_1.1.0.11-1vmw.703.0.20.19193900, VMW_bootbank_rste_2.0.2.0088-7vmw.703.0.20.19193900, VMW_bootbank_sfvmk_2.4.0.2010-6vmw.703.0.20.19193900, VMW_bootbank_smartpqi_70.4149.0.5000-1vmw.703.0.20.19193900, VMW_bootbank_vmkata_0.1-1vmw.703.0.20.19193900, VMW_bootbank_vmkfcoe_1.0.0.2-1vmw.703.0.20.19193900, VMW_bootbank_vmkusb_0.1-7vmw.703.0.50.20036589, VMW_bootbank_vmw-ahci_2.0.11-1vmw.703.0.20.19193900, VMware_bootbank_elx-esx-libelxima.so_12.0.1200.0-4vmw.703.0.20.19193900, VMware_bootbank_lsuv2-hpv2-hpsa-plugin_1.0.0-3vmw.703.0.20.19193900, VMware_bootbank_lsuv2-intelv2-nvme-vmd-plugin_2.7.2173-1vmw.703.0.20.19193900, VMware_bootbank_lsuv2-lsiv2-drivers-plugin_1.0.0-12vmw.703.0.50.20036589, VMware_bootbank_lsuv2-nvme-pcie-plugin_1.0.0-1vmw.703.0.20.19193900, VMware_bootbank_lsuv2-oem-dell-plugin_1.0.0-1vmw.703.0.20.19193900, VMware_bootbank_lsuv2-oem-hp-plugin_1.0.0-1vmw.703.0.20.19193900, VMware_bootbank_lsuv2-oem-lenovo-plugin_1.0.0-1vmw.703.0.20.19193900, VMware_bootbank_lsuv2-smartpqiv2-plugin_1.0.0-8vmw.703.0.20.19193900, VMware_bootbank_qlnativefc_4.1.14.0-26vmw.703.0.20.19193900, VMware_bootbank_vmware-esx-esxcli-nvme-plugin_1.2.0.44-1vmw.703.0.20.19193900



Pour vérifier que l'ESXi a bien été mis à jour, vous pouvez vérifier sa version grâce à cette commande :

.. code-block:: console

    vmware -v




Lorsque toutes ces étapes ont été effectuées une fois, les prochaines alertes de legacy CPU pourront être remédiées directement depuis l'interface vSphere.






.. seealso::
    https://pio.nz/2023/01/05/keeping-esxi-up-to-date-on-obsolete-hw/
    https://infra.engineer/miscellaneous/71-vmware-upgrade-esxi-host-with-esxcli
    https://www.vinchin.com/vm-tips/best-practice-to-backup-and-restore-vmware-vcenter.html
    https://williamlam.com/2020/04/quick-tip-allow-unsupported-cpus-when-upgrading-to-esxi-7-0.html



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


Snapshots
^^^^^^^^^^^^^^

Les snapshots sont des "photos" de l'état d'un volume à un instant "t".


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




Tools
==============

Copy/Paste to VM
---------------------

Si vous souhaitez pouvoir utiliser le copier-coller entre votre machine et une VM, vous devez ajouter des arguements à la configuration avancée de votre VM.

Dans ESXi v8.0, voici les éléments à ajouter :

 isolation.tools.setGUIOptions.enable
TRUE

 isolation.tools.paste.disable
FALSE

 isolation.tools.copy.disable
FALSE



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/cp.png




HPE iLO
====================

L'iLO HPE offre une interface de gestion pour votre serveur HPE, accessible hors-ligne ou en-ligne.


Vous pouvez y gérer les disques, les volumes logiques, exécuter des diagnostics...

Elle peut s'avérer très utile pour de la maintenance ou du dépannage.



L'iLO peut être atteint en réseau via l'interface RJ45 "iLO", vous pouvez y attribuer une adresse IP fixe ou bien par DHCP.



Update HPE iLO
-----------------------

L'iLO d'HP est un composant essentiel pour la gestion de serveurs HPE, qu'elle soit à distance ou locale.


Ici, nous allons couvrir comment mettre à jour le firmware de l'iLO à travers le shell ESXi.

Activer SSH
---------------------




iSCSI
==========================================

Ajouter un adaptateur iSCSI dans vSphere 8.0
--------------------------------------------------

Dans vSphere 8.0, vous pouvez ajouter un adaptateur virtuel iSCSI depuis le menu de configuration de l'hôte (ici dl380).


Ajouter un LUN à un datastore
---------------------------------

Un LUN (Logical Unit Number) est une tranche ou une partie d'un ensemble configuré de disques qui est présentable à un hôte et monté en tant que volume dans le système d'exploitation.

Afin de ne pas perdre de performances, le LUN devrait être lié à l'hôte ESXi via une connexion 10Gbit/s voire 25Gbit/s.


.. warning::

    N'ajoutez un LUN que si vous êtes certain de le garder. Le démonter du datastore demande un temps de maintenance important !


.. warning::

    Ne pas ajouter plus d'1 seul LUN sur un datastore. Cela peut causer des problèmes de performance.




Détacher un LUN d'un datastore
-------------------------------------


Détacher un LUN d'un datastore est une procédure compliquée qui nécessite d'être effectuée minutieusement pour ne pas perdre ou corrompre quelconque donnée / VM.



Démonter le datastore
^^^^^^^^^^^^^^^^^^^^^^

La première étape à effectuer est de démonter le datastore.
Pour cela, migrez toutes les VM stockées sur le datastore en question ainsi que les dossiers, fichiers présents dessus.

Démontez désormais le datastore.


Détacher le LUN
^^^^^^^^^^^^^^^^^^^^^^^^^^


Dans les Storage Devices, vous pouvez maintenant détacher le LUN du datastore. 


Supprimer le LUN
^^^^^^^^^^^^^^^^^^^^^^


Vous pouvez désromais supprimer le LUN que vous avez créé sur le périphérique distant (NAS etc...).


Nerworking
==============


vSwitch
----------


Le vSwitch agit tel un switch virtuel.
Il peut être attribué à un ou plusieurs groupes de ports ("groupes de NIC") dans l'interface de configuration ESXi web ou vSphere client.

Plusieurs paramètres sont disponibles dans le menu de paramétrage


Paramètres de sécurité
^^^^^^^^^^^^^^^^^^^^^^^^^^

Vous povuez activer ou désactiver plusieurs modes :

.. tabs::

   .. tab:: Promiscuous mode

        Activer cela permettra à chaque port (interface) des VM connectées au vSwitch de voir tout le trafic passant sur ce dernier.
        C'est une sorte de mode "hub".


        .. warning::
            
            Si vous comptez paramétrer un cluster de pares-feu PFSense, il est nécessaire d'activer le Promiscuous mode sur chaque vSwitch où une VIP CARP est configurée.


Groupe de ports
--------------------


vCenter Server
===================


Updates
----------------

vCenter Server
^^^^^^^^^^^^^^^^^^^

Afin de patcher les failles et les bugs d'une instance vCenter Server, il est nécessaire de la mettre à jour.


Pour cela, nous pouvons nous rendre sur la page de management de vCenter.

https://vcenter.lan:5480



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vcenter-mgmt1.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vcenter-mgmt2.png

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/vcenter-mgmt3.png


ESXi (Standalone)
^^^^^^^^^^^^^^^^^^^^^^^^


Ayant un seul hôte dl380p Gen9 dans mon cluster vCenter, il m'est impossible de mettre à jour ce dernier via vCenter Lifecycle Manager.

Cela est dû au fait que vCenter nécessite que TOUTES les VM aient le statut "turned off" sur l'hôte en question, ce qui n'est pas possible puisque ce dernier héberge la VM de vCenter qui est censé effectuer l'update !



Pour remédier à cela, il est donc nécessaire de télécharger l'offline bundle officiel d'HPE (dans mon cas) via le site de Broadcom, et de procéder à l'installation via SSH !

Téléchargement
~~~~~~~~~~~~~~~~~~~~~~

.. admonition:: Liens

    `Broadcom custom bundles & ISOs<https://support.broadcom.com/group/ecx/productfiles?subFamily=VMware%20vSphere&displayGroup=VMware%20vSphere%20-%20Enterprise%20Plus&release=8.0&os=&servicePk=202628&language=EN>`_

.. note::

    Veillez à bien sélectionner l'offline bundle pour le fichier .zip    


A la suite de cela, uploadez le fichier dans le datastore de votre ESXi. Ici, on utilisera "datastore1".

Vous devriez maintenant le voir apparaître :

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/esxi-vib-upload.png


.. tip::
    Mettez de côté le chemin d'accès absolu du dépôt zip !

Accès SSH
~~~~~~~~~~~~~~~~~~~~~~

Bien évidemment, activez le service ssh de votre hôte pour pouvoir y accéder ;)

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/ESXi/esxi-ssh-activate.png


Installation de l'update
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. warning::

    Avant de commencer, prenez en compte que les commandes esxcli software vib install/update sont dépréciées. Tout tuto utilisant ces commandes est donc caduc...
    Nous utilisons ici les commandes basées sur esxcli software profile.


Tout d'abord, nous allons devoir trouver le nom du profil que notre dépôt utilise pour l'update.

Pour déterminer cela, nous pouvons effectuer la commande suivante :


.. code-block:: console

    [root@dl380:~] esxcli software sources profile list -d /vmfs/volumes/58186ae8-12781af6-b630-ecb1d7b19420/ISO/vibupdate/VMware-ESXi-8.0.3-24280767-HPE-803.0.0.11.8.0.6-Oct2024-depot.zip

.. note::

    58186ae8-12781af6-b630-ecb1d7b19420 est l'UUID de mon "datastore1"

Ce qui nous retournera normalement : 

Name                               Vendor                      Acceptance Level  Creation Time        Modification Time
---------------------------------  --------------------------  ----------------  -------------------  -----------------
HPE-Custom-AddOn_803.0.0.11.8.0-6  Hewlett Packard Enterprise  PartnerSupported  2024-09-16T06:00:58  2024-09-16T06:00:58


Maintenant que nous savons que **HPE-Custom-AddOn_803.0.0.11.8.0-6** est le nom de notre profil, nous allons pouvoir installer le VIB.


.. code-block:: console

    [root@dl380:~] esxcli software profile update --depot=/vmfs/volumes/58186ae8-12781af6-b630-ecb1d7b19420/ISO/vibupdate/VMware-ESXi-8.0.3-24280767-HPE-803.0.0.11.8.0.6-Oct2024-depot.zip --profile=HPE-Custom-AddOn_803.0.0.11.8.0-6


Si votre CPU n'est plus "officiellement" supporté, l'erreur suivante apparaîtra : 

[HardwareError]
 Hardware precheck of profile HPE-Custom-AddOn_803.0.0.11.8.0-6 failed with warnings: <CPU_SUPPORT OVERRIDEWARNING: The CPUs on this host are not supported by ESXi 8.0.3. You can override and force install, but it is not officially supported nor recommended. Please refer to KB 82794 for more details.> Apply --no-hardware-warning option to ignore the warnings and proceed with the transaction.

 Mais pas de panique, il suffit, comme indiqué, de rajouter l'option --no-hardware-warning pour outrepasser l'avertissement.


.. code-block:: console

    [root@dl380:~] esxcli software profile update --depot=/vmfs/volumes/58186ae8-12781af6-b630-ecb1d7b19420/ISO/vibupdate/VMware-ESXi-8.0.3-24280767-HPE-803.0.0.11.8.0.6-Oct2024-depot.zip --profile=HPE-Custom-AddOn_803.0.0.11.8.0-6 --no-hardware-warning


Après un petit moment, le terminal vous retournera ceci :

.. spoiler:: 

    Update Result
   Message: The update completed successfully, but the system needs to be rebooted for the changes to be effective.
   VIBs Installed: BCM_bootbank_bnxtnet_230.0.136.0-1OEM.800.1.0.20613240, BCM_bootbank_bnxtroce_230.0.136.0-1OEM.800.1.0.20613240, HPE_bootbank_amsdv_701.11.7.0.2-1OEM.701.0.0.16850804, HPE_bootbank_ilorest_800.5.2.0.0.18-21495797, HPE_bootbank_sut_800.5.2.0.5-1OEM.800.1.0.20613240, INT_bootbank_i40en_2.8.4.0-1OEM.800.1.0.20613240, INT_bootbank_iavmd_9.0.0.1012-1OEM.800.1.0.20613240, INT_bootbank_icen_1.14.2.0-1OEM.800.1.0.20613240, MEL_bootbank_mft-oem_4.28.0.881-0, MEL_bootbank_mft_4.28.0.881-0, MIS_bootbank_ssacli2_6.40.6.0-8.0.0.20613240.oem, MVL_bootbank_qlnativefc_5.4.83.1-1OEM.803.0.0.23710970, QLC_bootbank_qcnic_2.0.67.0-1OEM.700.1.0.15843807, QLC_bootbank_qedentv_3.71.63.0-1OEM.800.1.0.20613240, QLC_bootbank_qedrntv_3.71.62.0-1OEM.800.1.0.20613240, QLC_bootbank_qfle3_1.4.51.0-1OEM.700.1.0.15843807, QLC_bootbank_qfle3f_2.1.34.0-1OEM.700.1.0.15843807, VMW_bootbank_mlnx-bfbootctl-esxio_0.1-7vmw.803.0.35.24280767, VMW_bootbank_nvmetcp-esxio_1.0.1.29-1vmw.803.0.35.24280767, VMW_bootbank_nvmetcp_1.0.1.29-1vmw.803.0.35.24280767, VMW_bootbank_rshim-net_0.1.0-1vmw.803.0.35.24280767, VMW_bootbank_rshim_0.1-12vmw.803.0.35.24280767, VMware_bootbank_bmcal-esxio_8.0.3-0.35.24280767, VMware_bootbank_bmcal_8.0.3-0.35.24280767, VMware_bootbank_clusterstore_8.0.3-0.35.24280767, VMware_bootbank_cpu-microcode_8.0.3-0.35.24280767, VMware_bootbank_crx_8.0.3-0.35.24280767, VMware_bootbank_drivervm-gpu-base_8.0.3-0.35.24280767, VMware_bootbank_esx-base_8.0.3-0.35.24280767, VMware_bootbank_esx-dvfilter-generic-fastpath_8.0.3-0.35.24280767, VMware_bootbank_esx-update_8.0.3-0.35.24280767, VMware_bootbank_esx-xserver_8.0.3-0.35.24280767, VMware_bootbank_esxio-base_8.0.3-0.35.24280767, VMware_bootbank_esxio-combiner-esxio_8.0.3-0.35.24280767, VMware_bootbank_esxio-combiner_8.0.3-0.35.24280767, VMware_bootbank_esxio-dvfilter-generic-fastpath_8.0.3-0.35.24280767, VMware_bootbank_esxio-update_8.0.3-0.35.24280767, VMware_bootbank_esxio_8.0.3-0.35.24280767, VMware_bootbank_gc-esxio_8.0.3-0.35.24280767, VMware_bootbank_gc_8.0.3-0.35.24280767, VMware_bootbank_infravisor_8.0.3-0.35.24280767, VMware_bootbank_loadesx_8.0.3-0.35.24280767, VMware_bootbank_loadesxio_8.0.3-0.35.24280767, VMware_bootbank_native-misc-drivers-esxio_8.0.3-0.35.24280767, VMware_bootbank_native-misc-drivers_8.0.3-0.35.24280767, VMware_bootbank_trx_8.0.3-0.35.24280767, VMware_bootbank_vcls-pod-crx_8.0.3-0.35.24280767, VMware_bootbank_vdfs_8.0.3-0.35.24280767, VMware_bootbank_vds-vsip_8.0.3-0.35.24280767, VMware_bootbank_vsan_8.0.3-0.35.24280767, VMware_bootbank_vsanhealth_8.0.3-0.35.24280767, VMware_locker_tools-light_12.4.5.23787635-24262298
   VIBs Removed: BCM_bootbank_bnxtnet_228.0.116.0-1OEM.800.1.0.20613240, BCM_bootbank_bnxtroce_228.0.116.0-1OEM.800.1.0.20613240, HPE_bootbank_amsdv_701.11.6.0.3-1OEM.701.0.0.16850804, HPE_bootbank_ilorest_800.5.0.0.0.2-21495797, HPE_bootbank_sut_800.5.0.0.11-1OEM.800.1.0.20613240, INT_bootbank_i40en_2.7.2.0-1OEM.800.1.0.20613240, INT_bootbank_iavmd_3.5.1.1002-1OEM.800.1.0.20613240, INT_bootbank_icen_1.13.2.0-1OEM.800.1.0.20613240, MEL_bootbank_mft-oem_4.25.0.802-0, MEL_bootbank_mft_4.25.0.802-0, MIS_bootbank_ssacli2_6.30.8.0-8.0.0.20143090.oem, MVL_bootbank_qlnativefc_5.4.82.0-1OEM.800.1.0.20613240, QLC_bootbank_qcnic_2.0.66.0-1OEM.700.1.0.15843807, QLC_bootbank_qedentv_3.71.52.0-1OEM.800.1.0.20613240, QLC_bootbank_qedrntv_3.71.50.0-1OEM.800.1.0.20613240, QLC_bootbank_qfle3_1.4.46.0-1OEM.700.1.0.15843807, QLC_bootbank_qfle3f_2.1.33.0-1OEM.700.1.0.15843807, VMW_bootbank_mlnx-bfbootctl-esxio_0.1-6vmw.803.0.0.24022510, VMW_bootbank_nvmetcp-esxio_1.0.1.28-1vmw.803.0.0.24022510, VMW_bootbank_nvmetcp_1.0.1.28-1vmw.803.0.0.24022510, VMware_bootbank_bmcal-esxio_8.0.3-0.0.24022510, VMware_bootbank_bmcal_8.0.3-0.0.24022510, VMware_bootbank_clusterstore_8.0.3-0.0.24022510, VMware_bootbank_cpu-microcode_8.0.3-0.0.24022510, VMware_bootbank_crx_8.0.3-0.0.24022510, VMware_bootbank_drivervm-gpu-base_8.0.3-0.0.24022510, VMware_bootbank_esx-base_8.0.3-0.0.24022510, VMware_bootbank_esx-dvfilter-generic-fastpath_8.0.3-0.0.24022510, VMware_bootbank_esx-update_8.0.3-0.0.24022510, VMware_bootbank_esx-xserver_8.0.3-0.0.24022510, VMware_bootbank_esxio-base_8.0.3-0.0.24022510, VMware_bootbank_esxio-combiner-esxio_8.0.3-0.0.24022510, VMware_bootbank_esxio-combiner_8.0.3-0.0.24022510, VMware_bootbank_esxio-dvfilter-generic-fastpath_8.0.3-0.0.24022510, VMware_bootbank_esxio-update_8.0.3-0.0.24022510, VMware_bootbank_esxio_8.0.3-0.0.24022510, VMware_bootbank_gc-esxio_8.0.3-0.0.24022510, VMware_bootbank_gc_8.0.3-0.0.24022510, VMware_bootbank_infravisor_8.0.3-0.0.24022510, VMware_bootbank_loadesx_8.0.3-0.0.24022510, VMware_bootbank_loadesxio_8.0.3-0.0.24022510, VMware_bootbank_native-misc-drivers-esxio_8.0.3-0.0.24022510, VMware_bootbank_native-misc-drivers_8.0.3-0.0.24022510, VMware_bootbank_trx_8.0.3-0.0.24022510, VMware_bootbank_vcls-pod-crx_8.0.3-0.0.24022510, VMware_bootbank_vdfs_8.0.3-0.0.24022510, VMware_bootbank_vds-vsip_8.0.3-0.0.24022510, VMware_bootbank_vsan_8.0.3-0.0.24022510, VMware_bootbank_vsanhealth_8.0.3-0.0.24022510, VMware_locker_tools-light_12.4.0.23259341-24022510
   VIBs Skipped: HPE_bootbank_amsd_701.11.10.0.4-1OEM.701.0.0.16850804, HPE_bootbank_fc-enablement_800.3.9.0.30-1OEM.800.1.0.20172892, HPE_bootbank_hpe-upgrade_901.2.0.5-1OEM.800.0.0.20172892, HPE_bootbank_ilo_700.10.8.2.2-1OEM.700.1.0.15843807, INT_bootbank_igbn_1.12.0.0-1OEM.800.1.0.20613240, INT_bootbank_ixgben_1.18.2.0-1OEM.800.1.0.20613240, MEL_bootbank_nmst_4.25.0.802-1OEM.802.0.0.21974771, QLC_bootbank_qedf_2.74.1.0-1OEM.800.1.0.20613240, QLC_bootbank_qedi_2.74.1.0-1OEM.800.1.0.20613240, QLC_bootbank_qfle3i_2.1.14.0-1OEM.700.1.0.15843807, VMW_bootbank_atlantic_1.0.3.0-13vmw.803.0.0.24022510, VMW_bootbank_bcm-mpi3_8.8.1.0.0.0-1vmw.803.0.0.24022510, VMW_bootbank_bfedac-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_brcmfcoe_12.0.1500.3-4vmw.803.0.0.24022510, VMW_bootbank_cndi-igc_1.2.10.0-1vmw.803.0.0.24022510, VMW_bootbank_dwi2c-esxio_0.1-7vmw.803.0.0.24022510, VMW_bootbank_dwi2c_0.1-7vmw.803.0.0.24022510, VMW_bootbank_elxiscsi_12.0.1200.0-11vmw.803.0.0.24022510, VMW_bootbank_elxnet_12.0.1250.0-8vmw.803.0.0.24022510, VMW_bootbank_intelgpio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_ionic-cloud_20.0.0-48vmw.803.0.0.24022510, VMW_bootbank_ionic-en-esxio_20.0.0-56vmw.803.0.0.24022510, VMW_bootbank_ionic-en_20.0.0-56vmw.803.0.0.24022510, VMW_bootbank_irdman_1.4.0.1-1vmw.803.0.0.24022510, VMW_bootbank_iser_1.1.0.2-1vmw.803.0.0.24022510, VMW_bootbank_lpfc_14.4.0.39-35vmw.803.0.0.24022510, VMW_bootbank_lpnic_11.4.62.0-1vmw.803.0.0.24022510, VMW_bootbank_lsi-mr3_7.728.02.00-1vmw.803.0.0.24022510, VMW_bootbank_lsi-msgpt2_20.00.06.00-4vmw.803.0.0.24022510, VMW_bootbank_lsi-msgpt35_29.00.00.00-1vmw.803.0.0.24022510, VMW_bootbank_lsi-msgpt3_17.00.13.00-3vmw.803.0.0.24022510, VMW_bootbank_mnet-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_mtip32xx-native_3.9.8-1vmw.803.0.0.24022510, VMW_bootbank_ne1000_0.9.2-1vmw.803.0.0.24022510, VMW_bootbank_nenic_1.0.35.0-7vmw.803.0.0.24022510, VMW_bootbank_nfnic_5.0.0.42-1vmw.803.0.0.24022510, VMW_bootbank_nhpsa_70.0051.0.100-5vmw.803.0.0.24022510, VMW_bootbank_nipmi_1.0-1vmw.803.0.0.24022510, VMW_bootbank_nmlx5-cc-esxio_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlx5-cc_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlx5-core-esxio_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlx5-core_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlx5-rdma-esxio_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlx5-rdma_4.23.6.2-7vmw.803.0.0.24022510, VMW_bootbank_nmlxbf-gige-esxio_2.2-1vmw.803.0.0.24022510, VMW_bootbank_nmlxbf-pmc-esxio_0.1-6vmw.803.0.0.24022510, VMW_bootbank_ntg3_4.1.14.0-4vmw.803.0.0.24022510, VMW_bootbank_nvme-pcie-esxio_1.2.4.15-1vmw.803.0.0.24022510, VMW_bootbank_nvme-pcie_1.2.4.15-1vmw.803.0.0.24022510, VMW_bootbank_nvmerdma_1.0.3.9-1vmw.803.0.0.24022510, VMW_bootbank_nvmxnet3-ens-esxio_2.0.0.23-6vmw.803.0.0.24022510, VMW_bootbank_nvmxnet3-ens_2.0.0.23-6vmw.803.0.0.24022510, VMW_bootbank_nvmxnet3-esxio_2.0.0.31-12vmw.803.0.0.24022510, VMW_bootbank_nvmxnet3_2.0.0.31-12vmw.803.0.0.24022510, VMW_bootbank_penedac-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_pengpio-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_pensandoatlas_1.46.0.E.41.1.326-2vmw.803.0.0.0.23797590, VMW_bootbank_penspi-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_pvscsi-esxio_0.1-7vmw.803.0.0.24022510, VMW_bootbank_pvscsi_0.1-7vmw.803.0.0.24022510, VMW_bootbank_qflge_1.1.0.11-2vmw.803.0.0.24022510, VMW_bootbank_rd1173-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_rdmahl_1.0.0-1vmw.803.0.0.24022510, VMW_bootbank_rste_2.0.2.0088-7vmw.803.0.0.24022510, VMW_bootbank_sfvmk_2.4.0.2010-18vmw.803.0.0.24022510, VMW_bootbank_smartpqi_80.4700.0.5000-2vmw.803.0.0.24022510, VMW_bootbank_spidev-esxio_0.1-1vmw.803.0.0.24022510, VMW_bootbank_vmkata_0.1-1vmw.803.0.0.24022510, VMW_bootbank_vmksdhci-esxio_1.0.3-3vmw.803.0.0.24022510, VMW_bootbank_vmksdhci_1.0.3-3vmw.803.0.0.24022510, VMW_bootbank_vmkusb-esxio_0.1-22vmw.803.0.0.24022510, VMW_bootbank_vmkusb_0.1-22vmw.803.0.0.24022510, VMW_bootbank_vmw-ahci_2.0.17-1vmw.803.0.0.24022510, VMware_bootbank_elx-esx-libelxima.so_12.0.1200.0-6vmw.803.0.0.24022510, VMware_bootbank_esx-ui_2.18.0-23593406, VMware_bootbank_lsuv2-hpv2-hpsa-plugin_1.0.0-4vmw.803.0.0.24022510, VMware_bootbank_lsuv2-intelv2-nvme-vmd-plugin_2.7.2173-2vmw.803.0.0.24022510, VMware_bootbank_lsuv2-lsiv2-drivers-plugin_1.0.3-1vmw.803.0.0.24022510, VMware_bootbank_lsuv2-nvme-pcie-plugin_1.0.0-1vmw.803.0.0.24022510, VMware_bootbank_lsuv2-oem-dell-plugin_1.1.0-2vmw.803.0.0.24022510, VMware_bootbank_lsuv2-oem-lenovo-plugin_1.0.0-2vmw.803.0.0.24022510, VMware_bootbank_lsuv2-smartpqiv2-plugin_1.0.0-11vmw.803.0.0.24022510, VMware_bootbank_vmware-esx-esxcli-nvme-plugin-esxio_1.2.0.56-1vmw.803.0.0.24022510, VMware_bootbank_vmware-esx-esxcli-nvme-plugin_1.2.0.56-1vmw.803.0.0.24022510, VMware_bootbank_vmware-hbrsrv_8.0.3-0.0.24022510
   Reboot Required: true
   DPU Results:

Il ne manque plus que rebooter l'ESXi désormais !!!!

.. code-block:: console

    [root@dl380:~] reboot