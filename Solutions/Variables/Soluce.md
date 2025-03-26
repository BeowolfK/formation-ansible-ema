# Variables
[Enoncé Blog Microlinux](https://blog.microlinux.fr/formation-ansible-12-variables/)

## Exercice 1

Écrivez un playbook myvars1.yml qui affiche respectivement votre voiture et votre moto préférée en utilisant le module debug et deux variables mycar et mybike définies en tant que play vars.
```yaml
---
- name: Display favorite car and bike
  hosts: localhost
  gather_facts: no
  vars:
    mycar: Porsche 991 Type 991 GT3RS
    mybike: Suzuki GSX 1300R Hayabusa
  tasks:
    - name: Show favorite car
      debug:
        msg: "Favorite car : {{ mycar }}"
    - name: Show favorite bike
      debug:
        msg: "Favorite bike : {{ mybike }}"
...
```
En utilisant les extra vars, remplacez successivement l’une et l’autre marque – puis les deux à la fois – avant d’exécuter le play.
```console
$ ansible-playbook playbooks/myvars1.yml | grep msg
    "msg": "Favorite car : Porsche 991 Type 991 GT3RS"
    "msg": "Favorite bike : Suzuki GSX 1300R Hayabusa"
$ ansible-playbook playbooks/myvars1.yml -e "mycar='Mercedes Maybach S680'"| grep msg
    "msg": "Favorite car : Mercedes Maybach S680"
    "msg": "Favorite bike : Suzuki GSX 1300R Hayabusa"
$ ansible-playbook playbooks/myvars1.yml -e "mybike='Encore l\'Hayabusa'"| grep msg
    "msg": "Favorite car : Porsche 991 Type 991 GT3RS"
    "msg": "Favorite bike : Encore l'Hayabusa"
$ ansible-playbook playbooks/myvars1.yml -e "mycar='BMW Alpina B7 Biturbo' mybike='L\'Hayabusa sera toujours ma moto préféré ;)'"| grep msg
    "msg": "Favorite car : BMW Alpina B7 Biturbo"
    "msg": "Favorite bike : L'Hayabusa sera toujours ma moto préféré ;)"
```
Écrivez un playbook myvars2.yml qui fait essentiellement la même chose que myvars1.yml, mais en utilisant une tâche avec set_fact pour définir les deux variables.
```yaml
---
- name: Display favorite car and bike with set_fact
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Set car and bike variables
      set_fact:
        mycar: Porsche 991 Type 991 GT3RS
        mybike: Suzuki GSX 1300R Hayabusa
    - name: Show favorite car
      debug:
        msg: "Favorite car : {{ mycar }}"
    - name: Show favorite bike
      debug:
        msg: "Favorite bike : {{ mybike }}"
...
```
Là aussi, essayez de remplacer les deux variables en utilisant des extra vars avant l’exécution du play.
```yaml
$ ansible-playbook playbooks/myvars2.yml -e "mycar='Lamborghini Countach' mybike='Toujours la faucon japonais, et vous?'"| grep msg
    "msg": "Favorite car : Lamborghini Countach"
    "msg": "Favorite bike : Toujours la faucon japonais, et vous?"
```
Écrivez un playbook myvars3.yml qui affiche le contenu des deux variables mycar et mybike mais sans les définir. Avant d’exécuter le playbook, définissez VW et BMW comme valeurs par défaut pour mycar et mybike pour tous les hôtes, en utilisant l’endroit approprié.
Nous devons créer l'arborescence de fichier tel que : 
```console
$ tree
.
├── ansible.cfg
├── group_vars
│   └── all.yml
├── inventory
└── playbooks
    ├── myvars1.yml
    ├── myvars2.yml
    └── myvars3.yaml
```
Voici les fichiers détaillés : 
 - group_vars/all.yml :
```yaml
mycar: VW
mybike: BMW
```
 - playbooks/myvars3.yaml :
```
---
# myvars3.yml
- name: Display favorite car and bike from defaults
  hosts: all
  gather_facts: no
  tasks:
    - name: Show favorite car
      debug:
        msg: "Favorite car : {{ mycar }}"
    - name: Show favorite bike
      debug:
        msg: "Favorite bike : {{ mybike }}"
...
```
Effectuez le nécessaire pour remplacer VW et BMW par Mercedes et Honda sur l’hôte target02.
```console
$ mkdir host_vars
$ echo -e "mycar: Mercedes\nmybike: Honda" > host_vars/target02.yml 
$ ansible-playbook playbooks/myvars3.yaml 

PLAY [Display favorite car and bike from defaults] *****************************************************************************************************************************************************************************************

TASK [Show favorite car] *******************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Favorite car : VW"
}
ok: [target03] => {
    "msg": "Favorite car : VW"
}
ok: [target02] => {
    "msg": "Favorite car : Mercedes"
}

TASK [Show favorite bike] ******************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Favorite bike : BMW"
}
ok: [target02] => {
    "msg": "Favorite bike : Honda"
}
ok: [target03] => {
    "msg": "Favorite bike : BMW"
}

PLAY RECAP *********************************************************************************************************************************************************************************************************************************
target01                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
Écrivez un playbook display_user.yml qui affiche un utilisateur et son mot de passe correspondant à l’aide des variables user et password. Ces deux variables devront être saisies de manière interactive pendant l’exécution du playbook. Les valeurs par défaut seront microlinux pour user et yatahongaga pour password. Le mot de passe ne devra pas s’afficher pendant la saisie.
```yaml
---
- name: Display user and password
  hosts: localhost
  gather_facts: no
  vars_prompt:
    - name: user
      prompt: "Enter username"
      private: no
      default: microlinux
    - name: password
      prompt: "Enter password"
      private: yes
      default: yatahongaga
  tasks:
    - name: Show user information
      debug:
        msg: "User: {{ user }} | Password: {{ password }}"
...
```

