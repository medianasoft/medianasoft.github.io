---
layout: page
title: MedianaDesigner
group: 
---

# Essais traditionnels avec comparaisons dose-placebo multiples et critères d’évaluation multiples (étude de cas G3)

## Essai de phase III avec trois comparaisons dose-placebo et deux critères d’évaluation multiples

Pour introduire un processus général de calcul de puissance dans les essais cliniques présentant des problèmes complexes de multiplicité, par exemple des essais avec deux sources de multiplicité, un essai de phase III chez des patients atteints de schizophrénie psychotique aiguë (Nasrallah et al., 2013) sera utilisé. Cet essai a examiné le profil d’efficacité de trois doses de lurasidone (40 mg / jour, 80 mg / jour et 120 mg / jour) par rapport au placebo. Les évaluations de l’efficacité ont été réalisées en fonction de deux critères d’évaluation définis prospectivement (critère d’évaluation primaires et critère d’évaluation secondaire clé) :

* Critère d’évaluation 1 (primaire) : Variation du score total de l’échelle PANSS (Positive and Negative Syndrome Scale) à la semaine 6.
* Critère d’évaluation 2 (critère d’évaluation secondaire clé) : Variation de la Baseline à la semaine 6 pour le score CGI-S (Clinical Global Impressions Severity Scale).

Les deux critères d’évaluation sont normalement distribués et la corrélation entre eux est supposée être de 0.5. Il est important de noter que des valeurs plus faibles du score total PANSS et du score CGI-S sont associées à une amélioration et indiquent un effet bénéfique du traitement.

Le même cadre général sera supposé dans cette étude de cas. Les doses du traitement expérimental seront étiquetées comme Dose L, Dose M et Dose H. Les trois comparaisons dose-placebo constituent la première source de multiplicité et l’évaluation des critères d’évaluation primaires et secondaires définit la deuxième source de multiplicité. Cela signifie que l’évaluation de l’efficacité implique six hypothèses nulles d’absence d’effet. Les hypothèses nulles sont définies comme suit :

* H1 : Hypothèse nulle d’absence de différence entre la Dose L et le placebo par rapport au critère d’évaluation 1.
* H2 : Hypothèse nulle d’absence de différence entre la Dose M et le placebo par rapport au critère d’évaluation 1.
* H3 : Hypothèse nulle d’absence de différence entre la Dose H et le placebo par rapport au critère d’évaluation 1.
* H4 : Hypothèse nulle d’absence de différence entre la Dose L et le placebo par rapport au critère d’évaluation 2.
* H5 : Hypothèse nulle d’absence de différence entre la Dose M et le placebo par rapport au critère d’évaluation 2.
* H6 : Hypothèse nulle d’absence de différence entre la Dose H et le placebo par rapport au critère d’évaluation 2.

Les hypothèses nulles sont naturellement regroupées en deux familles en fonction du critère d’évaluation sous-jacent :

* Famille 1 (famille primaire) : Hypothèses H1, H2 et H3.
* Famille 2 (famille secondaire) : Hypothèses H4, H5 et H6.

Les familles seront testées séquentiellement en commençant par la famille primaire. Les hypothèses de la famille primaire imposent des restrictions logiques aux hypothèses secondaires en ce sens qu’une comparaison entre une dose et le placebo pour le critère d’évaluation secondaire clé ne sera effectuée que si un effet du traitement significatif est établi à la même dose dans la famille primaire. Cela implique que l’hypothèse secondaire H4 ne peut être testée que si l’hypothèse primaire correspondante H1 est rejetée, sinon cette hypothèse secondaire est acceptée sans test.

Pour tenir compte des deux sources de multiplicité, un ajustement de la multiplicité basé sur une procédure gatekeeping sera utilisé dans cette étude de cas. Cette procédure gatekeeping applique des procédures composantes de type Hochberg dans chaque famille :

* Une procédure de Hochberg tronquée est utilisée dans la Famille 1 puisque cette famille sert de gatekeeper pour la famille secondaire. En raison de l’utilisation d’une procédure tronquée dans la famille primaire, la procédure gatekeeping peut passer à la Famille 2 même si une seule hypothèse est rejetée dans la Famille 1, c’est-à-dire qu’une seule dose est statistiquement significative sur le critère d’évaluation principal. La procédure de Hochberg tronquée est définie à l’aide d’un paramètre de troncature prédéfini de 0.8.
* La procédure de Hochberg régulière est utilisée dans la Famille 2 car il s’agit de la dernière famille dans la séquence.

Comme dans [l’étude de cas G1](https://medianasoft.github.io/CaseStudyG1), les procédures composantes de Hochberg assurent un contrôle fort du risque d’erreur de première espèce au sein de chaque famille, car les statistiques de test correspondant aux comparaisons dose-placebo suivent une distribution normale multivariée avec des corrélations positives par paires.

Les calculs de puissance pour la procédure gatekeeping basée sur Hochberg seront effectués à l’aide de la fonction `MultAdj`.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `MultAdj`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/MultAdjFrench.pdf).

## Calculs de puissance à l’aide de la fonction `MultAdj`

Le processus d’évaluation des caractéristiques les plus importantes de l’ajustement de multiplicité spécifié, par exemple la probabilité globale de succès dans l’essai ainsi que la probabilité de rejeter au moins une hypothèse nulle dans la famille secondaire, sera mis en place à l’aide de la fonction `MultAdj`.

Le processus commence par le chargement du paquet MedianaDesigner et le lancement d’une liste vide de paramètres :

``` r
library(MedianaDesigner)
parameters = list()
```

