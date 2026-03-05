Projet GreenAndCoop : Pipeline de Données Météo

# ETAPE 1 : 

# Contexte
GreenAndCoop, fournisseur coopératif d’électricité renouvelable, souhaite fournir des données météorologiques fiables aux Data Scientists pour la prévision de consommation.

# Objectif
Mettre en place un pipeline complet pour :

1. Collecter les données depuis des sources Excel et JSON.

2. Centraliser les fichiers dans un bucket S3.

3. Transformer les données pour les rendre compatibles avec MongoDB.

4. Stocker et contrôler l’intégrité des données dans MongoDB.

## Étape 1 – Extraction des données
- Récupération des données via Airbyte depuis les sources locales ou distantes.
- Chargement dans un bucket S3 pour centralisation et archivage.
- Objectif : automatiser la collecte et garantir un accès stable aux données.

## Étape 2 – Transformation des données
Normalisation des formats :
- Vitesse du vent → float (mph)
- Température et pression → formats numériques
- Valeurs manquantes → NaN si nécessaire
Structure des documents MongoDB :
- InfoClimat → données brutes horaires par station
- WeatherIchtegem et WeatherLaMadeleine → données nettoyées et typées
- StationsMetadata → informations sur chaque station (ID, localisation, logiciel, matériel)
Typage :
- Numérique : float64 / int64
- Texte ou objets complexes : object

## Étape 3 – Insertion dans MongoDB

Base : GreenAndCoop
Collections créées :
- InfoClimat
- WeatherIchtegem
- WeatherLaMadeleine
- StationsMetadata

Tests d’intégrité appliqués :
- Valeurs manquantes
- Doublons
- Types des colonnes
Résultat : toutes les collections sont insérées correctement.

## Étape 4 – Automatisation

Le script Python peut être planifié pour exécuter :

- Transformations
- Tests d’intégrité avant et après insertion

## Étape 5 – Pré-requis et installation

1. Installer MongoDB localement.
2. Lancer MongoDB :

mongod --dbpath /data/db


3. Installer les dépendances Python :

pip install -r requirements.txt


4. Exécuter le script :

python transform_to_mongo.py


5. Vérifier les collections :

use GreenAndCoop
show collections
db.WeatherIchtegem.findOne()

## Notes importantes

Certaines valeurs météo manquantes sont normales (mesures non relevées).

Les colonnes contenant des listes ou objets dans InfoClimat ne sont pas testées pour les doublons.

Le script peut être étendu pour des traitements supplémentaires (conversion automatique en unités SI, etc.).

# ETAPE 2 

# Migration des données météo vers MongoDB

## Objectif
Ce script permet de transférer les données météo issues de fichiers JSONL stockés sur S3 vers une base de données MongoDB Atlas.

## Logique de la migration
Le processus suit ces étapes :

1. **Connexion au bucket S3** : récupération des fichiers JSONL contenant les données météorologiques.
2. **Lecture et transformation des fichiers** : extraction des données `_airbyte_data` et conversion en DataFrame pandas.
3. **Connexion à MongoDB Atlas** : établissement de la connexion sécurisée avec certificat `certifi`.
4. **Insertion des données** : suppression éventuelle des données existantes dans la collection `WeatherData`, puis insertion de toutes les données collectées.
5. **Contrôle qualité** : le script permet de vérifier que toutes les lignes ont été insérées correctement dans la base.

## Fonctionnement du script
- **Cellule 1** : import des librairies nécessaires.
- **Cellule 2** : configuration de l’accès AWS et connexion au bucket S3.
- **Cellule 3** : définition de la fonction pour lire les fichiers JSONL depuis S3.
- **Cellule 4** : définition des chemins des fichiers S3.
- **Cellule 5** : lecture des fichiers et création des DataFrames.
- **Cellule 6** : connexion à MongoDB Atlas.
- **Cellule 7** : concaténation des DataFrames, suppression des anciennes données et insertion dans MongoDB.
- **cellule 8** : Vérification / Qualité des données

