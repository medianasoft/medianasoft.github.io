---
layout: page
title: MedianaDesigner
group: 
---

# Essais adaptatifs avec randomisation adaptative à la réponse (étude de cas F1)

## Essai de phase II de détermination de la dose

Une approche basée sur des simulations pour concevoir des essais avec randomisation adaptative à la réponse sera illustrée à l’aide d’un essai de phase II chez des patients atteints d’un trouble dépressif majeur partiellement réactif. Cet essai sera mené pour étudier le profil dose-réponse d’un nouveau traitement. Quatre doses de ce traitement (100 mg/jour, 200 mg/jour, 400 mg/jour et 800 mg/jour) seront comparées au placebo. L’analyse d’efficacité primaire sera effectuée à l’aide d’un critère d’évaluation continu, c’est-à-dire la réduction moyenne par rapport à la Baseline à 4 semaines pour le score total MADRS (Montgomery-Asberg Depression Rating Scale). Le critère d’évaluation est normalement distribué et, sans traitement, la moyenne et l’écart-type de la variation à 4 semaines devraient être de -10 et 14, respectivement.

Un design en quatre étapes sera utilisé dans cet essai clinique avec 50 patients inscrits à chaque étape. Les patients à l’étape 1 seront randomisés également aux quatre doses du traitement expérimental ou du placebo et, à partir de l’étape 2, une approche de randomisation adaptative à la réponse sera appliquée. À la fin de chaque étape, les probabilités postérieures d’atteindre l’efficacité cible seront calculées dans chacun des quatre bras et le schéma de randomisation pour l’étape suivante sera mis à jour de manière appropriée (notez que le rapport de randomisation dans le bras placebo sera fixé à 20%). Le but ultime de ce design adaptatif à la réponse est de traiter plus de patients aux doses les plus efficaces.

Les étapes requises pour effectuer une évaluation basée sur des simulations de ce design adaptatif à la réponse à l’aide de la fonction `ADRand` sont présentées ci-dessous. Pour plus d’informations sur la méthodologie statistique utilisée dans cette fonction, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ADRandFrench.pdf).

## Randomisation adaptative à la réponse à l’aide de la fonction `ADRand`

Les principales caractéristiques de la fonction `ADRand` et le processus d’évaluation de l’essai de phase II de détermination de la dose sont présentés ci-dessous.

Le paquet MedianaDesigner doit d’abord être chargé, c’est-à-dire

``` r
library(MedianaDesigner)
```

et une liste vide des paramètres de la fonction doit être créée :

``` r
parameters = list()
```

Les paramètres de la fonction seront définis en commençant par les paramètres du critère d’évaluation. Cela inclut le type de critère d’évaluation, c’est-à-dire

``` r
parameters$endpoint_type = "Normal"
```

et la direction de l’effet favorable pour le critère d’évaluation principal. Une réduction plus importante du score total MADRS indique un effet bénéfique et, par conséquent, le paramètre de `direction` sera défini par « Lower », c’est-à-dire

``` r
parameters$direction = "Lower"
```

L’ensemble de paramètres suivant définit le design de l’essai et les hypothèses d’effet du traitement. Tout d’abord, les niveaux de dose à examiner dans l’essai sont spécifiés (avec 0 correspondant au bras placebo) :

``` r
parameters$dose_levels = c(0, 100, 200, 400, 800)
```

Un design en quatre étapes sera utilisé dans l’essai et 50 patients devraient être recrutés à chaque étape. Le nombre de patients recrutés à chaque étape est spécifié comme suit :

``` r
parameters$stage_sample_size = c(50, 50, 50, 50)
```

