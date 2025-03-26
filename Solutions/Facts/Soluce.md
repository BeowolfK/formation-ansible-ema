# Facts
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-14-facts/)

## Exercice 1
Écrivez trois playbooks pour afficher des informations sur chacun des Target Hosts :

 - pkg-info.yml pour afficher le gestionnaire de paquets utilisé
```yaml
---
- name: Display packet manager
  hosts: all
  gather_facts: yes
  tasks:
    - name: Show packet manager
      debug:
        msg: "Package manager : {{ ansible_facts.pkg_mgr }}"
...
```
 - python-info.yml pour afficher la version de Python installée
```yaml
---
- name: Display Python version
  hosts: all
  gather_facts: yes
  tasks:
    - name: Show Python version
      debug:
        msg: "Python version : {{ ansible_facts.python_version }}"
...
```
 - dns-info.yml pour afficher le(s) serveur(s) DNS utilisé(s)
```yaml
---
- name: Display DNS server(s) information
  hosts: all
  gather_facts: yes
  tasks:
    - name: Show DNS servers
      debug:
        msg: "DNS server(s) : {{ ansible_facts.dns.nameservers }}"
...
```
