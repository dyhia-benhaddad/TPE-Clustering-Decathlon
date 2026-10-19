# Classification non supervisée de performances d’athlètes de décathlon avec R

**Travail personnel encadré (TPE) — Master 1 Mathématiques et Applications**  
Université Le Havre Normandie · Année universitaire 2025–2026

**Réalisé par :** Dyhia Benhaddad et Manuella Ikoutou Souamy  
**Encadrement :** Mme Sylviane Wey  
**Note obtenue :** 16/20

---

## Présentation du projet

Comment regrouper des athlètes selon leurs performances sportives sans connaître leurs groupes à l’avance ?

Ce projet porte sur l’application et la comparaison de plusieurs méthodes de **classification non supervisée (*clustering*)** à un jeu de données de décathlon. Il couvre les principales étapes d’une analyse statistique : préparation des données, réduction de dimension, construction des groupes, évaluation de leur qualité et interprétation des profils obtenus.

## 1. Problématique et objectifs

La classification non supervisée consiste à rechercher des groupes d’individus présentant des caractéristiques similaires, sans disposer de groupes prédéfinis.

Le projet vise à répondre à deux questions :

- Comment choisir une méthode de classification adaptée à la structure des données ?
- Comment évaluer la qualité des groupes obtenus lorsque les classes de référence ne sont pas connues ?

Les objectifs sont les suivants :

- résumer l’information contenue dans les dix disciplines à l’aide d’une analyse en composantes principales (ACP) ;
- appliquer trois méthodes de classification : la classification ascendante hiérarchique (CAH), K-means et DBSCAN ;
- étudier le choix des paramètres et du nombre de classes à l’aide de critères de validation ;
- caractériser et interpréter les groupes obtenus ;
- comparer les méthodes et analyser leurs limites sur ce jeu de données.

## 2. Données et préparation

Le fichier `decath.Rda` contient l’objet R `decathlon`, un tableau de **41 athlètes et 13 variables** :

- 28 athlètes ayant participé aux Jeux olympiques et 13 au Decastar, identifiés par la variable `Competition` ;
- les performances dans les dix disciplines du décathlon : 100 m, saut en longueur, lancer du poids, saut en hauteur, 400 m, 110 m haies, lancer du disque, saut à la perche, lancer du javelot et 1 500 m ;
- le classement final (`Classement`) et le total de points (`Points`).

### Préparation des données

- Sélection des dix variables de performance pour les analyses.
- Exclusion de `Points` et `Classement`, qui sont des variables dérivées des performances, ainsi que de `Competition`, variable qualitative réservée à l’interprétation.
- Centrage-réduction des dix variables afin de les rendre comparables malgré leurs unités différentes.
- Calcul de statistiques descriptives et étude des corrélations entre disciplines.

**Point d’interprétation :** pour les épreuves chronométrées, un temps faible correspond à une meilleure performance. Pour les sauts et les lancers, une valeur élevée correspond à une meilleure performance.

## 3. Méthodes utilisées

| Méthode | Espace de calcul | Objectif |
|---|---|---|
| **ACP** | Dix variables standardisées | Réduire la dimension, interpréter les axes factoriels et visualiser les groupes. |
| **CAH — critère de Ward** | Dix variables standardisées | Construire une hiérarchie de groupes et étudier le nombre de classes à retenir. D’autres critères d’agrégation sont également examinés. |
| **K-means** | Dix variables standardisées | Former K groupes en minimisant la dispersion intra-classe. Le calcul utilise 25 initialisations et une graine fixée avec `set.seed(123)`. |
| **DBSCAN** | Dix variables standardisées, puis plan des deux premiers axes de l’ACP | Détecter des zones denses et des observations atypiques (*bruit*) sans fixer le nombre de groupes à l’avance. |

### Critères d’évaluation et d’interprétation

- Méthode du coude et saut d’inertie pour étudier le nombre de classes.
- Indice de silhouette pour évaluer la cohésion des groupes et leur séparation.
- Coefficient agglomératif pour la CAH.
- Graphique des distances aux plus proches voisins pour étudier le choix du paramètre ε de DBSCAN.
- Caractérisation statistique des classes avec `catdes()` de `FactoMineR`.
- Rapport de corrélation η² entre les classes et les deux premiers axes de l’ACP.

## 4. Principaux résultats

### 4.1. Analyse en composantes principales

Les quatre premières composantes présentent les résultats suivants :

