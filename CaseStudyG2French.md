---
layout: page
title: MedianaDesigner
group: 
---

# Essais traditionnels avec plusieurs critères d’évaluation (étude de cas G2)

## Essai de phase II avec deux critères d’évaluation

Cette étude de cas décrit les calculs de puissance dans les essais cliniques de phase II avec plusieurs critères d’évaluation. Dans ces essais de preuve de concept, plusieurs critères d’évaluation cliniques sont le plus souvent introduits pour soutenir une évaluation globale de l’effet du traitement pour les critères d’évaluation sélectionnés. Par conséquent, au lieu de méthodes d’ajustement de la multiplicité, des méthodes de test global sont appliquées pour combiner les preuves d’efficacité de tous les critères d’évaluation.

À titre d’illustration, considérons un essai clinique de phase II chez des patients atteints d’arthrose. L’essai de preuve de concept sera mené pour évaluer les effets d’un traitement expérimental sur deux critères d’évaluation, la sous-échelle de la douleur et la sous-échelle de la fonction physique de l’indice d’arthrose des universités Western Ontario and McMaster Universities (WOMAC) (Bellamy, 2002) par rapport au placebo. Les sous-échelles sont normalement distribuées et des valeurs plus élevées indiquent un résultat favorable.

L’évaluation de l’efficacité pour chaque critère d’évaluation sera effectuée en utilisant la variation moyenne entre la Baseline et la semaine 6. L’évaluation globale de l’effet du traitement sera réalisée à l’aide de la procédure de test global O’Brien basée sur l’estimation ordinaire des moindres carrés de la taille d’effet commune pour les deux critères d’évaluation. Cette procédure de test globale aidera le sponsor de l’essai à évaluer la force des preuves en faveur de l’hypothèse alternative globale selon laquelle le traitement expérimental est uniformément meilleur que le placebo en ce qui concerne les deux critères d’évaluation. Un calcul de puissance dans cette étude de cas est facile à effectuer à l’aide de la fonction `MultAdj`.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `MultAdj`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/MultAdjFrench.pdf).

## Calculs de puissance à l’aide de la fonction `MultAdj`

Avant de passer en revue les étapes d’exécution d’un calcul de puissance dans cette étude de cas, il est important de passer en revue les options prises en charge par la fonction `MultAdj`. Dans les essais comportant plusieurs comparaisons dose-contrôle, cette fonction ne prend en compte que les méthodes d’ajustement de la multiplicité, car les évaluations globales de l’effet du traitement sur une gamme de doses ne sont pas menées dans les essais cliniques. En revanche, lorsque des essais avec plusieurs critères d’évaluation sont pris en compte, cette fonction permet à l’utilisateur de choisir entre une procédure de tests multiples ou une procédure de test globale. Les procédures de tests multiples sont souvent envisagées dans le contexte des essais de confirmation de phase III et la procédure de test global est plus pertinente dans les essais de phase II de preuve de concept.

Pour en revenir à cette étude de cas, un calcul de puissance visera à estimer la probabilité de succès de la procédure de test global O’Brien. En plus de cela, la puissance marginale sera calculée pour chaque critère d’évaluation individuel.

Pour spécifier le design et d’autres paramètres pertinents requis pour le calcul de puissance, la première étape consiste à charger le paquet MedianaDesigner :

``` r
library(MedianaDesigner)
```

puis configurez une liste vide des paramètres de la fonction :

``` r
parameters = list()
```

Le type de critère d’évaluation commun pour les sous-échelles de douleur et de fonction physique est spécifié en premier :

``` r
parameters$endpoint_type = "Normal"
```

Pour les deux critères d’évaluation, des valeurs plus élevées indiquent un résultat favorable et, par conséquent, la direction commune du résultat favorable est spécifiée comme suit :

``` r
parameters$direction = "Lower"
```

Un design à deux bras avec une seule comparaison traitement-placebo est utilisé dans cet essai et le profil d’efficacité du traitement expérimental est évalué à l’aide de deux critères d’évaluation, ce qui implique que

``` r
parameters$n_comparisons = 1
parameters$n_endpoints = 2
```

Un design équilibré avec 180 patients par groupe sera utilisée dans cet essai :

``` r
parameters$sample_size = c(180, 180)
```

et seulement 5% des patients devraient abandonner l’essai au cours de la période de traitement de 6 semaines :

``` r
parameters$dropout_rate = 0.05
``` 

Les hypothèses d’effet du traitement doivent être spécifiées pour chaque critère d’évaluation, par exemple, deux valeurs moyennes sont requises dans le bras placebo

``` r
parameters$control_mean = c(35, 110)
```

et de même, deux valeurs moyennes sont requises dans le bras expérimental

``` r
parameters$treatment_mean = c(60, 200)
```

Des écarts-types communs spécifiques aux critères d’évaluation sont supposés dans les deux bras de l’essai :

``` r
parameters$control_sd = c(100, 270)
parameters$treatment_sd = c(100, 270)
```

Pour spécifier la procédure de test global O’Brien, l’argument `mult_test` doit être défini comme « O’Brien », c’est-à-dire

``` r
parameters$mult_test = "O'Brien"
```

Le risque d’erreur de première espèce unilatéral est défini à 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

toutefois, en général, des taux d’erreur plus élevés peuvent être utilisés dans les essais de preuve de concept.

Le calcul de la puissance pour la procédure de test globale sera exécuté sur la base de 10 000 simulations :

``` r
parameters$nsims = 10000
```

La liste des paramètres résultante sera transmise à la fonction `MultAdj` avec les résultats de simulation enregistrés dans l’objet `results` :

``` r
results = MultAdj(parameters)
```

Un rapport de simulation détaillé peut être généré à partir de cet objet en le transmettant à la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyG2.docx")
```

Le rapport de simulation sera enregistré dans le répertoire de travail actuel.

La caractéristique la plus importante de l’essai, à savoir la puissance globale basée sur la procédure d’essai globale, est présentée dans la Table 9. À titre d’information complémentaire, la puissance marginale pour chaque critère d’évaluation individuel est présentée dans la Table 8. En général, en raison d’un effet synergique, la puissance globale a tendance à être beaucoup plus élevée que les valeurs de puissance marginale.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyG2.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyG2.docx).

## Références

Bellamy, N. WOMAC Osteoarthritis Index User Guide. Version V. 2002. Brisbane, Australia.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
