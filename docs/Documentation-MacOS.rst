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

Si vous utilisez un Mac pour suivre ce tuto, vous pouvez vous rendre dans l'AppStore et télécharger MacOS Ventura.

Lorsque ce dernier est terminé, l'assistant d'installation se lancera automatiquement, et vous n'aurez qu'à le fermer.



Etape 2 : Formatage et création de la clé USB bootable
----------------------------------------------------------

.. danger::
    Veillez à sauvegarder toute donnée qui vous est utile sur votre clé USB, car à la suite des prochaines étapes, elles seront **définitivement perdues**.



:command:`sudo /Applications/Install\ macOS\ Ventura.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume`

Remplacez "*MyVolume*" par le nom de votre clé USB

