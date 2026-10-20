# Sélection de variables et régularisation : prédiction du prix de biens immobiliers

Comparaison de sept méthodes de sélection de variables et de régularisation pour un modèle de régression linéaire, sur 5 000 biens immobiliers et 25 variables explicatives, avec Python.

Projet réalisé en binôme avec Maxence Basset, dans le cadre du Master Ingénierie des Risques Économiques et Financiers – Economic Risk and Data Sciences (Université de Bordeaux).

## Objectif

Prédire le prix de vente d'un bien, en milliers d'euros, et déterminer quelles variables comptent vraiment, en tenant compte :
- de la colinéarité entre variables ;
- de l'arbitrage entre qualité de prédiction, simplicité du modèle et coût de calcul.

## Protocole

- **Découpage :** 80 % des données pour l'apprentissage (4 000 biens) et 20 % pour le test (1 000 biens), avec une graine fixée.
- **Choix sur l'apprentissage uniquement :** toutes les décisions sont prises sur l'échantillon d'apprentissage, par validation croisée à 5 plis. L'échantillon de test ne sert qu'une seule fois, pour la comparaison finale, afin d'éviter toute fuite d'information.
- **Pipelines scikit-learn :** la standardisation est refaite dans chaque pli de validation croisée.

## Méthodes comparées

1. **Moindres carrés ordinaires** sur les 25 variables, avec diagnostic de colinéarité par le VIF (statsmodels).
2. **Sélection exhaustive (best subset)** sur un sous-ensemble de 15 variables candidates, soit 32 768 modèles, puis choix du nombre de variables par AIC, BIC, R² ajusté et RMSE en validation croisée.
3. **Sélection ascendante et descendante** (forward et backward), pilotées par la RMSE en validation croisée.
4. **Ridge**, avec le paramètre de pénalité réglé par validation croisée.
5. **Lasso**, avec chemin de régularisation et étude du comportement face aux groupes de variables corrélées.
6. **Algorithme génétique codé à la main**, avec un chromosome binaire et le BIC comme critère à minimiser. Il utilise une sélection par tournoi, un croisement uniforme, une mutation et l'élitisme. Une étude de sensibilité porte sur la taille de la population et le taux de mutation, chaque réglage étant testé sur 5 graines.

## Résultats sur l'échantillon de test

| Méthode | Nb de variables | RMSE test (k€) | R² test |
|---|---|---|---|
| Modèle complet | 25 | 2,748 | 0,765 |
| Best subset | 9 | 2,749 | 0,765 |
| Forward | 11 | 2,747 | 0,765 |
| Backward | 11 | 2,747 | 0,765 |
| Ridge | 25 | 2,748 | 0,765 |
| Lasso | 15 | 2,748 | 0,765 |
| Algorithme génétique | 9 | 2,749 | 0,765 |

## Enseignements

- **Les sept méthodes prédisent aussi bien les unes que les autres :** l'écart de RMSE est inférieur à 0,002 k€. Le choix se fait donc sur la simplicité et le coût de calcul.
- **Neuf variables suffisent.** La sélection exhaustive, le BIC et l'algorithme génétique retiennent les mêmes : surface habitable, qualité du quartier, revenu médian, distance au centre, criminalité, écoles, ancienneté, chômage et DPE. Les autres variables n'apportent que du bruit.
- **La colinéarité est détectée et gérée.** Les quatre variables de taille du bien sont corrélées à environ 0,85 et ont un VIF autour de 5. Le Lasso concentre leur effet sur la surface habitable.
- **Ridge n'apporte presque rien ici :** avec n = 4 000 et p = 25, les moindres carrés sont déjà stables.
- **L'algorithme génétique trouve le modèle optimal** en 2 550 évaluations au plus, contre 33,5 millions pour une recherche exhaustive. Il n'y parvient de façon stable qu'avec une population d'au moins 25 individus et un taux de mutation proche de 1/p.

## Reproduire l'analyse

```bash
pip install -r requirements.txt
jupyter notebook selection_variables_prix_immobilier.ipynb
```

Placer au préalable les données dans `data/` (voir `data/README.md`).

## Structure du dépôt

```
├── selection_variables_prix_immobilier.ipynb   # Notebook complet, avec les sorties
├── requirements.txt                            # Dépendances Python
└── data/
    └── README.md                               # Description des données
```

## Outils

Python, pandas, NumPy, scikit-learn, statsmodels, Matplotlib, seaborn.

## Auteurs

- **Jules Marin** – [LinkedIn](https://www.linkedin.com/in/jules-marin-64611035a/)
- **Maxence Basset**
