# Cibles hétérogènes
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-15-cibles-heterogenes/)

## Exercice 1
Écrivez successivement deux playbooks pour mettre en place la synchronisation NTP avec Chrony sur vos quatre Target Hosts sous Debian, Rocky Linux, SUSE Linux et Ubuntu. Il vous faudra identifier le nom du paquet, le service correspondant et le chemin vers le fichier de configuration par défaut pour chacune des distributions.

Voici la configuration qu’il faudra installer sur chacune des quatre cibles :
```ini
# chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
Le premier playbook chrony-01.yml utilisera les modules de gestion de paquets natifs apt, dnf et zypper et s’inspirera de la méthode « gros sabots » utilisée plus haut dans cet article.

```yaml
---
- name: Install and configure Chrony
  hosts: all
  become: yes
  tasks:
    - name: Install Chrony on Debian/Ubuntu
      apt:
        name: chrony
        state: present
      when: ansible_facts['os_family'] == 'Debian'
    
    - name: Install Chrony on Rocky Linux
      dnf:
        name: chrony
        state: present
      when: ansible_facts['distribution'] == 'Rocky'

    - name: Install Chrony on openSUSE Leap Linux
      zypper:
        name: chrony
        state: present
      when: ansible_facts['distribution'] == 'openSUSE Leap'

    - name: Copy custom chrony.conf file
      copy:
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'
    
    - name: Enable and start Chrony service on Debian/Ubuntu and Rocky
      service:
        name: chronyd
        state: started
        enabled: yes
      when: ansible_facts['os_family'] != 'openSUSE Leap'
      notify:
        - Restart chrony service

    - name: Enable and start Chrony service on openSUSE Leap
      service:
        name: chronyd
        state: started
        enabled: yes
      when: ansible_facts['distribution'] == 'openSUSE Leap'
      notify:
        - Restart chrony service

  handlers:
    - name: Restart chrony service
      service:
        name: chronyd
        state: restarted
...
```
Le deuxième playbook chrony-02.yml définira trois variables chrony_package, chrony_service et chrony_confdir et utilisera le module de gestion de paquets générique package.
```yaml
- name: Install and configure Chrony NTP server
  hosts: all
  become: yes
  vars:
    chrony_package: chrony
    chrony_service: chronyd
    chrony_confdir: /etc/chrony.conf
  tasks:
    - name: Install Chrony package
      package:
        name: "{{ chrony_package }}"
        state: present

    - name: Copy custom chrony.conf file
      copy:
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        dest: "{{ chrony_confdir }}"
        owner: root
        group: root
        mode: '0644'
      notify: Restart chrony service

    - name: Enable and start Chrony service
      service:
        name: "{{ chrony_service }}"
        state: started
        enabled: yes

  handlers:
    - name: Restart chrony service
      service:
        name: "{{ chrony_service }}"
        state: restarted
```
