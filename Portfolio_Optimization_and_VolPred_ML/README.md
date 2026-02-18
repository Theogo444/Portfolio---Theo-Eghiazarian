
# Markowitz Portfolio Optimization with ML Volatility Prediction


## **Projet**
Optimisation de portefeuille **Markowitz Minimum Variance** boostée par **prédictions de volatilité Random Forest** (5j ahead) sur **20+ actifs mondiaux**.

**Résultats** : Sharpe **3.45** | Ret **24.3%** | Vol **6.5%**

## **Fonctionnalités**
- **Univers multi-actifs** : Actions US/EU/Asie, ETF, Obligations, Matières premières, FX, Crypto
- **Random Forest Vol** : Features lags(5), EWMA(10), rolling(20)
- **Markowitz prédictif** : `diag(vol_pred) @ corr @ diag(vol_pred)`
- **Visualisations interactives** : Plotly (heatmap, bar charts)
- **Optimiseur** : Contraintes max 20%/actif

## **Pipeline**
```
1. yfinance → Log Returns → Stats annuelles
2. RF par actif → Vol prédite 5j
3. Σ_pred = D_vol_pred @ corr_hist @ D_vol_pred
4. min √(wᵀ Σ_pred w) → w_optimal
```

## **Résultats**
```
Portefeuille Optimal :
├─ Gold_Futures     : 25%
├─ ETF_SP500        : 20% 
└─ Bond_US_Aggregate: 15%
Sharpe: 3.45 | Vol optimisée: 6.5%
```

## **Installation**
```bash
git clone <ton-repo>
cd portfolio-markowitz-ml
pip install -r requirements.txt
jupyter notebook Portfolio-Optimization-4.ipynb
```

## **Dépendances**
```txt
pandas
numpy
scikit-learn
scipy
plotly
yfinance
```

## **Fichiers**
```
├── Portfolio-Optimization-4.ipynb  # Notebook principal
├── stock_prices.xlsx              # Données exemple
└── README.md                      # Ce fichier
```

## **Utilisation**
1. **Cells 1-3** : Chargement données + stats
2. **Cells 4-6** : Markowitz classique
3. **Cells 7-10** : **ML Vol + MinVar prédictif**
4. **Graphiques interactifs** générés !

## **Insights Techniques**
```
Feature Importance RF : vol_ewma(42%) > lags(30%)
EWMA > Rolling : Réagit aux changements de régime
Corrélations historiques stables → Vols dynamiques
```
