### OpenStack Neutron — Networking

![OpenStack Docs: OpenStack Networking](https://images.openai.com/static-rsc-4/EdAVAiSqV4DTQZvhV_DM5XtVEw0WzUXug0iXqduwTgXKyc9NQmjWzT9p18RfYZjEGDdz2qtfa2lsJWkL-VNkEh6oq5m22J4W-raXv2GdFEgjvIUzhKrXxrqTwz2WDs1iR0mPeRbiuAKCn_1fkRSdM-MzRTsP7fgfjBjPDfZTztrYqrZerED7dfUXp5--4NN1?purpose=fullsize)

### 1\. À quoi sert Neutron ?

Neutron est le service qui gère le réseau virtuel de ton infrastructure OpenStack. Il joue un rôle comparable à Amazon VPC dans AWS, même si leurs fonctionnalités et leur architecture diffèrent.

Avec Neutron, tu peux créer :

* Networks : des réseaux virtuels isolés.

* Subnets : des plages d'adresses IP associées à un réseau.

* Routers : des routeurs qui relient les réseaux et éventuellement Internet.

* Ports : des interfaces réseau virtuelles attachées aux VM.

* Security Groups : des règles de filtrage du trafic réseau.

* Floating IPs : des adresses IP externes associables à certaines ressources.

### 2\. Exemple concret d'architecture

Internet

Routeur Neutron

Passerelle vers le réseau externe

Réseau privé : 10.0.0.0/24

VM Web

10.0.0.11

VM BDD

10.0.0.12

Dans cet exemple :

* La VM Web peut recevoir les requêtes HTTP autorisées.

* La VM de base de données n'accepte que les connexions nécessaires, par exemple depuis la VM Web sur le port TCP 3306.

* Le routeur permet la communication avec les réseaux externes selon sa configuration.

* Les Security Groups filtrent les flux autorisés.

### 3\. Les principales ressources Neutron

| Ressource      | Fonction                  | Exemple                             |
| -------------- | ------------------------- | ----------------------------------- |
| Network        | Réseau virtuel            | reseau-prod                         |
| Subnet         | Plage d'adresses IP       | 10.0.0.0/24                         |
| Router         | Relie les réseaux         | routeur-prod                        |
| Port           | Interface réseau d'une VM | 10.0.0.11                           |
| Security Group | Filtre les flux           | Autoriser TCP 443                   |
| Floating IP    | Adresse externe           | 203.0.113.10 (exemple documentaire) |

### 4\. Commandes pratiques

Ces commandes utilisent le client OpenStack CLI.

```bash
# Créer un réseau privé
openstack network create reseau-prod

# Créer un sous-réseau IPv4
openstack subnet create subnet-prod \
  --network reseau-prod \
  --subnet-range 10.0.0.0/24 \
  --ip-version 4

# Créer un routeur
openstack router create routeur-prod

# Lister les réseaux
openstack network list

# Lister les sous-réseaux
openstack subnet list

# Lister les routeurs
openstack router list
```

Pour connecter le sous-réseau au routeur, il faut ensuite définir une passerelle externe si nécessaire, puis ajouter une interface :

```bash
openstack router set routeur-prod \
  --external-gateway reseau-externe

openstack router add subnet routeur-prod subnet-prod
```

`reseau-externe` doit correspondre à un réseau externe existant et accessible dans ton cloud OpenStack.

### 5\. Sécurité : comment Neutron contrôle les accès ?

Les Security Groups appliquent des règles entrantes et sortantes, généralement avec suivi des connexions.

Par exemple, pour autoriser HTTPS entrant :

```bash
openstack security group rule create \
  --ingress \
  --protocol tcp \
  --dst-port 443 \
  groupe-web
```

La règle autorise TCP 443 depuis toutes les sources par défaut, sauf si une restriction de source est ajoutée. En production, limite les sources au strict nécessaire.

### 6\. Équivalents AWS

| OpenStack      | AWS                                      |
| -------------- | ---------------------------------------- |
| Neutron        | Amazon VPC                               |
| Network        | VPC ou réseau logique, selon le contexte |
| Subnet         | Subnet                                   |
| Router         | Routage VPC et composants associés       |
| Security Group | EC2 Security Group                       |
| Floating IP    | Elastic IP, selon le cas                 |

À retenir : Octavia distribue le trafic entre les serveurs ; Neutron fournit le réseau sur lequel ces ressources communiquent.