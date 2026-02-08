================================
Exchange
================================

.. note::

    Si vous hébergez un serveur Exchange On Premise, lors de la connexion à ce serveur via Outlook, ce dernier passera tout de même par les serveurs de Microsoft.
    Il faut donc faire attention quant aux restrictions par pays pour ne pas bloquer des serverzs Microsoft légitimes !


Exchange Online
===================

Désactiver l'automapping
----------------------------------


.. code-block:: console

    Add-MailboxPermission -Identity <MailboxIdentity> -User <UserIdentity> -AccessRights FullAccess -AutoMapping $false


.. note::

    https://learn.microsoft.com/en-us/outlook/troubleshoot/profiles-and-accounts/remove-automapping-for-shared-mailbox



Send as permissions
-------------------------

.. code-block:: console
    
    Add-RecipientPermission | Remove-RecipientPermission> -Identity <MailboxOrGroupIdentity> -Trustee <DelegateIdentity> -AccessRights SendAs

.. note::

    https://learn.microsoft.com/en-us/exchange/recipients-in-exchange-online/manage-permissions-for-recipients


Exchange On-Premises
======================================



Build number and release dates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Disponibles sur le `site de Microsoft <https://learn.microsoft.com/en-us/exchange/new-features/build-numbers-and-release-dates>`_

Commandes utiles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Scripts utiles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note::
   
   `Scripts <https://microsoft.github.io/CSS-Exchange/>`_ fournis par Microsoft.




Limites globales de transport
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: powershell

   C:\>Get-TransportConfig | fl MaxReceiveSize,MaxSendSize

Cette commande permet de récupérer les limites globales du serveur pour les tailles d'envoi et de réception d'emails

Par défaut, cette limite est 



Migration 2016 vers 2019
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note::
   Ce chapitre sera basé principalement sur le `KB 0001472 <https://www.petenetlive.com/kb/article/0001472/>`_ de Petenetlive.



Configuration relais SMTP Exchange 2016 (et versions ultérieures)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note::
   Ce chapitre sera basé principalement sur `cet article <https://www.samuraj-cz.com/en/article/exchange-server-2016-mail-flow-mail-routing-and-connectors/>`_ de Samuraj-cz.com.
