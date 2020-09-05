Ansible est un outil d'automatisation. Il permet d'automatiser des choses que les developpeurs ou les admins font manuellement (en général). 

Ansible peut automatiser des étapes de manière à ce que ces étapes soient facilement lisibles et maintenables par les autres.

Par exemple: des configurations de firewall, déploiement d'applications...

Par la suite, nous allons voir les étapes suivantes:
- Pourquoi choisir Ansible? 
- Qu'est-ce que c'est que les `Ansible playbook`?
- Comment configurer des serveurs et sécuriser des données?
- Comment implémenter des SCM (source control management) comme git? (par exemple: afin d'avoir votre propre serveur comme github)?
- Comment deboguer des problèmes sur Ansible?
- Les modules Ansible.
- En tant que projet de fin, nous allons faire le déploiement d'une application web en Ansible

# 1. Pourquoi Ansible?
La première raison est parce que c'est le plus facile de tous les outils d'automatisation.
Il existe d'autres comme `Puppet`, `Chef`...

La deuxième raison est que c'est très maintenable: On peut lire les playbooks quelques années après et les comprendre tout de suite.

Le code source d'ansible peut se trouver sur github (https://github.com/ansible/ansible)

Si vous regardez dans ce repository de code, le plus important à voir est `lib/ansible` (en particulier le dossier `module`) car ce sont les librairies qui est plus important puisque ce sont eux qui font le travail.

Egalement, la doc officielle se trouve dans `https://docs.ansible.com/`. La partie la plus importante pour nous est la partie `Getting started`. On y trouve des guides d'installation, des guides d'utilisations, des exemples et plein de guides sur d'autres aspects de ansible (playbook, ...). Et donc en particulier ce qui nous interesserait le plus est la doc sur les modules (https://docs.ansible.com/ansible/latest/modules/modules_by_category.html) pour toutes les choses que nous devons implémenter (ex: pour les automatisations des actions sur les fichiers, vous pourrez voir le module `file`). Ces documents sont fournies avec des exemples.

Un autre document qui nous interesse également est https://www.fullstackpython.com/table-of-contents.html.  On y trouve un peu de tout.

Les chapitres qui devraient nous intéréssés le plus, sont les chapitre 5 (Deploiements d'application web) et chapitre 6 (Devops).

# 2. Installation de l'environnement

Pour utiliser Ansible, il faut installer le paquet `python3-venv` (virtual environement pour python3).
Cela nous permet d'utiliser un environnement virtuel et ne pas casser notre machine hôte. En effet, python a plusieurs versions de plusieurs librairies et virtualenv nous permet d'avoir des versions pour un environnement donné.

- vérifier qu'on a `python3`

```js
patou@pa-linux:~$ python3 --version
Python 3.7.3
```
- Pour chercher le paquet virtualenv, on va taper la commande suivante.
On peut l'installer en utilisant `pip3` ou en utilisant `apt-get install`. 
Nous allons utiliser `apt-get` pour cette installation.
```bash
patou@pa-linux:~/Documents/bizna/pasFini/Ansible/venvs$ aptitude search venv
p   apt-venv                                                           - apt virtual environment                                                     
p   elpa-pyvenv                                                        - Python virtual environment interface                                        
p   python3-venv                                                       - pyvenv-3 binary for python3 (default python3 version)                       
v   python3-venv:any                                                   -                                                                             
i A python3.7-venv                                                     - Interactive high-level object-oriented language (pyvenv binary, version 3.7)
v   python3.7-venv:any                                                 - 
```
- Nous allons installer alors le paquet:

```sh
patou@pa-linux:~/Documents/bizna/pasFini/Ansible/venvs$ sudo apt-get install python3-venv
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances       
Lecture des informations d'état... Fait
Les paquets supplémentaires suivants seront installés : 
  python3.7-venv
Les NOUVEAUX paquets suivants seront installés :
  python3-venv python3.7-venv
0 mis à jour, 2 nouvellement installés, 0 à enlever et 14 non mis à jour.
Il est nécessaire de prendre 7 328 o dans les archives.
Après cette opération, 44,0 ko d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] o
Réception de :1 http://ftp.fr.debian.org/debian buster/main amd64 python3.7-venv amd64 3.7.3-2+deb10u2 [6 148 B]
Réception de :2 http://ftp.fr.debian.org/debian buster/main amd64 python3-venv amd64 3.7.3-1 [1 180 B]
7 328 o réceptionnés en 0s (23,3 ko/s)       
Sélection du paquet python3.7-venv précédemment désélectionné.
(Lecture de la base de données... 310023 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../python3.7-venv_3.7.3-2+deb10u2_amd64.deb ...
Dépaquetage de python3.7-venv (3.7.3-2+deb10u2) ...
Sélection du paquet python3-venv précédemment désélectionné.
Préparation du dépaquetage de .../python3-venv_3.7.3-1_amd64.deb ...
Dépaquetage de python3-venv (3.7.3-1) ...
Paramétrage de python3.7-venv (3.7.3-2+deb10u2) ...
Paramétrage de python3-venv (3.7.3-1) ...
Traitement des actions différées (« triggers ») pour man-db (2.8.5-2) ...
Scanning processes...                                                                                                                                
Scanning linux images...                                                                                                                             

Running kernel seems to be up-to-date.

Failed to check for processor microcode upgrades.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

```
- testons alors l'installation de virtualenv.

```sh
patou@pa-linux:~$ python3 -m venv
usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT]
            ENV_DIR [ENV_DIR ...]
venv: error: the following arguments are required: ENV_DIR

```
Cette commande nous permet de savoir que `venv` est donc disponible.

- Pour créer un environnement virtuel `Python3`, nous devons avoir un répertoire pour mettre toute l'installation de l'environnement virtuel. 
Pour ma part, Je vais créer un répertoire `venvs` dans `/home/patou/Documents/bizna/pasFini/demoAnsible/venvs/` et je vais entrer dedans en ligne de commande et créer mon environnement à partir de ce répertoire. 

On va nommer notre nouvel environnement virtuel `introansible`

```bash
patou@pa-linux:~$ cd /home/patou/Documents/bizna/pasFini/Ansible/venvs/
patou@pa-linux:~/Documents/bizna/pasFini/Ansible/venvs$ python3 -m venv introansible
```
Si aucune réponse, c'est que tout s'est bien passé.

- Pour lancer mon environnement virtuel, il faut sourcer un script `bin/activate` dans le répertoire de l'environnement (rappel sur le terme `sourcer un script` https://forums.commentcamarche.net/forum/affich-19783797-lancer-un-script-vs-sourcer-un-script). 

Pour cela, on va taper la commande suivante dans la ligne de commande.

```sh
patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ source introansible/bin/activate
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ 
```
Quand le script est bien sourcé, nous sommes alors dans l'environnement virtuel. Cela est signalé par le fait que nous avons le nom de notre environnement virtuel entre parenthèse devant la ligne de commande.

On peut alors tout installer dans l'environnement virtuel et il sera impacté par les changements mais notre machine hôte (la machine non virtuelle) ne sera pas impacté. Cela signifie que si nous installons `Ansible` dans la machine virtuelle, la machine hôte n'aura pas `Ansible` car on l'a installé dans l'environnement virtuel.
 
- Installation de `Ansible` dans l'environnement virtuel.

```js
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ pip install ansible 
Collecting ansible
  Downloading https://files.pythonhosted.org/packages/32/62/eec759cd8ac89a866df1aba91abf785486fed7774188a41f42f5c7326dcb/ansible-2.9.13.tar.gz (14.3MB)
    100% |████████████████████████████████| 14.3MB 107kB/s 
Collecting PyYAML (from ansible)
  Downloading https://files.pythonhosted.org/packages/64/c2/b80047c7ac2478f9501676c988a5411ed5572f35d1beff9cae07d321512c/PyYAML-5.3.1.tar.gz (269kB)
    100% |████████████████████████████████| 276kB 2.1MB/s 
Collecting cryptography (from ansible)
  Downloading https://files.pythonhosted.org/packages/43/2e/8d2de0d73d177184bc9a15137cd9aae46c1b3a59842b5fde30c8b80a5b4e/cryptography-3.1-cp35-abi3-manylinux1_x86_64.whl (2.6MB)
    100% |████████████████████████████████| 2.6MB 557kB/s 
Collecting jinja2 (from ansible)
  Using cached https://files.pythonhosted.org/packages/30/9e/f663a2aa66a09d838042ae1a2c5659828bb9b41ea3a6efa20a20fd92b121/Jinja2-2.11.2-py2.py3-none-any.whl
Collecting six>=1.4.1 (from cryptography->ansible)
  Downloading https://files.pythonhosted.org/packages/ee/ff/48bde5c0f013094d729fe4b0316ba2a24774b3ff1c52d924a8a4cb04078a/six-1.15.0-py2.py3-none-any.whl
Collecting cffi!=1.11.3,>=1.8 (from cryptography->ansible)
  Downloading https://files.pythonhosted.org/packages/ad/82/cc3e38b7859e1426d7c004503962074317f3ccbd46adbef5b8094c9688c3/cffi-1.14.2-cp37-cp37m-manylinux1_x86_64.whl (401kB)
    100% |████████████████████████████████| 409kB 1.6MB/s 
Collecting MarkupSafe>=0.23 (from jinja2->ansible)
  Downloading https://files.pythonhosted.org/packages/98/7b/ff284bd8c80654e471b769062a9b43cc5d03e7a615048d96f4619df8d420/MarkupSafe-1.1.1-cp37-cp37m-manylinux1_x86_64.whl
Collecting pycparser (from cffi!=1.11.3,>=1.8->cryptography->ansible)
  Downloading https://files.pythonhosted.org/packages/ae/e7/d9c3a176ca4b02024debf82342dab36efadfc5776f9c8db077e8f6e71821/pycparser-2.20-py2.py3-none-any.whl (112kB)
    100% |████████████████████████████████| 112kB 2.0MB/s 
Building wheels for collected packages: ansible, PyYAML
  Running setup.py bdist_wheel for ansible ... error
  Complete output from command /home/patou/Documents/bizna/pasFini/Ansible/venvs/introansible/bin/python3 -u -c "import setuptools, tokenize;__file__='/tmp/pip-install-kpp2q4kg/ansible/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" bdist_wheel -d /tmp/pip-wheel-s9xjn_ef --python-tag cp37:
  usage: -c [global_opts] cmd1 [cmd1_opts] [cmd2 [cmd2_opts] ...]
     or: -c --help [cmd1 cmd2 ...]
     or: -c --help-commands
     or: -c cmd --help
  
  error: invalid command 'bdist_wheel'
  
  ----------------------------------------
  Failed building wheel for ansible
  Running setup.py clean for ansible
  Running setup.py bdist_wheel for PyYAML ... error
  Complete output from command /home/patou/Documents/bizna/pasFini/Ansible/venvs/introansible/bin/python3 -u -c "import setuptools, tokenize;__file__='/tmp/pip-install-kpp2q4kg/PyYAML/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" bdist_wheel -d /tmp/pip-wheel-h6lormrk --python-tag cp37:
  usage: -c [global_opts] cmd1 [cmd1_opts] [cmd2 [cmd2_opts] ...]
     or: -c --help [cmd1 cmd2 ...]
     or: -c --help-commands
     or: -c cmd --help
  
  error: invalid command 'bdist_wheel'
  
  ----------------------------------------
  Failed building wheel for PyYAML
  Running setup.py clean for PyYAML
Failed to build ansible PyYAML
Installing collected packages: PyYAML, six, pycparser, cffi, cryptography, MarkupSafe, jinja2, ansible
  Running setup.py install for PyYAML ... done
  Running setup.py install for ansible ... done
Successfully installed MarkupSafe-1.1.1 PyYAML-5.3.1 ansible-2.9.13 cffi-1.14.2 cryptography-3.1 jinja2-2.11.2 pycparser-2.20 six-1.15.0


```
Dès qu'on voit `Successfully installed`,  cela signifie que `ansible` est maintenant installé.
Pour sortir du virtualenv, on tape à la console `deactivate`.


Nous allons maintenant tester ansible. Pour ce test, nous allons utiliser ce qu'on appelle une commande `ad-hoc` (en langue française "ad-hoc" signifie qui convient parfaitement à une situation - https://www.linternaute.fr/dictionnaire/fr/definition/ad-hoc/). 

Une introduction aux commandes ad-hoc se trouve dans la doc officielle (https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)

Voici la syntax d'une commande ad-hoc 
```bash
$ ansible [pattern] -m [module] -a "[module options]"
```
[pattern] corresponds à la machine ou au groupe de machine sur lesquels voudriez lancer la commande (ansible permet de lancer des commandes sur plusieurs machines également)
[module] est le module. 

Sur le site ci-dessus, permet de voir plusieurs exemples.

Pour tester, nous allons automatiser l'affichage d'un caractère dans `localhost`.
Pour afficher en ligne de commande, on utilise la commande `echo`
Par exemple : 
![](img/echo_ansible.png)

Maintenant, nous allons utiliser  ansible pour afficher une chaîne de caractère à partir de notre environnement virtuel (commande lancé dans l'environnement virtuel vers localhost (la machine hôte)).

La commande est la suivante:
```bash
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ansible localhost -a "echo 'Hello world'"
```

remarquez que les guillements (`"` ont été remplacés par des quotes `'`)

La réponse attendue est la suivante :

```bash
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | CHANGED | rc=0 >>
Hello world
```
Il y a des warnings mais le message est affiché.

Cela nous permet de voir que `ansible` est correctement installé.

# 3. Installation d'une clé ssh pour l'authentification

Une première information sur ssh se trouve sur wikipedia (https://fr.wikipedia.org/wiki/Secure_Shell).

Pour se connecter à d'autres ordinateurs, on peut utiliser le protocole SSH (Secure Shell)

Dans notre cas, nous allons simuler la connexion à plusieurs ordinateurs pour automatiser des tâches dessus. Pour l'authentification, nous utiliserons SSH.

Pour commencer, nous allons installer une clé ssh. Nous allons utiliser la commande `ssh-keygen` pour générer une clé rsa (https://fr.wikipedia.org/wiki/Chiffrement_RSA)

Un petit tuto sur `ssh-keygen` se trouve ici (à voir quand on en a besoin https://www.ssh.com/ssh/keygen/)
Nous allons utiliser l'option `-t` pour choisir un algorithme (les algos valides sont : rsa, dsa, ecdsa etc etc ...), et l'option `-b` pour choisir la taille de la clé à générer et `-C` pour rajouter un commentaire.

Quand vous executerez la commande, elle demandera où le fichier de clé sera généré (car la clé sera rangé dans un fichier). Le nom de cette clé est par défaut `id_rsa`. Mais nous allons plutôt l'enregistrer `introansible` (taper le chemin complet `/home/patou/.ssh/introansible`). 

Ne pas spécifier de passphrase dans la deuxième étape. Il faut juste valider quand il demande le passphrase.

Remplacer le mail par le votre.




La commande pour générer notre clé sera donc : 

```bash
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ssh-keygen -t rsa -b 4096 -C xchess64@gmail.com
Generating public/private rsa key pair.
Enter file in which to save the key (/home/patou/.ssh/id_rsa): /home/patou/.ssh/introansible
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/patou/.ssh/introansible.
Your public key has been saved in /home/patou/.ssh/introansible.pub.
The key fingerprint is:
SHA256:vax5QhSDvNZwO3oTMqdCprnsLfUL5mi4BSNHHJ07Bo8 xchess64@gmail.com
The key's randomart image is:
+---[RSA 4096]----+
|  .. o .         |
| ...o + +        |
|  o+ . = +       |
| .E B = B.       |
|+ .* o BSo.      |
|.+o o o +. .     |
| o.ooo o .o      |
|..== .. .o.      |
|.+o.o ..oo       |
+----[SHA256]-----+

```

A ce stade, nous avons généré notre clé ssh.
Nous allons maintenant vérifier que la clé est bien generé.

```bash 
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ls /home/patou/.ssh
introansible  introansible.pub  known_hosts

```
On a les deux clés `privée` et `publique`. 
- la clé privée est : `introansible`. Cette clé doit être caché et ne doit pas être divulgué.
- la clé publique est : `introansible.pub`. Cette clé peut être divulgué et même mis sur les serveurs distants qu'on voudrait accéder en ssh.
- Le fichier `known-host` est un fichier texte qui contient la clé publique de tous les ordinateurs sur lesquels votre ordinateur s'est déjà connecté. Donc si vous n'avez jamais utilisé ssh, c'est normal de ne pas avoir ce fichier.

Maintenant que nous avons notre clé ssh, nous allons commencer à travailler vraiment sur Ansible.


# 4. Concepts ansibles à comprendre
Il existe quelques termes et concepts que nous devons apprendre et comprendre avant d'avancer dans l'utilisation d'Ansible. Cela nous permettra de bien comprendre ce qu'on fait et les termes qu'on utilise.

- les modules: Ce sont des libraries qui nous permettent d'executer des actions spécifiques (sur des fichiers, sur git, sur des bases de données, service ...). Les noms des modules dans Ansibles ont des noms familiers, c'est-à-dire que si vous travaillez avec git, le module Ansible responsable des opérations sur git s'appelera git également.
![](img/module.png)

- Les tasks: Ce sont des codes, qu'on a écrit pour executer une action et ces tasks, en général, utilisent des mdoules.
Ex: Si on souhaite spécifier un répository git où on souhaite pousser notre code, on peut ecrire un task ansible pour le faire.
![](img/tasks.png)

- Les roles: Comme on peut avoir des dizaines ou des centaines de task, il nous faut également des manières d'organiser ou de grouper les tasks. Les rôles nous permettent de définir quels tâches doivent être appliqués à quels type de serveurs. Les rôles sont appliqués à chaque serveurs. On peut par exemple définir une configuration de sécurité propre à chaque serveur, dans ce cas, on définirait un role pour cela. 
![](img/role_playbook.png)

- Les playbooks: Un playbook est une manière d'organiser des roles et de les regrouper. On peut également lancer une tâche `ad-hoc` dans Ansible, comme nous l'avons déjà vu, mais les actions les tasks les plus complexes sont mis dans des playbook.

Ce qui nous manque maintenant est comment nous allons addresser les différentes machines. On n'adressera pas par adresse IP ou nom de hote car ce ne serait pas réutilisable (il faut changer d'adresse dès qu'on souhaite changer de machine). Mais nous avons un autre concept pour cela. C'est l'Inventaire (Inventory).

- Inventory: L'inventory associe un role à une adresse IP ou un nom de domaine. Cela nous permet de définir ce qui doit être executé dans les role et à quel endroit les executer dans les inventaires.
![](img/inventory.png)

- Le dernier concept qu'on doit connaitre est YAML: C'est al langage qui nous permettra d'écrire des tasks. C'est le même langage que ce qu'on a utiliser pour ecrire des fichier `docker-compose.yml`. 

Nous avons donc vu 6 concepts fondamentaux d'ansible:
- `Modules`: le code qui fait partie d'Ansible
- `Tasks`: pour décrire quels modules on souhaite utiliser et comment on veut les utiliser
- `Roles`: qui nous permet de grouper des tasks ensembles selon ce qu'ils font
- `Playbooks` qui sont des ensembles de roles que nous allons executer.
- `Inventory` ou Inventaire qui nous permet de cibler sur quelles machines, on souhaite lancer un playbook donné
- `YAML` qui est le langage qui nous permet d'ecrire des tasks et des fichiers d'inventaire.

Nous allons les voir un à un.

## 4.1 Modules
Les modules sont des codes qui sont fournies par Ansible (en python, en ruby ou en Java)

Ce code permet de faire une action spécifique et claire comme par exemple: 
- cloner un dépot git
- Activer le firewall d'un OS
- envoyer une notification (email par ex)

En général, il existe des modules pour faire toute action auquel on peut penser. Ainsi, le site officiel a classé les modules par catégories.

Et on les voit ici (https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)

Ainsi, on a évoqué un module qui permet d'envoyer des notifications et dans cette liste on a bien une catégorie `Notification` (https://docs.ansible.com/ansible/latest/modules/list_of_notification_modules.html)

Et si vous êtes assez ambitieux pour cliquer sur `All modules`, vous trouverez des centaines de modules.

Les modules sont appelés quand on écrit des tasks.

## 4.2 Tasks 

Les tâches (ou tasks) sont les éélments qui nous permettent d'effectuer ce que nous souhaitons faire sous Ansible.

Les tasks sont écrits en YAML, ils invoquent/appellent des modules pour effectuer des actions.

Voici un exemple de task écrit en YAML pour installer ltrace

```yml
- name: s'assurer que ltrace est installe
  apt: name=ltrace state=present update_cache=yes
  become: true
```

`name` est une description de ce que fait la tache et c'est lisible par un humain. Dans notre cas, le task sert à s'assurer que Git est installé sur une machine.

La deuxième ligne contient `apt` (le module apt - donc on suppose qu'on est sur un système Debian ou Ubuntu) et on lui passe 3 arguments ( ̀`name` (nom du package qu'on souhaite vérifier), `state`(l'état du packet qu'on souhaite - ici on souhaite qu'il soit présent donc installé sinon on aurait mis `absent` donc desinstallé) et ̀`update_cache`(le cache est la liste des paquets dans l'OS et on veut le mettre à jour avant d'installer - comme si on faisait un `apt-get update`)).

La troisième ligne: `become:true` signifie qu'on souhaite utiliser les privilèges du superuser (comme si on faisait `sudo` avant de lancer la tâche)

### 4.2.1 Lancer une tâche en mode "ad-hoc"

Pour commencer nous allons nous assurer que le paque `ltrace` n'est pas installé sur notre machine.

```bash
patou@pa-linux:~$ aptitude search ltrace
p   babeltrace                                                         - Trace conversion program                                                    
p   libbabeltrace-ctf-dev                                              - Babeltrace development files (transitional package)                         
p   libbabeltrace-ctf1                                                 - Babeltrace conversion libraries (transitional package)                      
p   libbabeltrace-dev                                                  - Babeltrace development files                                                
p   libbabeltrace1                                                     - Babeltrace conversion libraries                                             
p   libdevel-calltrace-perl                                            - Code tracer to follow function calls                                        
p   ltrace                                                             - Tracks runtime library calls in dynamically linked programs                 
p   python3-babeltrace                                                 - Babeltrace Python 3 bindings                                                
```

et voit la ligne `p   ltrace       ` qui signifie que le paquet n'est pas installé. Si il était installé, on aurait pu avoir `i` à la place de `p`. 
De plus, si on tape `ltrace` dans la ligne de commande,on aurait:

```bash
patou@pa-linux:~$ ltrace
bash: ltrace : commande introuvable
```

Nous allons alors l'installer en utilisant `Ansible` 

- Entrer dans votre virtualenv. On rappele que pour cela, il faut entrer dans le répertoire où nous avons créé le virtualenv et sourcer le script `bin/activate` comme ci-dessous (si pas clair, revoir le s section 2)

```bash
patou@pa-linux:~$ cd ~/Documents/bizna/pasFini/demoAnsible/venvs/
patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ source introansible/bin/activate
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ 
```
Pour s'assurer que le virtualenv est en route, on a le nom de l'environnement entre parenthèse à gauche de la ligne de commande

Avant de lancer une tâche, nous allons récupérer les informations concernant la machine où on va installer `ltrace` ( dans notre cas, nous allons installer sur `localhost`).
Cette étape s'appelle `facts gathering` (ou rassemblement des informations)
La commande est la suivante:
```bash
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ansible localhost -m setup
```
La commande ne fera aucune action mais juste rassembler des informations concernant la machine `localhost`. Ces informations sont affichés en json en réponse à la commande.

Maintenant, nous allons construire la commande adhoc ansible  qui va se charger d'installer ltrace sur notre machine localhost.

Comment va-t-on créer notre commande `ansible`? 

Comme nous l'avons vu dans la doc donnée en section 2 (pour le mode adhoc - https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html), on va envoyer la commande `ansible` et : 
 - on va spécifier le module avec l'option `-m <nom_module>`. Ici notre module sera `apt` puisque qu'on fera de l'installation.
 - le deuxième options sera `-a` (pour adhoc command) et il sera suivi d'une chaine de caractère (entre `"`) qui sera les 3 arguments du module  `apt` (tel qu'il a été décrit dans la définition de notre task en paragraphe `4.1`). Donc ce sera `"name=ltrace state=present update_cache=yes"`

- Comme nous avons l'option `become:true`, donc dans la ligne de commande nous devons rajoute `-b`. C'est l'equivalent en mode ligne de commande `ansible` et nous allons également rajouter l'option `-K` pour s'assurer que anible va bien demander bien le mot de passe du superutilisateur avant de faire quoi que ce soit.

- Enfin, nous devons communiquer à ansible qu'il doit utiliser la version de python3 et pour cela, on lui renvoie une variable d'environnement spécial `ansible_python_interpreter` dont on la valeur est le chemin de python3 complet (donc `/usr/bin/python3`). Cela empêchera ansible d'utiliser n'importe quel python installé par défaut (ex: python2 )

Tête bien reposé, la commande à taper est donc la suitante. Il faut penser à fournir le mot de passe root de votre machine quand ansible le demandera. 


```bash
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ansible localhost -m apt -a "name=ltrace state=present update_cache=yes" -b -K -e ansible_python_interpreter=/usr/bin/python3
BECOME password: 
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | CHANGED => {
    "cache_update_time": 1599224228,
    "cache_updated": false,
    "changed": true,
    "stderr": "",
    "stderr_lines": [],
    "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following NEW packages will be installed:\n  ltrace\n0 upgraded, 1 newly installed, 0 to remove and 14 not upgraded.\nNeed to get 143 kB of archives.\nAfter this operation, 372 kB of additional disk space will be used.\nGet:1 http://ftp.fr.debian.org/debian buster/main amd64 ltrace amd64 0.7.3-6.1 [143 kB]\nFetched 143 kB in 0s (558 kB/s)\nSelecting previously unselected package ltrace.\r\n(Reading database ... \r(Reading database ... 5%\r(Reading database ... 10%\r(Reading database ... 15%\r(Reading database ... 20%\r(Reading database ... 25%\r(Reading database ... 30%\r(Reading database ... 35%\r(Reading database ... 40%\r(Reading database ... 45%\r(Reading database ... 50%\r(Reading database ... 55%\r(Reading database ... 60%\r(Reading database ... 65%\r(Reading database ... 70%\r(Reading database ... 75%\r(Reading database ... 80%\r(Reading database ... 85%\r(Reading database ... 90%\r(Reading database ... 95%\r(Reading database ... 100%\r(Reading database ... 310034 files and directories currently installed.)\r\nPreparing to unpack .../ltrace_0.7.3-6.1_amd64.deb ...\r\nUnpacking ltrace (0.7.3-6.1) ...\r\nSetting up ltrace (0.7.3-6.1) ...\r\nProcessing triggers for man-db (2.8.5-2) ...\r\n\nRunning kernel seems to be up-to-date.\n\nFailed to check for processor microcode upgrades.\n\nNo services need to be restarted.\n\nNo containers need to be restarted.\n\nUser sessions running outdated binaries:\n patou @ session #3: sh[4024]\n",
    "stdout_lines": [
        "Reading package lists...",
        "Building dependency tree...",
        "Reading state information...",
        "The following NEW packages will be installed:",
        "  ltrace",
        "0 upgraded, 1 newly installed, 0 to remove and 14 not upgraded.",
        "Need to get 143 kB of archives.",
        "After this operation, 372 kB of additional disk space will be used.",
        "Get:1 http://ftp.fr.debian.org/debian buster/main amd64 ltrace amd64 0.7.3-6.1 [143 kB]",
        "Fetched 143 kB in 0s (558 kB/s)",
        "Selecting previously unselected package ltrace.",
        "(Reading database ... ",
        "(Reading database ... 5%",
        "(Reading database ... 10%",
        "(Reading database ... 15%",
        "(Reading database ... 20%",
        "(Reading database ... 25%",
        "(Reading database ... 30%",
        "(Reading database ... 35%",
        "(Reading database ... 40%",
        "(Reading database ... 45%",
        "(Reading database ... 50%",
        "(Reading database ... 55%",
        "(Reading database ... 60%",
        "(Reading database ... 65%",
        "(Reading database ... 70%",
        "(Reading database ... 75%",
        "(Reading database ... 80%",
        "(Reading database ... 85%",
        "(Reading database ... 90%",
        "(Reading database ... 95%",
        "(Reading database ... 100%",
        "(Reading database ... 310034 files and directories currently installed.)",
        "Preparing to unpack .../ltrace_0.7.3-6.1_amd64.deb ...",
        "Unpacking ltrace (0.7.3-6.1) ...",
        "Setting up ltrace (0.7.3-6.1) ...",
        "Processing triggers for man-db (2.8.5-2) ...",
        "",
        "Running kernel seems to be up-to-date.",
        "",
        "Failed to check for processor microcode upgrades.",
        "",
        "No services need to be restarted.",
        "",
        "No containers need to be restarted.",
        "",
        "User sessions running outdated binaries:",
        " patou @ session #3: sh[4024]"
    ]
}

```

Maintenant, testons si ltrace est bien installé sur la machine `localhost`.
- Tapez `ltrace` dans la ligne de commande du virtualenv, il devrait être installé.
- Sortir du virtualenv en tapant `deactivate`
- Retaper `ltrace` dans la console en dehors du virtualenv. Il est toujours installé. Vérifiez avec `apt-get` si le paquet est maintenant installé effectivement. 

```bash
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ ltrace
ltrace: too few arguments
Try `ltrace --help' for more information.
(introansible) patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ deactivate 
patou@pa-linux:~/Documents/bizna/pasFini/demoAnsible/venvs$ aptitude search ltrace
p   babeltrace                                                         - Trace conversion program                                                    
p   libbabeltrace-ctf-dev                                              - Babeltrace development files (transitional package)                         
p   libbabeltrace-ctf1                                                 - Babeltrace conversion libraries (transitional package)                      
p   libbabeltrace-dev                                                  - Babeltrace development files                                                
p   libbabeltrace1                                                     - Babeltrace conversion libraries                                             
p   libdevel-calltrace-perl                                            - Code tracer to follow function calls                                        
i   ltrace                                                             - Tracks runtime library calls in dynamically linked programs                 
p   python3-babeltrace                                                 - Babeltrace Python 3 bindings   
```

On voit que c'est maintenant 
```sh 
i   ltrace                                                             - Tracks runtime library calls in dynamically linked programs                 
```
`i` signifie que le paquet est installé.

Dans la suite, nous allons plutôt lancer les tâches sous la forme de playbook,. Ceci était pour montrer qu'on pouvait très bien utiliser le mode `adhoc` qui signifie d'ailleurs ausi `à la volée`.

## 4.3 Les roles en détail
Un rôle est une manière conventionnel (c'est à dire que tout le monde doit accepter) de nommer des fichiers et des dossiers pour grouper des tâches et des variables. 

D'autres définitions existent mais il est peut-être plus facile de le comprendre avec un exemple.

![](img/role_example.png)

Dans cette structure de fichier, nous avons 2 roles: 
- common
- webserver

`Common` contient un task spécifié par 2 fichiers yml: `main.yml` et `git.yml` et ils sont rangés dans le dossier `task` qui est lui même dans le dossier `common`.

Nous avons également 2 fichiers de variables dans cette structure: `all` et `websever`. 

Ansible définit par convention que le dossier contenu dans le dossier `role` sont les noms des roles que nous avons définit.

Et les dossiers et fichiers dans ces dossiers de role (dans notre exemple, ce sont `common` et `webserver` sont accessibles 
quand vous lancer la commande ansible qui comprend cette structure de dossier.

Alors pourquoi voudrait-on utiliser des roles?
Parce que les roles peuvent rendre des tasks réutilisables pour différentes serveurs et différents projets. 

Les roles facilitent le relancement des tasks.

Nous allons voir le vrai pouvoir des roles quand on travaillera sur les playbooks. Il est important de comprendre que si on n'utilise pas les roles, on n'utilise pas la vrai puissance d'Ansible.

## 4.4 Les playbooks en détail

Playbook est le terme qu'on entendra souvent dans Ansible. 
C'est définit comme une collection de role (qui eux-même comme nous l'avons vu dans 4.3 sont des collections de tasks et toutes les variables et informations nécéssaires à l'execution de ces tasks).

Pour lancer un playbook, ansible dispose d'une commande spécifique `ansible-playbook`

Voici la structure de répertoire d'un playbook
 
![](img/playbook_example.png)

Nous reconnaissons ce qu'on a vu dans les roles. Dans ce playbook nous avons 2 roles `common` et `webserver`. 

Et surtout nous avons 2 fichiers en plus par rapport à la section role:
- `deployment.yml`: est le fichier d'instruction du playbook que nous allons référencer quand nous allons utiliser le playbook.
Il contient également les références des serveurs sur lesquels nous allons lancer les roles. C'est le pont qui fait la relation entre les roles à appliquer et les serveurs sur lesquels on souhaite les appliquer. 

- `hosts`: Ce fichier contient la liste des serveurs sur lesquels nous souhaitons appliquer les roles. C'est aussi connu sous le nom de `invetory file` (fichier d'inventaire)