| Composante | Valeur propre | Variance expliquée | Variance cumulée |
|---|---:|---:|---:|
| Axe 1 | 3,27 | 32,72 % | 32,72 % |
| Axe 2 | 1,74 | 17,37 % | 50,09 % |
| Axe 3 | 1,40 | 14,05 % | 64,14 % |
| Axe 4 | 1,06 | 10,57 % | 74,71 % |

Les principaux enseignements sont les suivants :

- Selon la règle de Kaiser (valeur propre supérieure à 1), quatre composantes sont retenues. Elles expliquent **74,71 % de la variance totale**.
- Les deux premiers axes expliquent **50,09 % de la variance** et servent à visualiser les observations dans un plan factoriel.
- L'axe 1 traduit le **niveau de performance global**. Il est corrélé négativement aux courses de vitesse (100 m : −0,77 ; 110 m haies : −0,75 ; 400 m : −0,68) et positivement à la longueur (0,74), au poids (0,62), à la hauteur (0,57) et au disque (0,55). Comme un temps faible correspond à une bonne performance, les athlètes situés à droite de l'axe sont à la fois rapides et performants en saut et en lancer.
- L'axe 2 distingue des **profils de spécialisation**. Il est surtout lié au disque (0,61), au poids (0,60), au 400 m (0,57) et au 1 500 m (0,47).
- Le plan factoriel ne fait pas apparaître de séparation nette entre les athlètes selon leur compétition d'origine (Jeux olympiques ou Decastar).



### 4.2. Comparaison des classifications

| Méthode | Espace de calcul | Paramètres retenus | Résultat | Silhouette moyenne |
|---|---|---|---|---:|
| CAH — Ward | Dix variables standardisées | K = 4 | Quatre classes de 3, 19, 11 et 8 athlètes | 0,131 |
| K-means | Dix variables standardisées | K = 4 | Quatre classes de 13, 3, 13 et 12 athlètes | 0,141 |
| DBSCAN | Dix variables standardisées | MinPts = 4 ; ε testé de 2,5 à 4,0 | Un seul groupe pour les valeurs testées | — |
| DBSCAN | Plan des deux premiers axes de l’ACP | ε = 1,5 ; MinPts = 3 | Trois groupes de 3, 32 et 3 athlètes, plus 3 points de bruit | 0,291, hors bruit |


### 4.3. Indicateurs complémentaires

- **CAH :** coefficient agglomératif de 0,76.
- **K-means :** l’inertie inter-classes représente 39,5 % de l’inertie totale.

Le rapport de corrélation η² entre les classes et les deux premiers axes de l’ACP est présenté ci-dessous.

| Méthode | η² avec l’axe 1 | η² avec l’axe 2 |
|---|---:|---:|
| CAH — Ward | 0,734 | 0,470 |
| K-means | 0,805 | 0,176 |
| DBSCAN sur le plan de l’ACP, bruit traité comme un groupe | 0,431 | 0,239 |

Ces valeurs décrivent le lien entre l’appartenance aux groupes et les coordonnées des individus sur les axes factoriels. Elles ne constituent pas, à elles seules, une mesure globale de la qualité de la classification.

## 5. Interprétation et limites

### Principaux enseignements

- Les trois méthodes isolent le même groupe de trois athlètes olympiques — Sebrle, Clay et Karpov —, significativement au-dessus de la moyenne dans la plupart des disciplines.
- La CAH et K-means produisent des partitions différentes pour les autres athlètes. K-means distingue notamment un groupe associé à de bonnes performances au saut à la perche.
- Les valeurs de η² montrent que les classes de la CAH et de K-means sont fortement liées au premier axe de l’ACP, particulièrement pour K-means.
- DBSCAN, appliqué au plan des deux premiers axes de l’ACP, identifie trois observations atypiques — Warners, Drews et Casarsa — comme points de bruit.

### Limites de l’analyse

- Les silhouettes de la CAH (0,131) et de K-means (0,141) sont faibles, ce qui indique une séparation limitée entre les groupes.
- Les critères de choix du nombre de classes ne convergent pas tous vers la même solution. Le choix de quatre classes repose également sur l’interprétabilité des résultats et la comparaison entre la CAH et K-means.
- Dans les dix dimensions initiales, DBSCAN ne met pas en évidence plusieurs groupes pour les valeurs de paramètres testées. Ce résultat illustre la sensibilité de cette méthode au choix des paramètres et à la représentation des données.
- La silhouette de DBSCAN est calculée dans le plan des deux premiers axes de l’ACP, en excluant les points de bruit. Les silhouettes de la CAH et de K-means sont calculées sur les dix variables standardisées : **ces valeurs ne sont donc pas directement comparables**.
- Le jeu de données ne comprend que 41 athlètes. Les conclusions sont propres à cet échantillon et ne peuvent pas être généralisées à l’ensemble des décathloniens.

