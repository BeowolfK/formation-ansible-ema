# Un serveur Web simple
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-10-apache/)

## Exercice 1
Écrivez trois playbooks :

    Un premier playbook apache-debian.yml qui installe Apache sur l’hôte debian avec une page personnalisée Apache web server running on Debian Linux.
```yaml
---  # apache-debian.yml
- name: Installer Apache sur Debian 
  hosts: debian
  become: yes
  tasks:
    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Debian Linux</h1>
            </body>
          </html>
...
```
    Un deuxième playbook apache-rocky.yml qui installe Apache sur l’hôte rocky avec une page personnalisée Apache web server running on Rocky Linux.
```yaml
---
- name: Installer Apache sur Rocky Linux
  hosts: rocky
  become: yes
  tasks:
    - name: Installer Apache
      dnf:
        name: httpd
        state: present

    - name: Copier la page personnalisée
      copy:
        dest: /var/www/html/index.html
        content: |
          <html>
            <head><title>Apache Web Server Running on Rocky Linux</title></head>
            <body>
              <h1>Apache Web Server Running on Rocky Linux</h1>
            </body>
          </html>
        owner: apache
        group: apache
        mode: '0644'

    - name: Démarrer et activer Apache
      systemd:
        name: httpd
        state: started
        enabled: yes
...
```
    Un troisième playbook apache-suse.yml qui installe Apache sur l’hôte suse avec une page personnalisée Apache web server running on SUSE Linux.
```yaml
---
- name: Installer Apache sur SUSE
  hosts: suse
  become: yes
  tasks:
    - name: Installer Apache
      zypper:
        name: apache2
        state: present

    - name: Copier la page personnalisée
      copy:
        dest: /srv/www/htdocs/index.html
        content: |
          <html>
            <head><title>Apache Web Server Running on SUSE Linux</title></head>
            <body>
              <h1>Apache Web Server Running on SUSE Linux</h1>
            </body>
          </html>
        owner: wwwrun
        group: www
        mode: '0644'

    - name: Démarrer et activer Apache
      systemd:
        name: apache2
        state: started
        enabled: yes
...
```

