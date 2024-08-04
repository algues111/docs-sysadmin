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




 Lier MacOS à un annuaire LDAP
===============================



