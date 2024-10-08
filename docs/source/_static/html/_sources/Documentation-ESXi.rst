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


iSCSI
==========================================

Ajouter un adaptateur iSCSI dans vSphere 8.0
--------------------------------------------------

Dans vSphere 8.0, vous pouvez ajouter un adaptateur virtuel iSCSI depuis le menu de configuration de l'hôte (ici dl380).


Ajouter un LUN à un datastore
---------------------------------

Un LUN (Logical Unit Number).

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
