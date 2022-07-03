---
layout: page
title: MedianaDesigner
group: 
---

# Prédiction d’événements en aveugle dans les essais basés sur les évènements (étude de cas E1)

## Essai de phase III et fonction `EventPred`

Les prédictions d’événements jouent un rôle important dans les essais cliniques avec des critères d’évaluation de type temps jusqu’à un évènement. Pour illustrer le processus de prévision des événements, considérons un essai de phase III dans une population oncologique, par exemple, l’essai de phase III chez des patients atteints de leucémie myéloïde aiguë utilisé dans [l’étude de cas A1](https://medianasoft.github.io/CaseStudyA1). L’essai est mené pour étudier l’efficacité d’un traitement expérimental par rapport à un contrôle (meilleurs soins de soutien). Le critère d’évaluation principal est la survie globale (temps écoulé entre le recrutement du patient et le décès, quelle qu’en soit la cause).

Pour permettre le processus décisionnel interne, des prédictions seront effectuées pour estimer le nombre moyen d’événements à des moments futurs. Les prédictions sont basées sur les données en aveugle, y compris le recrutement des patients, l’abandon des patients et les informations sur le temps jusqu’aux événements.

Il sera supposé que l’appercu actuel des données d’essai en aveugle est inclus dans l’ensemble de données `EventPredData`, fourni avec le paquet `MedianaDesigner`. Cet ensemble de données comprend les informations sur le recrutement des patients, l’abandon des patients et les informations sur le temps jusqu’aux événements pour 150 patients 12 mois après le début de l’étude. Les données en aveugle, ainsi que les informations antérieures pertinentes, seront utilisées pour prédire le nombre moyen d’événements et définir un intervalle prédictif à 95% pour chaque temps futur prédéfini à l’aide de la fonction `EventPred`.

Une caractéristique importante de l’algorithme de prévision d’événements dans la fonction `EventPred` est qu’il repose sur une approche bayésienne et que des distributions antérieures doivent être définies pour les paramètres suivants :

* Taux de risque ("hazard rate") d’événement.
* Taux de risque d’abandon des patients.
* Taux de recrutement des patients.

Le processus de spécification des distributions antérieures est illustré ci-dessous.

Pour plus d’informations sur la méthodologie statistique utilisée dans la fonction `EventPred`, téléchargez le [manuel technique](http://www.mediana.us/MedianaDesigner/EventPredFrench.pdf).

## Prédiction d’événements à l’aide de la fonction `EventPred`

Cette section illustre les étapes à suivre pour exécuter des prédictions d’événements dans cet essai de phase III à l’aide de la fonction `EventPred`.

Comme toujours, supposons que le paquet a été chargé :

``` r
library(MedianaDesigner)
```

Après cela, les paramètres de la fonction seront spécifiés à l’aide d’une liste de paramètres :

``` r
parameters = list()
```

La prévision des événements sera effectuée à l’aide des données en aveugle disponibles sur le recrutement des patients, l’abandon des patients et les événements stockées dans l’ensemble de données `EventPredData`. Il convient de noter que `EventPredData` est un ensemble de données intégré, qui est chargé avec le paquet (aucune étape supplémentaire n’est requise pour charger cet ensemble de données). Le nom de cet ensemble de données doit être attribué au paramètre `data_set` :

``` r
parameters$data_set = EventPredData
``` 

Si l’utilisateur souhaite effectuer des prévisions d’événements à l’aide d’un autre ensemble de données, il sera important de s’assurer que cet ensemble de données répond aux exigences suivantes. Tout d’abord, il doit s’agir d’une base de données avec une seul entrée par patient. Deuxièmement, les quatre variables suivantes doivent être incluses dans la base de données :

* `enrollment` : Temps de recrutement du patient par rapport au début de l’étude.
* `time` : Temps jusqu’à l’événement d’intérêt, par exemple, le décès du patient, si l’événement s’est produit ou jusqu’au dernier contact si l’événement est censuré. Le temps jusqu’à l’événement ou le dernier contact est défini par rapport au recrutement du patient.
* `event` : Indicateur d’événement (1 si l’événement d’intérêt s’est produit et 0 autrement).
* `dropout` : Indicateur d’abandon du patient (1 si le patient abandonne l’essai/est perdu de vue et 0 dans le cas contraire).

Les temps futurs prédéfinis auxquels les prédictions d’événements seront calculées, c’est-à-dire 12, 13, ..., 24 mois après le début de l’étude, doivent être définis comme suit :

``` r 
parameters$time_points = seq(from = 12, to = 24, by = 1)
```

L’ensemble de paramètres suivant traite de la spécification des distributions antérieures pour les composants clés du modèle sous-jacent, c’est-à-dire le taux de risque d’événement, le taux de risque d’abandon des patients et le taux de recrutement des patients. Ces paramètres sont supposés suivre des distributions gamma. Les paramètres de forme et de vitesse des distributions gamma peuvent être spécifiés directement ou, alternativement, l’utilisateur peut s’appuyer sur la fonction `EventPredPriorDistribution`. Cette fonction facilite le processus de spécifications des distributions antérieures en permettant à l’utilisateur de fournir la valeur attendue d’un paramètre d’intérêt, par exemple, le taux de risque d’événement, ainsi que le paramètre d’incertitude :

* `expected` : Valeur attendue du taux de risque, par exemple, le taux de risque d’événement, le taux de risque d’abandon des patients ou le taux de recrutement des patients.
* `uncertainty` : Paramètre d’incertitude (coefficient de variation).

Puisque le paramètre d’incertitude est le coefficient de variation, il quantifie la quantité de variabilité autour de la valeur attendue. À titre indicatif, le paramètre d’incertitude de 0.1 définit une distribution antérieure de confiance élevée, tandis que 0.3 et 0.5 correspondent à des distributions antérieures de confiance moyenne et de confiance faible, respectivement.

Pour illustrer, considérons la spécification d’une distribution antérieure de la famille gamma pour le taux de risque d’événement. La valeur attendue sera calculée en supposant que le temps de survie médian est de 15 mois. Notez que le temps médian de 15 mois doit être transformé pour calculer le taux de risque, c’est-à-dire que le taux de risque est égal à log(2) / 15. Pour établir une distribution antérieure de confiance moyenne pour le taux de risque d’événement, le paramètre d’incertitude sera égal à 0.3. Les paramètres de forme et de vitesse de cette distribution gamma sont calculés comme suit :

``` r
parameters$event_prior_distribution = 
    EventPredPriorDistribution(expected = log(2) / 15, uncertainty = 0.3)
```

Il est facile de vérifier que les paramètres de forme et de vitesse sont égaux à 11.1 et 240.5, respectivement.

Les distributions antérieures pour les deux autres paramètres, c’est-à-dire le taux de risque d’abandon des patients et le taux de recrutement des patients, sont définies de la même manière :

``` r
parameters$dropout_prior_distribution = 
    EventPredPriorDistribution(expected = log(2) / 80, uncertainty = 0.3)

parameters$enrollment_prior_distribution = 
    EventPredPriorDistribution(expected = 35, uncertainty = 0.3)
```

Ces deux spécifications supposent une distribution antérieure de confiance moyenne avec un paramètre d’incertitude défini sur 0.3. La valeur attendue du taux de risque d’abandon des patients est calculée en supposant le temps d’abandon médian de 80 mois et le taux de recrutement des patients est supposé être de 35 patients par mois.

Les prédictions d’événements seront exécutées à l’aide de 1 000 simulations :

``` r
parameters$nsims = 1000
```

L’utilisateur peut également définir l’amorce de l’algorithme de génération de nombres aléatoires pour permettre une reproductibilité, par exemple,

``` r
parameters$random_seed = 20213
```

Toutefois, ce paramètre est facultatif et la valeur par défaut (49283) sera utilisée si aucune amorce n’est spécifiée.

La dernière étape consiste à appeler la fonction `EventPred` pour prédire le nombre moyen d’événements et calculer les intervalles prédictifs à 12, 13, ..., 24 mois :

``` r
results = EventPred(parameters)
```

Un rapport de simulation complet est généré en appelant la fonction `GenerateReport`. L’objet créé par la fonction `EventPred`, c’est-à-dire `results`, doit être transmis à la fonction `GenerateReport` :

``` r
GenerateReport(results, 
               "CaseStudyE1.docx")
```

Cette fonction créera un document Microsoft Word avec le nom spécifié dans l’appel de fonction et l’enregistrera dans le répertoire de travail. Le document comprend un résumé des paramètres de l’essai ainsi que des résumés tabulaires et graphiques de la prédiction d’événements aux temps prédéfinis.

## Code et rapport de simulation

Le code et le rapport de simulation peuvent être téléchargés à partir du site Web de Mediana :

* [Code R](http://www.mediana.us/MedianaDesigner/CaseStudyE1.r).
* [Rapport de simulation](http://www.mediana.us/MedianaDesigner/CaseStudyE1.docx).

# Manuel en ligne

Retour au [manuel en ligne](https://medianasoft.github.io/MedianaDesignerFrench).
