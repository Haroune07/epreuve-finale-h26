# Projet Final Infonuagique - Guide de Déploiement VanLife

Ce fichier contient toutes les commandes utiles pour construire, déployer et détruire l'infrastructure AWS et les conteneurs Docker de l'application VanLife.

**Note :** Toutes ces commandes sont prévues pour être exécutées dans un terminal **Windows PowerShell**.

---

## 1. Conteneurisation (Docker)

Ces commandes sont utiles uniquement si vous avez modifié le code source ou les `Dockerfile` et que vous devez mettre à jour les images sur Docker Hub.

**Pour le Frontend :**
```powershell
cd "c:\Users\Haroune Belhachani\Downloads\epreuve-finale-h26\414-vanlife-frontend"
docker build -t haroune07/vanlife-frontend:latest .
docker push haroune07/vanlife-frontend:latest
```

**Pour l'API :**
```powershell
cd "c:\Users\Haroune Belhachani\Downloads\epreuve-finale-h26\414-vanlife-api"
docker build -t haroune07/vanlife-api:latest .
docker push haroune07/vanlife-api:latest
```

---

## 2. Configuration des accès AWS

Avant toute opération Terraform, vous devez lier votre terminal à votre compte AWS Academy.
Dans la plateforme Vocareum, cliquez sur **"AWS Details"**, puis sur **"Show"**.
Copiez-collez vos clés en utilisant le format PowerShell ci-dessous :

```powershell
$env:AWS_ACCESS_KEY_ID="VOTRE_ACCESS_KEY_ICI"
$env:AWS_SECRET_ACCESS_KEY="VOTRE_SECRET_KEY_ICI"
$env:AWS_SESSION_TOKEN="VOTRE_SESSION_TOKEN_ICI"
```

---

## 3. Déploiement avec Terraform

C'est ici que l'infrastructure complète (VPC, Cluster ECS, Base de données RDS MySQL) est créée.

```powershell
# 1. Se déplacer dans le dossier de l'infrastructure
cd "c:\Users\Haroune Belhachani\Downloads\epreuve-finale-h26\terraform"

# 2. Initialiser Terraform (Nécessaire une seule fois)
terraform init

# 3. Vérifier les changements prévus (Optionnel)
terraform plan

# 4. Lancer le déploiement (L'opération prend 3 à 5 minutes pour la création de la BD)
terraform apply -auto-approve
```

---

## 4. Accéder à l'application (Obtenir l'IP)

Puisque le cluster ECS (Fargate) démarre de manière asynchrone, l'adresse IP publique n'est pas connue immédiatement au moment où Terraform termine. 

Pour récupérer facilement la nouvelle adresse IP publique de votre site web, lancez ce script depuis le dossier `terraform` :

```powershell
# Assurez-vous d'être dans le dossier \terraform
python get_ip_wait.py
```
> Le script affichera directement l'adresse IP (ex: `http://35.89.x.x`). Cliquez dessus pour voir votre application en ligne !

---

## 5. Nettoyage de l'infrastructure (Destruction)

**⚠️ CRITIQUE :** AWS Academy facture à la minute pour les instances RDS et ECS Fargate. Dès que votre présentation/vérification est terminée, vous devez détruire l'infrastructure pour ne pas épuiser vos crédits.

```powershell
# Assurez-vous d'être dans le dossier \terraform
terraform destroy -auto-approve
```
> Attendez bien la fin du processus (message `Destroy complete!`) pour être sûr que tout a été supprimé.
