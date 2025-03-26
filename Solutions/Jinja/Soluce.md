# Jinja & Templates
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-16-jinja-templates/)

## Exercice 1
Dans notre précédent exercice, nous avons mis en place la synchronisation NTP avec Chrony sur nos Target Hosts, en installant le fichier de configuration ci-dessous sur chacune des quatre cibles :
```ini
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
Écrivez un playbook chrony.yml qui installe un fichier de configuration personnalisé sur vos cibles. La première ligne de commentaire devra indiquer le chemin complet vers le fichier :
 - Dans certains cas ce sera /etc/chrony/chrony.conf.
 - Dans d’autres cas ce sera simplement /etc/chrony.conf.
```yaml
---
- name: Configure Chrony NTP synchronization
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    - name: Set Task for Debian/Ubuntu
      set_fact:
        chrony_conf_path: '/etc/chrony/chrony.conf'
      when: ansible_facts['os_family'] == 'Debian'
        
    - name: Set Task for Rocky/Suse
      set_fact:
        chrony_conf_path: '/etc/chrony.conf'
      when: ansible_facts['os_family'] in ['Suse','RedHat']
    
    - name: Install Chrony package
      package:
        name: chrony
        state: present

    - name: Deploy custom Chrony configuration
      template:
        src: chrony.conf.j2
        dest: "{{ chrony_conf_path }}"
        owner: root
        group: root
        mode: '0644'
      notify: Restart Chrony

  handlers:
    - name: Restart Chrony
      service:
        name: chronyd
        state: restarted
```
Et le fichier de template Jinja2 : 
 - `playbook/template/chrony.conf.j2`
```jinja
# Configuration file: {{ chrony_conf_path }}

server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst

driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
