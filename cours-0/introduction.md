---
marp: true
markdown.marp.enableHtml: true
footer: 'Master 2 IMSD - 2024'
theme: default
---
<style>

section 
  background: white;


img[alt~="center"] 
  display: block;
  margin: 0 auto;

blockquote 
  background: #ffedcc;
  border-left: 10px solid #d1bf9d;
  margin: 1.5em 10px;
  padding: 0.5em 10px;

blockquote:before
  content: unset;

blockquote:after
  content: unset;

</style>


# OLAP, NoSQL et systèmes distribués

**Thierry GAMEIRO MARTINS**

---
<!-- paginate: true -->

# 1. Plan du cours


1. Introduction et prise en main d'Onyxia
2. Les systèmes distribués : Hadoop et Kubernetes
3. Le stockage des données en NoSQL
4. Le passage en production
5. L'orchestration et l'automatisation


---
# 2. Modalité d'évaluation (1/4)


## Objectif
Présenter par groupe (de 4 ou 5 personnes) un POC (*Proof of Concept*) d'une chaîne de traitement de la données comme solution pour un client


- **Présentation des travaux** : exposé de 15 minutes 
- **Questions/réponses** : 10 minutes de question individuelles
- **Livrable** : Powerpoint détaillant votre solution à envoyer avant le jour de la présentation


---

<!--footer: ""-->

# 2. Modalité d'évaluation (2/4)



## Points à détailler lors de la présentation

- Présentation du sujet choisi 
- Choix de la problématique et comment la solution y répond **avec réalisme**
- Les différentes étapes de traitements de la donnée (pré-processing, collecte, valorisation ou d'exposition de la donnée) 
- Présenter les différentes briques techniques et fonctionnelles choisies
- Etude comparative des technologies considérées pour chacune des étapes (extraction, stockage, traitement, valorisation, etc.) 


---

# 2. Modalité d'évaluation (3/4)


## Choix des sujets

- 6 sujets sont proposés
- Possibilité de proposer son propre sujet (à valider avant)
- Date limite de proposition de sujet

---

# 2. Modalité d'évaluation (4/4)

- Analyse de tweets https://tinyurl.com/y5v4j8f6
- Parsing de données IOT (Airparif) https://tinyurl.com/y6xdod7p
- Analyse des données de disponibilité des vélib à Paris https://tinyurl.com/yykzr6hv
- Analyse des données de subventions aux associations parisiennes https://tinyurl.com/y5be9ynp
- Analyse et comparaison des trajets uber / Taxi à New York https://tinyurl.com/y29k2jco
- Système de recommandation de Films https://tinyurl.com/v2oynmf

---

# 3. Prise en main d'Onyxia (1/2)

**Onyxia** est une application web qui permet aux data scientists d'accéder à un environnement de travail distant

- Permet d'explorer des données 
- De lancer des services (éditeur de code, base de données, outils d'orchestration, etc.)
- Se base sur Kubernetes et permet de s'y initier
- Fournit des formations

![bg right:30% fit](./assets/Onyxia.png) 


> Lien pour se connecter disponible sur : https://datalab.sspcloud.fr

---

# 3. Prise en main d'Onyxia (1/2)



