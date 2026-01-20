# 🏀 NBA Player Salary Prediction - Machine Learning Project

## Description

Projet de Machine Learning visant à prédire les salaires des joueurs NBA en fonction de leurs statistiques de performance. Ce projet combine la collecte de données via l'API NBA officielle, le preprocessing avancé, le fetaure engineering et la modélisation avec plusieurs algorithmes de régression. 

## Objectif

Développer un modèle prédictif capable d'estimer le salaire d'un joueur NBA en se basant sur ses performances statistiques (points, rebonds, passes, etc.) et son contexte (âge, équipe, plafond salarial). Le projet vise à identifier les facteurs déterminants de la valeur marchande des joueurs et à quantifier leur impact sur les contrats.

## Sources de données

### 1. Données collectées via NBA API

**Source** : `nba_api` - API officielle des statistiques NBA 
**Endpoint** : `playercareerstats.PlayerCareerStats` 
**Échantillon** : 5,135 joueurs NBA (actifs et historiques) 

### Statistiques par joueur et saison

- **PPG** : Points par match 
- **RPG** : Rebonds par match 
- **APG** : Passes décisives par match 
- **SPG** : Interceptions par match 
- **BPG** : Contres par match 
- **FG%** : Pourcentage aux tirs 
- **FT%** : Pourcentage aux lancers francs 
- **FG3M/FG3A** : Tirs à 3 points réussis/tentés par match 
- **TOVPG** : Pertes de balle par match 
- **PFPG** : Fautes personnelles par match 
- **GP** : Nombre de matchs joués 
- **MIN** : Minutes jouées 

### 2. Données salariales

**Source** : Fichiers CSV externes (`Salaries.csv`, `salary_cap.csv`)
**Période** : Saisons 2000-01 à 2024-25
**Variables** :
- **Salary** : Salaire annuel du joueur en USD
- **SalaryCapUSD** : Plafond salarial de la ligue par saison
- **ratio_cap** : Ratio salaire/plafond salarial (métrique normalisée)

### Défis techniques de la collecte

L'API NBA impose des limitations strictes (erreur 403 après ~200 requêtes). Pour contourner ce problème, un système de collecte par batchs a été implémenté avec pause entre requêtes (`time.sleep(0.5)`) et sauvegarde progressive en 25 fichiers CSV distincts. 

## Preprocessing et Feature Engineering

### Nettoyage des données

**Filtre temporel** : Joueurs ayant débuté leur carrière à partir de la saison 2000-01 
**Filtre de participation** : Suppression des lignes avec moins de 30 matchs joués (GP < 30)
**Gestion des transferts** : Conservation uniquement des statistiques totales (TEAM_ABBREVIATION = 'TOT') pour les joueurs transférés en cours de saison

### Suppression des valeurs aberrantes

Élimination des lignes contenant des zéros dans les colonnes critiques (PPG, RPG, APG, GP, FG_PCT, FTA, FTM, PFPG), indiquant un manque flagrant de données de jeu.

**Résultat** : 9,402 saisons de joueurs NBA après nettoyage

### Fusion des datasets

Jointure entre les statistiques de performance et les données salariales sur les clés `Player` et `SEASON_ID`.

### Features calculées

- **Per-game statistics** : Conversion de toutes les stats cumulées en moyennes par match 
- **ratio_cap** : Normalisation du salaire par rapport au plafond salarial de chaque saison pour tenir compte de l'inflation

## Modélisation

### Algorithmes testés

Le projet compare plusieurs modèles de régression  :

1. **Linear Regression** : Modèle de base pour établir un benchmark
2. **Ridge Regression** : Régularisation L2 pour gérer la multicolinéarité
3. **Lasso Regression** : Régularisation L1 pour sélection de features
4. **Decision Tree Regressor** : Modèle non-linéaire simple
5. **Random Forest Regressor** : Ensemble de décision trees pour robustesse
6. **Support Vector Regression (SVR)** : Régression à vecteurs de support

