# Monte Carlo Pricing - Options Financières

##  Description

Projet de finance quantitative implémentant la méthode de Monte Carlo pour le pricing de produits dérivés complexes. Ce projet explore les simulations numériques pour évaluer différents types d'options pour lesquelles aucune formule analytique simple n'existe. 

## Objectif

Développer un moteur de pricing flexible utilisant les simulations de Monte Carlo pour évaluer divers instruments dérivés (options européennes, asiatiques, à barrières, lookback) dans le cadre du modèle de Black-Scholes. Le projet illustre également les techniques de réduction de variance pour améliorer la précision et l'efficacité des simulations.

## Contexte théorique

La méthode de Monte Carlo, introduite par Boyle en 1977 pour le pricing de dérivés, permet de résoudre des problèmes probabilistes en simulant de nombreux scénarios possibles. Elle est particulièrement utile pour : 

- **Statistiques de portefeuille** : Calcul des rendements attendus, métriques de risque (VaR, CVaR), probabilités de drawdown 
- **Pricing d'options** : Évaluation sous la mesure risque-neutre (Q-measure) pour déterminer le prix d'instruments complexes 

### Formule générale
Pour toute option, le prix théorique à \( t=0 \) est  : 

$$
\text{Prix}(t=0) = e^{-rT} \times \mathbb{E}[\text{Payoff}(T)]
$$


## Types d'options pricées

### 1. Options européennes
**Call européen** : Payoff = $$\max(S_T - K, 0)\$$

**Put européen** : Payoff = $$\max(K - S_T, 0)\$$ 

### 2. Options asiatiques (Average Price)
Payoff basé sur la moyenne arithmétique du sous-jacent : $$\max(\bar{S} - K, 0)\$$ 
Moins chères que les options vanilles car la moyenne réduit la volatilité.

### 3. Options à barrières
**Up-and-Out Call** : Option qui disparaît si le prix dépasse une barrière 
**Down-and-In Put** : Option qui s'active si le prix passe sous une barrière 

### 4. Options Lookback
Le strike correspond au prix minimum observé pendant la période 
Payoff = $$\( S_T - \min(S) \)$$ (achat au plus bas, vente au final)

## 🛠️ Méthodologie

### Génération des trajectoires
Simulation des chemins du sous-jacent selon le modèle de Black-Scholes  :
- **Drift** : $$\( (r - 0.5\sigma^2)dt \)$$
- **Diffusion** : $$\ \sigma\sqrt{dt} \cdot Z \$$ où $$\ Z \sim \mathcal{N}(0,1) \$$

### Techniques de réduction de variance

**Variables antithétiques** : Génération de trajectoires avec $$\ Z\$$ et $$\-Z\$$ pour réduire la variance de l'estimateur 

**Variables de contrôle** : Utilisation d'un call européen (dont le prix analytique est connu) comme variable de contrôle pour améliorer la précision du pricing d'options exotiques 

### Architecture orientée objet
- **Classe abstraite `Derivative`** : Interface pour tous les produits dérivés 
- **Classes d'options** : `EuropeanCall`, `AsianCall`, `UpAndOutCall`, `LookbackCall` 
- **Simulateur Monte Carlo** : `MonteCarloSimulator` avec gestion des variables antithétiques et calcul d'intervalles de confiance

## Technologies utilisées

- **Python 3.x**
- **NumPy** : Calculs numériques et génération de nombres aléatoires
- **pandas** : Manipulation de données
- **scipy** : Distribution statistiques et calculs d'intervalles de confiance 
- **matplotlib** : Visualisations des trajectoires et distributions
- **Plotly** : Graphiques interactifs
- **yfinance** : Récupération de données de marché (optionnel)

## Résultats

Exemple de pricing avec les paramètres suivants  : 
- Prix initial : S₀ = 100
- Strike : K = 105
- Taux sans risque : r = 5%
- Volatilité : σ = 20%
- Maturité : T = 1 an
- Nombre de simulations : 100,000

**Résultats obtenus**  : 
- **European Call** : 7.57 € (IC 95% : [7.88, 8.04])
- **Asian Call** : 3.34 € (moins cher grâce à la moyenne)
- **Up-and-Out Call** (barrière 120) : 0.57 € (probabilité d'extinction)
- **Lookback Call** : 15.81 € (option la plus chère)

Le projet génère également des visualisations des trajectoires simulées et de la distribution finale du sous-jacent

## Utilisation

```python
# Installer les dépendances
pip install numpy pandas scipy matplotlib plotly yfinance

# Exécuter le notebook
jupyter notebook Monte_Carlo_Pricing.ipynb
```

### Exemple de code

```python
# Paramètres de marché
S0 = 100
K = 105
r = 0.05
sigma = 0.20
T = 1.0

# Création du simulateur
simulator = MonteCarloSimulator(n_paths=100000, n_steps=252)

# Pricing d'un call européen
euro_call = EuropeanCall(S0, K, r, sigma, T)
result = simulator.price(euro_call)
print(f"Prix : {result['price']:.4f}")
print(f"IC 95% : [{result['ci_95'][0]:.4f}, {result['ci_95'] [ppl-ai-file-upload.s3.amazonaws](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/137473217/7166084f-0607-421c-a334-90b18feb736f/Monte_Carlo_Pricing.ipynb):.4f}]")
```

## Applications pratiques

- **Trading et structuration** : Évaluation d'options exotiques sur mesure
- **Risk management** : Simulation de scénarios de stress testing
- **Pricing de produits structurés** : Évaluation de produits sans formule fermée
- **Validation de modèles** : Benchmark contre des méthodes analytiques (Black-Scholes)

## Contact

Pour plus d'informations sur ce projet, n'hésitez pas à me contacter via theo.eghiazarian@edhec.com ou https://www.linkedin.com/in/th%C3%A9o-eghiazarian-88623030b/.

***

*Dernière mise à jour : Janvier 2026*
