---
layout: page
title: MedianaDesigner
group: 
---

# Designs adaptatifs avec réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données (étude de cas A1)

## Essai de phase III avec un critère d’évaluation de type temps jusqu’à un évènement

L’étude de cas guide l’utilisateur tout au long du processus de conception d’essais adaptatifs qui prennent en charge une option permettant de modifier le nombre total de patients ou d’événements en fonction des données disponibles lors d’une analyse intermédiaire. Le design général de l’essai QUAZAR AML-001 (Wei et al., 2020) est utilisé dans cette étude de cas. Cet essai clinique de phase III a été mené pour étudier l’efficacité et l’innocuité de l’azacitidine orale comme traitement d’entretien chez les patients atteints de leucémie myéloïde aiguë.
L’objectif d’efficacité de l’essai a été formulé en termes d’évaluation de la prolongation de la survie par rapport au contrôle (meilleurs soins de soutien). Un critère d’évaluation de type temps jusqu’à un évènement (temps de survie global) a été utilisé dans cet essai. L’essai initial utilisait une seule analyse intermédiaire de la futilité après que 30% des événements se soient produits. Dans cette étude de cas, le design original est étendu à un design adaptatif avec deux analyses intermédiaires :

* L’analyse intermédiaire 1 évalue l’arrêt précoce pour futilité si le signal d’efficacité est trop faible.
* L’analyse intermédiaire 2 met en place une option permettant d’augmenter le nombre cible d’événements dans l’essai si le signal d’efficacité est plus faible que prévu.

Le troisième point de décision est l’analyse finale.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `ADSSMod`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/ADSSModFrench.pdf).

## Design adaptatif avec réestimation du nombre d’événements à l’aide de la fonction `ADSSMod`

Cette section présente les principales fonctionnalités de la fonction `ADSSMod` et illustre le processus d’évaluation des principales caractéristiques fonctionnelles du design adaptatif défini ci-dessus. Les paramètres du design et les hypothèses d’effet du traitement présentés dans cette section sont basés sur les paramètres correspondants de l’essai QUAZAR AML-001.

La première étape consiste à charger le paquet MedianaDesigner :

``` r
library(MedianaDesigner)
```

Les paramètres de la fonction `ADSSMod`, comme toute autre fonction de ce paquet, sont spécifiés à l’aide d’une liste de paramètres et l’utilisateur doit commencer par une liste vide :

``` r
parameters = list()
```

Les arguments requis de cette liste de paramètres sont ensuite renseignés comme indiqué ci-dessous.

Tout d’abord, le type de critère d’évaluation doit être défini :

``` r
parameters$endpoint_type = "Time-to-event"
```

Étant donné qu’un temps de survie plus long représente un résultat plus favorable dans cet essai, le paramètre de `direction` doit être défini comme « Higher », c’est-à-dire

``` r
parameters$direction = "Higher"
```

Le nombre total de patients recrutés a été supposé être de 460 dans l’essai QUAZAR AML-001 et un rapport de randomisation de 1:1 a été utilisé dans l’essai. Compte tenu de cela, la taille des échantillons dans les bras de contrôle et de traitement est spécifiée comme suit :

``` r
parameters$sample_size = c(235, 235)
```

En plus du nombre de patients recrutés, le nombre cible d’événements (décès toutes causes confondues) à l’analyse finale doit également être fourni :

``` r
parameters$event_count = 330
``` 

Cet objectif correspond au design original de l’essai, c’est-à-dire avant que la réestimation du nombre d’événements ne soit appliquée à l’analyse intermédiaire 2. Le nombre maximal d’événements à l’analyse finale après la réestimation du nombre d’événements est défini ci-dessous.

L’ampleur de l’effet du traitement dans l’essai est spécifiée en utilisant les temps de survie médians dans les deux bras de l’essai (les temps sont mesurés en mois) :

``` r
parameters$control_time = 16
parameters$treatment_time = 22.9
```

La distribution de survie est supposée exponentielle et, dans cette hypothèse, ces temps de survie médians correspondent à un taux de risque ("hazard rate") de 0.7. Il convient de noter que cette hypothèse s’est avérée conservatrice et qu’un effet de traitement plus fort a été établi dans l’essai QUAZAR AML-001. Les temps de survie médians dans les groupes contrôle et traitement étaient de 14.8 mois et 24.7 mois, respectivement.

L’ensemble suivant de paramètres d’essai définit les règles de décision lors des deux analyses intermédiaires. En commençant par les fractions d’information, les analyses intermédiaires 1 et 2 seront effectuées après 30% et 60% des événements survenus, ce qui signifie que les fractions d’information à ces points de décision sont égales à 0.3 et 0.6. La fraction d’information à l’analyse finale originale (avant la réestimation du nombre d’événements) est simplement de 1 et la fraction d’information à l’analyse finale après la réestimation du nombre d’événements correspond à un plafond pour l’augmentation du nombre d’événements. Supposons que ce plafond soit de 30%, c’est-à-dire que le nombre d’événements ne peut pas être ajusté de plus de 30%. Cela signifie que la fraction d’information à l’analyse finale après la réestimation du nombre d’événements est de 1.3.

Le vecteur des fractions d’information est alors mis en place comme suit :

