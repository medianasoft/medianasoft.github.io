---
layout: page
title: MedianaDesigner
group: 
---

# Essais traditionnels avec des designs randomisés par grappes (étude de cas H1)

## Étude d’intervention pour évaluer l’impact des tests de diagnostic rapide du paludisme

Cette étude de cas est vaguement basée sur une étude récente menée au Ghana pour examiner l’impact de la fourniture de tests de diagnostic rapide du paludisme sur la gestion de la fièvre chez les enfants et les adultes (Ansah et al., 2015).

L’objectif ultime de cette étude était de réduire le surdiagnostic et le surtraitement du paludisme et le critère d’évaluation principal était un critère d’évaluation binaire basé sur la proportion de tests de paludisme négatifs chez les sujets ayant reçu des traitements antipaludiques sélectionnés. Un design randomisé par grappes avec deux bras (traitement versus contrôle) a été utilisé dans cette étude. Les pharmacies de plusieurs communautés d’un district du Ghana ont été utilisées comme grappes. Le nombre total de grappes dans l’étude était de 24 (12 grappes par bras) et un cadre équilibré avec un nombre égal de sujets dans les grappes a été pris en compte à l’étape de la conception de l’étude.

Plusieurs hypothèses simplificatrices sont formulées dans l’étude de cas, par exemple, une seule population de sujets, à savoir des adultes, est prise en compte. Le taux primaire est supposé être de 60 % et 40 % dans les groupes témoin et traitement, respectivement. Une mesure de la cohérence à l’intérieur des grappes couramment utilisée dans les études randomisées par grappes, c.-à-d. le coefficient de coefficient de corrélation intracluster (ICC), est fixée à 0.1 dans les deux groupes de l’étude. Cette faible valeur indique que les résultats des sujets devraient être faiblement corrélés au sein de chaque groupe.

Le code présenté ci-dessous illustre l’utilisation de la fonction ClustRand pour évaluer la probabilité de succès de l’étude et identifier le nombre nécessaire de sujets qui terminent l’étude pour atteindre une puissance de 80 % avec un niveau de signification bilatéral standard de 5% (ou, de manière équivalente, un niveau de signification unilatéral de 2.5%).

Pour plus d’informations sur la méthodologie statistique utilisée dans cette fonction, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ClustRandFrench.pdf).

## Calculs de puissance à l’aide de la fonction `ClustRand`

La fonction `ClustRand` permet des calculs de puissance basés sur des simulations dans une large classe d’études et d’essais avec des plans randomisés en grappes. Pour effectuer des calculs de puissance, le paquet MedianaDesigner doit d’abord être chargé:

``` r
library(MedianaDesigner)
```

et une liste vide des paramètres de la fonction doit être créée:

``` r
parameters = list()
```

Dans ce qui suit, la liste des paramètres sera remplie à l’aide des informations résumées ci-dessus. À partir du critère d’évaluation principal, le type de critère d’évaluation doit être spécifié:

``` r
parameters$endpoint_type = "Binary"
```

L’hypothèse principale de l’étude stipule que la proportion de tests de paludisme négatifs devrait être réduite dans le bras de traitement par rapport au bras témoin et que le paramètre de `direction` doit donc être réglé sur « Lower », c’est-à-dire

``` r
parameters$direction = "Lower"
```

Les spécifications relatives aux proportions attendues de tests de paludisme négatifs dans les deux bras de l’étude, c’est-à-dire 60% dans le groupe témoin et 40% dans le groupe traitement, sont définies comme suit:

``` r
parameters$control_rate = 0.6
parameters$treatment_rate = 0.4

```

Les données sont recueillies auprès de 24 grappes (pharmacies) et les grappes sont assignées au hasard aux deux groupes de l’étude. On s’attend à ce que 960 sujets terminent l’étude dans chaque bras, c’est-à-dire

``` r
parameters$sample_size = c(960, 960)

```

Cela implique une taille de groupe commune de 80 sujets. Pour définir la distribution des sujets entre les grappes, les deux vecteurs suivants doivent être configurés:

``` r
parameters$control_cluster_size = rep(80, 12)
parameters$treatment_cluster_size = rep(80, 12)
```

Selon cette spécification, il y aura 12 grappes de 80 sujets chacune dans le bras témoin ainsi que dans le bras de traitement. Cette configuration correspond à un schéma de regroupement par grappe fixe:

``` r
parameters$cluster_scheme = "Fixed"
```

Il s’agit du paramètre le plus couramment utilisé dans les essais randomisés par grappes. La fonction `ClustRand` prend également en charge des tailles de cluster aléatoires. Ce paramètre est souvent pris en compte dans le contexte des évaluations de sensibilité, voir ci-dessous.

Un coefficient de corrélation intracluster commun de 0.1 sera supposé dans les deux bras de l’étude:

``` r
parameters$control_icc = 0.1
parameters$treatment_icc = 0.1

```

Enfin, la méthodologie d’analyse des résultats corrélés au sein de chaque cluster doit être choisie par l’utilisateur. La fonction `ClustRand` prend en charge la méthode GEE (équations d’estimation généralisées) et la méthode GLMEM (modèle linéaire généralisé à effets mixtes). Par exemple, la méthode GEE peut être spécifiée comme suit:

