# Ansible : L'automatisation pour tous !

Bienvenue dans le monde d'Ansible ! Si vous en avez marre de vous connecter manuellement à des serveurs pour installer des logiciels ou configurer des services, vous êtes au bon endroit. Ansible est un outil d'automatisation qui vous permet de gérer des flottes de serveurs de manière simple et efficace.

## Pourquoi Ansible est votre nouvel allié ?

Ansible se distingue par sa simplicité. Contrairement à d'autres outils comme Puppet ou Chef, Ansible est **agentless**, ce qui signifie que vous n'avez rien à installer sur les serveurs que vous voulez gérer (à part Python, qui est généralement déjà là).

- **Simple :** Les fichiers de configuration d'Ansible (appelés *playbooks*) sont écrits en YAML, un format très lisible par les humains.
- **Puissant :** Automatisez tout, du provisionnement de serveurs au déploiement d'applications complexes.
- **Agentless :** Pas de démons à gérer, pas de ports à ouvrir en permanence. Ansible communique via SSH.

> **Anecdote :** Le nom "Ansible" vient de la science-fiction. C'est un appareil de communication supraluminique (plus rapide que la lumière) fictif. Un nom parfait pour un outil qui envoie des commandes à des serveurs à la vitesse de l'éclair !

## Les concepts fondamentaux d'Ansible

Pour maîtriser Ansible, il suffit de comprendre quelques concepts de base.

### 1. L'inventaire (Inventory)

C'est un simple fichier texte (souvent au format INI ou YAML) qui liste les serveurs que vous voulez gérer. Vous pouvez regrouper vos serveurs pour appliquer des configurations différentes.

**Exemple d'inventaire (`hosts.ini`):**
```ini
[webservers]
server1.example.com
server2.example.com

[dbservers]
db1.example.com
```

### 2. Les Playbooks

C'est le cœur d'Ansible. Un *playbook* est un fichier YAML qui décrit une série de tâches à exécuter sur un groupe de serveurs.

**Exemple de playbook simple (`nginx.yml`):**
```yaml
---
- name: Installer et configurer Nginx
  hosts: webservers
  become: yes # Pour exécuter les tâches en tant que root (sudo)

  tasks:
    - name: Installer Nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Démarrer le service Nginx
      ansible.builtin.service:
        name: nginx
        state: started
```

### 3. Les Modules

Chaque tâche dans un playbook utilise un **module**. Un module est un petit bout de code qui effectue une action spécifique. Ansible en a des milliers ! `apt` pour gérer les paquets sur Debian/Ubuntu, `service` pour gérer les services, `copy` pour copier des fichiers, etc.

### 4. Les Rôles (Roles)

Quand vos playbooks deviennent complexes, vous pouvez les organiser en **rôles**. Un rôle est une structure de dossiers qui regroupe des tâches, des templates, des fichiers et des variables pour une tâche spécifique (par exemple, un rôle `nginx`). C'est un excellent moyen de rendre votre code réutilisable.

---

## Cheatsheet de l'Admin Automatisé

Voici quelques commandes et concepts pour bien démarrer avec Ansible, avec plus de détails et d'exemples.

### 1. Commandes de base

