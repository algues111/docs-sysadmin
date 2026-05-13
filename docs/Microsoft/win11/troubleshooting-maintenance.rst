================================
Troubleshooting & Maintenance
================================


.. _windows11-troubleshooting-maintenance:

==============================================
Dépannage et Maintenance Windows 11
==============================================


.. note::
   Cette documentation couvre les outils natifs de dépannage et de maintenance
   disponibles sous Windows 11. Elle s'appuie sur la documentation officielle
   Microsoft et les meilleures pratiques communautaires.

   **Références officielles Microsoft :**

   - `Vue d'ensemble des outils de maintenance Windows <https://learn.microsoft.com/fr-fr/windows-hardware/manufacture/desktop/what-is-dism>`_
   - `Utiliser SFC pour réparer les fichiers système <https://support.microsoft.com/fr-fr/topic/utiliser-l-outil-v%C3%A9rificateur-des-fichiers-syst%C3%A8me-pour-r%C3%A9parer-les-fichiers-syst%C3%A8me-manquants-ou-endommagés-79aa86cb-ca52-166a-92a3-966e85d4094e>`_

---------------------------------------------

Outils de Réparation des Fichiers Système
=============================================

SFC — System File Checker
--------------------------

L'outil **SFC** (System File Checker) analyse l'intégrité de tous les fichiers
système protégés de Windows et remplace les versions incorrectes par les versions
correctes Microsoft.

.. seealso::
   - `Documentation SFC sur Microsoft Learn <https://learn.microsoft.com/fr-fr/windows-server/administration/windows-commands/sfc>`_
   - `Forum Microsoft Community — SFC /scannow ne fonctionne pas <https://answers.microsoft.com/fr-fr/windows/forum/all/sfc-scannow/>`_

**Lancer une analyse SFC :**

.. code-block:: doscon

   C:\> sfc /scannow

.. code-block:: doscon

   C:\> sfc /verifyonly

.. list-table:: Options SFC
   :widths: 30 70
   :header-rows: 1

   * - Paramètre
     - Description
   * - ``/scannow``
     - Analyse et répare immédiatement tous les fichiers système protégés
   * - ``/verifyonly``
     - Analyse sans effectuer de réparation
   * - ``/scanfile=<chemin>``
     - Analyse et répare un fichier spécifique
   * - ``/verifyfile=<chemin>``
     - Vérifie uniquement un fichier spécifique
   * - ``/offbootdir``
     - Spécifie le répertoire de démarrage hors ligne
   * - ``/offwindir``
     - Spécifie le répertoire Windows hors ligne

.. warning::
   SFC doit être exécuté en tant qu'**Administrateur** depuis une invite de
   commandes élevée. Sur un système corrompu, il est recommandé de lancer
   SFC depuis l'environnement de récupération Windows (WinRE).

**Résultats possibles après exécution :**

- ``La Protection des ressources Windows n'a trouvé aucune violation d'intégrité.``
  — Aucun fichier corrompu détecté.
- ``La Protection des ressources Windows a trouvé des fichiers corrompus et les a réparés.``
  — Réparation réussie.
- ``La Protection des ressources Windows a trouvé des fichiers corrompus mais n'a pas pu en réparer certains.``
  — Utiliser DISM pour continuer la réparation.

---------------------------------------------

DISM — Deployment Image Servicing and Management
--------------------------------------------------

**DISM** est un outil avancé permettant de monter, gérer et réparer des images
Windows. Il est souvent utilisé en complément de SFC lorsque ce dernier ne
parvient pas à réparer des fichiers système.

.. seealso::
   - `DISM sur Microsoft Learn <https://learn.microsoft.com/fr-fr/windows-hardware/manufacture/desktop/dism-overview>`_
   - `Réparer une image Windows avec DISM <https://learn.microsoft.com/fr-fr/windows-hardware/manufacture/desktop/repair-a-windows-image>`_
   - `Tech Talk Microsoft — Utilisation avancée de DISM <https://techcommunity.microsoft.com/t5/windows-it-pro-blog/using-dism-to-fix-windows-10-issues/ba-p/3803386>`_

