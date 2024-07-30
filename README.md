# Vaultwarden On-Premise

## Sommaire

- Introduction
- Prérequis
    - Matériels
    - Logiciels
- Installation du systèmes d'exploitation et configurations de base
- Mise à jour des logiciels
- Installation de Docker
- Installation de CertBot
- Installation de Vaultwarden
- Accès à Vaultwarden en HTTP
- Accès à Vaultwarden en HTTPs
    - Configuration de la Box Internet (LiveBox 5)
    - Définition d'un nom d'hôte public pour l'accès à Vaultwarden
    - Obtention d'un nouveau certificat
    - Démarrage de Vaultwarden en HTTPs

## Introduction

[Vaultwarden](https://github.com/dani-garcia/vaultwarden) est une implémentation non officielle du gestionnaire de mots de passe [Bitwarden](https://bitwarden.com/).

Ce dernier propose notamment :
- Le stockage sécurisé d'identifiants, de mots de passe, de cartes de crédits, etc...
- Le remplissage automatique des identifiants sur les sites web et applications.
- La génération de mots de passe et de phrases de passe forts et personnalisés.
- La synchronisation d'un coffre-fort entre différents appareils sous Windows, macOS, Linux, Android ou iOS. Une extension de navigateur est également disponible.
- Le partage sécurité d'informations confidentielles avec d'autres utilisateurs.
- Le support de codes TOTP pour l'accès multi-facteur.

L'intégralité des fonctionnalités de Bitwarden sont implémentées au sein de Vaultwarden. Cependant, Bitwarden n'est pas compatible avec les processeurs ARM. Ainsi, il n'est pas possible d'installer Bitwarden sur un Raspberry Pi. C'est pourquoi Vaultwarden a été développé.

## Prérequis

### Matériels

- Kit de démarrage Raspberry Pi 4 :
    - 1 Raspberry Pi 4 Model B 8GB
    - 1 Boitier officiel
    - 1 carte micro SD NOOBS 32 GB
    - 1 alimentation officielle
    - 1 kit de 4 dissipateurs thermiques
    - 1 câble officiel Micro HDMI vers HDMI
- Clavier
- Souris

### Logiciels

- Raspberry Pi Imager

## Installation du systèmes d'exploitation et configurations de base

**IMPORTANT : Cette documentation part du principe que le Raspberry est branché en Ethernet directement à la box internet.**

Pour installer le système d'exploitation sur la carte micro SD et configurer le Raspberry, suivre la [documentation officielle](https://www.raspberrypi.com/documentation/computers/getting-started.html) à ce sujet.

**Remarque** : Activer les interfaces SSH et VNC peut s'avérer utile pour l'accès distant au Raspberry.

Pour ce faire, se rendre sur Menu d'application / Préférences / Configuration du Raspberry Pi.

Cliquer sur le menu Interfaces puis activer ou désactiver les services selon vos préférences.

## Mise à jour des logiciels

Une fois le système d'exploitation installé et le Raspberry Pi initialisé, mettre à jour les logiciels installés par défaut.

Executer les commandes suivantes dans un terminal :

``` BASH
sudo apt update
```

``` BASH
sudo apt upgrade
```

Redémarrer le Raspberry :

``` BASH
sudo reboot
```

## Installation de Docker

Installer Docker en exécutant les commandes suivantes :

``` BASH
curl -fsSL https://get.docker.com | sh
```

``` BASH
sudo usermod -aG docker $USER
```

Vérifier la bonne installation de Docker :

```
docker ps
```

## Installation de CertBot

**Remarque** : Si le conteneur Vaultwarden est démarré, l'arrêter au préalable à l'aide de la commande suivante :

``` BASH
sudo docker stop [ID]
```

Installer l'application CertBot de *Let's Encrypt* :

``` BASH
sudo apt install certbot
```

## Installation de Vaultwarden

Récupérer l'image Vaultwarden :

``` BASH
docker pull vaultwarden/server:latest
```

## Accès local à Vaultwarden en HTTP

**Remarque** : Bien que l'utilisation temporaire d'un gestionnaire de mots de passe en HTTP puisse s'avérer pratique lors de tests, ce mode de fonctionnement n'est pas recommandé à long terme d'un point de vue sécuritaire. Il est préférable de privilégier une solution reposant sur le protocole HTTPS pour une utilisation pérenne, afin de mieux protéger vos identifiants.

Démarrer le conteneur Vaultwarden en HTTP :

``` BASH
docker run -d --name vaultwarden -v /vw-data/:/data/ -p 80:80 vaultwarden/server:latest
```

Démarrer un navigateur et y entrer l'une des adresses suivantes :

``` TEXT
http://192.168.X.X/
```

``` TEXT
http://localhost/
```

``` TEXT
http://127.0.0.1/
```

## Accès distant à Vaultwarden en HTTPs

### Configuration de la Box Internet (LiveBox 5)

**Remarque** : Bien que cette procédure soit spécifiquement conçue pour la LiveBox 5 d'Orange, elle reste globalement la même, quel que soit le modèle de box internet utilisé.

Se rendre sur la page d'administration de la LiveBox :

```
http://192.168.1.1/
```

Se rendre sur la page `Paramètres avancés`.

Ouvrir la page `Réseau`

- Premièrement, dans l'onglet `DHCP`, attribuer une adresse IP statique à votre Raspberry selon le schéma suivant :

|  Equipement | Adresse IP statique |    Adresse MAC    |
|:-----------:|:-------------------:|:-----------------:|
| raspberrypi |     192.168.1.X     | XX:XX:XX:XX:XX:XX |

- Secondement, dans l'onglet `NAT/PAT`, définir une redirection de port selon le schéma suivant :

| Application/Service | Port interne | Port externe | Protocole |  Equipement | IP externe |
|:-------------------:|:------------:|:------------:|:---------:|:-----------:|:----------:|
|     Vaultwarden     |      443     |      443     |    TCP    | raspberrypi |   Toutes   |

### Définition d'un nom d'hôte public pour l'accès à Vaultwarden

Se rendre sur le [site de Dynv6](https://dynv6.com/) et se connecter ou, à défaut, se créer un compte.

Cliquer sur le menu `My Zones`.

Cliquer sur le bouton `More...`.

Cliquer sur le bouton `Create new Zone`.

Définir les différents champs selon le schéma suivant :

|   Name   |   Domain  | IPv4 Address | IPv6 prefix |
|:--------:|:---------:|:------------:|:-----------:|
| christ0u | dynv6.net |    X.X.X.X   |      /      |

**Remarque** : L'adresse IP attendue correspond à l'[adresse IPv4 publique de votre box internet](https://ipinfo.io/).

Pour finir, cliquer sur le bouton `Create Zone`.

### Obtention d'un nouveau certificat

Générer un certificat en exécutant la commande suivante en remplaçant `<example.domaine.com>` par le nom d'hôte défini précédemment :

``` BASH
sudo certbot certonly -d <example.domaine.com>
```

Exemple :

``` BASH
sudo certbot certonly -d christ0u.dynv6.net
```

L'invite suivante apparait :

``` TEXT
How would you like to authenticate with the ACME CA?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: Spin up a temporary webserver (standalone)
2: Place files in webroot directory (webroot)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```

Entrer `1` puis presser la touche `Entrée`.

Le certificat a été généré avec succès.

Copier les fichiers de certificats générés dans un nouveau dossier en remplaçant `<example.domaine.com>` par le nom d'hôte défini précédemment :

``` BASH
sudo cp /etc/letsencrypt/live/<example.domaine.com>/fullchain.pem /ssl/keys/certs.pem
sudo cp /etc/letsencrypt/live/<example.domaine.com>/privkey.pem /ssl/keys/key.pem
```

Exemple : 

``` BASH
sudo cp /etc/letsencrypt/live/christ0u.dynv6.net/fullchain.pem /ssl/keys/certs.pem
sudo cp /etc/letsencrypt/live/christ0u.dynv6.net/privkey.pem /ssl/keys/key.pem
```

### Démarrage de Vaultwarden en HTTPs

Démarrer le conteneur Vaultwarden en HTTPs :

``` BASH
docker run -d --name vaultwarden -e ROCKET_TLS='{certs="/ssl/certs.pem",key="/ssl/key.pem"}' -v /ssl/keys/:/ssl/ -v /vw-data/:/data/ -p 443:80 vaultwarden/server:latest
```

Démarrer un navigateur et y entrer l'adresse suivante en remplaçant `<example.domaine.com>` par le nom d'hôte défini précédemment.

``` TEXT
https://<example.domaine.com>/
```

Exemple :

``` TEXT
https://christ0u.dynv6.net/
```