- **Exécuter un playbook :**
  ```bash
  ansible-playbook -i hosts.ini mon_playbook.yml
  ```
  * `-i` spécifie le fichier d'inventaire.
  * `--check` : Pour un "dry run" (simule l'exécution sans faire de changements).
  * `--diff` : Montre les changements qui seraient effectués.

- **Exécuter une commande simple (ad-hoc) :**
  Utile pour des actions rapides sans écrire de playbook.
  ```bash
  # Pinger tous les serveurs définis dans l'inventaire par défaut (ou -i hosts.ini)
  ansible all -m ping

  # Vérifier l'espace disque sur les serveurs du groupe 'webservers'
  ansible webservers -m shell -a "df -h"

  # Redémarrer un service sur un hôte spécifique
  ansible server1.example.com -m service -a "name=apache2 state=restarted"
  ```
  * `-m` spécifie le module à utiliser.
  * `-a` passe des arguments au module.
  * `all` cible tous les hôtes de l'inventaire.

### 2. Inventaire (Inventory) - Options avancées

L'inventaire peut être dynamique (généré par un script) ou statique.

**Exemple d'inventaire YAML (`inventory.yml`):**
```yaml
all:
  hosts:
    server1.example.com:
    server2.example.com:
  children:
    webservers:
      hosts:
        server1.example.com:
        server2.example.com:
    dbservers:
      hosts:
        db1.example.com:
```
Utilisation : `ansible-playbook -i inventory.yml mon_playbook.yml`

### 3. Structure d'un Playbook

```yaml
---
- name: Nom descriptif du play
  hosts: groupe_de_serveurs # ou 'all' pour tous les hôtes
  become: yes # Pour exécuter les tâches en tant que root (sudo/su)
  vars: # Variables locales au play
    ma_variable: valeur
    autre_variable: "{{ lookup('env', 'SOME_ENV_VAR') }}" # Exemple avec lookup plugin

  tasks:
    - name: Description de la tâche spécifique (ex: Installer Nginx)
      ansible.builtin.package: # Module pour gérer les paquets (apt, yum, dnf, etc.)
        name: nginx
        state: present
      tags: install # Permet d'exécuter/sauter cette tâche avec --tags/--skip-tags

    - name: Copier un fichier de configuration
      ansible.builtin.copy:
        src: files/nginx.conf # Chemin relatif depuis le playbook
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'
      notify: Redémarrer Nginx # Déclenche le handler si le fichier change

    - name: Configurer un template Jinja2
      ansible.builtin.template:
        src: templates/index.html.j2
        dest: /var/www/html/index.html
      notify: Redémarrer Nginx

    - name: Exécuter une commande shell simple
      ansible.builtin.shell: echo "Hello from {{ ansible_hostname }}" > /tmp/hello.txt
      args:
        creates: /tmp/hello.txt # N'exécute la commande que si le fichier n'existe pas

  handlers: # Les handlers ne s'exécutent qu'une fois par play, même s'ils sont notifiés plusieurs fois
    - name: Redémarrer Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```
*   **Handlers :** Ce sont des tâches spéciales qui ne s'exécutent que si elles sont "notifiées" par une autre tâche (souvent pour redémarrer un service après un changement de configuration).

### 4. Variables - Hiérarchie et priorité

Ansible a une hiérarchie de variables complexe mais très utile. Les variables définies à un niveau plus spécifique ont priorité sur celles définies à un niveau plus général.

-   **Extra Vars (`-e "key=value"`)** : La priorité la plus élevée.
-   **Lookup Plugins** (ex: `{{ lookup('env', 'VAR') }}`)
-   **Inventory vars** (host_vars, group_vars)
-   **Role vars**
-   **Play vars**
-   **Facts** (informations collectées sur les hôtes)
-   **Defaults** (définies dans les rôles)

### 5. Collecte de faits (Fact Gathering)

Par défaut, Ansible collecte des informations détaillées sur chaque hôte (OS, IP, mémoire, etc.). Ces "faits" sont stockés dans des variables préfixées par `ansible_` et sont utilisables dans vos playbooks.

Désactiver la collecte de faits (si non nécessaire) pour accélérer le playbook :
```yaml
---
- name: Play sans collecte de faits
  hosts: all
  gather_facts: no # Désactive la collecte de faits
  tasks:
    - name: Afficher un fait spécifique (si gather_facts était activé)
      ansible.builtin.debug:
        msg: "Le système d'exploitation est {{ ansible_facts['distribution'] }}"
```

### 6. Modules Courants supplémentaires

-   **`ansible.builtin.file`** : Gérer les fichiers et répertoires (créer, supprimer, changer les permissions).
    ```yaml
    - name: Créer un répertoire
      ansible.builtin.file:
        path: /opt/mon_app
        state: directory
        mode: '0755'
    ```
-   **`ansible.builtin.lineinfile`** : Insérer, modifier ou supprimer une ligne dans un fichier.
    ```yaml
    - name: Ajouter une ligne au fichier de configuration
      ansible.builtin.lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?Port'
        line: 'Port 2222'
        state: present
    ```
-   **`ansible.builtin.systemd`** ou **`ansible.builtin.service`** : Gérer les services système.
    ```yaml
    - name: S'assurer que le service foo est lancé et activé au démarrage
      ansible.builtin.systemd:
        name: foo
        state: started
        enabled: yes
    ```
-   **`ansible.builtin.cron`** : Gérer les tâches planifiées.
    ```yaml
    - name: Ajouter une tâche cron
      ansible.builtin.cron:
        name: "nettoyer_logs"
        minute: "0"
        hour: "3"
        job: "/usr/local/bin/nettoyage_script.sh"
    ```
-   **`ansible.builtin.debug`** : Afficher des messages ou des variables pendant l'exécution.
    ```yaml
    - name: Afficher le contenu d'une variable
      ansible.builtin.debug:
        var: ma_variable
    ```

### 7. Boucles (Loops)

Utilisez `loop` pour répéter des tâches.
```yaml
- name: Installer plusieurs paquets
  ansible.builtin.package:
    name: "{{ item }}"
    state: present
  loop:
    - apache2
    - mariadb-server
    - php
```

---

## Liens pour aller plus loin

- **[Documentation officielle d'Ansible](https://docs.ansible.com/) :** Complète, claire et indispensable.
- **[Tutoriels Red Hat Ansible](https://www.ansible.com/resources/get-started) :** Des guides pas à pas pour débuter.
- **[Ansible 101 with Jeff Geerling (YouTube)](https://www.youtube.com/playlist?list=PL2_OBreMn7FqZkvMYt6ATmgC0KAGGz73X) :** Une excellente série de vidéos pour apprendre Ansible.

Ansible est un outil incroyablement puissant qui peut vous faire gagner un temps précieux. Une fois que vous aurez commencé à automatiser, vous ne pourrez plus vous en passer !

<!-- Placeholder pour un logo Ansible -->
![Ansible Logo](assets/ansible_logo.svg)