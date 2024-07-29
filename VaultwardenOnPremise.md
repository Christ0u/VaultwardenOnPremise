# Vaultwarden On-Premise

## Sommaire

- Introduction
- Prérequis
    - Matériels
    - Logiciels
- Installation du systèmes d'exploitation
- Mise à jour du système d'exploitation

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