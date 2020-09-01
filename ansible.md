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
Cela nous permet d'utiliser un environnement virtuel et ne pas casser notre installation global de python avec des librairies de différentes versions.

- vérifier qu'on a `python3`

```js
patou@pa-linux:~$ python3 --version
Python 3.7.3
```
Pour installer virtualenv, on va taper la commande suivante 

