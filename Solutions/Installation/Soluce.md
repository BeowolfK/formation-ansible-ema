# Installation
[Enoncé Blog Migrolinux](https://blog.microlinux.fr/formation-ansible-03-installation/)

## Exercice 1
Démarrez la VM ubuntu depuis le répertoire atelier-01.
```console
$ vagrant up ubuntu
```
Connectez-vous à cette VM.
```console
$ vagrant ssh ubuntu
```
Rafraîchissez les informations sur les paquets.
```console
$ sudo apt update
```
Recherchez le paquet ansible avec les options qui vont bien.
```console
$ sudo apt search ansible
```
Installez le paquet officiel fourni par la distribution.
```console
$ sudo apt install -y ansible
```
Vérifiez si l’installation s’est bien déroulée.
```console
$ dpkg -l | grep ansible
```
Notez la version d’Ansible.
```console
$ ansible --version
ansible [core 2.14.18]
  config file = None
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.11.2 (main, Nov 30 2024, 21:22:50) [GCC 12.2.0] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```
Déconnectez-vous et supprimez la VM.
```console
$ vagrant destroy -f ubuntu
```

## Exercice 2

Répétez l’exercice précédent en configurant un dépôt PPA (Personal Package Archive) pour Ansible :
```console
$ sudo apt-add-repository ppa:ansible/ansible
```
Notez la version fournie par ce dépôt tiers et comparez avec la version officielle de l’exercice précédent.
```console
$ sudo apt install ansible -y
$ ansible --version
ansible [core 2.17.9]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Jan 17 2025, 14:35:34) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
```
Nous pouvons voir que la version d'Ansible est plus récente depuis le dépôt PPA

## Exercice 3
Lancez une VM Rocky Linux et installez Ansible en utilisant PIP et Virtualenv.
```console
$ vagrant up rocky
$ vagrant ssh rocky 
$ python3 -m venv ansible-env
$ source ansible-env/bin/activate
$ pip install --upgrade pip
$ pip install ansible
$ ansible --version
ansible [core 2.15.13]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/ansible-env/lib64/python3.9/site-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/vagrant/ansible-env/bin/ansible
  python version = 3.9.18 (main, Sep  7 2023, 00:00:00) [GCC 11.4.1 20230605 (Red Hat 11.4.1-2)] (/home/vagrant/ansible-env/bin/python3)
  jinja version = 3.1.6
  libyaml = True
```
Nous pouvons voir que la version d'Ansible est encore une version différente que ce soit depuis le dépôt Ubuntu _"classique"_ ou le PPA Ubuntu 