``` r
parameters$method_type = "GEE"
```

La comparaison des proportions de tests de paludisme négatifs dans les groupes témoin et de traitement sera effectuée en utilisant un niveau de signification unilatéral de 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

et les calculs de puissance seront effectués à l’aide de 1,000 simulations:

``` r
parameters$nsims = 1000
```

Il est important de noter que le nombre d’exécutions de simulation peut être facilement augmenté à 10,000 exécutions pour la méthode GEE. Cependant, la mise en œuvre actuelle de la méthode GLMEM est beaucoup moins efficace que celle de la méthode GEE et entraînera probablement des temps d’exécution de simulation beaucoup plus longs. Pour cette raison, il est fortement recommandé de tirer parti des calculs parallèles pris en charge par la fonction `ClustRand`. L’utilisateur peut spécifier le nombre de cœurs:

``` r
parameters$ncores = 4
```

et les simulations seront exécutées en parallèle sur ces cœurs, puis combinées à la fin.

En général, l’amorce de l’algorithme de génération de nombres aléatoires peut être explicitement spécifiée, par exemple,

``` r
parameters$random_seed = 50219
```

Si l’amorce n’est pas spécifiée, la valeur par défaut (49283) sera utilisée dans cette fonction.

Un autre paramètre facultatif est `descriptive_statistics`. Si cette option est activée, c’est-à-dire

``` r
parameters$descriptive_statistics = TRUE
``` 

des statistiques descriptives clés, telles que la proportion de tests de paludisme négatifs dans chaque bras de l’étude, seront calculées et enregistrées pour chaque simulation. Par défaut, la fonction ne calcule pas les statistiques descriptives.

Maintenant que les paramètres requis ont été définis, la liste des `parameters` doit être transmise à la fonction `ClustRand` pour exécuter les simulations :

``` r
results = ClustRand(parameters)
```

Les résultats des simulations sont enregistrés dans l’objet `results` et, pour créer un rapport de simulation avec un résumé des calculs de puissance, cet objet est transmis à la fonction `GenerateReport`:

``` r
GenerateReport(results, 
               "CaseStudyH1.docx")
```

Le rapport de simulation résultant (document Microsoft Word) est enregistré dans le répertoire de travail actuel.

Le rapport présente un résumé des paramètres du design enregistrés dans la liste des `parameters` et un tableau unique (tableau 8) avec les résultats du calcul de la puissance. Une caractéristique importante de la méthode d’analyse utilisée dans cette étude de cas (GEE) est que la version standard de cette méthode est plutôt peu fiable lorsque le nombre de grappes est faible, par exemple 24 grappes, et est connue pour entraîner des taux d’erreur de type I trop élevés. D’autres formulations de la méthode GEE, connues sous le nom de corrections de biais, sont recommandées pour les environnements avec un nombre de grappes plus petit. Dans ce cas, la taille supposée de l’échantillon et le nombre de grappes garantissent une puissance de 80 % pour les deux méthodes d’analyse avec correction du biais (correction de Kauermann-Carroll et correction de Mancl-DeRouen).

## Évaluations de sensibilité à l’aide de la fonction `ClustRand`

Comme indiqué dans le [manuel technique] (http://www.mediana.us/MedianaDesigner/ClustRandFrench.pdf), il est recommandé d’effectuer des évaluations de sensibilité pour déterminer l’impact des paramètres clés sur la probabilité de succès de l’étude et, en fin de compte, assurer un design d’essai robuste. Les évaluations de sensibilité sont souvent effectuées en fonction des facteurs suivants:

* ICC mal spécifié.
* Variabilité de la taille des grappes.
* Petit nombre de grappes.

À titre d’exemple, l’utilisateur peut examiner l’incidence des changements dans la taille des grappes sur la probabilité de succès de l’étude. Pour ce faire, un schéma de regroupement par grappe aléatoire doit être activé:

``` r
parameters$cluster_scheme = "Random"
```

Dans ce cas, le nombre de grappes dans chaque bras d’étude est fixe, mais la taille des grappes est aléatoire. Les tailles de grappes sont générées à partir d’une distribution de Bernoulli généralisée avec des ensembles prédéfinis de tailles de grappes relatives. Par exemple, la spécification suivante dans le bras témoin

``` r
parameters$control_cluster_proportion = rep(1 / 12, 12)
```

définit un vecteur de tailles de grappes relatives. En moyenne, la taille de chacune des 12 grappes est de 960 / 12 = 80 sujets.

Un vecteur de tailles de grappes relatives dans le bras de traitement peut être spécifié de la même manière:


``` r
parameters$control_treatment_proportion = rep(1 / 12, 12)
```

Les calculs de puissance pour cette étude de cas peuvent maintenant être exécutés à l’aide du schéma de regroupement par grappe aléatoire et la taille de l’échantillon peut être sélectionnée pour assurer une puissance de 80% dans ce schéma.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana:

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyH1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyH1.docx).

## Références

Ansah et al. The impact of providing rapid diagnostic malaria tests on fever management in the private retail sector in Ghana: A cluster randomized trial. British Medical Journal. 2015; 350:h1019. DOI: 10.1136/bmj.h1019.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).

