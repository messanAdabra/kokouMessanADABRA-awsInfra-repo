
######## AWS CloudFormation Template: S3-Triggered Lambda with DynamoDB & EC2 Setup ##########

# Description

Ce template AWS CloudFormation crée une architecture complète pour traiter automatiquement les fichiers déposés dans un bucket S3 et stocker les metadonnées dns une Base DynmoDB:

-Un **bucket S3** qui déclenche une **fonction Lambda**
-Une **fonction Lambda** en Python qui extrait les métadonnées et les stocke dans une **table DynamoDB**
-Une **instance EC2** avec configuration personnalisée pour les opérations CLI AWS
-Un **groupe de sécurité** pour accès SSH

---

# Composants AWS créés

- S3Bucket ==> Stocke les fichiers. Déclenche Lambda à chaque upload (`s3:ObjectCreated:*`) 
- LambdaFunction ==> Fonction Python qui lit l’événement S3 et insère le nom du fichier et du bucket dans DynamoDB
- DynamoDBTable ==> Table NoSQL pour stocker les métadonnées des fichiers
- EC2 Instance ==> Machine virtuelle Amazon Linux Ubuntu avec AWS CLI préinstallé
- Security Group ==> Autorise les connexions SSH d'un hote
- IAM Permission ==> Autorise S3 à invoquer la Lambda via `AWS::Lambda::Permission`

---

# Paramètres du template 

EnvName ==> Nom d’environnement (ex: `dev`, `prod`). Utilisé pour nommer les ressources.
VPcId ==> ID du VPC où placer l’instance EC2 et le groupe de sécurité2E


# Focus sur la fonction Lambda

A chaque nouvel objet créé dans le bucket S3 (`${EnvName}-file-metadata-bucket`), cette fonction :
1. **Récupère** le nom du bucket et la clé (chemin) de l’objet.
2. **Formate** ces informations comme des éléments DynamoDB.
3. **Insère** un enregistrement `{ FileName, BucketName }` dans la table DynamoDB dédiée.


