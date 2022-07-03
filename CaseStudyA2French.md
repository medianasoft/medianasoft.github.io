---
layout: page
title: MedianaDesigner
group: 
---

# Designs adaptatifs avec réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données (étude de cas A2)

## Essai de phase III avec un critère d’évaluation continu

Cette étude de cas complète [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1French) en fournissant une brève description du processus de conception des essais avec une réévaluation adaptative de la taille de l’échantillon lorsque le critère d’évaluation principal de l’efficacité est continu et suit une distribution normale.

Cette étude de cas est librement adaptée de l’essai de phase III pour le traitement de la schizophrénie (Meltzer et al., 2011). Cet essai clinique a été mené pour évaluer les profils d’efficacité et d’innocuité de lurasidone chez les patients atteints de schizophrénie aiguë. Il est important de préciser que cet essai particulier comprenait quatre bras (deux doses de lurasidone, un placebo et un contrôle actif). Cet exemple suppose une configuration simplifiée avec une dose unique du traitement expérimental par rapport au placebo.

Trois points de décision seront utilisés dans cet essai :

* Analyse intermédiaire 1 (règle d’arrêt pour futilité).
* Analyse intermédiaire 2 (règle de réestimation de la taille de l’échantillon).
* Analyse finale.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `ADSSMod`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ADSSModFrench.pdf).

## Design adaptatif avec réestimation de la taille de l’échantillon à l’aide de la fonction `ADSSMod`

Comme dans [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1), la fonction `ADSSMod` sera utilisée pour évaluer les principales caractéristiques fonctionnelles du design adaptatif proposée.

Le paquet MedianaDesigner doit d’abord être chargé :

``` r
library(MedianaDesigner)
```

et une liste vide des paramètres de la fonction doit être configurée:

``` r
parameters = list()
```

Le critère d’évaluation principal de cet essai est la variation de la Baseline à la semaine 6 pour le score total PANSS (Positive and Negative Syndrome Scale). Le type de critère d’évaluation doit être défini comme suit :

``` r
parameters$endpoint_type = "Normal"
```

Une réduction plus importante du score total PANSS correspond à un résultat favorable et donc le paramètre de `direction` sera défini comme « Lower », c’est-à-dire

``` r
parameters$direction = "Lower"
```

Deux cents patients par bras seront initialement recrutés dans l’essai et randomisés à l’aide d’un schéma 1:1 pour recevoir un placebo ou le traitement expérimental. Cela signifie que le nombre initial de patients dans les groupes placebo et traitement doit être spécifié comme suit :

``` r
parameters$sample_size = c(200, 200)
```

La taille de l’échantillon peut être ajustée à l’analyse intermédiaire 2, comme expliqué ci-dessous.

Comme dans de nombreux autres essais en neurosciences, un taux d’abandon des patients assez élevé est attendu dans l’essai. Le taux sera fixé à 20% :

``` r
parameters$dropout_rate = 0.2
``` 

Les hypothèses d’effet du traitement dans les groupes placebo et traitement seront définies à l’aide de la moyenne et de l’écart-type pour le critère d’évaluation principal :

``` r
parameters$control_mean = -15
parameters$control_sd = 25
parameters$treatment_mean = -22.5
parameters$treatment_sd = 25
```

Les paramètres de l’analyse intermédiaire seront définis comme suit. Les fractions d’information aux deux analyses intermédiaires sont fixées à 30% et 70%, respectivement. La taille de l’échantillon à l’analyse intermédiaire 2 peut être augmentée jusqu’à un plafond défini prospectivement, qui est fixé à 30%. En conséquence, le vecteur des fractions d’information est donné par

``` r
parameters$info_frac = c(0.3, 0.7, 1, 1.3)
``` 

La règle d’arrêt pour futilité dans l’analyse intermédiaire 1 reposera sur un seuil de 10% (ce qui signifie que l’essai sera terminé pour futilité si la puissance conditionnelle n’est pas supérieure à 10%) :

``` r
parameters$futility_threshold = 0.1
``` 

La règle de réestimation de la taille de l’échantillon dans l’analyse intermédiaire 2 sera définie en utilisant les limites inférieure et supérieure de l’intervalle prometteur ainsi que la puissance conditionnelle cible pour augmenter le nombre de patients :

``` r
parameters$promising_interval = c(0.5, 0.9)
parameters$target_power = 0.9
``` 

Enfin, le risque d’erreur de première espèce et le nombre de simulation doivent être spécifiés. Le risque d’erreur de première espèce unilatéral sera fixé à 2.5% dans cet essai de phase III :

``` r
parameters$alpha = 0.025
```

De plus, 10 000 simulations seront utilisées dans cet exercice de simulation :

``` r
parameters$nsims = 10000
```

La liste des paramètres doit être transmise à la fonction `ADSSMod`, la fonction exécutera des simulations pour calculer les caractéristiques fonctionnelles clés et enregistrer les résultats dans l’objet `results` :

``` r
results = ADSSMod(parameters)
```

Un rapport de simulation complet est facile à créer en appelant la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyA2.docx")
```

Le rapport de simulation sera enregistré dans le répertoire de travail actuel.

Tout comme dans [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1French), le rapport de simulation fournit un résumé détaillé des caractéristiques fonctionnelles du design, y compris la probabilité d’arrêt pour futilité à l’analyse intermédiaire 1 et la probabilité d’ajuster la taille de l’échantillon à l’analyse intermédiaire 2. Le rapport fournit également des comparaisons de probabilité de succès entre le design adaptatif et un design traditionnel sans possibilité d’augmenter la taille de l’échantillon.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyA2.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyA2.docx).

## Références

Meltzer HY et al. (2011). Lurasidone in the treatment of schizophrenia: A randomized, double-blind, placebo- and olanzapine-controlled study. American Journal of Psychiatry. 168, 957-967. DOI: 10.1176/appi.ajp.2011.10060907.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
