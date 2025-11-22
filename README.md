
# ☁️ Mini-Projet 4 : Automatisation Cloud-Native (K8s + Ansible)



---

## 📖 Présentation du Projet

Ce projet illustre la transition des architectures réseaux classiques vers les **architectures Cloud-Native**.

L'objectif principal est de déployer une **Fonction Réseau Conteneurisée (CNF)** agissant comme un **Pare-feu (iptables)**, au sein d'un cluster **Kubernetes**. L'ensemble du déploiement est automatisé grâce à **Ansible**, simulant ainsi un environnement de production moderne (**Infrastructure as Code**).

---

## 🎯 Objectifs

* **Infrastructure :** Mise en place d'un cluster Kubernetes léger avec **K3s**.
* **Réseau :** Déploiement d'une CNF capable de filtrer le trafic (**iptables**).
* **Automatisation :** Écriture de playbooks **Ansible** pour gérer le cycle de vie de l'application.
* **Validation :** Test de pénétration et blocage de protocoles (**ICMP/Ping**).

---

## 🏗️ Architecture Technique

Le projet repose sur une **Machine Virtuelle Linux (Ubuntu)** qui héberge l'ensemble de la stack :

| Composant | Technologie | Rôle |
| :--- | :--- | :--- |
| **Host** | Ubuntu 22.04 | Système hôte pour la virtualisation. |
| **Orchestrateur** | K3s | Distribution Kubernetes optimisée pour l'Edge. |
| **CNF** | Ubuntu Pod | Conteneur privilégié exécutant iptables. |
| **Automation** | Ansible | Outil d'automatisation sans agent. |
| **Connectivité** | CNI | Gestion du réseau inter-pods. |

---

## 🛠️ Pré-requis

Avant de démarrer, assurez-vous d'avoir les outils suivants installés sur votre machine (VM Ubuntu) :

### Mise à jour et Outils de base

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl git python3 python3-pip
````

### Cluster Kubernetes (K3s)

```bash
curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -
```

### Ansible et dépendances Python

```bash
sudo apt install -y ansible
pip3 install kubernetes  # Indispensable pour le module k8s d'Ansible
```

-----

## 🚀 Installation et Utilisation

### 1\. Cloner le projet

Récupérez le code source depuis GitHub :

```bash
git clone https://github.com/hodaifa-ech/Automatisation-Cloud-Native.git
cd Automatisation-Cloud-Native
```

### 2\. Configuration de l'accès Kubernetes

Configurez les droits pour que votre utilisateur puisse interagir avec le cluster sans `sudo` :

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(whoami):$(whoami) ~/.kube/config
```

### 3\. Lancer le déploiement automatisé

Exécutez le playbook Ansible. Ce script va créer le namespace `nfv` et déployer le pare-feu conteneurisé.

```bash
ansible-playbook deploy.yml
```

-----

## 🧪 Tests et Vérification

### 1\. Vérifier l'état des Pods

Assurez-vous que le pod pare-feu est en statut `Running` :

```bash
kubectl get pods -n nfv
```

### 2\. Activer le Pare-feu (Simulation)

Par défaut, tout le trafic passe. Nous allons injecter une règle pour bloquer le **Ping (ICMP)** au sein du conteneur CNF.

**Récupération automatique du nom du pod**

```bash
POD_NAME=$(kubectl get pods -n nfv -l app=firewall -o jsonpath="{.items[0].metadata.name}")
```

**Injection de la règle iptables dans le conteneur**

```bash
kubectl exec -it $POD_NAME -n nfv -- iptables -A INPUT -p icmp -j DROP
```

### 3\. Tester la connectivité

Essayez de pinger le pod. Si le pare-feu fonctionne, vous devriez avoir **100% de perte de paquets**.

**Récupération de l'IP du pod**

```bash
POD_IP=$(kubectl get pod $POD_NAME -n nfv --template '{{.status.podIP}}')
```

**Test de ping**

```bash
ping -c 3 $POD_IP
```

-----

## 📂 Structure des Fichiers

```
.
├── README.md           # Documentation complète du projet
├── deploy.yml          # Playbook Ansible pour l'automatisation
└── firewall-cnf.yaml   # Manifeste Kubernetes (Déploiement du Pod)
```

-----

## 👥 Auteurs

Réalisé par : 
👨‍💻 **Hodaifa ECHFFANI**
👨‍💻 **Mohamed Amine BAHASSOU**



