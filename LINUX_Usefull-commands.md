# Users
## types
- root (UID=0 | GID=0)
- daemon (1-999 | 1-999) : pour les comptes et les services
- Utilisateur (>1000 | >1000)

## Groups
- Edit a group `groupmod -g [New_GID] -n [New_name] Group_to_edit`
- Delete a groupe `groupdel Group_to_delete` (only if no memebers in it)

### informations génériques
> Un user doit toujours avoir un groupe principal
### creation d'un utilisteur
- `useradd -m -g adm -G groupsec1, groupsec2,wheel -s /bin/bash -d /home/jjacques jeanjacques`
  - `-m` create home folder
  - `-g` groupe principal (son propre group si omis)
  - `-G` groupes secondaires
  - `-s` shell type
### mettre en place et modifier le mot de passe
- `passwd -e -l pierre` pour générer le mot de passe (sans laisser de trace dans l'historique)
  - `-e` devra être changé au premier login
  - `-l` pour vérouiller le compte  (ajoute ! devant le hash du password)
### définition des paramètres par défaut
  - definitin des paramètres par defaut `/etc/default/useradd`
  - Arborescence par défaut des users : dans `/etc/skel` (certains fichiers exitent déja)
### fichier /etc/passwd et /etc/shadow
- `/etc/passwd` :no:pass:GCOS (5 elements par des virgules : nom,prenom,entreprise...):repertoire perso:shell...
- si un `x`dans le pass, il est égéré par le fichier `/etc/shadow`
- `/etc/shadow` identifiant:hash:creation:modification:agemini:agemaxi... (les infos ne sont pas souvent utilsiées autre que le hash)
### modifier un utilisateur
- `usermod -g groupprincipal -aG groupsec4, groupsec5 -L|U` 
  - `-g` pour changer le groupe principal
  - `-G` pour les groupes secondaires (si pas `-aG`, alors on remplace les droits existants)
  - `-L` pour "locker" l'utilsiateur | `-U` pour "Unlocker"
### supprimer un utilisateur
- `userdel -r username` pour supprimer l'utilsiateur 
  - `-r` pour supprimer le dossier home et les fichiers

## Droits d'adminsitration
- `su - | ou sudo -i` pour changer d'utilisateur
- `su -s` pour changer le shell type
- `sudo -k` > Oublier le mot de passe
- `sudo -u user_to_execute_command`
### changer les droits sudo
- ajouter un user au group `sudo` (à éviter)
### Comment configurer sudo à travers sudoers
- On ne modifie pas le fichier `/etc/sudoers`
- `visudo` pour changer les droits `root`
```bash
User_Alias WBMASTERS = master1, master2         # pour créer un alias pour des utilisateurs
Command_Alias DUMPS = /bin/dump, /sbin/dump...  # pour créer des alias de commands
WEBMASTERS ALL = DUMPS                          # Autorise les commandes
John ALL = /usr/local/bin/apt
%admins ALL = /usr/bin/bash                     # le %indique qu'il s'agit d'un groupe            
John All = command1, NOPASSWD:commande2         # peut lancer com1 avec et com2 sans mot de passe

# Rights / Owner
## Change Rights
<p class="callout info"> Use `chmod u+x script.sh` instead of `chmod +x script.sh`</p>
`chmod -R [ugoa][+-=][rwx(X)] file|folder`
[ugoa] : user | group | other | all
+-= : Add | Remove | set
rwx : Read | Write | Execute
options : -R (recursive) | -c (show changes) | -f (force = supress errors)

## Change owner
`chown user:group file|folder` change user and group owner of file/folder   
`chown :group file` or `chown user: file`

# LOGS
## Journal 
rsyslog - journald
- Les logs des tâches gérés par `systemd` sont dans `journald` 
### JOURNALD
- `journald` est configuré dans `/etc/systemd/journald.conf`
- `journalctl` tous les logs de tous les services (reset au redémarrage) > transfert vers `rsyslog`
  - `journalctl -f` affichage en direct des logs    
  - `journalctl -u service` pour un service
  - `journalctl _PID=1004` pour un PID
  - `journalctl /usr/sbin/sshd` pour une commande
  - `journalctl -p emerg | alert | crit | err | warning | notice | info | debug` par priorité
### RSYSLOG
- travaille sur des 'facilities' et sur des 'priorités'
  - `auth | daemon | kern | mail | user | * | none | local[0-7]`
  - Priorités : same as `journalctl`
- Se configure via `/etc/rsyslog.com`
  - le `-` indique un enregistrement asynchrone (écrit de temps à autres)
- `logger -p cron.info message de test`

