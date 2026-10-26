# Configuration d’un cloud d’entreprise avec OpenStack

Parcours de formation de trois jours (18 heures) consacré à OpenStack et à
l’exploitation d’un cloud privé. Chaque journée associe apports théoriques,
démonstrations et LAB guidés dans une machine virtuelle Ubuntu.

Le dépôt contient les trois supports de cours en Markdown, accompagnés de ce
README. Aucune machine virtuelle préconstruite n’est fournie : la VM est créée
pas à pas au jour 1.

## Sommaire

- [Présentation](#présentation)
- [Public et objectifs](#public-et-objectifs)
- [Programme](#programme)
  - [Jour 1 — Concepts fondamentaux et installation](jour-1.md)
  - [Jour 2 — Configuration, administration et surveillance](jour-2.md)
  - [Jour 3 — Conception, sécurisation et sauvegarde](jour-3.md)
- [Prérequis](#prérequis)
- [Organisation du dépôt](#organisation-du-dépôt)
- [Démarrage rapide](#démarrage-rapide)
- [Conventions du laboratoire](#conventions-du-laboratoire)
- [Compétences visées](#compétences-visées)
- [Annexe — Dépannage KVM sous Windows 11](annexe-depannage-kvm-windows11.md)
- [Références](#références)

## Présentation

Le parcours part des concepts du cloud privé, puis installe un cloud OpenStack
de démonstration avec DevStack. Il progresse ensuite vers l’administration des
services, la surveillance, le dépannage, la sécurité et la sauvegarde.

Le fil conducteur est concret : une analyse des besoins réalisée au jour 1
devient une architecture cible au jour 3, déployée sous forme de deux projets
isolés, puis sécurisée et sauvegardée.

- **Jour 1** : concepts du cloud, modèles IaaS, PaaS et SaaS, analyse des
  besoins, présentation d’OpenStack, installation de DevStack et premiers
  contrôles du cloud.
- **Jour 2** : configuration avancée de Keystone, Neutron, Nova et Glance,
  utilisation d’Horizon, surveillance des performances, lecture des journaux,
  dépannage et maintenance de DevStack.
- **Jour 3** : conception d’une architecture cible, déploiement d’un scénario
  multi-projets, validation des accès, application du moindre privilège,
  optimisation, sauvegarde et restauration.

## Public et objectifs

Ce parcours s’adresse aux équipes techniques qui doivent comprendre, déployer
ou administrer un cloud privé : administrateurs système et réseau, architectes
cloud, ingénieurs exploitation et profils sécurité.

À l’issue des trois journées, les participants savent :

- expliquer les modèles de cloud et situer les services OpenStack dans une
  architecture ;
- créer une machine virtuelle Ubuntu, configurer son réseau et y installer
  DevStack ;
- administrer les identités, les projets, les rôles et les droits avec
  Keystone ;
- configurer des réseaux, sous-réseaux, routeurs, adresses flottantes et
  groupes de sécurité avec Neutron et OVN ;
- gérer les images, les graines, les paires de clés et le cycle de vie des
  instances avec Nova et Glance ;
- surveiller les performances, lire les journaux et appliquer une démarche de
  dépannage structurée ;
- sécuriser un déploiement, le sauvegarder, le restaurer et proposer une
  feuille de route d’exploitation.

## Programme

| Jour | Support | Thèmes principaux | Durée |
| --- | --- | --- | --- |
| 1 | [jour-1.md](jour-1.md) | Cloud privé, services OpenStack, installation de DevStack, `local.conf`, `stack.sh`, Horizon | 6 h |
| 2 | [jour-2.md](jour-2.md) | Keystone et RBAC, Neutron et OVN, Nova, Glance, surveillance, journaux, dépannage, maintenance | 6 h |
| 3 | [jour-3.md](jour-3.md) | Architecture cible, scénario multi-projets, sécurité, optimisation, sauvegarde et restauration | 6 h |

### Jour 1 — Concepts fondamentaux et installation

La matinée pose le vocabulaire du cloud et les besoins d’un cloud privé. Elle
présente ensuite l’histoire, la gouvernance et la carte des services
OpenStack : Keystone, Nova, Neutron, Glance, Cinder, Swift, Horizon et
Placement.

L’après-midi est consacré à la pratique : création de la VM `lab-openstack`
sous VirtualBox, installation d’Ubuntu Server, configuration réseau NAT et
host-only, puis installation de DevStack `stable/2026.1`. Les participants
étudient `local.conf` et `stack.sh`, vérifient le jeton, le catalogue et les
services, et découvrent Horizon.

### Jour 2 — Configuration, administration et surveillance

La matinée approfondit la configuration des services : domaines, projets,
utilisateurs et rôles avec Keystone ; réseaux, routeurs, adresses flottantes
et groupes de sécurité avec Neutron ; graines, images, clés et instances avec
Nova ; corrélation entre la ligne de commande et Horizon.

L’après-midi traite l’exploitation : mesures de performance, quotas, état de
l’hyperviseur, lecture des journaux avec `journalctl`, unités `devstack@*`,
arbre de décision de dépannage, maintenance avec `unstack.sh` et `stack.sh`,
et sauvegarde de la configuration.

### Jour 3 — Conception, sécurisation et sauvegarde

La matinée transforme la note d’analyse du jour 1 en architecture cible
dimensionnée pour la VM du laboratoire. Les participants déploient un scénario
complet avec deux projets isolés, un serveur web et un serveur de base de
données, puis valident la connectivité par adresse flottante et par SSH.

L’après-midi applique le moindre privilège, le contrôle d’accès fondé sur les
rôles (RBAC) et des groupes de sécurité restrictifs. Les participants
identifient les goulots d’étranglement d’un nœud unique, mettent en place une
sauvegarde chiffrée, testent une restauration et closent le parcours par une
synthèse et une feuille de route.

## Prérequis

### Poste hôte

- Windows 10 ou 11 avec VirtualBox 7.x, ou un hôte Linux disposant des mêmes
  fonctions de virtualisation.
- Virtualisation matérielle activée dans le BIOS ou l’UEFI.
- Assez de mémoire pour allouer 8 Go à la VM ; 4 vCPU et au moins 60 Go
  d’espace disque libre.
- Accès Internet pour télécharger l’image Ubuntu, les paquets et le dépôt
  DevStack.
- Sous Windows 11, si `/dev/kvm` est absent dans la VM (Hyper-V, WSL2 ou VBS
  actifs), appliquer l’[annexe de dépannage KVM](annexe-depannage-kvm-windows11.md).

### Machine virtuelle

- Ubuntu Server 24.04 LTS, avec le compte `stack` disposant de `sudo`.
- Deux interfaces réseau : NAT pour Internet et host-only sur
  `192.168.56.0/24`, avec l’adresse `192.168.56.10/24`.
- Virtualisation imbriquée active et périphérique `/dev/kvm` disponible.
- DevStack `stable/2026.1` installé dans `/opt/stack/devstack`.

### Temps et organisation

- Chaque journée représente environ 6 heures, avec une pause déjeuner.
- La création de la VM et l’installation de DevStack peuvent prendre plusieurs
  heures ; `stack.sh` demande généralement de 30 à 90 minutes.
- Les trois journées s’enchaînent : les ressources créées au jour 1 servent au
  jour 2, puis au jour 3.

## Organisation du dépôt

```text
.
├── README.md   Ce fichier : présentation, sommaire et prérequis
├── jour-1.md   Jour 1 : concepts, OpenStack et installation
├── jour-2.md   Jour 2 : configuration, administration et surveillance
├── jour-3.md   Jour 3 : conception, sécurisation et sauvegarde
└── annexe-depannage-kvm-windows11.md   Dépannage KVM sous Windows 11
```

Les trois supports suivent un déroulé continu : chacun suppose que les LAB des
journées précédentes ont été réalisés. Les commandes, les valeurs du
laboratoire et les résultats attendus sont indiqués dans chaque fichier.

L’annexe de dépannage KVM complète le préambule du jour 1 pour les hôtes
Windows 11 dont la VM n’expose pas `/dev/kvm`.

## Démarrage rapide

1. Lire le préambule du [jour 1](jour-1.md) et créer la VM `lab-openstack`.
2. Installer Ubuntu Server, configurer le réseau et vérifier `/dev/kvm`. En cas
   d’absence sous Windows 11, suivre l’[annexe de dépannage KVM](annexe-depannage-kvm-windows11.md).
3. Installer DevStack `stable/2026.1` et contrôler le cloud avec
   `openstack token issue` et `openstack service list`.
4. Dérouler le [jour 1](jour-1.md), puis le [jour 2](jour-2.md) et enfin le
   [jour 3](jour-3.md), sans sauter d’étape.
5. Conserver les sorties de commandes, les journaux et les captures demandés
   dans les LAB : ils constituent les preuves de validation.

## Conventions du laboratoire

- La valeur `lab123` est le mot de passe public du laboratoire. Elle ne doit
  jamais être réutilisée sur un système réel.
- Les adresses `192.168.56.0/24`, `172.24.4.0/24` et `192.168.100.0/24` sont
  propres à l’environnement décrit. Les adapter si le réseau de l’hôte diffère.
- Les blocs de commandes précisent le contexte : machine hôte ou machine
  virtuelle. Les commentaires commencent par `#` et ne doivent pas être
  recopiés.
- Les LAB produisent des livrables : inventaire, note d’analyse, scripts,
  captures et résultats de vérification. Les conserver pour la revue finale.
- Le parcours privilégie un cloud minimal adapté à une VM de 8 Go. Swift n’est
  pas requis par les LAB.
- Les résultats peuvent varier selon la version de DevStack, la puissance de
  l’hôte et le débit du stockage.

## Compétences visées

| Domaine | Compétences travaillées |
| --- | --- |
| Cloud | Modèles IaaS, PaaS et SaaS ; cloud public, privé et hybride ; analyse des besoins |
| OpenStack | Rôle de Keystone, Nova, Neutron, Glance, Cinder, Swift, Horizon et Placement |
| Identité | Domaines, projets, utilisateurs, rôles, RBAC et isolation multi-projet |
| Réseau | Sous-réseaux, routeurs, adresses flottantes, groupes de sécurité, OVN |
| Calcul | Graines, images, paires de clés, instances, redimensionnement, instantanés |
| Exploitation | Surveillance, quotas, journaux, dépannage, maintenance de DevStack |
| Sécurité | Moindre privilège, durcissement, sauvegarde chiffrée, restauration |
| Architecture | Dimensionnement, marges, goulots d’étranglement, feuille de route |

## Références

- Documentation OpenStack : <https://docs.openstack.org/>
- DevStack : <https://docs.openstack.org/devstack/>
- Notes de version 2026.1 : <https://releases.openstack.org/2026.1/>
- Administration de Neutron et OVN :
  <https://docs.openstack.org/neutron/latest/admin/ovn/index.html>
- CIS Benchmarks : <https://www.cisecurity.org/benchmarks>
- VirtualBox : <https://www.virtualbox.org/>

Les supports sont alignés sur DevStack `stable/2026.1` (Gazpacho). Les dates
de session figurant dans les en-têtes peuvent être adaptées à chaque
promotion.