## 6. Contenu du dépôt

| Fichier | Description |
|---|---|
| [`Rapport_Méthodes_de_classification.pdf`](Rapport_Méthodes_de_classification.pdf) | Rapport complet de 55 pages : fondements théoriques, application, interprétations et scripts R en annexe. |
| [`Diapositives_Méthodes_de_classification.pdf`](Diapositives_Méthodes_de_classification.pdf) | Diapositives de la soutenance orale. |
| [`decath.Rda`](decath.Rda) | Jeu de données au format R, contenant l’objet `decathlon`. |
| [`ACP.R`](ACP.R) | Préparation des données et analyse en composantes principales. |
| [`CAH.R`](CAH.R) | Classification ascendante hiérarchique, choix du nombre de classes et caractérisation des groupes. |
| [`K-means.R`](K-means.R) | K-means, méthode du coude, silhouette et caractérisation des groupes. |
| [`DBSCAN.R`](DBSCAN.R) | Application de DBSCAN aux variables initiales standardisées puis au plan factoriel de l’ACP. |
| [`USArrests.R`](USArrests.R) | Exemple complémentaire sur le jeu de données `USArrests`, intégré à R, pour comparer des critères d’agrégation de la CAH. |

## 7. Outils et packages

**Langage :** R

**Packages utilisés :**

- `FactoMineR` : ACP avec `PCA()` et caractérisation des classes avec `catdes()`.
- `factoextra` : visualisation de l’ACP, des contributions, des classes et des silhouettes.
- `cluster` : classification hiérarchique avec `agnes()` et calcul de silhouettes.
- `dbscan` : algorithme DBSCAN et graphique des distances aux plus proches voisins.
- `ggplot2` : création de graphiques.

**Fonctions de R de base :** `scale()`, `dist()`, `hclust()`, `cutree()`, `kmeans()` et `aov()`.

## 8. Installation et exécution

### Prérequis

- R installé sur l’ordinateur.
- RStudio recommandé pour exécuter les scripts et visualiser les graphiques.

### Étape 1 — Récupérer le dépôt

Cloner le dépôt GitHub :

```bash
git clone https://github.com/dyhia-benhaddad/TPE-Clustering-Decathlon.git
```

Il est également possible de télécharger l’archive avec **Code > Download ZIP**, puis de la décompresser.

### Étape 2 — Ouvrir le projet

Dans R ou RStudio, définir le répertoire du dépôt comme répertoire de travail :

```r
setwd("chemin/vers/TPE-Clustering-Decathlon")
```

Les scripts doivent pouvoir accéder au fichier `decath.Rda` depuis ce répertoire.

### Étape 3 — Installer les packages

Si nécessaire, installer les packages suivants :

```r
install.packages(c("FactoMineR", "factoextra", "cluster", "dbscan"))
```

### Étape 4 — Exécuter les scripts

```r
source("ACP.R")
source("CAH.R")
source("K-means.R")
source("DBSCAN.R")
source("USArrests.R")
```

Les scripts permettent de reproduire les analyses et de consulter les résultats ainsi que les graphiques. Une exécution ligne par ligne dans RStudio facilite le suivi des différentes étapes.



## 9. Compétences mobilisées

- **Préparation des données :** sélection de variables, exclusion de variables dérivées, centrage-réduction, statistiques descriptives et étude des corrélations.
- **Statistique multivariée :** ACP, réduction de dimension, interprétation des axes, contributions et qualité de représentation.
- **Classification non supervisée :** mise en œuvre de la CAH, de K-means et de DBSCAN.
- **Évaluation statistique :** comparaison de critères de validation, analyse de la qualité des groupes et interprétation des limites des méthodes.
- **Programmation et visualisation :** scripts R, graphiques avec `factoextra` et `ggplot2`.
- **Communication scientifique :** rédaction d’un rapport de 55 pages et présentation orale des résultats.

## Conclusion

Ce projet met en évidence qu’aucune méthode de classification n’est universelle : son efficacité dépend de la structure des données, de leur dimension et de l’objectif de l’analyse.

La CAH, K-means et DBSCAN font ressortir des profils différents, tandis que les indicateurs de validation soulignent les limites de la séparation en groupes sur ce jeu de données. Le projet illustre ainsi une démarche complète d’analyse statistique, de la préparation des données à l’évaluation critique des résultats.

Cette démarche peut être mobilisée dans d’autres contextes de segmentation, par exemple pour l’analyse de profils de clients, de produits ou d’actifs financiers.
