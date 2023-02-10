# Exercice 22 - Ansible  - Playbook

Dans cet exercice, vous allez utiliser Ansible sur des machines distantes avec des playbook.

 Voici les tâches à réaliser dans cet exercice :
 
  - Créer un dossier *webapp* qui va contenir tous les fichiers du projet.
  - Créer un dépôt git pour le projet et le placer sur GitHub.
  - Créer un fichier d'inventaire pour le projet 
  - Créer un groupe *prod* dans votre fichier d'inventaire. 
  - Créer un fichier group_vars qui va contenir un fichier nommé *prod* qui contiendra les informations de connexion à utiliser par Ansible (Login et mot de passe)
  - Créez un playbook nommé deploy.yaml permettant de déployer apache à l'aide de Docker sur le client (l'image à utiliser est httpd et le port à exposer à l'extérieur est le 80)
  - Vous devez installer tous les prérequis à l'aide du module apt.
  - Vérifier la syntaxe du playbook avec la commande *ansible-lint* 
  - Vérifier qu'après l'exécution de votre playbook le site par défaut d’apache est bien disponible sur le port 80
  - Extraire le mot de passe.
  - Explorez les options de debug d’Ansible
  - Afin de conserver votre travail, poussez sur votre Github en mode privé. 
  - Ajouter le professeur à votre dépôt github.

## 1- Créer un dossier webapp qui va contenir tous les fichiers du projet.
Créez le dossier sous l'utilisateur deploy. Vous devez également y copier le fichier <code>ansible.cfg</code>.

```bash
su deploy
cd
mkdir webapp
cd webapp/
```

## 2- Suivre son code source sur GitHub

```bash
echo "# Webaap-ansible-apache" >>README.md
git config --global user.email "your@exemple.com" #si pas déjà définit.
git config --global user.name "Votre nom" #si pas déjà définit.
git init
git add *
git commit -m "Intialisation de mon dépôt"
# Créez le projet sur Github.com avec votre navigateur
git remote add origin git@github.com:[votrecompte git hub]/webapp-ansible-apache.git
git push -u origin master
```

Créez le projet sur Github.com, en ne mettant PAS de Readme. Il vaut mieux l’ajouter après, une fois que les fichiers ont été uploader, pour éviter tout conflit.

git remote add origin 

Le lien copié va désigner le répertoire distant comme cible du projet.


## 3- Créer un fichier inventaire.yaml 

Créer un fichier <code>inventaire.yaml</code> pour le projet webapp.yaml avec un groupe prod : 

```yaml
---
all:
  vars:
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
  hosts:
    control:
      ansible_connection: local
prod:
  hosts:
    srv-apache-1:
       ansible_host: 10.0.1.4 # A remplacer par l'IP de votre client
```

Modifiez votre fichier <code>ansible.cfg</code> pour qu'il tienne compte du nouveau fichier d'inventaire.

## 4- Création des group_vars:
Nous allons utiliser des variables de groupe défini dans un répertoire nommer <code>group_vars</code>.  
Le fichier <code>hosts</code> ou d'inventaire et le répertoire <code>group_vars</code> sont utilisés pour définir des variables pour les groupes d'hôtes et déployer lors des lectures/tâches Ansible sur chaque hôte/groupe. Les fichiers sous le répertoire <code>group_var</code> sont nommés d'après le nom du groupe d'hôtes ou all (pour tous), en conséquence, les variables seront affectées à ce groupe d'hôtes ou à tous les hôtes.

Créer un répertoire group_vars qui va contenir un fichier nommé <code>prod.yaml</code> qui contiendra les informations de connexion à utiliser par Ansible (Login et mot de passe)

```Bash
mkdir group_vars
vi group_vars/prod.yaml
```

Contenus du fichier prod.yaml

```yaml
---
all: 
  ansible_user: deploy
  ansible_password: votreMotDePasse
```

## 5- Créez un playbook nommé deploy.yaml

Un playbook Ansible est un fichier YAML contenant un ou plusieurs plays. Chaque play est un ensemble de tâches.  

