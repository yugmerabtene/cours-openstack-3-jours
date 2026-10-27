### OpenStack Nova — Le service de calcul

![Architecture Guide | Red Hat Enterprise Linux OpenStack Platform | 7 | Red Hat Documentation](https://images.openai.com/static-rsc-4/C3wuZz5cRrPqiBN07VsSOZ3m4mHqJUjHa8MUyL_DqtKtnmMGJDXcvqwp9OLluKNrwGsgrgEjUxN4myhGPuVjwCvEqE5gvp77IXKIBRpnLafYeZoSjoIFlZxRBaM-QIxww7uX1aVkNqMbiJwbtuTklLmWXnn4q8StaD5D0dBNYL0otyhgclH3aWgyUBWY2aXL?purpose=fullsize)

Après Octavia (Load Balancer) et Neutron (réseau), découvrons Nova, le service qui permet de créer, démarrer, arrêter et gérer les machines virtuelles dans OpenStack.

Son équivalent principal dans AWS est Amazon EC2.

### 1\. À quoi sert Nova ?

Nova gère le cycle de vie des instances virtuelles :

* Créer une machine virtuelle (VM).

* Choisir le nombre de vCPU et la quantité de RAM.

* Démarrer, arrêter, redémarrer et supprimer une VM.

* Choisir l'image système et le réseau de la machine.

* Planifier sur quel serveur physique la VM sera exécutée.

* Gérer les migrations et certaines opérations de maintenance, selon la configuration.

Nova ne virtualise pas directement le processeur lui-même : il orchestre les ressources de calcul en s'appuyant notamment sur un hyperviseur comme KVM.

### 2\. Architecture de Nova

Utilisateur / OpenStack CLI / Horizon

Nova API et Scheduler

Reçoit la demande et choisit un nœud de calcul

Compute Node 1

KVM / QEMU

VM Web 1

VM BDD

Compute Node 2

KVM / QEMU

VM Web 2

VM Web 3

Les principaux composants :

| Composant | Rôle                                                          |
| --------- | ------------------------------------------------------------- |
| Nova API  | Reçoit les demandes de création et de gestion des VM          |
| Scheduler | Sélectionne un nœud de calcul approprié                       |
| Compute   | Pilote les instances sur les serveurs physiques               |
| Conductor | Coordonne certaines opérations et l'accès aux données de Nova |
| Placement | Suit les ressources disponibles et leur allocation            |

### 3\. Créer une machine virtuelle avec Nova

En pratique, tu utilises le client OpenStack CLI.

### Étape 1 — Vérifier les ressources disponibles

```bash
# Images système disponibles
openstack image list

# Types de machines disponibles
openstack flavor list

# Réseaux disponibles
openstack network list

# Clés SSH disponibles
openstack keypair list
```

### Étape 2 — Créer une clé SSH

```bash
openstack keypair create --private-key ~/.ssh/cle-openstack cle-openstack

chmod 600 ~/.ssh/cle-openstack
```

Conserve cette clé privée en lieu sûr : elle peut permettre l'accès à tes machines.

### Étape 3 — Créer une VM

```bash
openstack server create \
  --image Ubuntu-24.04 \
  --flavor m1.small \
  --network reseau-prod \
  --key-name cle-openstack \
  vm-web-01
```

Les noms `Ubuntu-24.04`, `m1.small` et `reseau-prod` sont des exemples : ils doivent correspondre aux ressources réellement disponibles dans ton cloud.

### Étape 4 — Vérifier l'état de la VM

```bash
openstack server list

openstack server show vm-web-01
```

Tu peux ensuite gérer son cycle de vie :

```bash
# Arrêter la VM
openstack server stop vm-web-01

# Redémarrer la VM
openstack server start vm-web-01

# Redémarrer le système invité
openstack server reboot vm-web-01
```

### 4\. Les Flavors : dimensionner les VM

Un flavor définit les ressources de calcul proposées à une instance.

| Flavor d'exemple | vCPU | RAM  | Usage indicatif            |
| ---------------- | ---- | ---- | -------------------------- |
| m1.small         | 1    | 2 Go | Petit serveur Web          |
| m1.medium        | 2    | 4 Go | Application standard       |
| m1.large         | 4    | 8 Go | Application plus exigeante |

Ces valeurs sont illustratives : l'administrateur du cloud définit les flavors disponibles.

### 5\. Comment Nova fonctionne avec les autres services ?

Prenons une application Web répartie sur deux VM.

Nova

Crée les deux VM Web.

Neutron

Connecte les VM au réseau privé.

Octavia

Distribue les requêtes entre les deux VM.

Heat

Automatise le déploiement de l'ensemble.

### 6\. Équivalences AWS

| OpenStack     | AWS                                        |
| ------------- | ------------------------------------------ |
| Nova          | Amazon EC2                                 |
| Instance Nova | Instance EC2                               |
| Flavor        | Instance type, par exemple t3.small        |
| Image Glance  | AMI                                        |
| Compute Node  | Infrastructure physique sous-jacente d'EC2 |

À retenir : Nova gère les machines virtuelles, mais ne stocke pas à lui seul tous leurs disques persistants et ne gère pas leur réseau à lui seul.