``` r
parameters$info_frac = c(0.3, 0.6, 1, 1.3)
``` 

Une évaluation de la futilité sera effectuée lors de l’analyse intermédiaire 1 et le seuil de futilité pour la puissance conditionnelle à cette analyse intermédiaire est donné par :

``` r
parameters$futility_threshold = 0.1
``` 

L’essai sera interrompu pour futilité à ce stade de décision si la puissance conditionnelle est inférieure à 10%.

La règle de réestimation du nombre d’événements dans l’analyse intermédiaire 2 est également définie en termes de puissance conditionnelle. Le nombre cible d’événements sera augmenté pour atteindre un objectif prédéfini de puissance conditionnelle (jusqu’à la limite de 30%) si la puissance conditionnelle à ce point de décision se situe dans un intervalle prometteur. L’intervalle prometteur pour la puissance conditionnelle et la puissance conditionnelle cible sont spécifiés comme suit :

``` r
parameters$promising_interval = c(0.5, 0.9)
parameters$target_power = 0.9
``` 

Étant donné que le critère d’évaluation principal de cet essai est un critère d’évaluation de type temps jusqu’à un évènement, l’utilisateur est tenu de définir les détails des processus de recrutement et d’abandon des patients. Le recrutement des patients est défini à l’aide de deux paramètres, à savoir la durée de la période de recrutement des patients et le temps médian de recrutement (les deux sont mesurés en mois) :

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
``` 

Le recrutement des patients est régi par une distribution exponentielle tronquée et le temps médian de recrutement correspond au moment où 50% des patients ont été recrutés. Avec un temps de recrutement médian de 24 mois, il faudra deux ans pour inscrire la première moitié des patients et les 50% restants seront inscrits au cours de la troisième année. Au passage, pour spécifier une accumulation uniforme des patients, le temps médian d’inscription devrait être fixé au milieu de la période d’inscription des patients, c’est-à-dire à 18 mois.

Le taux annuel d’abandon des patients est fixé à 5% :

``` r
parameters$dropout_rate = 0.05
``` 

On suppose que le temps jusqu’à l’abandon des patients suit une distribution exponentielle.

Le dernier paramètre du design est le risque d’erreur de première espèce unilatéral dans l’essai. Dans les essais de confirmation de phase III, le risque unilatéral est égal à 2.5%, c’est-à-dire

``` r
parameters$alpha = 0.025
```

Les caractéristiques du design adaptatif seront évaluées au moyen de simulations basées sur 10 000 simulations :

``` r
parameters$nsims = 10000
```

L’utilisateur peut définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

La liste de paramètres résultante est prête à être transmise à la fonction `ADSSMod`. La fonction exécutera des simulations et enregistrera les résultats dans l’objet `results` :

``` r
results = ADSSMod(parameters)
```

Une caractéristique importante du paquet MedianaDesigner est qu’il offre une option pratique pour générer des rapports de simulation (documents Microsoft Word) avec un résumé détaillé des paramètres du design adaptatif et des résultats de simulation. Pour créer un rapport de simulation, l’utilisateur doit appeler la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyA1.docx")
```

Le rapport de simulation sera enregistré dans le répertoire de travail actuel.

Le rapport présente un résumé des principales caractéristiques fonctionnelles du design dans les Tables 8 et 9. La Table 8 énumère les probabilités des résultats suivants :

* Probabilité d’arrêt pour futilité à l’analyse intermédiaire 1.
* Probabilité d’augmenter le nombre d’événements à l’analyse intermédiaire 2.
* Puissance du design traditionnel.
* Puissance du design adaptatif.

Les deux dernières caractéristiques facilitent la comparaison du design adaptatif proposé et d’un design de référence. Le design de référence (design traditionnel) utilise également une règle d’arrêt pour futilité à l’analyse intermédiaire 1, mais ne prend pas en charge une option permettant d’augmenter le nombre cible d’événements à l’analyse intermédiaire 2 d’une manière basée sur les données.

Une comparaison plus informative des designs adaptatifs et traditionnels est présentée dans la Table 9, qui présente la probabilité conditionnelle de succès à l’analyse finale si l’effet du traitement à l’analyse intermédiaire 2 se situe dans chacun des trois intervalles prédéfinis (intervalle défavorable, intervalle prometteur et intervalle favorable). La probabilité conditionnelle de succès est la même pour les designs adaptatifs et traditionnels dans les intervalles défavorables et favorables. Cependant, le design adaptatif tend à fournir un avantage tangible en termes de puissance par rapport au design traditionnel dans l’intervalle prometteur.

Il est important de noter que, puisque la puissance de l’essai est suffisante dans ce cas particulier, le design adaptatif proposé n’offre pas beaucoup d’avantage en termes de puissance par rapport au design traditionnel. Pour mieux évaluer la performance du design adaptatif, il est recommandé d’envisager des scénarios avec un effet de traitement plus faible, par exemple en supposant un rapport de risque ("hazard ratio") de 0.75 et 0.8.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyA1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyA1.docx).

## Références

Wei AH et al. Oral azacitidine maintenance therapy for acute myeloid leukemia in first remission. The New England Journal of Medicine. 2020; 383:2526-2537. DOI: 10.1056/NEJMoa2004444.

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
