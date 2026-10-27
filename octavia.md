Dans OpenStack, le service qui gère le Load Balancer (répartiteur de charge) est Octavia.

![Red Hat OpenStack Services on OpenShift | 18.0 | Review release notes | Red Hat Documentation](https://images.openai.com/static-rsc-4/gMo4lIV__eIAFVt3VTCaoR-vmrBtdJbilt5bN-moH4DXy0bCRes0NMeSa_HeBLMsTzM5iMQ2PmM6E9-imQhNCszchdgioAE7zs5fi-w1sbjQBbEElyNIs_mdJEsP0-45_0m4t2p3sCGUg1b0sOQ_sTrMqlLdTq8f6FsTiDZ4nm9LINidaedRhsUnTV1_yqtd?purpose=fullsize)

### Comment fonctionne Octavia ?

Le trafic passe par plusieurs composants :

* Load Balancer : représente le répartiteur de charge et possède une VIP (adresse IP virtuelle).

* Listener : écoute sur un port, par exemple TCP 80 ou HTTPS 443.

* Pool : définit le groupe de serveurs qui recevront les requêtes.

* Members : les serveurs backend, par exemple trois VM Nginx.

* Health Monitor : vérifie que les serveurs sont disponibles et retire temporairement du pool ceux qui sont défaillants.

Exemple :

Client

Octavia — VIP : 192.168.1.100

Listener : TCP 80

Pool HTTP — algorithme ROUND_ROBIN

VM Nginx

192.168.1.11

VM Nginx

192.168.1.12

VM Nginx

192.168.1.13

### Commandes OpenStack CLI

Si Octavia est installé et que tu disposes des droits nécessaires :

```bash
# Créer un load balancer sur un sous-réseau
openstack loadbalancer create \
  --name lb-web \
  --vip-subnet-id subnet-web
```

```bash
# Créer un listener HTTP
openstack loadbalancer listener create \
  --name listener-http \
  --protocol HTTP \
  --protocol-port 80 \
  lb-web
```

```bash
# Créer un pool avec répartition circulaire
openstack loadbalancer pool create \
  --name pool-web \
  --protocol HTTP \
  --lb-algorithm ROUND_ROBIN \
  --listener listener-http
```

Il reste ensuite à ajouter les membres du pool et à configurer le contrôle de santé.

Attention : ces commandes supposent que le sous-réseau existe, que les ressources sont correctement configurées et que le client OpenStack CLI prend en charge les commandes Octavia