- Un **play** est un ensemble de tâches correspondant à un appareil ou un groupe d'appareils.
- Une **tâche** est une action unique qui fait référence à un **module** à exécuter avec tous les arguments et actions en entrée. Ces tâches peuvent être simples ou complexes selon le besoin d'autorisations, l'ordre d'exécution des tâches, etc.

Un playbook peut également contenir des **rôles**. Un rôle est un mécanisme permettant de diviser un playbook en plusieurs composants ou fichiers, de simplifier le playbook et de le rendre plus facile à réutiliser. Par exemple, le rôle **commun** est utilisé pour stocker les tâches qui peuvent être utilisées dans tous vos playbooks.

Le playbook Ansible YAML comprend des **objets**, des **listes** et des **modules**.
 
- Un objet YAML est une ou plusieurs paires de valeurs clés. Les paires de valeurs clés sont séparées par un deux-points sans l'utilisation de guillemets, par exemple  <code>hosts: srv-apache-1</code>.
-  Un objet peut contenir d'autres objets tels qu'une liste. YAML utilise des listes ou des tableaux. Un tirait "-" est utilisé pour chaque élément de la liste.
-  Ansible est livré avec un certain nombre de modules (appelés bibliothèque de modules) qui peuvent être exécutés directement sur des hôtes distants ou via des playbooks. Par exemple, le module <code>ping</code> utilisé pour vérifier la connectivité. Chaque tâche se compose généralement d'un ou de plusieurs modules Ansible.

Vous exécutez un playbook Ansible à l'aide de la commande <code>ansible-playbook</code>, par exemple :

```bash
ansible-playbook mon_playbook.yaml -i inventaire.yaml
```

La commande <code>ansible-playbook</code> utilise des paramètres pour spécifier :  
- Le playbook que vous voulez exécuter (mon_playbook.yaml)
- Le fichier d'inventaire et son emplacement (-i hosts). Ce paramètre est nécessaire si vous n'avez pas de fichier <code>ansible.cfg</code> qui change son emplacement par défaut.


Créez un playbook nommé <code>deploy.yaml</code> permettant de déployer Apache à l'aide de Docker sur le client (l'image à utiliser est httpd et le port à exposer à l'extérieur est le 80)

```Bash
vim deploy.yaml
```

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  tasks:
    - name: Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
          - "80:80"
```

## 6- Vérifier la syntaxe du playbook
Pour avoir l'outil de vérification de la syntaxe, nous aurons besoin d'ansible-lint qui s'installe avec l'installateur de package pour Python PIP. Voici les étapes :

```bash
sudo apt install python3-pip
sudo pip install ansible-lint
# Finalement, nous pouvons vérifier le fichier: 
ansible-lint deploy.yaml
```
Lorsqu'il n'y a pas d'erreur, exécutez le playbook

>[Attention]
  J'ai eu constamment une erreur. J'ai dû faire une nouvelle installation d’Ansible en allant chercher la version chez Ansible plutôt que celle d'Ubuntu. Voici les commandes :

<details>

```
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:Ansible/Ansible
sudo apt install Ansible
```

</details>


## 7- Lancer le playbook

```
ansible-playbook -i inventaire.yaml deploy.yaml
```

**Attention :** le paramètre <code>-i inventaire.yaml</code> est optionel si le fichier d'inventaire est déjà défini dans le fichier <code>ansible.cfg</code>.

Il devrait avoir une erreur d'absence du module Docker (l'erreur peut être différente):

![Absence module Docker](img/ErrModDocker.png)

On va ajouter le module manquant directement dans le playbook, c'est sa raison d'être après tout.

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  pre_tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution du playbook

```
ansible-playbook -i inventaire.yaml deploy.yaml
```

Nouvelle erreur: Permision  denied

Nous n'avons pas les droits. Le compte deploy n'est pas suffisant. Il faut une élévation de privilège. Avec l'ajout de <code>become: true</code>

```yaml
---
- name: Apache installation avec Docker
  hosts: prod
  become: true  
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution de playbook

```
ansible-playbook deploy.yaml
```

