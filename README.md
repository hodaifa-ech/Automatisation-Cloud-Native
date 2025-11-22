☁️ Mini-Projet 4: Cloud-Native Automation (K8s + Ansible)

Module : Big Data | Matière : Virtualisation & Cloud Computing

Université : Université Abdelmalek Essaadi - FST Tanger

Filière : LSI (Logiciels et Systèmes Intelligents)

📖 Aperçu du Projet

Ce projet illustre la transition de la virtualisation des fonctions réseau classique (NFV) vers les fonctions réseau Cloud-Native (CNF).

L'objectif est de déployer un Pare-feu conteneurisé au sein d'un cluster Kubernetes, le tout automatisé via Ansible. Cette architecture simule un environnement moderne de type Edge Computing où les fonctions réseau sont agiles, scalables et gérées par le code (Infrastructure-as-Code).

🎯 Objectifs Clés

Déployer un cluster Kubernetes léger (K3s).

Créer une CNF (Container Network Function) capable de filtrer les paquets.

Automatiser le déploiement complet avec Ansible.

Valider le fonctionnement du pare-feu en bloquant le protocole ICMP (Ping).

🏗️ Architecture

Le projet s'exécute sur une Machine Virtuelle Linux (Ubuntu) hébergeant la stack suivante :

Composant

Technologie

Description

Hôte

Ubuntu 22.04

Système d'exploitation de base.

Orchestrateur

K3s

Distribution Kubernetes légère pour l'Edge/IoT.

CNF

Ubuntu Pod + iptables

Le conteneur agissant comme Pare-feu.

Automatisation

Ansible

Gère le cycle de vie via des Playbooks.

Réseau

CNI (Flannel/Calico)

Gère la communication entre les Pods.

🛠️ Pré-requis

Avant de lancer l'automatisation, assurez-vous d'avoir installé les éléments suivants sur votre machine Linux :

Outils Système :

sudo apt update && sudo apt upgrade -y
sudo apt install -y curl git python3 python3-pip


Kubernetes (K3s) :

curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -


Ansible & Librairies Python :

sudo apt install -y ansible
pip3 install kubernetes  # Requis pour le module K8s d'Ansible


🚀 Installation & Utilisation

1. Cloner le dépôt

git clone [https://github.com/VOTRE_USERNAME/mini-projet-cloud-native.git](https://github.com/VOTRE_USERNAME/mini-projet-cloud-native.git)
cd mini-projet-cloud-native


2. Configurer l'environnement

Assurez-vous que votre utilisateur standard a accès à la configuration Kubernetes :

mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config


3. Lancer l'automatisation

Exécutez le playbook Ansible pour déployer le namespace et la CNF pare-feu :

ansible-playbook deploy.yml


Note : Le playbook utilise une connexion localhost pour communiquer avec le cluster K3s local.

🧪 Tests & Vérification

1. Vérifier le déploiement

Vérifiez que le Pod est bien en cours d'exécution dans le namespace nfv :

kubectl get pods -n nfv


2. Simuler une règle Pare-feu (Bloquer le Ping)

Par défaut, le conteneur laisse passer le trafic. Bloquons le protocole ICMP pour tester la capacité du pare-feu :

# Récupérer le nom du Pod
POD_NAME=$(kubectl get pods -n nfv -l app=firewall -o jsonpath="{.items[0].metadata.name}")

# Injecter la règle iptables
kubectl exec -it $POD_NAME -n nfv -- iptables -A INPUT -p icmp -j DROP


3. Tester la connectivité

Récupérez l'IP du Pod et essayez de le pinger :

POD_IP=$(kubectl get pod $POD_NAME -n nfv --template '{{.status.podIP}}')
ping -c 3 $POD_IP


Résultat attendu : 100% packet loss (Le pare-feu fonctionne correctement).

📂 Structure du Projet

.
├── README.md           # Documentation du projet
├── deploy.yml          # Playbook Ansible d'automatisation
└── firewall-cnf.yaml   # Manifeste Kubernetes (Définition du Déploiement)


👥 Auteurs & Crédits

Réalisé par : 
👨‍💻 Hodaifa ECHFFANI
👨‍💻 Mohamed Amine BAHASSOU


