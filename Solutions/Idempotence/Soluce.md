# Installation
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-08-idempotence/)

## Exercice 1

Installez successivement les paquets tree, git et nmap sur toutes les cibles.
```console
$ vagrant ssh ansible
$ cd ansible/projets/ema/
$ ansible all -m package -a "name=nmap state=present"
$ ansible all -m package -a "name=tree state=present"
$ ansible all -m package -a "name=git state=present"
```
Désinstallez successivement ces trois paquets en utilisant le paramètre supplémentaire state=absent.
```console
$ ansible all -m package -a "name=git state=absent"
$ ansible all -m package -a "name=tree state=absent"
$ ansible all -m package -a "name=nmap state=absent"
```
Copier le fichier /etc/fstab du Control Host vers tous les Target Hosts sous forme d’un fichier /tmp/test3.txt.
```console
$ ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
```
Supprimez le fichier /tmp/test3.txt sur les Target Hosts en utilisant le module file avec le paramètre state=absent.
```console
$ ansible all -m file -a "path=/tmp/test3.txt state=absent"
```
Enfin, affichez l’espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?
```console
$ ansible all -a "df -h /"
debian | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.3G  115G   2% /
suse | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3       124G  2.8G  118G   3% /
rocky | CHANGED | rc=0 >>
Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/rl_rocky9-root   70G  3.3G   67G   5% /
```
La commande `df -h /` est marquée comme _CHANGED_ dans Ansible, alors que la commande `df` ne modifie pas le système
