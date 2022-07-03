---
layout: page
title: MedianaDesigner
group: 
---

# Sélection optimale d’une règle d’arrêt pour futilité (Étude de cas D1)

## Essai de phase III avec un critère d’évaluation de type temps jusqu’à un évènement

L’étude de cas définit les étapes à suivre pour dériver une règle d’arrêt pour futilité avec des propriétés optimales dans un essai clinique avec un critère d’évaluation de type temps jusqu’à un évènement. L’essai considéré dans cette étude de cas est l’essai QUAZAR AML-001 chez des patients atteints de leucémie myéloïde aiguë, voir [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1). L’analyse principale de cet essai repose sur la comparaison des temps de survie globaux dans les bras de traitement et de contrôle.

L’essai est supposé utiliser une seule analyse intermédiaire qui permettra une évaluation de la futilité. L’analyse intermédiaire devra être effectué après que 50% des événements aient été accumulés. La règle d’arrêt pour futilité sera définie à l’aide d’une puissance conditionnelle et une recommandation d’arrêter l’essai pour futilité sera émise si la puissance conditionnelle à l’analyse intermédiaire est inférieure à un seuil prédéfini. Le sponsor de l’essai souhaite trouver un seuil de puissance conditionnelle qui offre un équilibre optimal entre la sensibilité et la spécificité de la règle de décision sous-jacente.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `FutRule`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/FutRuleFrench.pdf).

## Sélection optimale d’une règle d’arrêt pour futilité à l’aide de la fonction `FutRule`

Une règle d’arrêt optimale pour futilité est facile à dériver dans cet essai de phase III en invoquant la fonction `FutRule`.

Après avoir chargé le paquet MedianaDesigner, c’est-à-dire,

``` r
library(MedianaDesigner)
```

l’utilisateur doit initialiser la liste des paramètres qui seront transmis à la fonction FutRule, c’est-à-dire

``` r
parameters = list()
```

puis définir les arguments individuels. Les arguments requis définis ci-dessous sont très similaires à ceux utilisés par les fonctions de design adaptatif, à savoir `ADSSMod`, `ADTreatSel` et `ADPopSel`.

Le type de critère d’évaluation doit d’abord être spécifié :

``` r
parameters$endpoint_type = "Time-to-event"
```

Comme dans [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1), un temps de survie plus long est un résultat plus favorable dans cet essai et, par conséquent, le paramètre de `direction` doit être défini comme « Higher », c’est-à-dire

``` r
parameters$direction = "Higher"
```

Le nombre de patients à recruter dans les bras de contrôle et de traitement ainsi que le nombre cible d’événements à l’analyse finale sont spécifiés comme suit :

``` r
parameters$sample_size = c(230, 230)
parameters$event_count = 330
```

A l’étape suivante, les temps de survie médians dans les groupes de contrôle et de traitement (mesurés en mois) sont définis :

``` r
parameters$control_time = 16
parameters$treatment_time = 22.9
```

Comme dans les fonctions de design adaptatif, le temps jusqu’à l’événement est supposé être distribué de manière exponentielle dans la fonction `FutRule`.

La fraction d’information, exprimée en nombre d’événements, est fixée à 50% :

``` r
parameters$info_frac = 0.5
``` 

Les paramètres des processus de recrutement et d’abandon des patients sont requis dans ce cas particulier, car le critère d’évaluation principal est un critère d’évaluation de type temps jusqu’à un évènement. Ces paramètres sont définis avec les mêmes valeurs que dans [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1), c’est-à-dire

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
parameters$dropout_rate = 0.05
``` 

En d’autres termes, la durée de la période de recrutement des patients est de 36 mois et 50% des patients devraient être recrutés dans l’essai d’ici 24 mois. Le taux annuel d’abandon des patients, sous une distribution exponentielle des abandons, est supposé être de 5%.

Le risque d’erreur de première espèce dans cet essai de phase III est fixé à 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

L’algorithme, basé sur des simulations pour calculer un seuil optimal de puissance conditionnelle à l’analyse intermédiaire, s’appuiera sur 10 000 exécutions de simulation :

``` r
parameters$nsims = 10000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

Enfin, pour exécuter les calculs, la liste des paramètres d’essai est transmise à la fonction `FutRule` :

``` r
results = FutRule(parameters)
```

Les résultats de la simulation seront enregistrés dans l’objet `results` et il est facile de créer un rapport de simulation détaillé au format Microsoft Word en appelant la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyD1.docx")
```

Le rapport de simulation, qui sera enregistré dans le répertoire de travail, présente des graphiques des taux de sensibilité, de spécificité et de précision en fonction du seuil de puissance conditionnelle (voir les Figures 1, 2 et 3). Les taux de sensibilité et de spécificité sont définis comme la probabilité de poursuivre correctement l’essai et la probabilité d’arrêter correctement l’essai pour futilité lors de l’analyse intermédiaire, respectivement. Une valeur optimale du seuil doit simultanément améliorer les taux de sensibilité et de spécificité. Ceci est accompli en trouvant le seuil qui maximise le taux de précision, c’est-à-dire la moyenne des taux de sensibilité et de spécificité. Le seuil optimal est identifié dans la Figure 3. En plus du seuil optimal, la fonction `FutRule` calcule également un ensemble de valeurs presque optimales, appelées intervalle optimal à 95%. Les intervalles optimaux de ce type ont tendance à être larges si la fonction d’optimisation, c’est-à-dire le taux de précision en fonction du seuil, est assez plate autour de son maximum.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyD1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyD1.docx).

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
