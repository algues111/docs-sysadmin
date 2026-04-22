================================================
VPN IPSec IKEv2 EAP - FortiGate & Windows 11
================================================


Présentation
============

Ce document décrit la configuration d'un tunnel VPN IPSec IKEv2 avec authentification EAP
(Extensible Authentication Protocol) entre un pare-feu **FortiGate 70G** et le client VPN
natif **Windows 11**.

L'authentification des utilisateurs est gérée en local sur le FortiGate via un groupe
d'utilisateurs dédié (``Local-group``). Le FortiGate présente un certificat au client lors
de la négociation IKEv2, tandis que le client s'authentifie via EAP (identifiants
utilisateur/mot de passe).


Schéma fonctionnel
------------------

.. code-block:: text

   ┌─────────────────────┐                        ┌──────────────────────────┐
   │    Windows 11       │                        │     FortiGate 70G        │
   │  Client VPN natif   │◄──── IKEv2 / EAP ─────►│  Interface : wan1        │
   │                     │      (UDP 500/4500)    │  IP Pool : 10.10.11.x    │
   └─────────────────────┘                        └──────────────────────────┘
         IP attribuée :                                 LAN : 192.168.20.0/24
         10.10.11.10 → .20

Prérequis
=========

.. list-table:: Prérequis techniques
   :widths: 30 70
   :header-rows: 1

   * - Élément
     - Détail
   * - Pare-feu
     - FortiGate 70G (FortiOS 7.x recommandé)
   * - Client
     - Windows 11 avec client VPN natif (IKEv2)
   * - Certificat
     - Certificat serveur valide installé sur le FortiGate (``certificate``)
   * - DNS
     - Serveur DNS poussé aux clients : ``192.168.20.1``
   * - Ports ouverts
     - UDP 500 (IKE), UDP 4500 (NAT-T) sur l'interface WAN
   * - Utilisateurs
     - Comptes locaux membres du groupe ``Local-group`` sur le FortiGate

Configuration FortiGate
=======================

Phase 1 - IKEv2 (``IKEV2_EAP_VPN``)
-------------------------------------

La phase 1 définit les paramètres d'établissement du tunnel IKEv2, l'authentification
par certificat côté serveur, et la distribution d'adresses IP aux clients via le mode
``mode-cfg``.

.. code-block:: text

   config vpn ipsec phase1-interface
       edit "IKEV2_EAP_VPN"
           set type dynamic
           set interface "wan1"
           set ike-version 2
           set local-gw <public_ip>
           set authmethod signature
           set peertype any
           set net-device disable
           set mode-cfg enable
           set ipv4-dns-server1 192.168.20.1
           set proposal aes256-sha256 aes128-sha256
           set dpd on-idle
           set eap enable
           set eap-identity send-request
           set authusrgrp "Local-group"
           set certificate "certificate"
           set ipv4-start-ip 10.10.11.10
           set ipv4-end-ip 10.10.11.20
           set save-password enable
           set client-keep-alive enable
           set dpd-retryinterval 60
       next
   end

.. list-table:: Détail des paramètres Phase 1
   :widths: 35 65
   :header-rows: 1

   * - Paramètre
     - Description
   * - ``type dynamic``
     - Accepte les connexions de clients dont l'IP est dynamique (Road Warrior)
   * - ``interface "wan1"``
     - Interface WAN d'écoute du tunnel
   * - ``ike-version 2``
     - Utilisation du protocole IKEv2 exclusivement
   * - ``local-gw <public_ip>``
     - IP publique du FortiGate présentée aux clients
   * - ``authmethod signature``
     - Authentification serveur par certificat (signature numérique)
   * - ``peertype any``
     - Accepte tout type de pair (identité non restreinte)
   * - ``net-device disable``
     - Désactive la création d'une interface réseau dédiée par tunnel
   * - ``mode-cfg enable``
     - Active la distribution automatique d'IP, DNS aux clients (IKEv2 Config Payload)
   * - ``ipv4-dns-server1 192.168.20.1``
     - Serveur DNS poussé aux clients VPN
   * - ``proposal aes256-sha256 aes128-sha256``
     - Algorithmes de chiffrement/intégrité acceptés en phase 1
   * - ``dpd on-idle``
     - Active le Dead Peer Detection uniquement quand le tunnel est inactif
   * - ``dpd-retryinterval 60``
     - Intervalle de 60 secondes entre les tentatives DPD
   * - ``eap enable``
     - Active l'authentification EAP pour les clients
   * - ``eap-identity send-request``
     - Le FortiGate sollicite l'identité EAP auprès du client
   * - ``authusrgrp "Local-group"``
     - Groupe d'utilisateurs locaux autorisés à se connecter
   * - ``certificate "certificate"``
     - Certificat serveur présenté lors de la négociation IKEv2
   * - ``ipv4-start-ip / ipv4-end-ip``
     - Plage d'adresses IP attribuées aux clients (10.10.11.10 → 10.10.11.20)
   * - ``save-password enable``
     - Autorise le client à mémoriser ses identifiants
   * - ``client-keep-alive enable``
     - Maintien de session actif côté client

