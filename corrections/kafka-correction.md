# TP : Correction Kafka

L'objectif de ce TP est de manipuler des flux de données avec Kafka


## Exercice 1 : Découverte de l’interface Kafka

1. Lancer un service kafka et jupyter-pyspark sur Onyxia. Ouvrir un terminal dans le service jupyter puis lancer la commande suivante pour installer l'interface utilisateur de Kafka

```bash
kubectl run kafka-ui --image provectuslabs/kafka-ui --env DYNAMIC_CONFIG_ENABLED=true --port 8080 && kubectl expose pod/kafka-ui && kubectl create ingress --class onyxia --rule ${KUBERNETES_NAMESPACE}-kafka-ui.user.lab.sspcloud.fr/*=kafka-ui:8080 kafka-ui
```

2. Connecter votre cluster Kafka en renseignant comme serveur `kafka` et le port `9092`


![](../cours/assets/tp-k-1.png)

3. Depuis l'interface, créer un topic nommé `test` avec deux partitions

![](../cours/assets/tp-k-3.png)

4. Créer un message `test1` et `test2` avec pour valeurs `1` dans le topic `test` dans chacune des deux partitions

![](../cours/assets/tp-k-2.png)

## Exercice 2 : Création d’un producer et d’un consumer Kafka en Python

1. Utiliser la bibliothèque `kafka-python-ng` pour créer un *producer* qui envoi les 3 premières lignes du csv [`usagers-2022.csv`](https://www.data.gouv.fr/fr/datasets/r/62c20524-d442-46f5-bfd8-982c59763ec8) sous forme de 3 messages au topic `test`. Il faudra envoyer comme clé l'identifiant de l'accident et comme valeur sa gravité.


```python
from kafka import KafkaProducer
import csv
import pandas as pd

producer = KafkaProducer(
    bootstrap_servers='kafka:9092',
    key_serializer=lambda key: str(key).encode('utf-8'),
    value_serializer=lambda value: str(value).encode('utf-8')
)

df = pd.read_csv("usagers-2022.csv", sep=";")
for n, row in df.iterrows():
    key = row['Num_Acc']
    value = row['grav']
    producer.send("test", key=key, value=value)
    print(f"Message envoyé: Clé={key}, Valeur={value}")
    if n > 3:
        break
```

```
Message envoyé: Clé=202200000001, Valeur=3
Message envoyé: Clé=202200000001, Valeur=1
Message envoyé: Clé=202200000002, Valeur=4
Message envoyé: Clé=202200000002, Valeur=1
Message envoyé: Clé=202200000003, Valeur=1
```

2. Créer ensuite un *consumer* qui lit et affiche les nouveaux messages générés par le producer précédent en commençant par les plus anciens.

```python
from kafka import KafkaConsumer

# Configuration du consumer Kafka
consumer = KafkaConsumer(
    'test',
    bootstrap_servers='kafka:9092',
    auto_offset_reset='earliest',
    key_deserializer=lambda key: key.decode('utf-8'),
    value_deserializer=lambda value: value.decode('utf-8')
)
for message in consumer:
    print(f"Clé={message.key}, Valeur={message.value}")
```
```
Clé=202200000001, Valeur=3
Clé=202200000001, Valeur=1
Clé=202200000002, Valeur=4
Clé=202200000002, Valeur=1
Clé=202200000003, Valeur=1
```


3. Ajouter ensuite de nouvelles lignes du csv dans le topic Kafka et écrire ces lignes dans une collection `usagers-reels` dans MongoDB.

```python
from pymongo import MongoClient

client = MongoClient('mongodb://user:password@mongodb-0.mongodb-headless:27017/defaultdb')
db = client.defaultdb
collection = db['usagers-reels']

# Lecture des nouveaux messages du topic et insertion dans MongoDB
for message in consumer:
    document = {
        'Num_Acc': message.key,
        'grav': message.value
    }
    collection.insert_one(document)
    print(f"Document inséré dans MongoDB: {document}")

```
```
Document inséré dans MongoDB: {'Num_Acc': '202200000001', 'grav': '3', '_id': ObjectId('67403fa7f32ee67ed43c7210')}
Document inséré dans MongoDB: {'Num_Acc': '202200000001', 'grav': '1', '_id': ObjectId('67403fa7f32ee67ed43c7211')}
Document inséré dans MongoDB: {'Num_Acc': '202200000002', 'grav': '4', '_id': ObjectId('67403fa7f32ee67ed43c7212')}
Document inséré dans MongoDB: {'Num_Acc': '202200000002', 'grav': '1', '_id': ObjectId('67403fa7f32ee67ed43c7213')}
Document inséré dans MongoDB: {'Num_Acc': '202200000003', 'grav': '1', '_id': ObjectId('67403fa7f32ee67ed43c7214')}
```

> La documentation est disponible [ici](https://kafka-python.readthedocs.io/en/master/usage.html)

## Exercice 3 : Utiliser Kafka Connect

1. Installer Kafka Connect avec les commandes suivantes :

```bash
kubectl run kafka-connect --image=titigmr/cp-kafka-connect:latest --env=CONNECT_BOOTSTRAP_SERVERS=kafka:9092 --port=8083 --env=CONNECT_REST_PORT=8082 --env=CONNECT_GROUP_ID="connect" --env=CONNECT_CONFIG_STORAGE_TOPIC="connect-config" --env=CONNECT_OFFSET_STORAGE_TOPIC="connect-offsets" --env=CONNECT_STATUS_STORAGE_TOPIC="connect-status" --env=CONNECT_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" --env=CONNECT_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" --env=CONNECT_INTERNAL_KEY_CONVERTER="org.apache.kafka.connect.json.JsonConverter" --env=CONNECT_INTERNAL_VALUE_CONVERTER="org.apache.kafka.connect.json.JsonConverter" --env=CONNECT_REST_ADVERTISED_HOST_NAME="kafka-connect" --env=CONNECT_PLUGIN_PATH=/usr/share/java,/usr/share/confluent-hub-components
```

Puis lancer la commande :

```bash
kubectl expose pod/kakfa-connect
```

2. Dans l'interface utilisateur de Kafka, configurer Kafka-Connect en spécifiant comme serveur : `http://kafka-connect:8083`

3. Kafka Connect possède une API qui permet de le configurer. Lister les connecteurs disponible avec la commande suivante et vérifier que le connecteur MongoDB est présent.

```bash
curl http://kafka-connect:8083/connector-plugins/
```

> Le schéma de l'API est disponible [ici](https://docs.confluent.io/platform/current/connect/references/restapi.html)


4. Lancer un connecteur avec comme nom `mongodb` qui synchronise les nouvelles données de la collection `test`. Utiliser la configuration suivante :

> La documentation est disponible [ici](https://www.mongodb.com/docs/kafka-connector/current/source-connector/)

```json
{
    "connector.class": "com.mongodb.kafka.connect.MongoSourceConnector",
    "connection.uri": "mongodb://<username>:<password>@<host>:<port>/defaultdb",
    "database": "defaultdb",
    "collection": "test",
    "pipeline": "[{\"$match\": {}}]",
    "output.schema.infer.value": "true"
}
```


5. Insérer un nouveau document `{"usager": 1, "grav": 2}` dans la collection `test` et vérifer que le topic Kafka a bien crée le message via l'interface utilisateur