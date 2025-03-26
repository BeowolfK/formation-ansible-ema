# Handler
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-11-handler/)

## Exercice 1
Écrivez un playbook chrony.yml qui assure la synchronisation NTP de tous vos Target Hosts :

 - Installez le paquet chrony.
 - Activez et démarrez le service chronyd correspondant.
 - Jetez un œil sur le fichier de configuration /etc/chrony.conf fourni par défaut.
 - Installez une configuration personnalisée (cf. ci-dessous).
 - Prenez en compte cette nouvelle configuration.
 - Vérifiez la syntaxe correcte de votre playbook chrony.yml.
 - Vérifiez l’idempotence de votre playbook.

Voici la configuration à installer :
```ini
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
Le playbook yaml est le suivant :
```yaml
---
- name: Assurer la synchronisation NTP avec chrony
  hosts: debian
  become: yes
  tasks:
    - name: Installer le paquet chrony
      apt:
        name: chrony
        state: present

    - name: Activer et démarrer le service chronyd
      systemd:
        name: chronyd
        enabled: yes
        state: started

    - name: Déployer la configuration chrony.conf
      copy:
        dest: /etc/chrony.conf
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        owner: root
        group: root
        mode: '0644'
      notify: Redémarrer chronyd

  handlers:
    - name: Redémarrer chronyd
      systemd:
        name: chronyd
        state: restarted
...
```
Nous pouvons vérifier l'idempotence en executant une deuxieme fois le playbook : 
```console
$ ansible-playbook playbooks/chrony.yaml 

PLAY [Assurer la synchronisation NTP avec chrony] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Installer le paquet chrony] **********************************************************************************************************************************************************************************************************
changed: [target02]
changed: [target03]
changed: [target01]

TASK [Activer et démarrer le service chronyd] **********************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Déployer la configuration chrony.conf] ***********************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

RUNNING HANDLER [Redémarrer chronyd] *******************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target03]
changed: [target02]

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
target01                   : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

$ ansible-playbook playbooks/chrony.yaml 

PLAY [Assurer la synchronisation NTP avec chrony] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Installer le paquet chrony] **********************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Activer et démarrer le service chronyd] **********************************************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Déployer la configuration chrony.conf] ***********************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Nous pouvons voir que lors de la deuxieme fois, le handler n'as pas été trigger et que le service n'as pas été redemarré.
