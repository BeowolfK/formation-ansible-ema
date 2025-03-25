# Configuration de base
[Enoncé Blog Migrolinux](https://blog.microlinux.fr/formation-ansible-06-configuration/)

## Exercice 1
Démarrez les VM
```console
$ vagrant up
```
Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple.
```console
$ vagrant ssh control -- 'echo -e "192.168.56.20 target01\n192.168.56.30 target02\n192.168.56.40 target03" | sudo tee -a /etc/hosts'
```
Configurez l’authentification par clé SSH avec les trois Target Hosts.
On autorise ensuite la connexion par mot de passe aux différents serveur 
```console
$ vagrant ssh target01 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
$ vagrant ssh target02 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
$ vagrant ssh target03 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
$ vagrant ssh control -- "ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts"
$ vagrant ssh control -- "ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsa"
$ vagrant ssh control 
$ for host in target01 target02 target03; do ssh-copy-id vagrant@$host; done
```
Installez Ansible.
```console
$ vagrant ssh control -- sudo apt install ansible -y
```
Envoyez un premier ping Ansible sans configuration
```console
$ vagrant ssh control -- "ansible all -i target01,target02,target03 -m ping"
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
Créez un répertoire de projet ~/monprojet.
```console
$ vagrant ssh control
$ mkdir ~/monprojet
```
Créez un fichier vide ansible.cfg dans ce répertoire.
```console
$ cd $_
$ touch ansible.cfg
```
Vérifiez si ce fichier est bien pris en compte par Ansible.
```console
$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```
Spécifiez un inventaire nommé hosts.
```console
$ echo -e "[defaults]\ninventory = ./hosts" >> ansible.cfg
```
Activez la journalisation dans ~/journal/ansible.log.
```console
$ echo "log_path = ./journal/ansible.log" >> ansible.cfg 
```
Testez la journalisation.
```console
$ ansible all -i target01,target02,target03 -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
$ cat logs/ansible.log 
2025-03-25 09:52:36,141 p=3933 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-03-25 09:52:36,147 p=3933 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-03-25 09:52:36,148 p=3933 u=vagrant n=ansible | target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
Créez un groupe [testlab] avec vos trois Target Hosts.
```console
$ echo -e "[testlab]\ntarget01 ansible_host=192.168.56.20\ntarget02 ansible_host=192.168.56.30\ntarget03 ansible_host=192.168.56.40" > inventory
```
Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.
```console
$ echo -e "[testlab:vars]\nansible_user=vagrant" >> inventory
```
Envoyez un ping Ansible vers le groupe de machines [all].
```console
$ ansible all -m ping
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.
```console
echo -e "ansible_become=true\nansible_become_method=sudo\nansible_become_user=root" >> inventory
```
Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.
```console
$ansible all -m shell -a "head -n1 /etc/shadow"
target02 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target01 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
```
Quittez le Control Host et supprimez toutes les VM de l’atelier.
```console
$ exit
$ vagrant destroy -f
```