Vérification de l'état de l'image
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: doscon

   C:\> dism /online /cleanup-image /checkhealth

.. code-block:: doscon

   C:\> dism /online /cleanup-image /scanhealth

.. code-block:: doscon

   C:\> dism /online /cleanup-image /restorehealth

.. list-table:: Commandes DISM principales
   :widths: 45 55
   :header-rows: 1

   * - Commande
     - Description
   * - ``/checkhealth``
     - Vérifie si des corruptions ont été détectées (pas d'analyse active)
   * - ``/scanhealth``
     - Analyse complète de l'image pour détecter des corruptions
   * - ``/restorehealth``
     - Répare l'image en téléchargeant les fichiers depuis Windows Update
   * - ``/startcomponentcleanup``
     - Nettoie les composants remplacés et réduit la taille du dossier WinSxS
   * - ``/analyzecomponentstore``
     - Analyse le magasin de composants et affiche les statistiques

.. note::
   ``/restorehealth`` nécessite une connexion Internet active pour récupérer
   les fichiers de remplacement depuis les serveurs Windows Update de Microsoft.
   Pour un usage hors ligne, il est possible de spécifier une source locale :

   .. code-block:: doscon

      C:\> dism /online /cleanup-image /restorehealth /source:wim:D:\sources\install.wim:1 /limitaccess

Surveillance du fichier CBS.log
*********************************

Lors de l'exécution de DISM ou SFC, Windows consigne toutes les opérations dans
le fichier ``C:\Windows\Logs\CBS\CBS.log``. Ce fichier est essentiel pour
diagnostiquer les erreurs de réparation.

**Afficher les 10 dernières lignes du CBS.log avec PowerShell :**

.. code-block:: powershell

   Get-Content "C:\Windows\Logs\CBS\CBS.log" -Tail 10

**Afficher les 10 dernières lignes et surveiller en temps réel :**

.. code-block:: powershell

   Get-Content "C:\Windows\Logs\CBS\CBS.log" -Tail 10 -Wait

**Filtrer uniquement les erreurs dans le CBS.log :**

.. code-block:: powershell

   Get-Content "C:\Windows\Logs\CBS\CBS.log" | Select-String -Pattern "\[SR\]|\[DISM\]|Error|Cannot" | Select-Object -Last 20

.. note::
   Le paramètre ``-Tail 10`` de ``Get-Content`` est l'équivalent PowerShell de
   la commande Unix ``tail -n 10``. Il permet d'afficher uniquement les **N
   dernières lignes** d'un fichier texte sans charger l'intégralité du contenu
   en mémoire — particulièrement utile pour les fichiers de logs volumineux.

   Le paramètre ``-Wait`` maintient le flux ouvert et affiche les nouvelles
   lignes au fur et à mesure de leur écriture (comportement de ``tail -f``).

**Exemple de sortie CBS.log après un DISM /restorehealth réussi :**

.. code-block:: text

   [DISM] Found component store corruption
   [DISM] Contacting Windows Update server...
   [DISM] Downloading replacement files...
   [SR] Beginning Verify and Repair transaction
   [SR] Verify complete
   [SR] Repairing corrupted file [...]
   [SR] This component was successfully repaired.

**Consulter le rapport DISM dédié :**

.. code-block:: powershell

   Get-Content "C:\Windows\Logs\DISM\dism.log" -Tail 20

---------------------------------------------

CHKDSK — Vérification du Disque
==================================

**CHKDSK** (Check Disk) est l'outil intégré Windows permettant de vérifier
l'intégrité du système de fichiers et des métadonnées d'un volume, ainsi que
de corriger les erreurs logiques et physiques détectées sur un disque.

.. seealso::
   - `Documentation CHKDSK sur Microsoft Learn <https://learn.microsoft.com/fr-fr/windows-server/administration/windows-commands/chkdsk>`_
   - `Forum Microsoft — Erreurs CHKDSK courantes <https://answers.microsoft.com/fr-fr/windows/forum/all/chkdsk/>`_
   - `TechNet — Comprendre les résultats CHKDSK <https://techcommunity.microsoft.com/t5/storage-at-microsoft/chkdsk-deep-dive/ba-p/425613>`_

Syntaxe et options principales
--------------------------------

.. code-block:: doscon

   C:\> chkdsk C: /f /r /x

.. list-table:: Paramètres CHKDSK
   :widths: 20 80
   :header-rows: 1

   * - Paramètre
     - Description
   * - ``/f``
     - Corrige les erreurs détectées sur le disque
   * - ``/r``
     - Localise les secteurs défectueux et récupère les informations lisibles (implique ``/f``)
   * - ``/x``
     - Force le démontage du volume avant l'analyse (si nécessaire)
   * - ``/scan``
     - Lance une analyse en ligne sans démontage (NTFS uniquement)
   * - ``/spotfix``
     - Corrige uniquement les problèmes identifiés (NTFS uniquement, plus rapide)
   * - ``/b``
     - Réinitialise la liste des clusters défectueux et réanalyse (implique ``/r``)

.. warning::
   Si CHKDSK est lancé sur le volume système ``C:``, Windows proposera de
   planifier la vérification au prochain redémarrage. Répondre **O** (Oui)
   pour confirmer la planification.

**Vérifier les résultats CHKDSK via l'Observateur d'événements :**

.. code-block:: powershell

   Get-WinEvent -LogName "Application" | Where-Object {$_.ProviderName -eq "Microsoft-Windows-Chkdsk"} | Select-Object TimeCreated, Message | Select-Object -First 5

---------------------------------------------

Nettoyage du Disque
=======================

Outil Nettoyage de disque (cleanmgr)
---------------------------------------

L'utilitaire **Nettoyage de disque** permet de supprimer les fichiers temporaires,
les anciennes installations Windows, les fichiers de mise à jour et autres données
obsolètes.

.. seealso::
   - `Libérer de l'espace disque sous Windows <https://support.microsoft.com/fr-fr/windows/lib%C3%A9rer-de-l-espace-disque-sous-windows-85529ccb-c365-490d-b548-831022bc9b32>`_

**Lancer le nettoyage de disque classique :**

.. code-block:: doscon

   C:\> cleanmgr /d C:

**Lancer avec nettoyage des fichiers système (mode élevé) :**

.. code-block:: doscon

   C:\> cleanmgr /sageset:1
   C:\> cleanmgr /sagerun:1

Nettoyage avancé avec DISM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: doscon

   C:\> dism /online /cleanup-image /startcomponentcleanup /resetbase

.. warning::
   L'option ``/resetbase`` supprime définitivement toutes les sauvegardes des
   composants remplacés. Cette opération est **irréversible** et empêche le
   désinstallation des mises à jour ultérieures.

Nettoyage via PowerShell
^^^^^^^^^^^^^^^^^^^^^^^^^^

**Supprimer les fichiers temporaires utilisateur :**

.. code-block:: powershell

   Remove-Item -Path "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue

**Vider le dossier Temp système :**

.. code-block:: powershell

   Remove-Item -Path "C:\Windows\Temp\*" -Recurse -Force -ErrorAction SilentlyContinue

**Analyser l'espace utilisé par le magasin de composants WinSxS :**

.. code-block:: doscon

   C:\> dism /online /cleanup-image /analyzecomponentstore

---------------------------------------------

Défragmentation et Optimisation des Disques
=============================================

.. seealso::
   - `Optimiser les lecteurs Windows 11 <https://support.microsoft.com/fr-fr/windows/d%C3%A9fragmenter-votre-lecteur-windows-10-11-048aefac-7f1f-4632-d48a-9700abe3b43e>`_
   - `Documentation defrag sur Microsoft Learn <https://learn.microsoft.com/fr-fr/windows-server/administration/windows-commands/defrag>`_

.. note::
   Sous Windows 11, l'optimisation des disques est **automatiquement planifiée**
   une fois par semaine. La défragmentation classique ne s'applique qu'aux
   **disques HDD**. Les **SSD** bénéficient d'une optimisation par commande
   **TRIM**, qui est différente de la défragmentation.

Commandes d'optimisation
--------------------------

.. code-block:: doscon

   C:\> defrag C: /u /v

.. code-block:: doscon

   C:\> defrag C: /o

.. code-block:: doscon

   C:\> defrag /c /h /u

.. list-table:: Paramètres Defrag
   :widths: 20 80
   :header-rows: 1

   * - Paramètre
     - Description
   * - ``/u``
     - Affiche la progression en temps réel
   * - ``/v``
     - Mode verbeux — affiche des statistiques de fragmentation détaillées
   * - ``/o``
     - Optimise selon le type de média (TRIM pour SSD, défragmentation pour HDD)
   * - ``/c``
     - Optimise tous les volumes du système
   * - ``/h``
     - Exécute l'opération avec une priorité normale (au lieu de basse)
   * - ``/a``
     - Analyse uniquement sans défragmenter
   * - ``/x``
     - Consolide l'espace libre sur le volume

**Via PowerShell — Optimiser un volume :**

.. code-block:: powershell

   Optimize-Volume -DriveLetter C -Defrag -Verbose

.. code-block:: powershell

   Optimize-Volume -DriveLetter C -ReTrim -Verbose

---------------------------------------------

Observateur d'Événements
===========================

L'**Observateur d'événements** (Event Viewer) est l'outil central de diagnostic
Windows. Il centralise tous les journaux système, applicatifs et de sécurité,
permettant d'identifier les erreurs, avertissements et comportements anormaux.

.. seealso::
   - `Utiliser l'Observateur d'événements <https://learn.microsoft.com/fr-fr/shows/inside/event-viewer>`_
   - `Référence des canaux de journaux Windows <https://learn.microsoft.com/fr-fr/windows/win32/wes/windows-event-log>`_
   - `Forum TechCommunity — Analyse des Event Logs <https://techcommunity.microsoft.com/t5/ask-the-directory-services-team/bg-p/AskDS>`_

Accès à l'Observateur d'événements
-------------------------------------

.. code-block:: doscon

   C:\> eventvwr.msc

Journaux principaux
^^^^^^^^^^^^^^^^^^^^^

.. list-table:: Journaux Windows principaux
   :widths: 30 70
   :header-rows: 1

   * - Journal
     - Contenu
   * - **Windows Logs > Application**
     - Événements des applications et services installés
   * - **Windows Logs > Système**
     - Événements du noyau Windows, drivers et composants système
   * - **Windows Logs > Sécurité**
     - Audit des connexions, accès aux ressources, politique de sécurité
   * - **Windows Logs > Setup**
     - Événements d'installation et de mise à jour
   * - **Applications and Services Logs**
     - Journaux spécifiques aux rôles et applications (DNS, DHCP, etc.)

Requêtes PowerShell sur les événements
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Afficher les 10 dernières erreurs système :**

.. code-block:: powershell

   Get-WinEvent -LogName "System" -MaxEvents 10 | Where-Object {$_.LevelDisplayName -eq "Erreur"}

**Afficher les événements critiques des dernières 24h :**

.. code-block:: powershell

   Get-WinEvent -LogName "System" | Where-Object {
       $_.Level -eq 1 -and $_.TimeCreated -gt (Get-Date).AddHours(-24)
   }

**Rechercher un ID d'événement spécifique (ex: BSOD — Event ID 41) :**

.. code-block:: powershell

   Get-WinEvent -LogName "System" | Where-Object {$_.Id -eq 41}

**Exporter les événements en XML pour analyse :**

.. code-block:: powershell

   Get-WinEvent -LogName "System" -MaxEvents 100 | Export-Clixml -Path "C:\Logs\system_events.xml"

**IDs d'événements courants à surveiller :**

.. list-table:: Événements Windows critiques à surveiller
   :widths: 15 25 60
   :header-rows: 1

   * - Event ID
     - Source
     - Description
   * - ``41``
     - Kernel-Power
     - Arrêt inattendu du système (coupure de courant, BSOD)
   * - ``6008``
     - EventLog
     - Arrêt imprévu précédent
   * - ``1001``
     - BugCheck
     - Détails d'un écran bleu (BSOD)
   * - ``7034``
     - Service Control Manager
     - Un service s'est arrêté de manière inattendue
   * - ``7036``
     - Service Control Manager
     - Changement d'état d'un service
   * - ``7040``
     - Service Control Manager
     - Modification du type de démarrage d'un service
   * - ``4625``
     - Security
     - Échec de connexion (tentative non autorisée)
   * - ``4648``
     - Security
     - Tentative de connexion avec des informations d'identification explicites
   * - ``4740``
     - Security
     - Compte utilisateur verrouillé

---------------------------------------------

Tableau Récapitulatif — Commandes et Outils de Maintenance
============================================================

.. list-table:: Résumé des outils de maintenance Windows 11
   :widths: 20 30 50
   :header-rows: 1

   * - Outil / Commande
     - Usage principal
     - Commande type
   * - **SFC**
     - Vérification et réparation des fichiers système
     - ``sfc /scannow``
   * - **DISM CheckHealth**
     - Vérification rapide de l'image Windows
     - ``dism /online /cleanup-image /checkhealth``
   * - **DISM ScanHealth**
     - Analyse complète de l'image Windows
     - ``dism /online /cleanup-image /scanhealth``
   * - **DISM RestoreHealth**
     - Réparation de l'image Windows via Windows Update
     - ``dism /online /cleanup-image /restorehealth``
   * - **CBS.log (tail)**
     - Lecture des dernières lignes du journal SFC/DISM
     - ``Get-Content "C:\Windows\Logs\CBS\CBS.log" -Tail 10``
   * - **CHKDSK**
     - Vérification et réparation du système de fichiers
     - ``chkdsk C: /f /r /x``
   * - **Cleanmgr**
     - Nettoyage des fichiers temporaires et obsolètes
     - ``cleanmgr /d C:``
   * - **DISM ComponentCleanup**
     - Nettoyage du magasin de composants WinSxS
     - ``dism /online /cleanup-image /startcomponentcleanup``
   * - **Defrag / Optimize-Volume**
     - Défragmentation HDD ou TRIM SSD
     - ``defrag C: /o /u /v``
   * - **Observateur d'événements**
     - Consultation des journaux système et applicatifs
     - ``eventvwr.msc``
   * - **Get-WinEvent**
     - Requêtes PowerShell sur les journaux d'événements
     - ``Get-WinEvent -LogName "System" -MaxEvents 20``
   * - **Get-Content -Tail**
     - Lecture des N dernières lignes d'un fichier log
     - ``Get-Content <chemin_log> -Tail 10 -Wait``
   * - **Optimize-Volume**
     - Optimisation PowerShell d'un volume
     - ``Optimize-Volume -DriveLetter C -ReTrim -Verbose``

---------------------------------------------

.. rubric:: Ressources complémentaires

- `Microsoft Learn — Windows 11 Troubleshooting <https://learn.microsoft.com/fr-fr/troubleshoot/windows-client/welcome-windows-client>`_
- `Microsoft Tech Community — Windows IT Pro Blog <https://techcommunity.microsoft.com/t5/windows-it-pro-blog/bg-p/Windows10Blog>`_
- `Microsoft Q&A — Windows 11 <https://learn.microsoft.com/fr-fr/answers/topics/windows-11.html>`_
- `Forum Microsoft Community FR <https://answers.microsoft.com/fr-fr/windows>`_
- `Sysinternals Suite — Outils avancés de diagnostic <https://learn.microsoft.com/fr-fr/sysinternals/>`_
