---
layout: page
title: MedianaDesigner
group: 
---

# Designs adaptatifs avec sélection de la population basée sur les données (étude de cas C1)

## Essai de phase III avec un critère d’évaluation de type temps jusqu’à un évènement

Les designs adaptatifs avec des règles de décision visant à identifier la ou les populations de patients les plus prometteuses seront illustrés à l’aide d’une étude de cas basée sur l’essai SATURN pour le traitement du cancer du poumon non à petites cellules avancé (Cappuzzo et al., 2010). Le critère d’évaluation principal de l’efficacité était la survie sans progression (SSP) et cet essai a été mené pour évaluer l’effet de l’erlotinib dans deux populations de patients pré-spécifiées :

* Population globale de patients atteints d’un cancer du poumon non à petites cellules avancé.
* Sous-population de patients ayant un statut de biomarqueur positif, c’est-à-dire un statut immunohistochimie EGFR (epidermal growth factor receptor) positif.

L’approche multi-population a été utilisée pour mieux caractériser le profil d’efficacité de l’erlotinib dans cette population de patients et améliorer les chances de succès de l’essai.

L’essai SATURN s’est appuyé sur un design séquentiel par groupes avec une seule évaluation intermédiaire de l’efficacité. Cette étude de cas présente une extension du design de l’essai SATURN qui conserve l’approche multi-populations, mais utilise deux analyses intermédiaires avec des règles de décision basées sur des données :

* L’analyse intermédiaire 1 permet une évaluation de la futilité dans la population globale, c’est-à-dire que l’essai sera interrompu pour futilité si l’effet global du traitement est trop faible.
* L’analyse intermédiaire 2 met en place une option permettant de sélectionner la ou les meilleures populations de l’essai.

Le design adaptatif de sélection de la population pris en compte dans cette étude de cas suppose que le sponsor de l’essai prévoit de poursuivre trois allégations d’efficacité distinctes dans l’essai :

* Allégation d’efficacité dans la population globale.
* Allégation d’efficacité dans la sous-population prédéfinie (sous-population de patients biomarqueur-positifs).
* Allégation d’efficacité dans les deux populations.

Les allégations d’efficacité définissent les hypothèses nulles d’absence d’effet de traitement à tester au troisième point de décision (analyse finale). Ces hypothèses sont choisies lors de l’analyse intermédiaire 2. Par exemple, le recrutement de patients biomarqueur-négatifs peut être interrompu après l’analyse intermédiaire 2, auquel cas seule l’hypothèse nulle d’absence d’effet dans la sous-population de patients biomarqueur-positifs sera testée lors de l’analyse finale. L’évaluation de l’efficacité peut également être réalisée dans la population globale et la sous-population de patients biomarqueur positifs, c’est-à-dire que les deux hypothèses nulles correspondantes seront testées simultanément.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `ADPopSel`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ADPopSelFrench.pdf).

## Conception d’un essai adaptatif de sélection de population à l’aide de la fonction `ADPopSel`

L’essai adaptatif de sélection de la population décrit ci-dessus sera conçu en fonction des paramètres de l’essai SATURN. Les principales caractéristiques fonctionnelles du design adaptatif seront évaluées à l’aide de la fonction `ADPopSel`.

Premièrement, Le paquet MedianaDesigner sera chargé, c’est-à-dire,

``` r
library(MedianaDesigner)
```

et la liste des paramètres de la fonction `ADPopSel` sera initialisée, c’est-à-dire

``` r
parameters = list()
```

Les arguments requis de cette liste de paramètres sont assez similaires aux arguments des fonctions `ADSSMod` et `ADTreatSel` mais comprennent aussi des paramètres spécifiques à `ADPopSel`, par exemple, les paramètres de la règle de sélection de population qui seront appliqués à l’analyse intermédiaire 2.

Étant donné que le critère d’évaluation principal de l’efficacité est un critère d’évaluation de type temps jusqu’à un évènement, le type de critère d’évaluation « Time-to-event » doit être défini :

``` r
parameters$endpoint_type = "Time-to-event"
```

Un temps de survie plus long représente un résultat plus favorable dans cet essai et, par conséquent, le paramètre de `direction` doit être défini sur « Higher », c’est-à-dire

``` r
parameters$direction = "Higher"
```

Un design équilibré sera utilisé dans l’essai et le nombre total de patients dans les bras de contrôle et de traitement est donné par

``` r
parameters$sample_size = c(480, 480)
```

Le nombre de patients au sein de la sous-population des patients biomarqueur-positifs est établi en spécifiant la prévalence des patients positifs au biomarqueur dans la population globale. On suppose que la prévalence est de 50%, c’est-à-dire

``` r
parameters$prevalence = 0.5
``` 

ce qui signifie qu’il y aura environ 222 patients avec un statut biomarqueur positif dans chaque bras d’essai.

Étant donné qu’il y a deux populations de patients dans l’essai, des hypothèses d’effet de traitement spécifiques à chaque population doivent être faites. Un temps médian commun de SSP sera supposé dans les sous-populations biomarqueur-négatifs et biomarqueur-positifs dans le groupe de contrôle, à savoir un temps médian de 11 mois, c’est-à-dire
 
``` r
parameters$control_time = c(11, 11)
``` 

Lors de la spécification des temps médians de SSP dans le bras de traitement, un effet de traitement différentiel sera supposé, c’est-à-dire un effet plus faible dans le sous-ensemble de patients biomarqueur-négatifs et un effet plus fort chez les patients biomarqueur-positifs. Les temps médians de SSP dans ces deux sous-populations (mesurés en mois) sont donnés par

