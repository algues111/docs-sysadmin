=====================
Documentation Zabbix
=====================




Backup 
============

systemctl stop zabbix-server

sudo pg_dump -U zabbix -h localhost -F c -f zabbix_backup.sql zabbix

mkdir /opt/zabbix-backup/
cp /etc/zabbix/zabbix_server.conf /opt/zabbix-backup/
cp /etc/apache2/conf-enabled/zabbix.conf /opt/zabbix-backup/ #pas nécessaire si pas d'installation Apache2

cp -R /usr/share/zabbix/ /opt/zabbix-backup/
cp -R /usr/share/zabbix-* /opt/zabbix-backup/

Restore
=============

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
Il est possible de mettre Zabbix à jour entre des versions 7.4.x mineures (for example, from 7.4.1 to 7.4.3).

Il est d'abord nécessaire de mettre à jour les infos du repository :

.. code-block:: bash 
    
    sudo apt update

Ensuite, il suffit seulement de mettre le serveur à jour aussi avec apt :

.. code-block:: bash

    sudo apt install --only-upgrade 'zabbix*'


Installation and Configuration of TimescaleDB
=================================================

TimescaleDB agit comme un plug-in s'ajoutant à une base de données Zabbix déjà existante.

Ce plug-in permet à la fois d'améliorer les performances du serveur en question tout en limitant l'espace de stockage utilisé (grâce notamment à la compression ).