### Pipeline de preprocessing

**StandardScaler** : Normalisation des features numériques
**OneHotEncoder** : Encodage des variables catégorielles (équipe, saison)
**SimpleImputer** : Imputation des valeurs manquantes
**ColumnTransformer** : Application sélective des transformations

### Validation croisée

**GroupKFold** : Validation croisée groupée par joueur pour éviter le data leakage (un même joueur ne doit pas apparaître dans train et test)
**GroupShuffleSplit** : Split stratifié avec groupement par joueur

### Optimisation des hyperparamètres

**GridSearchCV** : Recherche exhaustive des meilleurs hyperparamètres pour chaque modèle

### Métriques d'évaluation

- **R² Score** : Coefficient de détermination (variance expliquée)
- **MAE (Mean Absolute Error)** : Erreur absolue moyenne en USD

## Technologies utilisées

- **Python 3.x**
- **nba_api** : Récupération des statistiques NBA officielles 
- **pandas** : Manipulation et transformation de données 
- **NumPy** : Calculs numériques
- **scikit-learn** : Modèles ML, preprocessing, validation croisée
- **statsmodels** : Analyses statistiques complémentaires
- **matplotlib & seaborn** : Visualisations des résultats

## Utilisation

### Installation des dépendances

```bash
pip install nba-api pandas numpy scikit-learn statsmodels matplotlib seaborn
```

### Exécution

```bash
# 1. Collecte des données (DataGathering.ipynb)
jupyter notebook DataGathering.ipynb

# 2. Modélisation et prédiction (main.ipynb)
jupyter notebook main.ipynb
```

### Exemple de prédiction

```python
# Charger le modèle entraîné
model = best_model  # Random Forest optimisé

# Prédire le salaire d'un joueur
player_stats = {
    'PPG': 25.0,
    'RPG': 7.5,
    'APG': 5.0,
    'FG_PCT': 0.485,
    'FT_PCT': 0.850,
    'PLAYER_AGE': 27,
    'GP': 75
}

predicted_salary = model.predict([player_stats])
print(f"Salaire prédit : ${predicted_salary[0]:,.0f}")
```

## Résultats attendus

### Variables les plus prédictives

- **PPG** : Points par match (impact majeur)
- **APG** : Passes décisives (playmakers valorisés)
- **FG%** : Efficacité offensive
- **PLAYER_AGE** : Prime de carrière (27-30 ans)
- **GP** : Disponibilité (durabilité valorisée)
- **ratio_cap** : Contexte économique de la saison

### Insights business

- Les joueurs all-around (scoring + playmaking) sont les mieux rémunérés
- La durabilité (GP élevé) est un facteur clé de valorisation
- Le plafond salarial influence fortement la distribution des contrats
- Les jeunes stars en progression rapide sont parfois sous-évalués

## Applications pratiques

- **Négociation de contrats** : Estimation objective de la valeur marchande
- **Gestion d'équipe** : Identification de joueurs sous/sur-payés
- **Scouting** : Projection du potentiel salarial de rookies
- **Analyse de marché** : Tendances d'évolution des salaires par position
- **Fantasy Basketball** : Évaluation du rapport performance/prix

## Améliorations possibles

- **Deep Learning** : Réseaux de neurones pour capturer des interactions complexes
- **Time Series** : Modélisation de l'évolution temporelle des performances
- **Features avancées** : Advanced stats (PER, Win Shares, VORP)
- **Clustering** : Segmentation des joueurs par archétype (scorer, defender, etc.)
- **Scraping complémentaire** : Données de blessures, médias sociaux, popularité

## Contact

Pour plus d'informations sur ce projet, n'hésitez pas à me contacter via theo.eghiazarian@edhec.com ou https://www.linkedin.com/in/th%C3%A9o-eghiazarian-88623030b/

***

*Dernière mise à jour : Janvier 2026*