Comme dans les autres études de cas, le premier paramètre à définir est le type de critère d’évaluation. Dans ce cas, deux critères d’évaluations normalement distribués seront spécifiés :

``` r
parameters$endpoint_type = "Normal"
```

Comme indiqué ci-dessus, des niveaux plus faibles des deux critères d’évaluation correspondent à un résultat plus favorable :

``` r
parameters$direction = "Lower"
```

Un problème de multiplicité « bivariée » avec deux sources de multiplicité se pose dans cette étude de cas en raison du fait que trois comparaisons dose-placebo seront effectuées par rapport à deux critères d’évaluation :

``` r
parameters$n_comparisons = 3
parameters$n_endpoints = 2
```

Le vecteur de taille d’échantillon avec une randomisation égale de 460 patients aux quatre bras de l’essai est spécifié comme suit :

``` r
parameters$sample_size = c(115, 115, 115, 115)
```

La durée de l’essai est assez courte dans cette étude de cas et 10% des patients devraient être perdus de vue d’ici la fin de la période de traitement de 6 semaines :

``` r
parameters$dropout_rate = 0.1
``` 

Les hypothèses d’effet du traitement doivent être spécifiées à l’aide de vecteurs de paramètres de distribution pour le bras placebo et de matrices pour chaque bras de traitement. En commençant par le groupe placebo, les changements moyens attendus et les écarts-types correspondants pour le score total PANSS et le score CGI-S sont donnés par

``` r
parameters$control_mean = c(-15, -1)
parameters$control_sd = c(20, 1)
```

Pour capturer les changements moyens pour les deux mêmes critères d’évaluation dans les trois bras de traitement, la matrice suivante doit être configurée :

``` r
parameters$treatment_mean = matrix(c(-20, -21, -23,
                                     -1.35, -1.40, -1.45), 2, 3, byrow = TRUE)

```

La fonction `MultAdj` s’attend à une matrice 2 par 3 puisque les lignes correspondent aux critères d’évaluation et les colonnes correspondent aux comparaisons dose-placebo. Pour vous assurer que la matrice est correctement composée de deux lignes, l’option `byrow` doit être définie sur TRUE.

Les écarts-types sont définis de la même manière :

``` r
parameters$treatment_sd = matrix(c(20, 20, 20,
                                     1, 1, 1), 2, 3, byrow = TRUE)

```

Le dernier paramètre de distribution à spécifier est la matrice de corrélation pour les critères d’évaluation primaire et secondaire clé. Étant donné que la corrélation entre les critères d’évaluation est définie à 0.5, la matrice est donnée par

``` r
parameters$endpoint_correlation = matrix(c(1, 0.5, 
                                           0.5, 1), 2, 2)
```

Pour définir les paramètres de la procédure gatekeeping, la procédure composante doit d’abord être spécifiée :

``` r
parameters$mult_test = "Hochberg"
```

Les règles de rejet d’hypothèse dans cette procédure gatekeeping seront construites en utilisant la méthode de mixture modifiée :

``` r
parameters$mult_method = "Modified"
```

Le paramètre de troncature pour la procédure de Hochberg tronquée dans la famille primaire est défini à 0.8 et, comme la procédure de Hochberg régulière est utilisée dans la famille secondaire, le paramètre de troncature correspondant est égal à 1. Cela implique que

``` r
parameters$mult_test_gamma = c(0.8, 1)
```

Le risque global d’erreur de première espèce unilatéral doit être contrôlé à un niveau de 2.5% dans cet essai de phase III et, par conséquent,

``` r
parameters$alpha = 0.025
```

Les calculs de puissance seront effectués à l’aide de 10 000 simulations :

``` r
parameters$nsims = 10000
```

Cette liste de paramètres doit maintenant être transmise à la fonction `MultAdj` :

``` r
results = MultAdj(parameters)
```

et les résultats des simulations seront enregistrés dans l’objet `results`. À l’aide de cet objet, un rapport de simulation détaillé avec un résumé des calculs de puissance est facilement généré à l’aide de la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyG3.docx")
```

Le rapport de simulation sera enregistré dans le répertoire de travail actuel.

Les principales caractéristiques de puissance de l’ajustement de multiplicité spécifié sont résumées dans les Tables 8 et 9. Comme dans [l’étude de cas G1](https://medianasoft.github.io/CaseStudyG1), la Table 8 fournit un résumé des calculs de puissance marginale. La puissance marginale, c’est-à-dire la probabilité de rejeter chaque hypothèse, est calculée avant et après l’ajustement de multiplicité. La Table 9 présente la puissance disjonctive et conjonctive au sein de chaque famille. La puissance disjonctive et conjonctive spécifique à chaque famille est défini comme suit :

* La puissance disjonctive est définie comme la probabilité de rejeter au moins une hypothèse au sein de chaque famille.
* La puissance conjonctive est définie comme la probabilité de rejeter toutes les hypothèses au sein de chaque famille.

Dans les deux cas, les calculs de puissance sont effectués après l’application de l’ajustement de multiplicité spécifié.

La puissance disjonctive dans la famille primaire définit la probabilité globale de succès dans l’essai et la puissance disjonctive dans la famille secondaire aide le sponsor de l’essai à déterminer la probabilité de pouvoir faire des allégations d’efficacité supplémentaires en fonction du critère d’évaluation secondaire clé sélectionné.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyG3.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyG3.docx).

## Références

Nasrallah et al. Lurasidone for the treatment of acutely psychotic patients with schizophrenia: A 6-week, randomized, placebo-controlled study. Journal of Psychiatric Research. 2013; 47:670-677. DOI: 10.1016/j.jpsychires.2013.01.020.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
