=====================
Documentation Zabbix
=====================

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Zabbix/logo.png
    :width: 300


.. warning::

    Cette documentation a été rédigée pour un serveur Zabbix 7.x avec la configuration suivante :
    | Ubuntu 24.04LTS
    | PostgreSQL
    | Nginx 


Backup 
============


DB
------

|br|

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Zabbix/logo-psql.png
    :width: 300

|br|


Avant d'initier une backup sur la DB postgres, il est préférable de stopper le service du serveur Zabbix afin d'éviter une surcharge des transactions sur la DB.

.. code-block:: bash

    systemctl stop zabbix-server


Par la suite, il est possible de faire une backup complète de la base de données (Option 1), ou bien de faire une backup excluant les plus grosses tables (Option 2).
Ce choix dépend de l'objectif et de votre RTO.


Option 1 :

.. code-block:: bash

    sudo -u zabbix pg_dump zabbix -h localhost -F c -f zabbix_backup.sql zabbix

Option 2 :

 .. code-block:: bash

    sudo -u zabbix pg_dump --schema==public --exclude-table-data=history* --exclude-table-data=trends* zabbix   


Configuration web et Zabbix
------------------------------

|br|

 .. code-block:: bash

    mkdir /opt/zabbix-backup/
    cp /etc/zabbix/zabbix_server.conf /opt/zabbix-backup/
    cp /etc/apache2/conf-enabled/zabbix.conf /opt/zabbix-backup/ #pas nécessaire si pas d'installation Apache2

    cp -R /usr/share/zabbix/ /opt/zabbix-backup/
    cp -R /usr/share/zabbix-* /opt/zabbix-backup/

Restore
=============

DB
----

|br|

.. note::

    Cette section est partiellement basée sur `cet article <https://blog.zabbix.com/backups-to-the-rescue/23442/>`_ du blog Zabbix officiel.

Afin de restaurer une DB postgresql zabbix, il est nécessaire d'avoir postgresql installé (v15, v16...) sur le serveur (ici Ubuntu 24.04) et d'avoir créé une DB "zabbix" vide.

.. code-block:: bash

    sudo -u postgres createuser --pwprompt zabbix #si l'utilisateur n'est pas déjà existant (après un DR par exemple)
    sudo -u postgres createdb -O zabbix zabbix

A partir de cela, il est possible de restaurer le dump SQL avec la commande suivante :abbr:

.. code-block:: bash

    psql zabbix < zabbix_db_2025-11-30_03-00-01.sql

Update
===========

Upgrade between minor versions
----------------------------------

Il est possible de mettre Zabbix à jour entre des versions 7.4.x mineures (for example, from 7.4.1 to 7.4.3).

Il est d'abord nécessaire de mettre à jour les infos du repository :

.. code-block:: bash 
    
    sudo apt update

Ensuite, il suffit seulement de mettre le serveur à jour aussi avec apt :

.. code-block:: bash

    sudo apt install --only-upgrade 'zabbix*'


Installation and Configuration of TimescaleDB
=================================================

.. note::

    PostgreSQL version 16.11

.. admonition:: Sources

     - `Tiger Data Docs <https://www.tigerdata.com/docs/docs/self-hosted/latest/install/installation-linux#add-the-timescaledb-extension-to-your-database>`_
     - `Tillnet.se <https://tillnet.se/index.php/2025/03/19/timescaledb-add-to-existing-postgresql-zabbix-7-0-lts-and-ubuntu-24-04-lts/>`_
     - `Zabbix Docs <https://www.zabbix.com/documentation/current/en/manual/appendix/install/timescaledb>`_


TimescaleDB agit comme un plug-in s'ajoutant à une base de données Zabbix déjà existante.

Ce plug-in permet à la fois d'améliorer les performances de housekeeping du serveur en question tout en limitant l'espace de stockage utilisé (grâce notamment à la compression ).

Installation
--------------------

Afin d'installer l'extension TimescaleDB sur la base de données postgres existante, il est nécessaire de se connecter à l'interface shell du serveur.


Ajout du repository et de la GPG
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    echo "deb https://packagecloud.io/timescale/timescaledb/ubuntu/ $(lsb_release -c -s) main" | sudo tee /etc/apt/sources.list.d/timescaledb.list

    wget --quiet -O - https://packagecloud.io/timescale/timescaledb/gpgkey | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/timescaledb.gpg

    sudo apt update


Installation du paquet
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Il est possible d'installer le paquet TimescaleDB pour Zabbix de 2 manières différentes :

- Option 1 : Installer la dernière version de TimescaleDB mais en changeant la configuration de Zabbix
- Option 2 : Installer la dernière version de TimescaleDB supportée par Zabbix



Option 1
~~~~~~~~~~~~~~~~

.. code-block:: bash

    sudo apt install timescaledb-2-postgresql-16 timescaledb-2-loader-postgresql-16 postgresql-client-16 -y

Option 2
~~~~~~~~~~~~~~~~

.. code-block:: bash

    sudo apt install timescaledb-2-postgresql-16='2.18.0*' timescaledb-2-loader-postgresql-16='2.18.0*' postgresql-client-16



Configuration
------------------


.. code-block:: bash

    timescaledb-tune --max-conns=125

L'option "--max-conns=125" permet d'augmenter les connexions simultanéées à la base de données. (Par défaut à 100)


Lors du setup, il est recommandé de répondre "Yes" à toutes les questions.
Le tuning permet l'optimisation de postgres pour l'utilisation de l'extension TimescaleDB.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Zabbix/timescaledb-tuning.png


Après l'exécution du setup avec succès, le service postgres nécessite d'être redémarré.

.. code-block:: bash

    sudo systemctl restart postgresql


Activation de l'extension
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activer TimescaleDB pour la base de données "zabbix".

.. code-block:: bash

    echo "CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;" | sudo -u postgres psql --dbname=zabbix



Migration des tables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. warning::

    Selon la taille de la base de données, la migration des tables peut prendre beaucoup de temps !


.. code-block::

    cat /usr/share/zabbix/sql-scripts/postgresql/timescaledb/schema.sql | sudo -u zabbix psql zabbix


Le script "schema.sql" paramètre le housekeeping comme suit :

- Override item history period
- Override item trend period

Ces paramètres doivent impérativement être activés pour utiliser le partitionnement TimescaleDB des tables "history" et "trends"
Il est cependant possible de l'activer seulement pour l'un ou pour l'autre individuellement.

2 autres paramètres sont aussi configurés grâce au script :

- Enable compression
- Compress records older than 7 days


Ces paramètres sont trouvables dans la web GUI via Administration > Housekeeping



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Zabbix/housekeeping-gui.png



.. |br| raw:: html

      <br>