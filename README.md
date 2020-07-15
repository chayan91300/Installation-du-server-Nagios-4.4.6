# installer-la-dernier-version-Nagios-4.4.6
Étape 1: mise à jour du système

Exécutez les commandes de mise à jour sur votre système Ubuntu pour vous assurer que vous êtes sur la dernière version
```
sudo apt update && sudo apt upgrade 
```
Il est recommandé de redémarrer si aucun service ne sera affecté.

 sudo systemctl reboot 

Étape 2: installez les packages requis

Une fois le système mis à niveau et redémarré. Vous devrez installer les packages requis pour construire Nagios Core sur Ubuntu Linux.

 sudo apt update sudo apt install wget unzip vim curl openssl build-essential libgd-dev libssl-dev libapache2-mod-php php-gd php apache2 

Étape 3: Téléchargez Nagios Core sur Ubuntu 20.04

Consultez la page des versions pour les derniers Nagios disponibles.

 export VER="4.4.6" curl -SL https://github.com/NagiosEnterprises/nagioscore/releases/download/nagios-$VER/nagios-$VER.tar.gz | tar -xzf - 

La commande télécharge et extrait l'archive principale de Nagios dans votre répertoire de travail actuel.

Passez au dossier Nagios créé.

 cd nagios-$VER 

Étape 4: installer Nagios Core 4.x sur Ubuntu 20.04

Après avoir extrait l'archive, exécutez le script de configuration:

  ./configure 

Exécutez la commande make avec toutes les options pour compiler le programme principal et les CGI.

 sudo make all 

Créer un utilisateur et un groupe:

 sudo make install-groups-users sudo usermod -a -G nagios www-data 

Installez Nagios Core 4.x sur Ubuntu 20.04:

 sudo make install 

Installez le script init dans / lib / systemd / system ,

 sudo make install-daemoninit 

Installez et configurez les autorisations sur le répertoire pour contenir le fichier de commande externe:

 sudo make install-commandmode 

Installez des exemples de fichiers de configuration dans / usr / local / nagios / etc.

 sudo make install-config 

Installez le fichier de configuration Apache pour l'interface Web de Nagios:

 sudo make install-webconf sudo a2enmod rewrite cgi sudo systemctl restart apache2 

Installez le thème Exfoliation pour l'interface Web de Nagios.

 sudo make install-exfoliation 

Si vous souhaitez utiliser le thème Nagios classique, exécutez:

 sudo make install-classicui 

Étape 5: créer un utilisateur Web Nagios

Un utilisateur est requis pour accéder à la console Web de Nagios.

  $ sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
 Nouveau mot de passe: 
 Re-taper le nouveau mot de passe: 
 Ajout d'un mot de passe pour l'utilisateur nagiosadmin 

Le htpasswd a été utilisé pour générer le mot de passe et l'écrire dans le fichier /usr/local/nagios/etc/htpasswd.users .
Étape 6: installer les plugins Nagios sur Ubuntu 20.04

Les plugins Nagios sont utilisés pour étendre les fonctionnalités de surveillance Nagios. Assurons-nous qu'ils sont installés. Recherchez la dernière version des plugins Nagios à partir de la page des versions de Github .

 cd ~/ VER="2.3.3" curl -SL https://github.com/nagios-plugins/nagios-plugins/releases/download/release-$VER/nagios-plugins-$VER.tar.gz | tar -xzf - 

Accédez au répertoire source des plugins:

 cd nagios-plugins-$VER 

Compilez et installez les plugins Nagios en exécutant les commandes ci-dessous.

 ./configure --with-nagios-user=nagios --with-nagios-group=nagios make sudo make install 

Étape 7: vérifier l'installation et démarrer le service Nagios

Confirmez que votre installation Nagios a réussi sur la machine Ubuntu 20.04 Linux.

 $ sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg 

