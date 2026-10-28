### OpenStack : liste des services, utilité et scénarios pratiques

OpenStack est une plateforme open source qui permet de construire et d'administrer un cloud privé. Elle transforme des serveurs physiques en ressources virtualisées que l'on peut attribuer à des utilisateurs, des équipes ou des applications.

L'idée est de retrouver, dans un cloud privé, des fonctions comparables à celles d'AWS : machines virtuelles, réseaux, stockage, gestion des identités, répartition de charge, orchestration et supervision.

### 1\. Vue d'ensemble des services OpenStack

![OpenStack Architecture | PDF](https://images.openai.com/static-rsc-4/sky2fMDartnmb9rbUbIx-HEK1LsDLytVPSSkg2-9FRctI9MstMHQuCdCiGQip3CmJyxYB0OyPTTxwiWTVY2RqCgEO5kP_S3ie8DvSQ05BOO7jPVtEFYnMBUrIFMKvBmw6A1Y08Cq3BsckJe7UTJjYmtjr-3zomjHN5V2zgy39NMfbHnghYS0iqsdUVfsG6Wo?purpose=fullsize)

Calcul et virtualisation

Nova, Placement, Cyborg

Création des machines virtuelles, choix des serveurs physiques et allocation de ressources spécialisées.

![OpenStack Docs: OpenStack Networking](https://images.openai.com/static-rsc-4/EdAVAiSqV4DTQZvhV_DM5XtVEw0WzUXug0iXqduwTgXKyc9NQmjWzT9p18RfYZjEGDdz2qtfa2lsJWkL-VNkEh6oq5m22J4W-raXv2GdFEgjvIUzhKrXxrqTwz2WDs1iR0mPeRbiuAKCn_1fkRSdM-MzRTsP7fgfjBjPDfZTztrYqrZerED7dfUXp5--4NN1?purpose=fullsize)

Réseau

Neutron, Octavia, Designate

Réseaux privés, sous-réseaux, routeurs, adresses IP, pare-feu réseau et répartition de charge.

![OpenStack Storage Overview | PPTX](https://images.openai.com/static-rsc-4/L5IQyVm0j498yfolZ03l3GYN8pe3i3mZAQQSYzZrGBVpPXLsFWblKcC3g-DasKZ2O3u_D5g1Yu3JbTYG5Lkk47XflxF8qIcMd5ER6dRhmTCwD4kMPF-0Li1MHty7qeotqqkzh-snjrTrfAe-Ww9VgTZ0P7p8Ix_xypcd6K9cexOOicnjJ2MVrNAUc13IQ1Av?purpose=fullsize)

Stockage

Cinder, Swift, Manila, Glance

Disques persistants, stockage objet, partages de fichiers et images de machines virtuelles.

![How OpenStack's Keystone handles authentication and authorization](https://images.openai.com/static-rsc-4/2xeAnPUq6p35UioSJO68cPjc4f4D4Loil-3HEXuLrVNqLDSPSmYkPWdQINdehLeYAxJMWqn0x2qD4_K2CHCS2fJAzmiyLC1z_zlHzdTGw3TunofMsr9Jrf3qwbzos0c0CgVI49EsSgwf_ox4R5jaMdNHXlotAGH2ilRs_VbaBXe0g2VmQRW2e_9D4JsGoltw?purpose=fullsize)

Identité et administration

Keystone, Horizon, Placement

Authentification, rôles, permissions, projets et interface d'administration.

![OpenStackメッセージング機構を探ってみる #openstack - Qiita](https://images.openai.com/static-rsc-4/DrAq8Ox2GAGzVSf7MVH0CgDCW7mNoaq38VdTXn3WT1WTqgHVSUtL2lpZroFCZ38LFNauzQo0_gK8ykMdaeTgmLISmy5UHXUO0GYIbPhnqF2Ls5gpawZt_xoXxbZWLZSHOeuLfGkiLs5tuDA-COSfppar-jF94dAXJmslJtKchxjZByWOh9svhWfRSw12fS5L?purpose=fullsize)

Automatisation et exploitation

Heat, Barbican, Telemetry, Watcher

Déploiement coordonné de ressources, gestion des secrets, collecte de métriques et optimisation.

Attention : OpenStack est un ensemble de projets indépendants. Une installation ne contient pas nécessairement tous ces services ; leur disponibilité dépend de la distribution, de la version et des choix de l'administrateur.

### 2\. Services fondamentaux : les indispensables

Ces services constituent le socle le plus important pour comprendre le fonctionnement d'OpenStack. La documentation officielle distingue notamment le calcul, le réseau, le stockage, l'identité et les images. docs.openstack.org +1

![OpenStack Keystone Important Privilege Escalation Risks OSSA-2026-015](https://images.openai.com/static-rsc-4/OJZyMF5m1kLlcTPa7LxJJKwAe2rqZQu0ioni95MRCfsKx7OfFRKMiURdo3_yfnNaTQinC0lfvb0mkNxpc_d7hDlAwhdOUyWzrJGww4EV-2ozvqJ7FxVGCyWw5yToAKm6KO5pw_ZjBgfbItju8Ix7kjHFbKJOde3N3PTv6YsHqBMiiVMHuGtjYqXxYH0vQnfs?purpose=fullsize)

1\. Keystone — Identité et contrôle d'accès

Gère les utilisateurs, les groupes, les projets, les rôles, les authentifications et les jetons d'accès. Il fournit également le catalogue des services et de leurs points d'accès.

Scénario : une équipe de développement peut créer ses machines virtuelles dans son projet, tandis qu'un stagiaire ne peut que consulter les ressources autorisées.

Équivalent AWS : IAM, pour les fonctions d'identité et d'autorisation.

![Week 9 Student Notes](https://images.openai.com/static-rsc-4/u-ZjyMXAvz5exaTXyLgDguh4dqP325A4y7FNZbQZAhPtPlWTmTJ44-sQGv6fnkk4grsS0XSIg5On_9rRVC_IqVV2AQDLLZt_OjiDaSv6mXQ9OCV2NuTv3XNvHfdT0uzN4SFjlkmcm8HWIQbZAKQkBF3UMKlZGSeL6PokD9ru0yaBVTnBWr2tInAaZ9Hh2YsH?purpose=fullsize)

2\. Nova — Calcul et machines virtuelles

Crée, démarre, arrête, supprime et déplace des instances virtuelles. Il coordonne les opérations avec les hyperviseurs et les autres services.

Scénario : une équipe demande un serveur Ubuntu avec 4 vCPU et 8 Go de RAM pour héberger une application web.

Équivalent AWS : Amazon EC2.

![Curso OpenStack](https://images.openai.com/static-rsc-4/kbqDvSuCQ0uF-PrBPkXb2bOA9ppwG41v0lfHFnDCTgE6YA3kIDAHxCiwjOxtnRO6Yqk_jw4j1BWTecKNYKChp2u6aw8PXLIxbnDergPPzIxxM96pLvo5E-Upg_x4eEMG3Rn_ThOqPOs3Uy-bpRv777xWMHdIsr_3jKvjBU96x1qZcNDKUMlksqrHfpUlWEc7?purpose=fullsize)

3\. Neutron — Réseau

Crée et gère les réseaux virtuels, sous-réseaux, ports réseau, routeurs, adresses IP flottantes et mécanismes de sécurité réseau pris en charge par le déploiement.

Scénario : les serveurs web sont accessibles depuis Internet, mais les serveurs de bases de données restent dans un réseau privé.

Équivalent AWS : Amazon VPC, avec les fonctions réseau correspondantes.

![Basic architecture — glance 12.0.1.dev24 documentation](https://images.openai.com/static-rsc-4/2yjyDcAcZjxO-rEYJoi-ppC-PhgnUYuJbVvOd_FuTxbFd_GNIVWQQ4l91RFztVoDiN5mGpb3SNGRjF-d_KaicOK4cBt5ThSTm1pltzlSuU-Vs1IeD7uFj8H4Fkta4XJPsgQCixuRr2CYnw9zrbYpK0FgYRWaPY1vZKbpm2oLLtI5lv9TtVmE9W-5ZdVWjzL7?purpose=fullsize)

4\. Glance — Images système

Référence et met à disposition les images disque nécessaires à la création des instances : Ubuntu, Debian, images personnalisées ou systèmes préinstallés.

Scénario : l'administrateur prépare une image Ubuntu standardisée avec les réglages approuvés pour que les développeurs créent rapidement des serveurs identiques.

Équivalent AWS : Amazon Machine Images (AMI) et les fonctions de catalogue d'images.

![OpenStack Docs: Storage architecture](https://images.openai.com/static-rsc-4/7JJoT_RrswLVc-SwdF9zjMKlhDbz123T5RaV_HCQ4z7cACxUOd1xQ2LO8E7Ez_syERBxaLhtMjX6Pf_WVFaa9t_oe02g9xCW-vwO19p9SzB0ZHw8wfcn7KEI9QhXap2wHHFGM9g_1oUWugt8_MRyJ9Rch29OSB0iGLT2N7jHnsRZrAf-iBXVe8VoJBSYVB08?purpose=fullsize)

5\. Cinder — Stockage en blocs

Fournit des volumes persistants que l'on attache aux machines virtuelles. Leur cycle de vie est indépendant de celui de l'instance, selon la configuration.

Scénario : une machine virtuelle utilise un volume séparé pour stocker les données de son application. On peut rattacher ce volume à une autre instance lors d'une migration ou d'une récupération.

Équivalent AWS : Amazon EBS.

![OpenStack 101: The parts that make up the project | Network World](https://images.openai.com/static-rsc-4/ueN0CC__eGpU0W29PHecHWAj0AV4AjlGENdB5PPFSRuDmUSaicIxp8iuaWLSwOlncYJCCJRa95j0aW1aV5iGiDACvjG1mZkXvNj03S6Fk5jrHU7IJAlY9JB6o15LucKCMp1dAAXlYo5uUgLnilgIOPDGe4qEsDTRDd0t8LknSNJiBnSn0qHgRfkemWRSz6Fc?purpose=fullsize)

6\. Horizon — Tableau de bord web

Propose une interface graphique pour gérer les instances, les réseaux, les volumes, les images et les autres ressources autorisées.

Scénario : un administrateur crée un projet, vérifie l'état des machines et attribue des ressources sans passer par la ligne de commande.

Équivalent fonctionnel : console de gestion AWS.

### 3\. Services réseau, sécurité et accès

| Service                               | Utilité                                                                 | Scénario concret                                                                                                                       |
| ------------------------------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Octavia                               | Fournit des répartiteurs de charge (Load Balancers).                    | Répartir les requêtes HTTPS entre deux serveurs web et retirer un serveur défaillant du trafic si les contrôles de santé le détectent. |
| Designate                             | Gestion du DNS en tant que service.                                     | Créer et gérer les zones DNS et les enregistrements d'un domaine hébergé dans le cloud.                                                |
| Barbican                              | Gestion des secrets et des clés cryptographiques.                       | Centraliser les certificats TLS et les clés utilisés par des services compatibles, au lieu de les disperser dans les configurations.   |
| Neutron Security Groups               | Contrôle du trafic entrant et sortant au niveau des ports réseau.       | Autoriser HTTPS vers les serveurs web et les connexions à la base de données uniquement depuis le réseau applicatif.                   |
| Neutron VPN as a Service (VPNaaS)     | Extension VPN lorsque le greffon et le pilote adaptés sont disponibles. | Relier le réseau d'un bureau au réseau privé du cloud par un tunnel VPN.                                                               |
| Neutron Firewall as a Service (FWaaS) | Fonctions de pare-feu réseau fournies par les greffons compatibles.     | Appliquer une politique de filtrage réseau à une frontière du réseau virtuel.                                                          |
| Neutron Dynamic Routing               | Annonce et apprentissage de routes dynamiques via les greffons adaptés. | Intégrer un cloud à un réseau d'entreprise utilisant BGP.                                                                              |

Point important : Keystone et Barbican ne font pas le même travail.

* Keystone répond à la question : « Qui es-tu et quelles actions as-tu le droit d'effectuer ? »

* Barbican répond à la question : « Où stocker et comment gérer les secrets et clés cryptographiques ? »

* Neutron contrôle la connectivité réseau.

* Octavia distribue le trafic entre plusieurs serveurs.

La présence de ces fonctions dépend des composants installés et de leur configuration. docs.openstack.org +2

### 4\. Services de stockage

| Service | Utilité                                                                 | Scénario concret                                          | Équivalent AWS approximatif               |
| ------- | ----------------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------- |
| Cinder  | Volumes de stockage en blocs persistants.                               | Disque de données d'une VM.                               | EBS                                       |
| Swift   | Stockage d'objets accessibles par API.                                  | Archives, documents, médias et fichiers applicatifs.      | S3                                        |
| Manila  | Systèmes de fichiers partagés entre plusieurs clients.                  | Plusieurs serveurs accèdent au même partage de fichiers.  | EFS, selon le protocole et le déploiement |
| Glance  | Catalogue d'images de machines virtuelles.                              | Image de référence d'un système Linux.                    | AMI et fonctions de catalogue             |
| Freezer | Sauvegarde et restauration, si le projet est intégré à la distribution. | Restaurer des données après une suppression accidentelle. | AWS Backup, de façon approximative        |

Il faut distinguer trois notions :

* Bloc : un disque que le système d'exploitation utilise comme un périphérique de stockage.

* Objet : un fichier stocké comme un objet indépendant, avec ses métadonnées, accessible par une API.

* Fichier partagé : un espace de fichiers accessible simultanément par plusieurs clients autorisés.

Par exemple, une application peut utiliser Cinder pour son disque de données, Swift pour archiver des factures et Manila pour partager des fichiers entre plusieurs serveurs.

### 5\. Services d'automatisation et d'orchestration

| Service | Utilité                                                                                            | Scénario concret                                                                                             |
| ------- | -------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Heat    | Orchestration de ressources à partir de modèles déclaratifs.                                       | Déployer ensemble un réseau, deux machines virtuelles, un répartiteur Octavia et les ressources nécessaires. |
| Mistral | Automatisation de workflows composés d'étapes et de décisions.                                     | Enchaîner une validation, la création de ressources et une notification de fin d'opération.                  |
| Zaqar   | Service de messagerie et de files de messages.                                                     | Permettre à plusieurs composants applicatifs d'échanger des messages de manière asynchrone.                  |
| Magnum  | Gestion de l'infrastructure de clusters de conteneurs, selon les orchestrateurs pris en charge.    | Mettre à disposition un cluster Kubernetes pour une équipe applicative.                                      |
| Zun     | Gestion de conteneurs comme ressources cloud.                                                      | Lancer des conteneurs sans nécessairement administrer directement une machine virtuelle pour chacun.         |
| Trove   | Bases de données proposées en tant que service, selon les moteurs et la configuration disponibles. | Fournir une instance de base de données à une équipe applicative.                                            |
| Blazar  | Réservation de ressources pour des périodes définies.                                              | Réserver des ressources de calcul pour une formation ou une campagne de tests.                               |
| Senlin  | Gestion de groupes de ressources et opérations de cycle de vie sur des clusters.                   | Administrer un groupe de ressources similaires et coordonner certaines opérations sur ce groupe.             |

Les services d'orchestration et les services de gestion des ressources ont des rôles distincts : Heat coordonne un déploiement global, tandis que Nova, Neutron et Cinder créent respectivement les ressources de calcul, de réseau et de stockage. docs.openstack.org +1

### Heat ou Load Balancer : quelle différence ?

### Heat

Orchestre la création des ressources

Nova

Crée les deux serveurs web.

Neutron

Prépare le réseau.

Octavia

Distribue le trafic.

Cinder

Fournit les volumes nécessaires.

Heat ne remplace donc pas Octavia. Heat demande la création et coordonne les ressources ; Octavia assure effectivement la répartition de charge une fois configuré.

### 6\. Services de supervision, métriques et diagnostic

| Service    | Utilité                                                                                             | Scénario concret                                                                                               |
| ---------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Ceilometer | Collecte des mesures et événements de télémétrie sur les ressources.                                | Suivre des mesures d'utilisation des instances ou des volumes lorsque les collecteurs adaptés sont configurés. |
| Aodh       | Déclenche des alarmes à partir de mesures ou d'événements.                                          | Signaler un dépassement de seuil de consommation ou une condition anormale surveillée.                         |
| Vitrage    | Analyse des causes possibles et des relations entre événements et ressources.                       | Aider à déterminer si plusieurs alertes sont liées à une même panne d'infrastructure.                          |
| Watcher    | Analyse et optimisation de l'utilisation des ressources.                                            | Identifier des possibilités d'amélioration de la répartition des charges entre serveurs physiques.             |
| Masakari   | Gestion de la haute disponibilité des instances en cas de défaillance détectée de l'infrastructure. | Détecter la panne d'un hôte de calcul et déclencher des actions de récupération selon la politique configurée. |
| CloudKitty | Mesure et calcul de coûts ou de consommation pour la facturation.                                   | Établir une répartition des coûts entre différents projets.                                                    |
| Rally      | Tests de performance et de charge d'un cloud OpenStack.                                             | Mesurer le comportement de la plateforme lors de la création simultanée de nombreuses instances.               |

Ces services n'ont pas tous la même finalité : mesurer une consommation, déclencher une alarme, analyser une panne, optimiser les ressources et assurer une reprise sont des fonctions différentes. Leur fonctionnement dépend des collecteurs, pilotes, politiques et intégrations disponibles. docs.openstack.org +2

### 7\. Services de calcul spécialisés et autres projets

Voici d'autres projets OpenStack officiels, notamment ceux qui répondent à des besoins spécifiques. La liste et le niveau de prise en charge varient selon les versions. docs.openstack.org +2

| Service ou projet | Utilité                                                                                                     | Scénario concret                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Ironic            | Provisionnement de serveurs physiques sans passer par une VM classique.                                     | Fournir un serveur physique dédié à une charge de calcul exigeante.                                   |
| Cyborg            | Gestion de ressources matérielles accélératrices compatibles.                                               | Mettre à disposition des GPU ou d'autres accélérateurs pris en charge.                                |
| Placement         | Inventaire des ressources disponibles et suivi de leurs allocations.                                        | Identifier un hôte ayant suffisamment de CPU, de RAM ou de ressources spécialisées pour une instance. |
| Sahara            | Déploiement et gestion de traitements de données distribués, notamment Hadoop selon les intégrations.       | Monter un environnement de traitement distribué pour une analyse de données.                          |
| Murano            | Catalogue et déploiement d'applications, projet historique dont la disponibilité dépend de la distribution. | Proposer aux utilisateurs un catalogue d'applications préconfigurées.                                 |
| Storlets          | Exécution de traitements à proximité des objets stockés dans Swift, avec les mécanismes compatibles.        | Effectuer un traitement sur des objets sans devoir tous les transférer vers une application externe.  |
| Adjutant          | Automatisation de processus administratifs et d'opérations d'exploitation.                                  | Organiser certaines demandes de création de projets ou de ressources.                                 |
| EC2 API (ec2-api) | Expose une interface compatible avec certaines API EC2.                                                     | Faciliter la compatibilité de certains outils conçus pour l'écosystème AWS.                           |
| Tacker            | Orchestration de fonctions réseau virtualisées et de services réseau, selon les intégrations.               | Déployer et gérer des fonctions réseau virtualisées.                                                  |
| Kuryr             | Intégration entre des réseaux de conteneurs et Neutron.                                                     | Connecter des charges de travail conteneurisées aux mécanismes réseau OpenStack compatibles.          |
| Freezer           | Sauvegarde et restauration.                                                                                 | Planifier des sauvegardes de ressources prises en charge et restaurer les données en cas de besoin.   |

### Projets de déploiement et bibliothèques

Tous les projets officiels ne sont pas des services que l'on consomme directement depuis Horizon.

| Projet                | Fonction                                                                                                            |
| --------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Kolla / Kolla-Ansible | Déployer et administrer OpenStack à l'aide de conteneurs et d'Ansible.                                              |
| OpenStack-Ansible     | Déployer et gérer OpenStack à l'aide de playbooks Ansible.                                                          |
| OpenStack SDK         | Fournir une bibliothèque de programmation pour interagir avec les API OpenStack.                                    |
| Oslo                  | Regroupe des bibliothèques communes utilisées par plusieurs services OpenStack.                                     |
| Tempest               | Tester le fonctionnement et l'interopérabilité d'un déploiement OpenStack.                                          |
| DevStack              | Installer un environnement de développement et de test OpenStack, plutôt qu'une plateforme de production classique. |

Pour consulter l'inventaire officiel actualisé, voir le [catalogue des projets OpenStack](https://docs.openstack.org/2026.2/projects.html?utm_source=chatgpt.com). docs.openstack.org +1

### 8\. Scénario complet : héberger une application web en haute disponibilité

Imaginons une entreprise qui souhaite héberger un site web avec deux serveurs applicatifs, une base de données et un stockage pour les documents.

Utilisateur Internet

Accède au site en HTTPS

Octavia

Répartit le trafic vers les serveurs disponibles

Serveur web 1

Nova

Serveur web 2

Nova

Base de données

Réseau privé Neutron et volume Cinder si nécessaire

Services transversaux

Keystone pour les permissions, Glance pour les images, Barbican pour les secrets, Swift pour les documents, Ceilometer et Aodh pour la supervision si configurés, Heat pour le déploiement coordonné.

### Déroulement du projet

* Keystone : l'administrateur crée le projet de l'application et attribue les rôles nécessaires à l'équipe.

* Glance : il choisit une image système approuvée pour les serveurs.

* Neutron : il crée le réseau privé, les sous-réseaux et les règles de communication.

* Nova : il crée les deux serveurs web et, si nécessaire, un serveur de base de données.

* Cinder : il fournit les volumes persistants nécessaires.

* Octavia : il configure le répartiteur de charge et les contrôles de santé.

* Barbican : il gère les secrets et certificats lorsque les intégrations correspondantes sont activées.

* Swift : il conserve les documents et objets applicatifs compatibles avec le stockage objet.

* Heat : il coordonne le déploiement des ressources décrites dans le modèle.

* Ceilometer et Aodh : ils collectent des mesures et déclenchent des alertes si l'environnement est configuré à cet effet.

Résultat attendu : l'entreprise dispose d'une application structurée en plusieurs composants, avec une séparation entre le trafic public, le réseau applicatif et les données.

Attention : deux serveurs web ne garantissent pas, à eux seuls, une haute disponibilité complète. Il faut également étudier la redondance du répartiteur, des bases de données, du stockage et des autres composants critiques.

### 9\. Scénario de sécurité : gérer les utilisateurs et les droits

Prenons trois profils : administrateur cloud, développeur et auditeur.

| Action                              | Administrateur cloud          | Développeur                      | Auditeur                  |
| ----------------------------------- | ----------------------------- | -------------------------------- | ------------------------- |
| Créer un réseau                     | Selon son rôle                | Selon les permissions attribuées | Généralement non          |
| Créer une VM                        | Oui, selon son périmètre      | Oui, dans son projet si autorisé | Non                       |
| Consulter les ressources            | Oui                           | Dans son périmètre               | Selon son rôle de lecture |
| Gérer les secrets                   | Selon les permissions         | Uniquement si autorisé           | Généralement non          |
| Modifier les rôles des utilisateurs | Selon les privilèges accordés | Non par défaut                   | Non par défaut            |

Le mécanisme général est le suivant :

* Utilisateur : l'identité qui demande une opération.

* Projet : le périmètre logique auquel les ressources appartiennent.

* Rôle : l'ensemble de permissions associé à l'utilisateur dans un contexte donné.

* Politique d'autorisation : les règles qui déterminent si l'opération demandée est permise.

* Jeton Keystone : un justificatif d'authentification et de contexte utilisé par les services compatibles pour traiter la demande.

Les droits effectifs dépendent aussi des politiques configurées pour chaque service. Un rôle attribué dans Keystone ne signifie pas automatiquement que toutes les opérations sont permises sur tous les projets.

### 10\. Tableau récapitulatif pour mémoriser les services

Catégorie

43 services et projets

TousFondamentauxRéseauStockageSécuritéAutomatisationSupervisionCalcul spécialiséIntégration

Keystone

Identités, projets, rôles et jetons

Fondamentaux

Nova

Machines virtuelles

Fondamentaux

Neutron

Réseaux virtuels et connectivité

Réseau

Glance

Images de machines virtuelles

Fondamentaux

Cinder

Volumes persistants en blocs

Stockage

Horizon

Interface web d'administration

Fondamentaux

Swift

Stockage objet

Stockage

Manila

Systèmes de fichiers partagés

Stockage

Octavia

Répartition de charge

Réseau

Designate

Gestion DNS

Réseau

Barbican

Secrets et clés cryptographiques

Sécurité

Heat

Orchestration de ressources

Automatisation

Mistral

Workflows automatisés

Automatisation

Zaqar

Messagerie asynchrone

Automatisation

Magnum

Gestion de clusters de conteneurs

Calcul spécialisé

Zun

Gestion de conteneurs

Calcul spécialisé

Trove

Bases de données en tant que service

Calcul spécialisé

Ironic

Serveurs physiques

Calcul spécialisé

Cyborg

Accélérateurs matériels

Calcul spécialisé

Placement

Inventaire et allocation de ressources

Fondamentaux

Ceilometer

Collecte de télémétrie

Supervision

Aodh

Alarmes sur mesures et événements

Supervision

Vitrage

Analyse des causes et relations entre alertes

Supervision

Watcher

Optimisation des ressources

Supervision

Masakari

Récupération d'instances après certaines pannes

Supervision

CloudKitty

Mesure des coûts et facturation

Supervision

Blazar

Réservation de ressources

Automatisation

Freezer

Sauvegarde et restauration

Stockage

Sahara

Traitement distribué de données

Calcul spécialisé

Murano

Catalogue d'applications, selon disponibilité

Calcul spécialisé

Storlets

Traitement au plus près des objets Swift

Stockage

Adjutant

Automatisation de processus administratifs

Automatisation

Tacker

Orchestration de fonctions réseau virtualisées

Réseau

Senlin

Gestion de groupes de ressources

Automatisation

Rally

Tests de charge et performances

Supervision

EC2 API

Compatibilité avec certaines API EC2

Intégration

Kuryr

Intégration réseau des conteneurs

Réseau

Tempest

Tests fonctionnels et d'interopérabilité

Intégration

Kolla-Ansible

Déploiement et exploitation d'OpenStack

Intégration

OpenStack-Ansible

Déploiement et administration par Ansible

Intégration

OpenStack SDK

Accès programmatique aux API

Intégration

Oslo

Bibliothèques communes

Intégration

DevStack

Environnement de développement et de test

Intégration

Cette synthèse regroupe les principaux services et plusieurs projets complémentaires. Elle n'est pas un inventaire exhaustif de tous les projets, sous-projets, greffons et bibliothèques de l'écosystème OpenStack.

### 11\. Dans quel ordre les apprendre ?

Pour comprendre OpenStack efficacement, je recommande cet ordre :

* Keystone — comprendre les identités, les projets et les permissions.

* Glance — comprendre les images système.

* Nova — créer et gérer des machines virtuelles.

* Neutron — comprendre les réseaux, les sous-réseaux, les routeurs et les règles de sécurité.

* Cinder — gérer les volumes persistants.

* Horizon — administrer les ressources depuis l'interface web.

* Octavia — comprendre le Load Balancer.

* Heat — apprendre à déployer un environnement complet.

* Barbican — gérer les secrets et les clés.

* Ceilometer et Aodh — surveiller les ressources et les alertes.

La priorité est de comprendre les liens entre les services : Keystone autorise, Glance fournit l'image, Nova crée la machine, Neutron la connecte, Cinder lui fournit du stockage et Octavia distribue le trafic. Heat peut coordonner l'ensemble.
