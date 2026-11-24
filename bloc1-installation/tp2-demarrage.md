TP2 – ADMINISTRATION LINUX

ÉTAPE 1 : MESURE DU DÉMARRAGE

1. Analyse du temps de démarrage

Commande utilisée :
systemd-analyze

Cette commande permet de mesurer le temps nécessaire au démarrage du système Linux.

Les cinq phases du démarrage sont :
1. Firmware (BIOS ou UEFI)
2. Bootloader (GRUB)
3. Kernel (noyau Linux)
4. Initramfs
5. Userspace (services et applications)

Résultat obtenu : à compléter avec le résultat exact de la commande.

2. Les trois services les plus lents

Commande utilisée :
systemd-analyze blame | head -n 10

Les trois services les plus lents observés sont :
- snapd.seeded.service : 6,249 secondes. Il prépare les paquets Snap.
- apport.service : 5,647 secondes. Il collecte les informations sur les erreurs et les plantages.
- initrd-switch-root.service : 5,535 secondes. Il participe au passage de l'environnement initramfs vers le système de fichiers racine.

3. Comparaison entre blame et critical-chain

Commandes utilisées :
systemd-analyze blame
systemd-analyze critical-chain

La commande blame classe les services selon leur durée de démarrage.
La commande critical-chain montre les services qui peuvent retarder le démarrage à cause de leurs dépendances.

4. Vérification des services en échec

Commandes utilisées :
systemctl --failed
journalctl -b -p err

La première commande affiche les services qui ont échoué. La deuxième affiche les erreurs enregistrées depuis le démarrage actuel.

ÉTAPE 2 : CONFIGURATION DE GRUB

1. Sauvegarde du fichier de configuration

Commande utilisée :
sudo cp /etc/default/grub /etc/default/grub.orig

Cette commande permet de sauvegarder le fichier de configuration de GRUB avant toute modification.

2. Modification du délai d'affichage

Dans le fichier /etc/default/grub, les paramètres à configurer sont :
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=5

Cela permet d'afficher le menu GRUB pendant 5 secondes avant le démarrage automatique.

3. Mise à jour de GRUB

Commande utilisée :
sudo update-grub

Cette commande permet de régénérer le fichier de configuration de GRUB à partir des paramètres définis.

4. Vérification

Commandes utilisées :
diff /etc/default/grub.orig /etc/default/grub
grep timeout /boot/grub/grub.cfg

Ces commandes permettent de vérifier les modifications effectuées et le délai configuré.

5. Pourquoi ne faut-il jamais modifier directement /boot/grub/grub.cfg ?

Il ne faut pas modifier directement ce fichier parce qu'il est généré automatiquement à partir des fichiers de configuration de GRUB. Une modification directe risque d'être écrasée lors de la prochaine mise à jour de GRUB.

Il faut donc modifier /etc/default/grub, puis exécuter sudo update-grub.
ÉTAPE 3 : LES CIBLES SYSTEMD

1. Vérification de la cible par défaut

Commande utilisée :
systemctl get-default

Résultat observé initialement :
multi-user.target

Cette cible permet de démarrer Linux en mode multi-utilisateur, sans interface graphique obligatoire.

2. Configuration de la cible par défaut

Commande utilisée :
sudo systemctl set-default multi-user.target

Vérification :
systemctl get-default

La cible par défaut doit être multi-user.target.

3. Démarrage en mode rescue

Au menu GRUB, sélectionner Ubuntu, appuyer sur e, puis ajouter à la fin de la ligne qui commence par linux :
systemd.unit=rescue.target

Ensuite, démarrer avec Ctrl + X ou F10.

Message observé :
You are in rescue mode. Press Enter for system maintenance (or press Control-D to continue)

Ce message indique que le système est en mode de secours.

4. Quel compte le mode rescue essaie-t-il d'utiliser ?

Le mode rescue demande généralement une authentification de l'utilisateur root pour effectuer la maintenance du système.

Sur Ubuntu, le compte root est verrouillé par défaut. Cela peut donc empêcher une connexion directe avec le mot de passe root.

5. Quelle est la faiblesse de sécurité ?

Une personne ayant accès physiquement à la console et au menu GRUB peut tenter de modifier les paramètres de démarrage pour obtenir un accès privilégié.

Il est donc important de protéger l'accès physique au serveur et de sécuriser GRUB.
SYNTHÈSE DE SÉCURITÉ

La modification non autorisée des fichiers système représente un risque important pour la sécurité du serveur.

Une personne ayant accès à la console peut tenter de contourner certaines protections et obtenir des privilèges élevés.

Pour protéger le serveur, il faut :
1. Limiter l'accès physique aux machines.
2. Protéger GRUB avec un mot de passe.
3. Utiliser des comptes utilisateurs sécurisés.
4. Limiter les droits d'administration.
5. Effectuer régulièrement des sauvegardes.

CONCLUSION

Ce TP m'a permis de comprendre les différentes phases du démarrage Linux, d'analyser les services systemd, de configurer GRUB, de découvrir les cibles systemd et de comprendre les risques liés à une mauvaise configuration des fichiers système.

