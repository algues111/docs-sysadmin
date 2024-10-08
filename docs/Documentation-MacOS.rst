=====================
Documentation MacOS
=====================


Fresh Install Ventura
=======================



.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/ventura.jpg


L'installation de MacOS, que ce soit Big Sur, Monterey, Ventura ou même Sonoma, n'est pas bien compliquée.

Cela se fait en quelques étapes si vous n'avez rien à sauvegarder au préalable !

Prérequis
---------------------------------

- Connexion Internet, pour télécharger l'image
- Clé USB de min. 16Gio
- Un ordinateur sous MacOS (de préférence, sinon il y a des workarounds)




Etape 1 : Téléchargement de l'image
--------------------------------------

Si vous utilisez un Mac pour suivre ce tuto, vous pouvez vous rendre dans l'App Store et télécharger MacOS Ventura.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/ventura-appstore.png




Lorsque ce dernier est terminé, l'assistant d'installation se lancera automatiquement, et vous n'aurez qu'à le fermer.



Etape 2 : Formatage et création de la clé USB bootable
----------------------------------------------------------

.. danger::
    Veillez à sauvegarder toute donnée qui vous est utile sur votre clé USB, car à la suite des prochaines étapes, elles seront **définitivement perdues**.

Premièrement, je vous invite à brancher votre clé USB sur votre Mac afin de pouvoir procéder à son formatage.

Vous devriez maintenant la voir sur votre bureau et dans **l'Utilitaire de disque**.

.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/disk-utility.png

Ici, ma clé est déjà préparée, mais elle a au préalable été renommée "Install macOS Ventura" afin qu'elle soit reconnaissable au maximum !


Si vous le souhaitez, vous pouvez la formater avant la commande ci-dessous, mais cela n'est pas vraiment nécessaire étant donné que la commande le fait d'elle même.


:command:`sudo /Applications/Install\ macOS\ Ventura.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume`


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/terminal.png

Remplacez "*MyVolume*" par le nom de votre clé USB (ici SANDISK-ALGUES111).



Etape 3 : Boot sur la clé USB
---------------------------------

Après avoir effectué les étapes précédentes, il est désormais temps d'installer MacOS Ventura sur votre Mac !

Nous avons donc besoin d'accéder au boot menu du mac.

.. tip::
    Pour ce faire, sur les Mac Intel, il faut enfoncer la touche option (⌥) et démarrer l'ordinateur.


Cela aura pour effet de vous amener au menu de démarrage dans lequel vous pourrez choisir la clé USB bootable qui s'affichera avec le logo de Ventura.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/macos-bootmenu.png


Après cela, un assistant d'installation vous guidera jusqu'à la fin du processus d'installation de MacOS Ventura.

Choisissez les options correspondant à vos besoins et feel free !


Best tools after fresh install
-------------------------------------


Après une fresh install, il est nécessaire de configurer et d'adapter MacOS à nos besoins et habitudes.
Pour cela, une liste personnelle d'outils et de paramètres est disponible ci-dessous


.. tabs::

    .. tab:: Keyboard Settings

        Augmentation de la vitesse de répétition des touches et diminution de la pause avant répétition.

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/keyboard-settings.png


    .. tab:: Dock settings
        
        Par défaut, la vitesse d'affichage du dock a une latence lorsque nous glissons le curseur dessus. Cette commande raccourcit cet effet :

        .. code-block:: console

            defaults write com.apple.dock autohide-time-modifier -float 0.4; killall Dock

        Nous pouvons aussi faire en sorte que le dock s'affiche à gauche de l'écran et seulement lorsque le curseur est dessus. 
        Voici mes réglages personnels :

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/dock-settings.png

    .. tab:: Stats

        En tant qu'admin système et réseau, j'aime bien avoir les informations esentielles de mon OS à portée de main (utilisation CPU, RAM, SSD, Network...).

        Pour cela, `Stats <https://github.com/exelban/stats>`_ est particulièrement efficace en affichant ces infos dans la barre des menus supérieure.

        
        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/menu-bar.png



        Vous pouvez modifier les réglages de l'application depuis l'interface ci-dessous :

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/stats-settings.png


    .. tab:: Updates and Upgrades

        Par défaut, MacOS active les mises à jour et mises à niveau automatiques, ce qui peut poser de gros problèmes pour la compatibilité de certaines applications voire pour les performances de votre ordinateur.

        Pour éviter cela, vous pouvez vous rendre dans le menu Général -> Mise à jour de logiciels -> Mises à jour automatiques

        Voici les paramètres que j'utilise personnellement :

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/updates.png
         

    .. tab:: Application Updates and Upgrades

        Souvent, nous téléchargeons nos applications depuis différentes sources. Mais cela peut s'avérer fatiguant de vérifier constamment si une MàJ est disponible, surtout si l'app ne le fait pas d'elle même.

        Une solution payante mais efficace existe : `MacUpdater 3 <https://www.corecode.io/index.html>`

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/mac-updater.png

        Ci-dessus nous voyons que j'ai quelques mise à jour à effectuer, et nous pouvons cliquer sur le bouton icône "Infos" pour voir le détail des MàJ que MacUpdater va chercher.

        .. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/mac-updater1.png



Lier MacOS à un annuaire LDAP
===============================

Sur MacOS, un utilitaire très utile est disponible pour enregistrer votre Mac sur un simple domaine LDAP ou Active Directory.


.. image:: https://raw.githubusercontent.com/algues111/docs-sysadmin/main/docs/source/images/MacOS/utilitaire-annuaire.png


Afin de modifier ces paramètres, une autorisation administrateur supplémentaire vous sera demandée lorsque vous cliquerez sur le cadenas.