Phase 2 - IPSec (``IKEV2_EAP_VPN_P2``)
-----------------------------------------

La phase 2 définit les paramètres de chiffrement du trafic encapsulé dans le tunnel IPSec.

.. code-block:: text

   config vpn ipsec phase2-interface
       edit "IKEV2_EAP_VPN_P2"
           set phase1name "IKEV2_EAP_VPN"
           set proposal aes256gcm aes128-sha256
           set dhgrp 19
           set keepalive enable
           set keylifeseconds 3600
           set src-subnet 192.168.20.0 255.255.255.0
       next
   end

.. list-table:: Détail des paramètres Phase 2
   :widths: 35 65
   :header-rows: 1

   * - Paramètre
     - Description
   * - ``phase1name "IKEV2_EAP_VPN"``
     - Référence à la phase 1 associée
   * - ``proposal aes256gcm aes128-sha256``
     - Algorithmes de chiffrement phase 2 (AES-256-GCM privilégié)
   * - ``dhgrp 19``
     - Perfect Forward Secrecy activé avec DH19
   * - ``keepalive enable``
     - Envoi de messages de maintien de session IPSec
   * - ``keylifeseconds 3600``
     - Durée de vie des clés de session : 1 heure
   * - ``src-subnet 192.168.20.0 255.255.255.0``
     - Définit le subnet locale accessbile par le client

Configuration Windows 11
=========================


.. warning::

  Windows ne supporte pas la configuration d'un "remote-id" sur le tunnel. 
  C'est à dire que si un autre tunnel configuré sur votre pare-feu utilise la même local gateway ET le même groupe DH en phase 1, cela pausera souci !
  Il est donc recommandé de choisir un groupe différent de tous les autres tunnels utilisant la même local gateway.

Création du profil VPN (PowerShell)
-------------------------------------

Le profil VPN est créé via PowerShell avec une politique IPSec personnalisée afin
d'aligner les algorithmes cryptographiques avec ceux du FortiGate.

.. code-block:: powershell

   # Création du profil VPN IKEv2
   Add-VpnConnection `
       -Name "Corporate VPN" `
       -ServerAddress "fdqn_vpn" `
       -TunnelType Ikev2 `
       -AuthenticationMethod Eap `
       -EncryptionLevel Custom `
       -RememberCredential $true `
       -SplitTunneling $true `
       -PassThru

   # Application de la politique IPSec personnalisée
   Set-VpnConnectionIPsecConfiguration `
       -ConnectionName "Corporate VPN" `
       -AuthenticationTransformConstants GCMAES256 `
       -CipherTransformConstants GCMAES256 `
       -DHGroup Group19 `
       -IntegrityCheckMethod SHA256 `
       -PfsGroup ECP256 `
       -EncryptionMethod AES256 `
       -Force

   # Ajout de la route pour le split SplitTunneling
   Add-VpnConnectionRoute -ConnectionName "Corporate VPN" -DestinationPrefix "192.168.20.0/24"
  
  

Vérification de la configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: powershell

   # Vérification du profil VPN
   Get-VpnConnection

   # Vérification de la politique IPSec
   Get-VpnConnection | Select-Object -ExpandProperty IPsecCustomPolicy