Vous devriez obtenir une sortie similaire à celle ci-dessous si l'installation de Nagios a réussi.

 Nagios Core 4.4.6 Copyright (c) 2009-present Nagios Core Development Team and Community Contributors Copyright (c) 1999-2009 Ethan Galstad Last Modified: 2020-04-28 License: GPL Website: https://www.nagios.org Reading configuration data... Read main config file okay... Read object config files okay... Running pre-flight check on configuration data... Checking objects... Checked 8 services. Checked 1 hosts. Checked 1 host groups. Checked 0 service groups. Checked 1 contacts. Checked 1 contact groups. Checked 24 commands. Checked 5 time periods. Checked 0 host escalations. Checked 0 service escalations. Checking for circular paths... Checked 1 hosts Checked 0 service dependencies Checked 0 host dependencies Checked 5 timeperiods Checking global event handlers... Checking obsessive compulsive processor commands... Checking misc settings... Total Warnings: 0 Total Errors: 0 Things look okay - No serious problems were detected during the pre-flight check 

Démarrez et activez le service nagios pour démarrer au démarrage.

 sudo systemctl enable --now nagios 

Confirmez que le service nagios est en cours d'exécution.

  $ systemctl status nagios
 ● nagios.service - Nagios Core 4.4.6
 Chargé: chargé (/lib/systemd/system/nagios.service; activé; préréglage du fournisseur: activé)
 Actif: actif (en cours d'exécution ) depuis Sun 2020-05-17 16:45:23 UTC;  Il y a 37s
 Documents: https://www.nagios.org/documentation
 Processus: 35008 ExecStartPre = / usr / local / nagios / bin / nagios -v /usr/local/nagios/etc/nagios.cfg (code = quitté, status = 0 / SUCCESS)
 Processus: 35009 ExecStart = / usr / local / nagios / bin / nagios -d /usr/local/nagios/etc/nagios.cfg (code = quitté, statut = 0 / SUCCÈS)
 PID principal: 35010 (nagios)
 Tâches: 6 (limite: 2344)
 Mémoire: 2,4 M
 CGroup: /system.slice/nagios.service
 ├─35010 / usr / local / nagios / bin / nagios -d /usr/local/nagios/etc/nagios.cfg
 ├─35011 / usr / local / nagios / bin / nagios --worker /usr/local/nagios/var/rw/nagios.qh
 ├─35012 / usr / local / nagios / bin / nagios --worker /usr/local/nagios/var/rw/nagios.qh
 ├─35013 / usr / local / nagios / bin / nagios --worker /usr/local/nagios/var/rw/nagios.qh
 ├─35014 / usr / local / nagios / bin / nagios --worker /usr/local/nagios/var/rw/nagios.qh
 └─35030 / usr / local / nagios / bin / nagios -d /usr/local/nagios/etc/nagios.cfg
 17 mai 16:45:23 ubuntu20 nagios [35010]: qh: Socket '/usr/local/nagios/var/rw/nagios.qh' initialisé avec succès
 17 mai 16:45:23 ubuntu20 nagios [35010]: qh: gestionnaire de requêtes principal enregistré
 17 mai 16:45:23 ubuntu20 nagios [35010]: qh: gestionnaire de requête de service d'écho enregistré
 17 mai 16:45:23 ubuntu20 nagios [35010]: qh: aide pour le gestionnaire de requêtes enregistré
 17 mai 16:45:23 ubuntu20 nagios [35010]: wproc: Gestionnaire enregistré avec succès en tant que @wproc avec gestionnaire de requêtes
 17 mai 16:45:23 ubuntu20 nagios [35010]: wproc: demande de registre: nom = Core Worker 35014; pid = 35014
 17 mai 16:45:23 ubuntu20 nagios [35010]: wproc: demande de registre: nom = Core Worker 35013; pid = 35013
 17 mai 16:45:23 ubuntu20 nagios [35010]: wproc: demande de registre: nom = Core Worker 35012; pid = 35012
 17 mai 16:45:23 ubuntu20 nagios [35010]: wproc: demande de registre: nom = Core Worker 35011; pid = 35011
 17 mai 16:45:24 ubuntu20 nagios [35010]: Lanceur de fichier de commande lancé avec succès avec pid 35030 

Étape 8: Accéder au tableau de bord Web de Nagios

si vous avez un pare-feu ufw, autorisez les ports http et https pour le trafic entrant.

 for i in http https ssh; do sudo ufw allow $i; done 

Utilisez maintenant votre navigateur pour accéder au tableau de bord Nagios sur http: [IP / hostname] / nagios /
