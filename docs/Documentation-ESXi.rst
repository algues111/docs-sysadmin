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




https://pio.nz/2023/01/05/keeping-esxi-up-to-date-on-obsolete-hw/
https://infra.engineer/miscellaneous/71-vmware-upgrade-esxi-host-with-esxcli
https://www.vinchin.com/vm-tips/best-practice-to-backup-and-restore-vmware-vcenter.html