Cette fois, "sudo: il est nécessaire de saisir un mot de passe". 
Nous allons y aller pour la façon la plus  simple bien sure, la moins sécuritaire :

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  become: true
  vars:
    ansible_sudo_pass: MotDePasse
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Exécution de playbook

```
ansible-playbook deploy.yaml
```

Résultat attendu : 

![Le playbook fonctionne](img/fonctionne.png)

Vérifions dans le navigateur :

![La page est là!](img/ItWork.jpg)


Et aussi sur la machine srv-apache-1 : 

![Cmd Docker ps](img/dockerps.jpg)


## 8- Sortir le mot de passe du playbook
Enlevez les objets (entrées) <code>vars</code> et <code>ansible_sudo_pass</code> de votre fichier <code>deploy.yml</code>.

Modifiez le fichier <code>ansible.cfg</code> comme suit :

```bash
vim ansible.cfg

# Ajoutons le paramètre nécessaire:
[privilege_escalation]
become_ask_pass=true
```

Exécution de playbook

```bash
ansible-playbook deploy.yaml
```

Le mot de passe est demandé.

## 9- Ansible Vault pour plus de sécurité
Essentiellement, Vault est un moyen pour garder secrètes les informations sensibles
de votre configuration Ansible. Il vous permet de chiffrer vos fichiers plutôt que d'avoir du texte brut dans vos playbooks.  

Nous pouvons essentiellement exécuter la commande <code>ansible-vault</code> pour chiffrer n'importe quel fichier. Pour l'instant, la plupart des fichiers que nous allons chiffrer seront des fichiers variables, car ils contiendront peut-être des mots de passe ou des clés sensibles.
Ces fichiers peuvent être partagés via un outil de contrôle de sources comme GIT, en gardant les mots de passe et les clés sensibles hors du contrôle de sources.  

Tous ces fichiers sont protégés par un mot de passe et le chiffrement par défaut est AES.

Ajoutons un répertoire <code>vars</code> :

```bash
mkdir vars
```

Nous allons créer notre fichier de variables sensibles :

```bash
ansible-vault create vars/secret-variables.yaml
```

Entrez un mot de passe pour protéger le fichier. Le fichier va s'ouvrir dans l'éditeur par défaut.

Entrez le mot de passe de l'utilisateur deploy (pour l'utilisation de la commande sudo) :

```yaml
# À ajouter dans le fichier
ansible_sudo_pass: "MotDePasse"
```

Pour éditer à nouveau le fichier, vous utilisez la commande <code>ansible-vault edit NomFichier</code>.
Vous pouvez vérifier que le fichier est bien chiffré :

```bash
cat -v vars/secret-variables.yaml
```

Enlevez les lignes pour <code>[privilege_escalation]</code> du fichier <code>ansible.cfg</code>.

Modifiez le fichier <code>deploy.yaml</code> pour ajouter le fichier contenant la variable chiffrer :

```yaml
---
- name: "Apache installation avec Docker"
  hosts: prod
  become: true
  vars_files:
    - ./vars/secret-variables.yaml
  pre_task:
   - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: yes
  tasks:
    - name : Create Apache container
      community.docker.docker_container:
        name: webapp
        image: httpd
        ports:
            - "80:80"
```

Maintenant, on doit ajouter le paramètre <code>--ask-vault-pass</code> au lancement de notre playbook :

```bash
ansible-playbook -i inventaire.yaml deploy.yaml --ask-vault-pass
```

Le mot de passe pour ouvrir le fichier <code>secret-variables.yaml</code> est demandé.

![L'exécution avec Vault.](img/Vault.png)

# Remise

Placer des captures des commandes suivantes dans un seul fichier et déposer le sur LÉA dans travaux exercice 22. 

>[!Astuce] Utiser un terminal pour les deux commande cat et un autre pour l'exécution de playbook et ce, côte à côte.

```bash
cat inventaire.yaml
cat deploy.yaml
ansible-playbook -i inventaire.yaml deploy.yaml --ask-vaut-pass
```

## Références
[Documentation ansible pour group_vars](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#organizing-host-and-group-variables)