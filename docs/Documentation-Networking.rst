====================================
Documentation Networking
====================================

Certification de câblage
====================================

Certifier le câblage est important afin de garantir la fiabilité et les performances de ce dernier.

Plusieurs marques fournissent des testeurs, tels que des Fluke ou des LanTek...


Ici, nous verrons les différents paramètres importants à configurer et à prendre en compte afin de certifier un câblage correctement.


LanTek II
---------------------

Le menu du LanTek II se présente comme suit :


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/lantek1.jpeg



Classification
---------------------


Pour répertorier et générer des rapports, il est nécessaire de configurer des dossiers / répertoires en fonction des entreprises, bâtiments ou même étages que vous testez.

Ici, nous notons le n° d'affaire du chantier (ex. 400777) suivi de son nom (ex. Rolex). Cela donne (400777 - Rolex).


Afin d'enregistrer les futurs tests sur un répertoire en particulier, il faut le sélectionner, aller dans "Options" et choisir "Activer ce répertoire".

Vous verrez vos différents tests apparaître au fur et à mesure dans le répertoire en question.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/lantek-repertoire.jpeg


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/lantek-repertoire1.jpeg


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/lantek-repertoire2.jpeg


Paramètres
-------------------------

NVP
^^^^^^^^^







Pares-feu
===================================


Selon les besoins et demandes du client, énormément de choix peuvent s'offrir à nous concernant les solutions de pares-feu.

Du pare-feu intégré au modem-routeur Swisscom, jusqu'à la dernière génération de pares-feu Fortinet ou Sophos, nous avons de quoi faire.

Mais nous pouvons aussi héberger des solutions complètement open-source sur des VM ou des NUC (Je pense notamment à PFSense ou OPNSense).




OPNSense
-----------------------------------


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/opnsense-logo.png



PFSense
-----------------------------------

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Networking/pfsense-logo.png


Fortigate
-------------------


NAT-PAT
^^^^^^^^^

Dans FortiOS, il est possible de créer des IP Virtuelles pour configurer le DNAT sur des services se trouvant derrière notre FortiGate.


.. note::
    Par exemple, si vous hébergez un service atteignable avec l'IP publique 10.54.66.81:55313 mais qui possède en réalité l'IP locale 172.16.10.10, il est nécessaire de créer une IP virtuelle qui convertira le port 





VIP
~~~~~~



Téléphonie SIP
=====================


2N
--------------


2N est une solution d'interphonie SIP / RTP cloud-based ou on-premise

Call Flow
^^^^^^^^^^^^^^^^^^^^^^^^^^


