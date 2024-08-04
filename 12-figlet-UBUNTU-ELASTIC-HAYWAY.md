# Installation et Configuration de `figlet` sur Ubuntu 22.04

Ce guide explique comment installer et configurer `figlet` sur Ubuntu 22.04 pour afficher un message d'accueil personnalisé "ELASTIC HAYWAY" dans votre terminal.

## Prérequis

- Une instance Ubuntu 22.04 avec accès à Internet.
- Privilèges sudo.

## Étapes d'Installation

### 1. Installation de `figlet`

Sur Ubuntu 22.04, `figlet` peut être installé directement à partir des dépôts de paquets officiels.

```sh
sudo apt update
sudo apt install figlet
```

### 2. Vérification de l'Installation

Pour vérifier que `figlet` est bien installé, exécutez la commande suivante :

```sh
figlet "ELASTIC HAYWAY"
```

### 3. Ajout du Message d'Accueil Personnalisé

Pour afficher le message "ELASTIC HAYWAY" à chaque ouverture de terminal, ajoutez la commande `figlet` à votre fichier de configuration de shell (`~/.bashrc` ou `~/.bash_profile`).

#### Modification de `~/.bashrc` ou `~/.bash_profile`

Ouvrez le fichier de configuration dans un éditeur de texte :

```sh
nano ~/.bashrc
```

ou

```sh
nano ~/.bash_profile
```

Ajoutez la commande `figlet` à la fin du fichier :

```sh
figlet "ELASTIC HAYWAY"
```

Enregistrez et fermez le fichier.

- Pour `nano`, faites `Ctrl+X`, puis `Y` pour confirmer, et appuyez sur `Entrée` pour enregistrer et quitter.

Rechargez votre fichier de configuration pour appliquer les modifications :

```sh
source ~/.bashrc
```

ou

```sh
source ~/.bash_profile
```

## Résultat Attendu

- Écrire :
  
```sh
bash
```

Chaque fois que vous ouvrez un nouveau terminal, vous devriez voir le message suivant :

```
  ______ _           _     _        _    _                              
 |  ____| |         | |   | |      | |  | |                             
 | |__  | | ___  ___| |__ | |_ __  | |__| | __ _ _ __  _ __   ___ _ __  
 |  __| | |/ _ \/ __| '_ \| | '_ \ |  __  |/ _` | '_ \| '_ \ / _ \ '__| 
 | |____| |  __/ (__| | | | | |_) || |  | | (_| | | | | | | |  __/ |    
 |______|_|\___|\___|_| |_|_| .__(_)_|  |_|\__,_|_| |_|_| |_|\___|_|    
                            | |                                         
                            |_|                                         
```

Ce guide fournit une méthode simple et efficace pour personnaliser votre environnement de terminal sur Ubuntu 22.04 avec un message d'accueil attrayant et professionnel pour "ELASTIC HAYWAY".

---

Cela devrait fournir un guide clair et professionnel pour l'installation et la configuration de `figlet` sur Ubuntu 22.04.
