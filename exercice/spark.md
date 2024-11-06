# TP : Spark

L'objectif de ce TP est de manipuler des données avec Spark


## Exercice 1 : Lancer un job spark

1. Lancer un jupyter notebook et lancer une SparkSession nommé `spark` en utilisant la configuration `local` et récupérer le sparkContext :

```python
spark = (SparkSession
         .builder
         .appName("TP")
         .master("local[5]")
         .getOrCreate()
        )
sc = spark.sparkContext
```

> La valeur 5 dénote que Spark utilisera 5 threads pour la parallélisation

2. Lancer ce script qui calcule la valeur de Pi avec la méthode de Monte Carlo et consulter le job spark via la spark-ui (le lien est disponible dans la note du service Onyxia)

```python
import random

NUM_SAMPLES = 10000000

def inside(p):
    x, y = random.random(), random.random()
    return x*x + y*y < 1

count = sc.parallelize(range(0, NUM_SAMPLES)) \
             .filter(inside).count()
print ("Pi is roughly %f" % (4.0 * count / NUM_SAMPLES))
```

3. Eteindre la session spark avec `spark.stop()`


## Exercice 2 : Explorer des données avec Spark

1. Créer une nouvelle SparkSession sans préciser le ressource manager (`kubernetes` est par défaut dans Onyxia). Lire ensuite fichier `s3a://projet-spark-lab/diffusion/formation/data/sirene/sirene.csv`

2. Afficher le schéma du `csv` avec la méthode `printSchema`.
3. En utilisant la syntaxe DataFrame :
   - Calculer le nombre total d’entreprises dans le jeu de données (nombre de siret unique)
   - Afficher les 5 premiers établissements dont les `activitePrincipaleEtablissement` commence par `47`
   - Afficher les 10 secteurs d'activités (code NAF) les plus représentés


## Exercice 3 : Ecrire des données avec Spark

1. Créer la table suivante dans un service postgresql :

```sql
CREATE TABLE sirene (
    siret VARCHAR,
    siren VARCHAR,
    denominationUniteLegale VARCHAR,
    activitePrincipaleUniteLegale VARCHAR,
    trancheEffectifsEtablissement VARCHAR,
    dateCreationUniteLegale DATE
);
```

2. Lancer une importation des données avec la méthode `DataFrame.write.jdbc` dans la table créée précédemment

> Un exemple est disponible [ici](https://spark.apache.org/docs/3.5.3/sql-data-sources-jdbc.html)