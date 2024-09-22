TP : Gestion d'une base de données de véhicules avec MongoDB

Objectifs
Se familiariser avec MongoDB et les opérations CRUD en manipulant des données sur les véhicules.
Effectuer des recherches complexes avec des agrégations.
Prérequis
MongoDB installé et configuré.
MongoDB Shell ou MongoDB Compass installé.
Partie 1 : Création de la base de données et insertion de données
Créer une base de données nommée garage :
Ouvrir le terminal MongoDB et exécuter :
javascript
Copier le code
use garage
Créer une collection nommée vehicules :
Ajouter un véhicule dans la collection :
javascript
Copier le code
db.vehicules.insertOne({
    "marque": "Tesla",
    "modele": "Model 3",
    "annee": 2021,
    "type": "Electrique",
    "couleur": "Blanc",
    "kilometrage": 15000,
    "prix": 45000
});
Ajouter plusieurs véhicules :
Insérer ces véhicules dans la collection :
javascript
Copier le code
db.vehicules.insertMany([
    {
        "marque": "Renault",
        "modele": "Clio",
        "annee": 2019,
        "type": "Essence",
        "couleur": "Rouge",
        "kilometrage": 30000,
        "prix": 12000
    },
    {
        "marque": "Peugeot",
        "modele": "308",
        "annee": 2020,
        "type": "Diesel",
        "couleur": "Bleu",
        "kilometrage": 20000,
        "prix": 18000
    },
    {
        "marque": "Tesla",
        "modele": "Model S",
        "annee": 2022,
        "type": "Electrique",
        "couleur": "Noir",
        "kilometrage": 5000,
        "prix": 80000
    }
]);
Partie 2 : Requêtes de base (Lecture)
Lister tous les véhicules :
javascript
Copier le code
db.vehicules.find().pretty();
Rechercher les véhicules de la marque "Tesla" :
javascript
Copier le code
db.vehicules.find({ "marque": "Tesla" });
Trouver les véhicules dont le kilométrage est inférieur à 20 000 km :
javascript
Copier le code
db.vehicules.find({ "kilometrage": { $lt: 20000 } });
Rechercher les véhicules dont le prix est compris entre 10 000 € et 50 000 € :
javascript
Copier le code
db.vehicules.find({ "prix": { $gte: 10000, $lte: 50000 } });
Partie 3 : Mise à jour des données
Mettre à jour le prix de la "Renault Clio" pour qu'il passe à 13 000 € :
javascript
Copier le code
db.vehicules.updateOne(
    { "modele": "Clio" },
    { $set: { "prix": 13000 } }
);
Ajouter un champ disponible à tous les véhicules, avec la valeur true :
javascript
Copier le code
db.vehicules.updateMany(
    {},
    { $set: { "disponible": true } }
);
Partie 4 : Suppression des données
Supprimer le véhicule "Peugeot 308" :
javascript
Copier le code
db.vehicules.deleteOne({ "modele": "308" });
Supprimer tous les véhicules de la marque "Tesla" :
javascript
Copier le code
db.vehicules.deleteMany({ "marque": "Tesla" });
Partie 5 : Agrégation
Trouver le prix moyen des véhicules par type (essence, diesel, électrique) :
javascript
Copier le code
db.vehicules.aggregate([
    { $group: { _id: "$type", prix_moyen: { $avg: "$prix" } } }
]);
Trouver le nombre total de véhicules disponibles par couleur :
javascript
Copier le code
db.vehicules.aggregate([
    { $group: { _id: "$couleur", total: { $sum: 1 } } }
]);
Partie 6 : Exercice pratique
Exercice 1 : Ajouter 3 nouveaux véhicules de votre choix avec des données différentes.
Exercice 2 : Trouver les véhicules de type "Electrique" ayant un kilométrage inférieur à 10 000 km et un prix inférieur à 50 000 €.