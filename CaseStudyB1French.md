---
layout: page
title: MedianaDesigner
group: 
---

# Designs adaptatifs avec sélection de traitement basée sur les données (étude de cas B1)

## Essai de phase III avec un critère d’évaluation binaire

Le design d’essais adaptatifs avec sélection de traitement basée sur des données sera illustrée à l’aide d’une étude de cas basée sur l’essai de phase III chez des nourrissons atteints d’hémangiome infantile proliférant (Leaute-Labrze et al., 2015). L’essai a été mené pour évaluer les profils d’efficacité et d’innocuité de quatre schémas posologiques de propranolol (1 ou 3 mg de base de propranolol par kilogramme de poids corporel par jour pendant 3 ou 6 mois) par rapport au placebo. L’évaluation primaire de l’efficacité était basée sur un critère d’évaluation binaire, à savoir le taux de réussite (taux de résolution complète ou presque complète de l’hémangiome cible) à la fin de la période de 6 mois. Une approche adaptative a été utilisée dans l’essai et le meilleur schéma posologique de propranolol a été choisi lors d’une analyse intermédiaire.

Dans cette étude de cas, une version étendue du design de l’essai sera envisagée. Un design adaptatif avec deux analyses intermédiaires sera supposé :

* Une règle d’arrêt pour futilité sera appliquée à l’analyse intermédiaire 1 pour éliminer les schémas posologiques inefficaces.
* Une règle de sélection du traitement sera utilisée lors de l’analyse intermédiaire 2 pour déterminer le meilleur schéma posologique.

Lors de l’analyse finale, l’effet du traitement sera évalué dans le groupe de schéma posologique sélectionné par rapport au placebo.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `ADTreatSel`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ADTreatSelFrench.pdf).

## Design adaptatif de sélection de traitement à l’aide de la fonction `ADTreatSel`

L’essai adaptatif proposé avec des règles de futilité et de sélection du traitement sera conçu à l’aide de la fonction `ADTreatSel`. Les paramètres de l’essai et les hypothèses d’effet du traitement seront librement adaptés de l’essai sur le propranolol.

Dans un premier temps, le paquet MedianaDesigner sera chargé :

``` r
library(MedianaDesigner)
```

La liste des paramètres de la fonction sera initialisée, c’est-à-dire

``` r
parameters = list()
```

et les arguments de cette liste de paramètres seront renseignés comme décrit ci-dessous.

Un critère d’évaluation principal binaire sera spécifié dans cet essai :

``` r
parameters$endpoint_type = "Binary"
```

Le critère d’évaluation est basé sur une proportion binaire du taux de réussite et une valeur plus élevée indique un résultat plus favorable. Le paramètre de `direction` doit être défini comme « Higher », c’est-à-dire,

``` r
parameters$direction = "Higher"
```

Une répartition équilibrée des patients dans les cinq bras (placebo et quatre schémas posologiques) est supposée avec 90 patients inscrits dans chaque bras, c’est-à-dire

``` r
parameters$sample_size = c(90, 90, 90, 90, 90)
```

L’ampleur de l’effet du traitement est spécifiée en utilisant le taux de réussite à 6 mois dans le bras placebo et les quatre groupes de schémas posologiques :

``` r
parameters$control_rate = 0.1
parameters$treatment_rate = c(0.30, 0.30, 0.35, 0.35)
```

Comme point de départ, un taux de réussite constant de 30% est supposé pour chaque schéma posologique, mais, bien sûr, il est conseillé d’examiner la performance du design adaptatif dans plusieurs ensembles plausibles de taux de réussite pour les quatre schémas posologiques.

Les fractions d’information des analyses intermédiaires 1 et 2 seront définies en fonction du nombre de patients qui terminent la période de traitement de 6 mois. Les analyses intermédiaires, correspondant aux fractions d’information de 20% et 50%, seront utilisées dans l’essai pour réduire la taille totale de l’échantillon :

``` r
parameters$info_frac = c(0.2, 0.5, 1)
``` 

Le seuil de puissance conditionnelle à l’analyse intermédiaire 1 sera fixé à 30%, c’est-à-dire

``` r
parameters$futility_threshold = 0.3
``` 

Ce seuil correspond à une règle d’arrêt assez libérale car les schémas posologiques individuels sont susceptibles d’être abandonnés en raison d’un manque d’efficacité à moins que l’effet du traitement ne soit plutôt fort, en particulier à moins que la puissance conditionnelle ne soit supérieure à 30%. L’essai sera terminé à ce stade de décision si tous les schémas posologiques sont simultanément abandonnés.

Un taux d’abandon des patients assez élevé de 15% est supposé en raison d’une durée assez longue de l’essai :

``` r
parameters$dropout_rate = 0.15
``` 

Les deux paramètres suivants définissent la règle de sélection du traitement qui sera appliquée à l’analyse intermédiaire 2. Tout d’abord, le meilleur traitement correspondant à la plus grande taille d’effet observée sera choisi à ce point de décision :

``` r
parameters$treatment_count = 1
```

Il convient de noter qu’en raison de la nature discrète du critère d’évaluation principal, il existe une chance non négligeable que la même taille d’effet puisse être observée dans plusieurs bras de schéma posologique. Dans ce cas, le schéma posologique avec la dose globale la plus faible sera choisi.

Étant donné que la sélection de traitement basée sur les données induit un problème de multiplicité, un ajustement de la multiplicité (procédure de tests multiples) doit être défini de manière prospective. La procédure de Hochberg sera utilisée dans cet essai :

``` r
parameters$mult_test = "Hochberg"
```

Le risque d’erreur de première espèce unilatéral dans cet essai de phase III est égal à 2.5% :

``` r
parameters$alpha = 0.025
```

Les caractéristiques fonctionnelles du design adaptatif seront évaluées à l’aide de 10 000 simulations :

``` r
parameters$nsims = 10000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

Les caractéristiques fonctionnelles du design adaptatif de sélection de la population avec les paramètres énumérés ci-dessus sont évaluées en appelant la fonction `ADTreatSel` :

``` r
results = ADTreatSel(parameters)
```

Les résultats de la simulation seront enregistrés dans l’objet `results`. Un rapport avec un résumé détaillé des résultats de simulation est généré en appelant la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyB1.docx")
```

Ce rapport sera enregistré dans le répertoire de travail.

Les résumés suivants sont inclus dans le rapport de simulation. La probabilité d’abandonner chaque schéma posologique et la probabilité globale de mettre fin à l’essai pour futilité (si tous les schémas posologiques sont simultanément inefficaces) à l’analyse intermédiaire 1 sont présentées dans la Table 7. La Table 8 présente les probabilités de sélection du traitement dans l’analyse intermédiaire 2, c’est-à-dire la probabilité que chaque schéma posologique soit sélectionné pour l’analyse finale. Les Tables 9 et 10 comparent la probabilité de succès de l’essai adaptatif proposé à celle des essais de référence (essais traditionnels). Dans le contexte de la sélection adaptative des traitements, il est naturel de définir plusieurs essais de référence. Chaque essai de référence suppose un design simple à deux bras où chaque schéma posologique individuel est comparé à un placebo avec une évaluation de la futilité lors d’une seule analyse intermédiaire.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyB1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyB1.docx).

## Références

Leaute-Labreze C et al. A randomized, controlled trial of oral propranolol in infantile hemangioma. The New England Journal of Medicine. 2015; 372:735-746. DOI: 10.1056/NEJMoa1404710.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
