=======================
Adobe Acrobat Pro 2020
=======================

.. warning::

    Adobe a mis fin au support d'Acrobat 2020 le **30 juin 2025** : plus aucune mise à jour de sécurité.
    Ouvrir des pièces jointes PDF avec un lecteur non patché est un vrai risque. Les versions récentes
    (Acrobat Pro abonnement ou perpétuelle plus récente) existent en 64 bits, ce qui évite aussi le problème ci-dessous.


Aperçu des PDF impossible dans Outlook classic 64 bits
======================================================

Acrobat 2020 n'existe qu'en **32 bits**. Son gestionnaire d'aperçu PDF (*PDF Preview Handler*) est enregistré
pour tourner dans l'hôte d'aperçu 32 bits, qu'Outlook classic **64 bits** ne sait pas charger.
Le correctif consiste à rediriger le handler vers l'hôte 64 bits.

1. Acrobat comme application PDF par défaut
-------------------------------------------

**Paramètres Windows → Applications → Applications par défaut** : associer ``.pdf`` à Adobe Acrobat.
Si Edge est l'application par défaut, Outlook peut ne pas utiliser l'aperçu d'Acrobat.

2. Activer l'aperçu dans Outlook
--------------------------------

**Fichier → Options → Centre de gestion de la confidentialité → Paramètres du Centre de gestion de la confidentialité
→ Gestion des pièces jointes → Aperçus des pièces jointes et des documents** :

- cocher **PDF Preview Handler** ;
- vérifier que **Désactiver l'aperçu des pièces jointes** n'est **pas** coché.

3. Correctif registre
---------------------

Fermer Outlook, puis dans une invite de commandes **en administrateur** :

.. code-block:: console

    reg add "HKLM\SOFTWARE\Wow6432Node\Classes\CLSID\{DC6EFB56-9CFA-464D-8880-44885D7DC193}" /v AppID /t REG_SZ /d "{534A1E02-D58F-44f0-B58B-36CBED287C7C}" /f

Cette commande fait passer le handler Adobe de l'hôte 32 bits ``{6d2b5079-2f0b-48dd-ab7f-97cec514d30b}``
à l'hôte 64 bits ``{534A1E02-D58F-44f0-B58B-36CBED287C7C}``. Relancer Outlook (ou redémarrer) puis tester l'aperçu.

.. tip::

    Exporter la clé dans ``regedit`` avant la modification. Pour revenir en arrière, relancer la même commande
    avec la valeur d'origine :

    .. code-block:: console

        reg add "HKLM\SOFTWARE\Wow6432Node\Classes\CLSID\{DC6EFB56-9CFA-464D-8880-44885D7DC193}" /v AppID /t REG_SZ /d "{6d2b5079-2f0b-48dd-ab7f-97cec514d30b}" /f

4. Si ça ne fonctionne toujours pas
-----------------------------------

**Panneau de configuration → Programmes → Adobe Acrobat 2020 → Modifier → Réparer**, puis refaire l'étape 3.

.. note::

    Une réparation (ou une mise à jour) d'Acrobat peut réinitialiser la valeur ``AppID`` : réappliquer le correctif registre après coup.
