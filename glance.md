### OpenStack Glance — Le service de gestion des images

![探索 OpenStack 之（10）：深入镜像服务Glance - SammyLiu - 博客园](https://images.openai.com/static-rsc-4/ElxwvpftTYFD7G4DswI4sLuKYB_i0WNf_xytrg2pd2XM2h5DI7W3HJ-ffbTVZOENeFHiyeKBiwkVegUSi3ByXu6r6vsiGCnphXf6y3_Kmg4OB5GINDSYaLlhAvjmtiW8LOTYNQh2jQ8j5orSGI6TZROQmAskp9p4NSC8-UAWtR9NBi48OJsmEfXKbDEbO5tK?purpose=fullsize)

OpenStack Glance est le service qui permet de stocker, cataloguer, rechercher et gérer les images système utilisées pour créer des machines virtuelles.

Son équivalent fonctionnel le plus proche dans AWS est Amazon Machine Images (AMI), même si l'architecture de stockage diffère.

### 1\. À quoi sert Glance ?

Imagine que tu veuilles créer 50 machines virtuelles Ubuntu. Tu n'as pas besoin d'installer Ubuntu manuellement sur chaque machine : tu peux utiliser une image système comme modèle.

Glance permet notamment de :

* Importer des images de systèmes Linux ou Windows.

* Répertorier les images disponibles.

* Gérer leurs métadonnées et leurs propriétés.

* Contrôler leur visibilité et leur accès.

* Fournir les images que Nova utilise lors de la création des instances.

Les formats courants incluent QCOW2 et RAW, selon les capacités du cloud et de l'hyperviseur.

### 2\. Comment Glance fonctionne avec Nova ?

Glance — Image système

Ubuntu 24.04 / QCOW2

Nova — Création de l'instance

Choisit le flavor et le réseau

VM Web 1

Ubuntu installé

VM Web 2

Ubuntu installé

Le principe est simple :

* Glance référence une image système.

* Tu demandes à Nova de créer une instance à partir de cette image.

* Nova prépare le disque de l'instance selon la configuration du cloud.

* L'hyperviseur démarre la VM.

L'image d'origine n'est pas nécessairement copiée de la même manière dans tous les environnements : le backend de stockage et la configuration de Nova déterminent le mécanisme utilisé.

### 3\. Commandes OpenStack CLI

### Lister les images disponibles

```bash
openstack image list
```

### Afficher les détails d'une image

```bash
openstack image show <ID_OU_NOM_IMAGE>
```

Tu peux notamment consulter son format, sa taille, son statut et ses propriétés.

### Télécharger une image

Si les droits et la visibilité de l'image le permettent :

```bash
openstack image save \
  --file ubuntu.qcow2 \
  <ID_OU_NOM_IMAGE>
```

### Importer une image QCOW2

Exemple pour une image Ubuntu que tu as déjà téléchargée et dont tu es autorisé à distribuer le contenu :

```bash
openstack image create ubuntu-custom \
  --file ubuntu-custom.qcow2 \
  --disk-format qcow2 \
  --container-format bare \
  --private
```

L'option `--private` limite la visibilité de l'image selon les règles d'accès du cloud. Elle ne remplace pas les contrôles d'accès du stockage ni les autres mesures de sécurité.

### Vérifier l'état d'une image

```bash
openstack image show ubuntu-custom
```

Une image importée doit généralement atteindre l'état `active` avant de pouvoir être utilisée normalement.

### 4\. Image, instance et snapshot : quelle différence ?

| Concept             | Signification                                                                  |
| ------------------- | ------------------------------------------------------------------------------ |
| Image Glance        | Modèle de système utilisé pour créer des VM                                    |
| Instance Nova       | Machine virtuelle en cours d'exécution ou arrêtée                              |
| Snapshot d'instance | Capture de l'état du disque d'une VM, souvent enregistrée comme image Glance   |
| Volume Cinder       | Disque persistant indépendant du cycle de vie de la VM, selon sa configuration |

Exemple : tu crées une VM Ubuntu, tu installes Nginx et tu ajustes la configuration. Tu peux ensuite créer un snapshot pour réutiliser cette configuration comme base pour de nouvelles VM, sous réserve des fonctionnalités activées dans ton cloud.

Attention : un snapshot de disque ne garantit pas à lui seul une sauvegarde cohérente de toutes les données applicatives, notamment celles d'une base de données active.

### 5\. Sécurité des images

Dans un environnement professionnel, les images système doivent être contrôlées avant leur utilisation :

* Vérifier leur provenance et leur intégrité.

* Utiliser des images maintenues et corrigées.

* Éviter d'y intégrer des mots de passe, clés privées ou secrets.

* Limiter les droits de création, modification et partage.

* Désactiver les services et accès inutiles dans les images personnalisées.

Une image compromise peut propager une vulnérabilité à toutes les VM créées à partir de celle-ci.

### 6\. Équivalences AWS

| OpenStack                 | AWS                              |
| ------------------------- | -------------------------------- |
| Glance                    | Gestion des images de VM         |
| Image Glance              | AMI, pour l'usage le plus proche |
| Nova                      | Amazon EC2                       |
| Snapshot de volume Cinder | Snapshot Amazon EBS              |

À retenir : Glance gère les images, tandis que Nova utilise ces images pour créer les machines virtuelles.