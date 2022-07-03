---
layout: page
title: MedianaDesigner
group: 
---

# Essais traditionnels avec des comparaisons dose-placebo multiples (étude de cas G1)

## Essai de phase III avec trois comparaisons dose-placebo

Les calculs de puissance dans les essais cliniques avec des comparaisons doses-placebo multiples seront illustrés à l’aide d’une étude de cas basée sur l’essai de saxagliptine chez des patients naïfs au traitement atteints de diabète de type 2 (Rosenstock et al., 2009). Trois doses de saxagliptine (2.5 mg/jour, 5 mg/jour et 10 mg/jour) ont été testées dans cet essai de phase III par rapport au placebo. L’évaluation primaire de l’efficacité a été réalisée en utilisant la variation d’hémoglobine A1c (HbA1c) de la Baseline à la semaine 24.

La multiplicité est induite dans cet essai par un seul facteur, c’est-à-dire par l’évaluation de trois comparaisons dose-placebo par rapport à un seul critère d’évaluation principal. Une famille de trois hypothèses nulles d’absence d’effet sera définie sur la base de la comparaison de chaque dose avec le placebo. Un ajustement de multiplicité basé sur la procédure de Hochberg sera appliqué dans cette étude de cas pour protéger le risque d’erreur de première espèce global à un niveau prédéfini (niveau unilatéral de 0.025). La procédure de Hochberg est une procédure de tests multiples efficace, connue pour être uniformément plus puissante que les procédures de Bonferroni ou de Holm. La procédure de Hochberg ne préserve pas toujours le risque d’erreur de première espèce, mais elle garantit un contrôle fort du risque d’erreur de première espèce dans ce contexte, car les statistiques de test correspondant aux comparaisons dose-placebo suivent une distribution normale multivariée avec des corrélations positives par paires. En outre, la procédure de Hochberg peut incorporer des informations sur l’importance relative des hypothèses individuelles mais, dans cet essai, des poids d’hypothèse égaux seront supposés. Un calcul de puissance sera effectué à l’aide de la fonction `MultAdj`.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `MultAdj`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/MultAdjFrench.pdf).

## Calculs de puissance à l’aide de la fonction `MultAdj`

La fonction `MultAdj` sera utilisée dans cette section pour illustrer le processus de calcul des caractéristiques de puissance dans cet essai, telles que la probabilité de rejeter chaque hypothèse nulle individuelle ainsi que la probabilité globale de succès dans l’essai, c’est-à-dire la probabilité de rejeter au moins une hypothèse dans la famille.

Dans un premier temps, le paquet MedianaDesigner doit être chargé :

``` r
library(MedianaDesigner)
```

et une liste vide des paramètres de la fonction doit être créée :

``` r
parameters = list()
```

Pour commencer à remplir la liste des paramètres, une hypothèse de normalité sera faite pour le critère d’évaluation principal de l’efficacité (variation d’HbA1c par rapport à la Baseline) dans l’essai :

``` r
parameters$endpoint_type = "Normal"
```

Une réduction du taux d’HbA1c correspond à un résultat plus favorable et donc le paramètre de `direction` sera défini par « Lower », c’est-à-dire

``` r
parameters$direction = "Lower"
```

Le type de problème de multiplicité dans cet essai est défini en spécifiant le nombre de comparaisons dose-placebo et le nombre de critères d’évaluation :

``` r
parameters$n_comparisons = 3
parameters$n_endpoints = 1
```

La taille totale de l’échantillon de 400 patients (100 patients par groupe d’essai) utilisés dans l’essai de saxagliptine sera supposée dans cette étude de cas. Un design équilibré sera envisagé et, par conséquent, les tailles d’échantillon dans les quatre bras d’essai seront spécifiées comme suit :

``` r
parameters$sample_size = c(100, 100, 100, 100)
```

Le taux d’abandon des patients au cours de la période de traitement de 24 semaines sera fixé à 30% :

``` r
parameters$dropout_rate = 0.30
``` 

Les calculs de puissance seront effectués à l’aide de l’ensemble suivant d’hypothèses d’effet de traitement. Tout d’abord, une légère augmentation du taux moyen d’HbA1c sera supposée dans le bras placebo (0.1%), c’est-à-dire

``` r
parameters$control_mean = 0.1
```

On supposera que les taux moyens d’HbA1c diminueront dans les trois bras de traitement comme suit :

``` r
parameters$treatment_mean = c(-0.4, -0.45, -0.5)
```

Un écart-type commun de 1.5% sera supposé dans l’ensemble des groupes d’essai, c’est-à-dire

``` r
parameters$control_sd = 1.5
parameters$treatment_sd = c(1.5, 1.5, 1.5)
```

Un ajustement de la multiplicité basé sur la procédure de Hochberg est spécifié comme suit :

``` r
parameters$mult_test = "Hochberg"
```

Étant donné que les hypothèses nulles d’intérêt sont supposées être pondérées de manière égale, le vecteur des pondérations d’hypothèse, c’est-à-dire `parameters$weights`, n’a pas besoin d’être explicitement spécifié.

Le risque global d’erreur de première espèce unilatéral dans cet essai de phase III sera contrôlé à un niveau de 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

Les calculs de puissance seront effectués à l’aide de 10 000 simulations :

``` r
parameters$nsims = 10000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour obtenir la reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

La liste des paramètres résultante sera transmise à la fonction `MultAdj` et les résultats de la simulation seront enregistrés dans l’objet `results` :

``` r
results = MultAdj(parameters)
```

Pour générer un rapport de simulation détaillé avec un résumé des calculs de puissance, la fonction `GenerateReport` sera appelée :

``` r
GenerateReport(results, 
               "CaseStudyG1.docx")
```

Le rapport de simulation sera enregistré dans le répertoire de travail actuel.

Deux caractéristiques clés sont présentées dans le rapport de simulation. Un résumé des calculs de puissance marginale se trouve dans la Table 8. La puissance marginale pour chaque hypothèse, c’est-à-dire la probabilité de rejeter chaque hypothèse ou la probabilité d’établir un effet significatif à chaque niveau de dose, est calculée avant et après l’ajustement de multiplicité basé sur Hochberg. Pour définir la puissance globale, deux approches sont envisagées :

* La puissance disjonctive est définie comme la probabilité de rejeter au moins une hypothèse, c’est-à-dire la probabilité qu’au moins une dose apporte une amélioration statistiquement significative par rapport au placebo dans l’essai, après l’ajustement de multiplicité spécifié. La probabilité globale de succès d’un essai est généralement définie en fonction de cette quantité.

* La puissance conjonctive est définie comme la probabilité de rejeter simultanément toutes les hypothèses après l’ajustement de multiplicité spécifié.

Les valeurs de puissance globale sont présentées dans la Table 9.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyG1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyG1.docx).

## Références

Rosenstock et al. Effect of saxagliptin monotherapy in treatment-naive patients with type 2 diabetes. Current Medical Research And Opinion. 2009; 25:2401-2411. DOI: 10.1185/03007990903178735.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
