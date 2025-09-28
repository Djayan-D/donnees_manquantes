# Remplacement des valeurs manquantes dans une base de données sur les votes en Loire-Atlantique

**Auteur** : Djayan Daëron  
**Contexte** : [Dossier de biostatistiques](https://drive.google.com/file/d/1rdpgP__CYJKHp3GdD7pgXfiS6Vz3aLsE/view?usp=sharing), Master 1 ECAP, Nantes Université (2024-2025)

---

## Contexte et objectifs
Ce projet vise à **reconstituer les valeurs manquantes (NA)** dans une base de données électorale du **premier tour de l'élection présidentielle 2022**, focalisée sur le département de la **Loire-Atlantique**. La base initiale, issue du Ministère de l'Intérieur et de l'IGN, combine :
- **Données électorales** : Résultats par bureau de vote (inscrits, abstentions, votes blancs/nuls/exprimés).
- **Données géographiques** : Coordonnées (latitude/longitude), circonscriptions, noms des communes.

**20 % des valeurs ont été rendues manquantes aléatoirement** pour simuler un scénario réel de données incomplètes. L'objectif est de **reconstituer ces NA** en utilisant des méthodes **logiques** (déductions basées sur les relations entre variables) et **statistiques** (imputation).

---

## Structure de la base de données
La base contient **1110 observations** et **11 variables**, classées en trois catégories :
1. **Géographiques/Administratives** (qualitatives) :
   - `circonscription` : 10 modalités (ex. : "1ère circonscription").
   - `nom_commune` : 192 communes (ex. : "Nantes", "Saint-Nazaire").
   - `code_bur_vote` : 204 bureaux de vote.

2. **Électorales** (quantitatives) :
   - `inscrits` : Nombre d'électeurs (min=159, max=2041).
   - `tx_absents`/`tx_votants`/`tx_blancs`/`tx_nuls`/`tx_exprimes` : Taux en %.

3. **Géographiques** (quantitatives) :
   - `latitude`/`longitude` : Coordonnées GPS.

---

## Méthodologie

### 1. Analyse descriptive
- **Univariée** :
  - Statistiques (moyenne, médiane, écart-type) et visualisations (histogrammes, boxplots) pour chaque variable.
  - Exemple : `tx_absents` a une moyenne de **22.41 %** (écart-type élevé).

- **Bivariée** :
  - **Matrice de corrélation** : `tx_absents` et `tx_votants` sont parfaitement corrélés (-1).
  - **Heatmaps** : Croisement des variables qualitatives (ex. : `circonscription` vs `nom_commune`).
  - **Diagrammes de violon** : Distribution des variables quantitatives par circonscription.

### 2. Remplacement des NA
#### Méthodes logiques
- **Noms de communes, latitudes et longitudes** :
  - Une commune est identifiée par son nom **ou** ses coordonnées. Si une ligne a une latitude mais pas de nom, le nom est déduit via une correspondance unique.
  - **Résultat** : Réduction des NA de **215 à 28** pour `nom_commune`.

- **Circonscription** :
  - Une circonscription est liée à une commune (sauf Nantes, répartie sur 5 circonscriptions). Utilisation de `nom_commune`, `latitude`, ou `longitude` pour déduire la circonscription.
  - **Résultat** : Réduction des NA de **221 à 65**.

- **Code des bureaux de vote** :
  - Les codes sont uniques par commune. Identification des "trous" dans la numérotation (ex. : bureaux 1, 2, 4 → le 3 est manquant).
  - **Complexité** : Certaines lignes sans `nom_commune` ou `circonscription` rendent l'imputation difficile.

#### Méthodes approximatives (à venir)
Pour les valeurs non déductibles logiquement :
- **Imputation par régression** : Prédire les NA via des modèles.
- **k-plus proches voisins (k-NN)** : Remplacer les NA par la moyenne des valeurs similaires.
- **MICE** : Méthode avancée pour les données manquantes.

---

## Résultats
- **Réduction significative des NA** :
  - `nom_commune` : **215 → 28 NA** (87 % de réduction).
  - `circonscription` : **221 → 65 NA** (70 % de réduction).
  - `latitude` : **217 → 26 NA**.
  - `longitude` : **230 → 35 NA**.

- **Limites** :
  - Certaines valeurs restent **indéterminables** (ex. : bureaux de vote à Nantes).
  - **Biais potentiels** si les NA masquent des patterns réels.

---

## Conclusion et perspectives
### Bilan
- **Méthodes logiques efficaces** pour les variables liées (ex. : nom/commune ↔ coordonnées).
- **Nécessité de méthodes statistiques** pour les NA résiduels (ex. : `code_bur_vote`).
- **Validation** : Comparaison avec la base originale pour évaluer la précision des reconstructions.

### Perspectives
- **Améliorer les méthodes** :
  - Utiliser des **algorithmes d'imputation avancés** (MICE, k-NN).
  - **Valider les résultats** en comparant avec la base originale.
- **Étendre l'analyse** :
  - Appliquer la méthodologie à d'autres départements ou élections.
  - **Cartographier les résultats** pour visualiser les tendances géographiques.

---

## Sources
- **Données électorales** : [Data.gouv.fr](https://www.data.gouv.fr/fr/datasets/elections-presidentielles-2022-resultats-par-bureau-de-vote/)
- **Données géographiques** : [IGN](https://geoservices.ign.fr/)
- **Outils** : R (`dplyr`, `ggplot2`), Quarto.

---
