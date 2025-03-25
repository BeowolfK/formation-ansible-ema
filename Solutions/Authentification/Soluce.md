# Authentification
[Enoncé Blog Migrolinux](https://blog.microlinux.fr/formation-ansible-04-authentification/)

## Exercice 1
Démarrez les VM
```console
$ vagrant up
```
Connectez-vous au Control Host :
```console
$ vagrant ssh control
```
Ansible est déjà installé sur cette machine :
```console
$ type ansible
ansible is /usr/bin/ansible
```
On modifie le fichier host pour ajouter ces lignes :
```console
$ tail -n3 /etc/hosts
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
```
Ensuite, on ajoute les fingerprint SSH au clé connus du serveur : 
```console
$ ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts
# target02:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.11
# target01:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.11
# target03:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.11
```
On autorise ensuite la connexion par mot de passe aux différents serveur 
```console
$ vagrant ssh target01 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
$ vagrant ssh target02 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
$ vagrant ssh target03 -- "sudo sed -i 's/^#PasswordAuthentication/PasswordAuthentication/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
```
On se reconnecte au serveur _control_ et on génere une clé ssh
```console
$ vagrant ssh control
$ ssh-keygen
$ for host in target01 target02 target03; do ssh-copy-id vagrant@$host; done
$ ansible all -i target01,target02,target03 -m ping
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
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
Nous pouvons désormais voir que nous accédons aux 3 serveurs _target_ grace a Ansible sans mot de passe ! 