On s’attend à ce qu’il faille environ 36 semaines pour recruter les 200 patients dans l’essai. Le recrutement des patients suivra un modèle non uniforme, en particulier une distribution exponentielle tronquée sera supposée avec un temps médian de recrutement de 24 semaines (le temps de recrutement médian correspond au moment où 50% des patients seront recrutés dans l’essai). Les deux paramètres de la distribution du recrutement des patients sont définis comme suit :

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
```

La durée de la période de traitement dans cet essai est de 4 semaines (l’évaluation primaire de l’efficacité sera effectuée 4 semaines après la randomisation) :

``` r
parameters$treatment_period = 4
```

Étant donné que la période de traitement est assez courte, seulement 5% des patients devraient être perdus de vue, ce qui conduit à la valeur suivante du taux d’abandon des patients :

``` r
parameters$dropout_rate = 0.05
```

Étant donné que le critère d’évaluation principal suit une distribution normale, les hypothèses d’effet du traitement seront définies en spécifiant la moyenne réelle et l’écart-type des distributions normales spécifiques à chaque bras. Les paramètres de la distribution du critère d’évaluation principal dans le groupe placebo sont définis en premier :

``` r
parameters$control_mean = -10
parameters$control_sd = 14
```

puis les paramètres correspondants sont définis dans les quatre bras de dosage:

``` r
parameters$treatment_mean = c(-12, -14, -16, -18)
parameters$treatment_sd = c(14, 14, 14, 14)
```

Avec un design adaptatif à la réponse, il est courant de fixer le ratio de randomisation dans le bras placebo. Dans cet essai, ce ratio sera fixé à 20% :

``` r
parameters$ratio_placebo = 0.2
```

Comme expliqué dans le manuel technique, la classe de modèles adaptatifs de réponse pour les essais de détermination de dose mis en œuvre dans ce module repose sur une approche de moyenne de modèle basée sur la méthode MCPMod. Cette méthode exige qu’un ensemble de fonctions dose-réponse qui décrivent des formes plausibles de la relation dose-réponse soit défini prospectivement. La mise en œuvre actuelle de la méthode suppose quatre modèles dose-réponse candidats, à savoir des modèles linéaires, exponentiels, Emax et logistiques. Les paramètres non linéaires des modèles exponentiel, Emax et logistique sont spécifiés comme suit :

``` r
# Paramètre non linéaire du modèle exponentiel (delta)
parameters$exponential_model_parameter = 400

# Paramètre non linéaire du modèle Emax (ED50)
parameters$emax_model_parameter = 600

# Paramètres non linéaires du modèle logistique (ED50 et delta)
parameters$logistic_model_parameters = c(400, 50)
```

Il est important de noter qu’aucun paramètre n’a besoin d’être spécifié pour le modèle linéaire.

Pour plus d’informations sur la méthodologie MCPMod et son implémentation, [consultez le paquet MCPModPack](https://cran.r-project.org/web/packages/MCPModPack/index.html).

Une fois que l’approche de la moyenne des modèles a été appliquée aux données dose-réponse pour estimer les effets moyens dans les cinq bras de l’essai, la probabilité postérieure d’atteindre l’efficacité cible est calculée pour chaque dose du traitement expérimental. Le profil d’efficacité cible est défini à l’aide d’un seuil qui correspond à une amélioration cliniquement pertinente par rapport au placebo :

``` r
parameters$delta = -6
```

La probabilité d’atteindre le profil d’efficacité cible joue un rôle clé dans la procédure de randomisation adaptative. Cette probabilité est calculée pour chaque dose à la fin de chaque étape et affecte directement les rapports de randomisation spécifiques aux doses à l’étape suivante. Notez qu’une valeur négative du seuil est requise dans ce cas puisqu’une réduction du score total MADRS indique un effet bénéfique.

Le dernier paramètre lié à la procédure de randomisation adaptative est le paramètre d’équilibre. Ce paramètre contrôle le degré d’équilibre pour l’allocation des patients dans la cohorte suivante. Si une valeur plus petite est choisie, l’allocation des patients entre les bras de dosage est plus susceptible d’être équilibrée et, avec une valeur plus grande de ce paramètre, l’allocation sera de plus en plus déséquilibrée. Dans cette étude de cas, le paramètre d’équilibre sera défini à 2 pour attribuer la plupart des patients aux doses les plus prometteuses :

``` r
parameters$balance = 2
```

La signification statistique de la relation dose-réponse dans le design adaptatif à la réponse, ainsi qu’un design traditionnel avec une randomisation égale, seront évaluées à un niveau alpha unilatéral de 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

Les principales caractéristiques fonctionnelles des designs adaptatifs et traditionnels seront estimées à l’aide de 1 000 simulations :

``` r
parameters$nsims = 1000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

La liste résultante des paramètres (`parameters`) sera transmise à la fonction `ADRand` pour calculer les caractéristiques fonctionnelles :

``` r
results = ADRand(parameters)
```

Il est important de souligner que cette fonction repose sur des calculs bayésiens et qu’il faudra plusieurs minutes pour exécuter les simulations.

La fonction `GenerateReport` doit être appelée pour créer un rapport de simulation. L’objet créé par la fonction `ADRand`, c’est-à-dire `results`, doit être transmis à la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyF1.docx")
```

Cette fonction génère un rapport de simulation basé sur Word avec le nom de fichier spécifié et l’enregistre dans le répertoire de travail. Le rapport comprend un résumé des paramètres de l’essai ainsi que les principales caractéristiques opérationnelles du design adaptatif, telles qu’un résumé de la taille de l’échantillon par groupe d’essai et un résumé de la probabilité de succès en fonction de l’importance de la relation dose-réponse.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyF1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyF1.docx).

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
