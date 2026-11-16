# crypto_price_prediction

Analyse exploratoire de données de cryptomonnaies et prédiction du prix de clôture du **Bitcoin (BTCUSDT)** avec **XGBoost**, optimisé par **Optuna**.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/omrani-wiem/crypto_price_prediction/blob/main/crypto_price_prediction.ipynb)

---

## 📌 Aperçu

Le projet tient dans un seul notebook (`crypto_price_prediction.ipynb`) qui suit un pipeline complet de data science :

1. Chargement et exploration des données de 127 cryptomonnaies
2. Traitement des valeurs manquantes
3. Visualisations (courbes de prix, chandeliers, volumes)
4. Étude statistique de la normalité des rendements du Bitcoin
5. Création de variables (features temporelles et lags)
6. Entraînement et optimisation d'un modèle XGBoost
7. Prédiction du prix sur une période future

---

## 📊 Données

- **Source** : dataset Kaggle [`yassinebenzekri/crypto`](https://www.kaggle.com/datasets/yassinebenzekri/crypto), téléchargé automatiquement avec `kagglehub`.
- **Contenu** : cinq fichiers CSV (`open`, `high`, `low`, `close`, `volume`).
- **Fréquence** : données **horaires**, du 01/06/2021 au 01/09/2022 (10 992 lignes).
- **Couverture** : 127 paires de cryptomonnaies cotées en USDT (BTC, ETH, ADA, DOGE, SOL, etc.).

### Valeurs manquantes

23 cryptomonnaies ont des valeurs manquantes (lancées après le début de la période, ou absentes comme `ICPUSDT`). Elles sont traitées par propagation avant (`ffill`), puis les valeurs restantes sont remplacées par 0.

---

## 🔬 Méthodologie

### 1. Exploration et visualisation
- Courbes de prix des coins avec données manquantes
- Graphique en chandeliers (candlestick) interactif d'ETHUSDT avec Plotly
- Volume total échangé par cryptomonnaie

### 2. Analyse des rendements du Bitcoin
- Calcul du rendement horaire : `close / open`
- Histogramme et densité, boxplot, Q-Q plot
- Kurtosis (≈ 8,26) et skewness (≈ 0,02)
- Tests de normalité **Shapiro-Wilk** et **Kolmogorov-Smirnov** : la normalité est rejetée sur les rendements bruts
- Normalisation avec un `QuantileTransformer` (sortie gaussienne) : après transformation, le test K-S ne rejette plus la normalité

### 3. Feature engineering
- **Variables temporelles** : heure, jour de la semaine, trimestre, mois, année, jour de l'année, jour du mois, semaine de l'année
- **Variables de retard (lags)** : `lag1` à `lag4`, décalages de 365, 730, 1095 et 1460 lignes du prix de clôture
- Les colonnes `open`, `high`, `low` et `rendement` sont **exclues** des variables explicatives pour éviter une fuite de données (data leakage)

### 4. Modélisation
- **Modèle** : `XGBRegressor`
- **Découpage chronologique** 70 % / 30 % (et non aléatoire, pour respecter l'ordre du temps)
- **Optimisation des hyperparamètres** avec Optuna (10 essais, minimisation du RMSE) : `n_estimators`, `max_depth`, `learning_rate`, `subsample`, `colsample_bytree`, `reg_alpha`, `reg_lambda`
- **Prédiction future** sur 365 pas de temps au-delà de la dernière date

---

## 📉 Résultats

| Métrique | Valeur |
|---|---|
| RMSE (test) | ≈ 17 873 |
| MAPE (test) | ≈ 74,6 % |

Meilleurs hyperparamètres trouvés : `n_estimators=613`, `max_depth=3`, `learning_rate≈0.052`, `subsample≈0.86`, `colsample_bytree≈0.76`.
---

## 🛠️ Technologies

- **Python 3**
- **Données** : `pandas`, `numpy`, `kagglehub`
- **Visualisation** : `matplotlib`, `seaborn`, `plotly`
- **Statistiques** : `scipy`, `statsmodels`
- **Machine learning** : `scikit-learn`, `xgboost`
- **Optimisation** : `optuna`

---

## 🚀 Utilisation

### Sur Google Colab (recommandé)
Cliquez sur le badge **Open In Colab** en haut de ce README, puis exécutez les cellules dans l'ordre.

### En local

```bash
git clone https://github.com/omrani-wiem/crypto_price_prediction.git
cd crypto_price_prediction

pip install kagglehub optuna pandas numpy matplotlib seaborn plotly scipy statsmodels scikit-learn xgboost jupyter

jupyter notebook crypto_price_prediction.ipynb
```

> Le téléchargement du dataset avec `kagglehub` peut nécessiter un compte Kaggle configuré.

---

## 📁 Structure du dépôt

```
crypto_price_prediction/
├── crypto_price_prediction.ipynb   # Notebook principal
└── README.md
```

---

## 👤 Auteur

**Wiem Omrani** — [@omrani-wiem](https://github.com/omrani-wiem)

---

## ⚖️ Avertissement

Ce projet est réalisé à des fins **éducatives**. Il ne constitue pas un conseil financier.