Résultat attendu (``Get-VpnConnection``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: text

   Name                  : Corporate VPN
   ServerAddress         : fdqn_vpn
   AllUserConnection     : False
   TunnelType            : Ikev2
   AuthenticationMethod  : {Eap}
   EncryptionLevel       : Custom
   UseWinlogonCredential : False
   ConnectionStatus      : Disconnected
   RememberCredential    : True
   SplitTunneling        : True

Résultat attendu (``IPsecCustomPolicy``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: text

   AuthenticationTransformConstants : GCMAES256
   CipherTransformConstants         : GCMAES256
   DHGroup                          : Group19
   IntegrityCheckMethod             : SHA256
   PfsGroup                         : ECP256
   EncryptionMethod                 : AES256

Correspondance des algorithmes cryptographiques
================================================

Le tableau suivant présente l'alignement des algorithmes entre les deux équipements :

.. list-table:: Correspondance FortiGate ↔ Windows 11
   :widths: 25 37 38
   :header-rows: 1

   * - Paramètre
     - FortiGate
     - Windows 11
   * - **Phase 1 - Chiffrement**
     - ``aes256-sha256`` / ``aes128-sha256``
     - ``AES256`` + ``SHA256``
   * - **Phase 1 - DH Group**
     - Implicite (Group 19 / 256 bits elliptique)
     - ``Group19``
   * - **Phase 2 - Chiffrement**
     - ``aes256gcm``
     - ``GCMAES256``
   * - **Phase 2 - Intégrité**
     - Intégré GCM (AEAD)
     - ``GCMAES256``
   * - **PFS**
     - ``Groupe19``
     - ``ECP256``
   * - **Durée de vie clés**
     - 3600 secondes
     - Par défaut Windows

.. note::

   AES-256-GCM est un algorithme AEAD (Authenticated Encryption with Associated Data).
   Il assure conjointement le chiffrement et l'intégrité des données, ce qui explique
   que ``AuthenticationTransformConstants`` et ``CipherTransformConstants`` soient tous
   deux définis sur ``GCMAES256`` côté Windows.

Architecture d'adressage
=========================

.. list-table:: Plan d'adressage
   :widths: 40 60
   :header-rows: 1

   * - Réseau / Hôte
     - Adresse
   * - Interface WAN FortiGate
     - ``<public_ip>`` (FQDN : ``fdqn_vpn``)
   * - Réseau LAN interne
     - ``192.168.20.0/24``
   * - Serveur DNS interne
     - ``192.168.20.1``
   * - Plage IP clients VPN
     - ``10.10.11.10`` → ``10.10.11.20`` (11 adresses)

Vérification et supervision
============================

Vérification côté FortiGate (CLI)
-----------------------------------

.. code-block:: text

   # Vérification des tunnels IPSec actifs
   get vpn ipsec tunnel summary

   # Détail d'un tunnel spécifique
   get vpn ipsec tunnel details

   # Vérification des sessions IKE actives
   diagnose vpn ike status

   # Logs en temps réel des négociations IKE
   diagnose debug application ike -1
   diagnose debug enable

   # Désactiver le debug après vérification
   diagnose debug disable
   diagnose debug reset

Vérification côté Windows 11
------------------------------

.. code-block:: powershell

   # Statut de la connexion VPN
   Get-VpnConnection | Select-Object Name, ConnectionStatus

   # Vérification des routes injectées après connexion
   Get-NetRoute | Where-Object { $_.InterfaceAlias -like "*VPN*" }

   # Journaux d'événements IKEv2 Windows
   Get-WinEvent -LogName "Microsoft-Windows-IKE/Operational" | Select-Object -First 20

Dépannage
==========

.. list-table:: Problèmes courants et résolutions
   :widths: 35 65
   :header-rows: 1

   * - Symptôme
     - Action corrective
   * - Échec de négociation Phase 1
     - Vérifier la correspondance des proposals (AES256/SHA256/Group14) des deux côtés
   * - Certificat rejeté par le client
     - S'assurer que le certificat du FortiGate est signé par une CA de confiance
       installée dans le magasin Windows (``Trusted Root CA``). Ici, certificat délivré par SwissSign
   * - Authentification EAP échouée
     - Vérifier que l'utilisateur est bien membre du groupe ``Local-group``
       et que le mot de passe est correct
   * - Pas d'IP attribuée au client
     - Vérifier la plage ``ipv4-start-ip / ipv4-end-ip`` et que ``mode-cfg`` est actif
   * - Tunnel monte mais pas de trafic
     - Vérifier les règles de pare-feu FortiGate autorisant le trafic depuis
       l'interface VPN vers le LAN
   * - DPD déconnecte le client
     - Vérifier ``client-keep-alive enable`` et le paramètre ``dpd-retryinterval``

.. warning::

   La plage d'adresses IP VPN (``10.10.11.10`` → ``10.10.11.20``) ne supporte que
   **11 clients simultanés**. Augmenter la plage si nécessaire en fonction du nombre
   d'utilisateurs.

.. note::

   Le tunnel étant configuré en **Split Tunnel** (``SplitTunneling : True``),
   seulement le trafic client destiné au réseau 192.168.20.0/24 transite par le VPN.
   Adapter les règles de pare-feu FortiGate en conséquence.
```