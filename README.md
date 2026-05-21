# 🚐 Projet Final Infonuagique - Guide de Déploiement VanLife

Ce dépôt contient le code source complet et l'infrastructure nécessaires pour déployer l'application **VanLife** sur le cloud AWS. L'architecture repose sur **Amazon ECS (Fargate)**, un **Application Load Balancer (ALB)**, et **Amazon RDS**.

> **Note :** Toutes ces commandes sont prévues pour être exécutées dans un terminal **Linux (LXC) ou Windows PowerShell**.

---

## 1. Récupération du Projet (Important !)

Ce projet utilise des **Sous-modules Git (Submodules)** pour séparer l'infrastructure, le frontend et le backend. Lors du clonage ou de la mise à jour, vous devez impérativement initialiser ces sous-modules :

```bash
# Pour cloner le projet pour la première fois :
git clone --recurse-submodules https://github.com/Haroune07/epreuve-finale-h26.git

# Si vous avez déjà cloné le projet, mettez à jour les sous-modules :
git pull origin main
git submodule update --init --recursive
```

---

## 2. Configuration des Accès AWS

Avant de pouvoir utiliser Terraform, vous devez authentifier votre terminal avec votre compte **AWS Academy**.

1. Allez sur Vocareum, cliquez sur **"AWS Details"**, puis sur **"Show"**.
2. Copiez les clés d'accès et collez-les dans votre terminal.

**Format pour Linux / Mac (Bash) :**
```bash
export AWS_ACCESS_KEY_ID="VOTRE_ACCESS_KEY_ICI"
export AWS_SECRET_ACCESS_KEY="VOTRE_SECRET_KEY_ICI"
export AWS_SESSION_TOKEN="VOTRE_SESSION_TOKEN_ICI"
```

---

## 3. Déploiement Automatisé (Terraform)

C'est ici que la magie opère. L'infrastructure complète (VPC sécurisé, Base de données MySQL privée, Load Balancer et Cluster ECS) sera créée dans la région **N. Virginia (`us-east-1`)**.

```bash
# 1. Se déplacer dans le dossier de l'infrastructure
cd terraform

# 2. Initialiser Terraform (Téléchargement des plugins AWS)
terraform init

# 3. Lancer le déploiement sur AWS
# (Cette opération prend environ 5 minutes, le temps que la base RDS s'installe)
terraform apply -auto-approve
```

---

## 4. Accéder à l'Application

L'application est servie de manière hautement disponible derrière un *Application Load Balancer*. 

Une fois la commande `terraform apply` terminée, vous verrez un bloc **Outputs** s'afficher dans votre terminal. Il ressemblera à ceci :

```text
Outputs:

alb_dns_name = "vanlife-alb-2471001-xxxxxxxxx.us-east-1.elb.amazonaws.com"
application_url = "http://vanlife-alb-2471001-xxxxxxxxx.us-east-1.elb.amazonaws.com"
rds_endpoint = "vanlife-db-2471001.xxxx.us-east-1.rds.amazonaws.com"
```

👉 **Pour voir le site en ligne :** Cliquez simplement sur le lien `application_url` fourni par Terraform ! (Laissez environ 1 minute au conteneur ECS le temps de démarrer et de passer les tests de santé de l'ALB).

---

## 5. Nettoyage de l'Infrastructure (Destruction)

**⚠️ CRITIQUE :** AWS Academy facture à la minute pour RDS et ECS Fargate. Pour ne pas gaspiller vos crédits, **détruisez toujours votre infrastructure** lorsque vous avez terminé.

```bash
# Assurez-vous d'être dans le dossier /terraform
terraform destroy -auto-approve
```
*Attendez le message `Destroy complete!` pour confirmer que tout a bien été supprimé et que vous ne serez plus facturé.*
