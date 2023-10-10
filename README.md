# CREATION DES VARIABLES

A l’instar d’autres langages de programmation, terraform permet d’utiliser des variables pour indiquer la valeur d’un paramètre.

Une variable se définit de la manière suivante :

```
variable "ma_variable" {}
```

Il est possible de d’ajouter des informations supplémentaires dans la définition d’une variable comme :
- Une description
- Un type
- Une valeur par défaut
- D’autres options avancées (sensitive, nullable, validation, etc…)

Une fois votre variable définie, vous pouvez lui donner une valeur dans un fichier. Ce fichier doit avoir l’extension .tfvars :

```
ma_variable = "ma-superbe-valeur"
```

Par défaut, lors de l’exécution de la commande **terraform apply**, terraform recherche automatiquement dans le répertoire courant la présence d’un fichier terraform.tfvars et y charge les valeurs qui y sont définies.

Si votre fichier .tfvars a un autre nom, il faudra alors l’indiquer au travers du paramètre **-var-file** :

```
terraform apply -var-file="developpement.tfvars"
```

Une fois votre variable valorisée, vous pouvez l’utiliser dans votre code pour indiquer la valeur du paramètre d’une ressource :

**terraform.tfvars**

```
s3_bucket_name = "bucket-the-nuum-factory"
```

**main.tf**

```
variable "s3_bucket_name" {
  description = "Le nom de mon bucket S3"
  type = string
}

resource "aws_s3_bucket" "mon_bucket_s3" {
  bucket = var.s3_bucket_name
}
```

Le bucket créé s’appellera bucket-the-nuum-factory.

Enfin, il est possible d’utiliser la valeur d’une variable au sein d’une chaîne de caractère. C'est l'interpolation :

```
variable "s3_bucket_name" {
  description = "Le nom de mon bucket S3"
  type = string
}

resource "aws_s3_bucket" "mon_bucket_s3" {
  bucket = "${var.s3_bucket_name}-01"
}
```

Le bucket créé s’appellera bucket-the-nuum-factory-01.

## Déclaration des variables

En vous appuyant sur l’exemple ci-dessus et sur la [documentation officielle de terraform](https://developer.hashicorp.com/terraform/language/values/variables), créez les variables suivantes dans votre fichier **main.tf**, et définissez leur valeur dans un fichier nommé **developpement.tfvars** que vous créerez dans votre répertoire de travail (remplacez les valeurs en **gras** par la bonne valeur) :

| Nom           | Type         | Valeur                                                                                                 |
|---------------|--------------|--------------------------------------------------------------------------------------------------------|
| vpc           | string       | "L’ID du VPC nuumfactory-vpc"                                                                          |
| environnement | string       | "dev"                                                                                                  |
| digit         | string       | "XX"                                                                                                   |
| elb_subnets   | list(string) | ["L’ID du sous-réseau nuumfactory-public-subnet-1"," L’ID du sous-réseau nuumfactory-public-subnet-2"] |
| ec2_subnet    | string       | "L’ID du sous-réseau nuumfactory-public-subnet-1 ou nuumfactory-public-subnet-2"                       |
| ec2_type      | string       | "t2.micro"                                                                                             |

En plus de ces variables, créez les 3 variables locales suivantes (doc [ici](https://developer.hashicorp.com/terraform/language/values/locals)) :

| Nom         | Type       | Valeur                                            |
|-------------|------------|---------------------------------------------------|
| elb_name    | string     | Le nom de votre load-balancer                     |
| ec2_name    | string     | Le nom de votre EC2                               |
| db_name     | string     | Le nom de votre base de données                   |

Vous trouverez ci-dessous le nom à renseigner pour vos futurs load-balancer, ec2 et base de données :
- Load-balancer : nuumfactory-\<environnement\>-lb-\<digit\>
- EC2 : nuumfactory-\<environnement\>-ec2-\<digit\>
- Base de données : nuumfactory-\<environnement\>-db-\<digit\>

Indice : les valeurs des variables locales interpolent deux des variables globales que vous venez de créer.

Une fois l’ensemble de vos variables déclarées dans votre fichier **main.tf** et valorisées dans votre fichier **developpement.tfvars**, mettez à jour votre fichier main.tf afin que ces variables soient utilisées dans les paramètres de vos ressources.

## Exécution du code terraform

Executez la commande **terraform plan -var-file="developpement.tfvars"** : vous devriez obtenir le message **No changes. Infrastructure is up-to-date.**.

En effet, bien que vous ayez modifier votre code, les ressources déclarées et la valeur de leurs paramètres n’ont pas changé : terraform n’a donc aucune action à effectuer. Si terraform indique qu’il va modifier une ou plusieurs de vos ressources, identifiez la root cause de ce changement non souhaité.

Vous pouvez si vous le souhaitez exécuter le commande **terraform apply -auto-approve -var-file="developpement.tfvars"** pour confirmer que terraform n’effectue aucune action.