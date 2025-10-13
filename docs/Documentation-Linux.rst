=====================
Documentation Linux
=====================


Tools
==========

CLI
----------


uname
^^^^^^^^^^

.. code-block:: bash

    ubuntu@ubuntu:~$ uname
    Linux


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


.. code-block:: bash

    ubuntu@ubuntu5:~$ df
    Filesystem     1K-blocks     Used Available Use% Mounted on
    tmpfs             391100     2572    388528   1% /run
    /dev/sda1       81145268 38910108  42218776  48% /
    tmpfs            1955488        0   1955488   0% /dev/shm
    tmpfs               5120        0      5120   0% /run/lock
    /dev/sda15        106858     6250    100609   6% /boot/efi
    tmpfs             391096       12    391084   1% /run/user/1002


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

.. code-block:: bash

    ubuntu@ubuntu:~$ ps
        PID TTY          TIME CMD
    1312260 pts/0    00:00:00 bash
    1312485 pts/0    00:00:00 ps

ps -ef 
~~~~~~~
uptime
^^^^^^^^^^^^

.. code-block:: bash

    ubuntu@ubuntu:~$ uptime
    09:44:56 up 2 days, 19:11,  1 user,  load average: 0.28, 0.19, 0.12

groups
^^^^^^^^^

.. code-block:: bash

    ubuntu@ubuntu:~$ groups
    ubuntu sudo users docker

Permissions
============


Bien comprendre les permissions sur les fichiers et dossiers est fondamental pour bien utiliser Linux.

ls
--------------

ls est une commande permettant de lister des fichiers et des dossiers depuis un terminal bash, zsh et autres... 

Nous pouvons rajouter des arguments à cette commande afin de trier ce que l'on veut afficher.

