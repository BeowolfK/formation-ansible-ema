# Installation
[Enoncé Blog Migrolinux](https://blog.microlinux.fr/formation-ansible-03-installation/)

## Exercice 1
Démarrez la VM ubuntu depuis le répertoire atelier-01.
	$ 	vagrant up
Connectez-vous à cette VM.
	$ vagrant ssh ubuntu
Rafraîchissez les informations sur les paquets.
	$ sudo apt update
Recherchez le paquet ansible avec les options qui vont bien.
	$ sudo apt search ansible
Installez le paquet officiel fourni par la distribution.
	$ sudo apt install -y ansible
Vérifiez si l’installation s’est bien déroulée.
	$ dpkg -l | grep ansible
Notez la version d’Ansible.
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
Déconnectez-vous et supprimez la VM.
	$ vagrant destroy -f ubuntu

