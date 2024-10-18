=====================
Documentation Linux
=====================


Tools
==========

CLI
----------


uname
^^^^^^^^^^

uname -a
~~~~~~~~~

Affiche la version du kernel linux ainsi que la version ed la distribution de l'OS.

.. code-block:: bash

    ubuntu@ubuntu:~$ uname -a
    Linux vps-4a827ff5 6.8.0-47-generic #47-Ubuntu SMP PREEMPT_DYNAMIC Fri Sep 27 21:40:26 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux

htop
^^^^^^^^^^

Permet de lister les processus en cours ainsi que les ressources utilisées sur la machine.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/Linux/htop.png


df
^^^^^^^^

Liste l'espace utilisé et libre de chaque partition des volumes.


df -h
~~~~~~

.. code-block:: bash

    ubuntu@ubuntu:~$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    tmpfs           382M  1.1M  381M   1% /run
    /dev/sda1        78G   24G   55G  31% /
    tmpfs           1.9G     0  1.9G   0% /dev/shm
    tmpfs           5.0M     0  5.0M   0% /run/lock
    /dev/sda15      105M  6.1M   99M   6% /boot/efi
    tmpfs           382M   12K  382M   1% /run/user/1002


ps
^^^^^^

ps -ef 
~~~~~~~
uptime
^^^^^^^^^^^^


groups
^^^^^^^^^


Permissions
============


Bien comprendre les permissions sur les fichiers et dossiers est fondamental pour bien utiliser Linux.

ls
--------------

ls est une commande permettant de lister des fichiers et des dossiers depuis un terminal bash, zsh et autres... 

Nous pouvons rajouter des arguments à cette commande afin de trier ce que l'on veut afficher.