``` r
parameters$treatment_time = c(13, 15.7)

``` 

Ces effets du traitement correspondent à peu près aux rapports de risque ("hazard ratio") de 0.85 dans la sous-population biomarqueur-négatif et de 0.7 dans la sous-population biomarqueur-positif.

De même, le nombre cible d’événements à l’analyse finale doit être établi séparément dans la population globale et la sous-population de patients biomarqueur-positifs :

``` r
parameters$event_count = c(700, 300)
``` 

Si les données d’efficacité de l’analyse intermédiaire 2 appuient la décision d’effectuer l’analyse finale dans la population globale, l’essai se poursuivra jusqu’à ce que 700 événements aient été accumulés dans cette population. Toutefois, si l’hypothèse nulle d’absence d’effet dans la sous-population biomarqueur-positif est choisie, la durée de l’essai sera déterminée par le temps qu’il faut pour accumuler 300 événements au sein de cette sous-population.

Les fractions d’information de l’analyse intermédiaire 1, de l’analyse intermédiaire 2 et de l’analyse finale sont définies comme suit :

``` r
parameters$info_frac = c(0.2, 0.6, 1)
``` 

Ces fractions sont exprimées en termes de nombre d’événements (nombre de patients présentant une progression de la maladie) dans la population globale de patients. En particulier, l’évaluation de la futilité à l’analyse intermédiaire 1 sera effectuée après 20% des événements dans la population globale et la règle de sélection de la population sera appliquée à l’analyse intermédiaire 2 après 60% du nombre total d’événements.

En continuant avec les règles de décision à appliquer aux deux analyses intermédiaires, le seuil de futilité de l’analyse intermédiaire 1 est fixé à 20%, c’est-à-dire

``` r
parameters$futility_threshold = 0.2
``` 

Cela signifie que l’essai sera terminé pour futilité si la puissance conditionnelle dans l’ensemble de la population est inférieure à 20%.

Les deux paramètres suivants sont les éléments clés de la règle de sélection de la population lors de l’analyse intermédiaire 2. Ces paramètres, connus sous le nom de seuils d’influence et d’interaction, définissent les conditions d’influence et d’interaction qui guident le processus d’identification des populations de patients les plus appropriées. La condition d’influence stipule qu’une allégation d’efficacité dans l’ensemble de la population ne peut être poursuivie que si un effet bénéfique global ne se limite pas à la sous-population biomarqueur positive. Si cette condition est remplie, la condition d’interaction est évaluée. Cette condition stipule que, pour conclure que le traitement est simultanément efficace dans les deux populations, l’effet du traitement dans la sous-population biomarqueur-positif devrait être sensiblement plus important que l’effet dans la sous-population biomarqueur-négatif. Les seuils d’influence et d’interaction sont définis avec les valeurs suivantes dans cet essai :

``` r
parameters$influence = 0.1
parameters$interaction = 1.3
``` 

Les spécifications de recrutement et d’abandon des patients sont alignées sur les hypothèses utilisées dans l’essai SATURN. La durée de la période de recrutement des patients est fixée à 18 mois. La distribution du recrutement des patients est une distribution exponentielle tronquée et le temps médian d’inscription est de 13.5 mois, ce qui signifie que 50% des patients seront recrutés avant 13.5 mois et les 50% restants après ce temps. De plus, on s’attend à ce que 5% des patients soient perdus de vue annuellement, en supposant une distribution exponentielle des abandons. Ces paramètres sont transmis à la fonction `ADPopSel` comme suit :

``` r
parameters$enrollment_period = 18
parameters$enrollment_parameter = 13.5
parameters$dropout_rate = 0.05
``` 

Le risque d’erreur de première espèce dans l’essai adaptatif est égal à 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

et 10 000 simulations seront exécutées pour calculer les caractéristiques fonctionnelles de l’essai :

``` r
parameters$nsims = 10000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

Les caractéristiques fonctionnelles du design adaptatif de sélection de la population avec les paramètres énumérés ci-dessus sont évaluées en appelant la fonction `ADPopSel` :

``` r
results = ADPopSel(parameters)
```

Les résultats de la simulation seront enregistrés dans l’objet `results` et il est facile de créer un rapport de simulation détaillé au format Microsoft Word en appelant la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyC1.docx")
```

Le rapport de simulation généré par cette fonction sera enregistré dans le répertoire de travail.

Le rapport comprend les résumés suivants avec les résultats des simulations. La Table 8 présente les principales caractéristiques des règles d’arrêt pour futilité et de sélection de la population lors des deux analyses intermédiaires :

* Probabilité d’arrêt pour futilité dans la population globale à l’analyse intermédiaire 1.
* Probabilité de sélectionner uniquement la population globale à l’analyse intermédiaire 2.
* Probabilité de sélectionner uniquement la sous-population biomarqueur-positif à l’analyse intermédiaire 2.
* Probabilité de sélection des deux populations à l’analyse intermédiaire 2.

Les Tables 9 et 10 évaluent la performance du design de sélection adaptative de la population proposé par rapport à un design de référence (design traditionnel). Le design de référence permet également l’arrêt pour futilité à l’analyse intermédiaire 1, mais la sélection de la population n’est pas mise en place, c’est-à-dire que ce design évalue l’importance de l’effet du traitement uniquement dans la population globale de patients lors de l’analyse finale.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyC1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyC1.docx).

## Références

Cappuzzo F. et al. Erlotinib as maintenance treatment in advanced non-small-cell lung cancer: A multicentre, randomised, placebo-controlled Phase 3 study. Lancet Oncology. 2010; 11:521-529. DOI: 10.1016/S1470-2045(10)70112-1.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
