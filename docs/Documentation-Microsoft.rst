Microsoft
=========

.. container:: dh-hero dh-microsoft

   🏢 Écosystème Microsoft

   Windows Server, Windows 11, Exchange et Microsoft 365

.. rubric:: 🖥️ Windows Server
   :class: dh-group

.. grid:: 1 2 3 3
   :gutter: 3

   .. grid-item-card:: 🛡️ Active Directory
      :link: Microsoft/winserv/ad
      :link-type: doc
      :class-card: dh-card

      Jonction au domaine, rôles FSMO, utilisateurs et GPMC.

   .. grid-item-card:: 📂 DFS
      :link: Microsoft/winserv/dfs
      :link-type: doc
      :class-card: dh-card

      Espaces de noms DFS : prérequis, installation et création.

   .. grid-item-card:: 🔗 Partage réseau
      :link: Microsoft/winserv/partage
      :link-type: doc
      :class-card: dh-card dh-todo

      Partages SMB, permissions NTFS et gestion des accès. *À rédiger.*

.. rubric:: 💻 Windows 11
   :class: dh-group

.. grid:: 1 2 3 3
   :gutter: 3

   .. grid-item-card:: 🏗️ Installation
      :link: Microsoft/win11/installation
      :link-type: doc
      :class-card: dh-card dh-todo

      Installation et préparation du poste. *À rédiger.*

   .. grid-item-card:: 🔧 Configuration
      :link: Microsoft/win11/configuration
      :link-type: doc
      :class-card: dh-card dh-todo

      Paramétrage du poste. *À rédiger.*

   .. grid-item-card:: 🛠️ Troubleshooting
      :link: Microsoft/win11/troubleshooting-maintenance
      :link-type: doc
      :class-card: dh-card

      SFC, DISM, CHKDSK, nettoyage de disque et dépannage.

.. rubric:: ☁️ Messagerie et cloud
   :class: dh-group

.. grid:: 1 2 2 2
   :gutter: 3

   .. grid-item-card:: 📧 Exchange
      :link: Microsoft/exchange
      :link-type: doc
      :class-card: dh-card

      Exchange Online et On-Premises : automapping, Send As, limites
      de transport, migration et relais SMTP.

      .. card-footer::

         :bdg-primary-line:`Online` :bdg-primary-line:`On-Premises` :bdg-primary-line:`SMTP`

   .. grid-item-card:: ☁️ Microsoft 365
      :link: Microsoft/ms365
      :link-type: doc
      :class-card: dh-card

      Tenant, Entra ID, accès conditionnel, compromission de compte,
      SharePoint, OneDrive, Teams, Defender et Purview.

      .. card-footer::

         :bdg-primary-line:`Entra ID` :bdg-primary-line:`Teams` :bdg-primary-line:`Defender` :bdg-primary-line:`Purview`

.. toctree::
   :hidden:

   Microsoft/winserv
   Microsoft/win11
   Microsoft/exchange
   Microsoft/ms365
