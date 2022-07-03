---
layout: page
title: MedianaDesigner
group: 
---

# Manuel en ligne

Ce paquet prend en charge des calculs efficaces de puissance et de taille d’échantillon basés sur des simulations pour une large gamme d’essais cliniques de stade avancé, y compris les essais de phase II, les essais de phase II/III combinés et les essais de phase III.

Les modules suivants sont actuellement inclus dans le paquet :

* Module A : Essais adaptatifs avec réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données.
* Module B : Essais adaptatifs avec sélection de traitement basée sur les données.
* Module C : Essais adaptatifs avec sélection de population basée sur les données.
* Module D : Sélection optimale d’une règle d’arrêt pour futilité.
* Module E : Prédiction d’événements en aveugle dans les essais basés sur les évènements.
* Module F : Essais adaptatifs avec randomisation adaptative à la réponse (module expérimental).
* Module G : Essais traditionnels avec des objectifs multiples (module expérimental).

# Version en développement

Comme indiqué sur la page principale, la dernière version stable du paquet peut être téléchargée à partir de [CRAN](https://cran.r-project.org/web/packages/MedianaDesigner/index.html) et la dernière version en développement est disponible sur [Github](https://github.com/medianasoft/MedianaDesigner). Cette version est facile à installer directement à partir de GitHub en utilisant le paquet `devtools`. Après avoir installé ce paquet, veuillez soumettre le code suivant pour installer la dernière version du paquet MedianaDesigner sur votre ordinateur :

``` r
devtools::install_github("medianasoft/MedianaDesigner")
```

## Avis et questions

Faites-nous savoir si vous avez des questions sur le paquet R ou la méthodologie sous-jacente. Vous pouvez nous contacter à l’adresse info arobase mediana.us. Aussi, vous pouvez soumettre des questions, des problèmes ou des demandes de fonctionnalités sur [la page des problèmes](https://github.com/medianasoft/MedianaDesigner/issues).

# Manuels techniques

Les manuels techniques avec une description détaillée de la méthodologie statistique mise en œuvre dans chaque module peuvent également être consultés sur le site Web de Mediana :

* [Essais adaptatifs avec réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données](http://www.mediana.us/MedianaDesigner/ADSSModFrench.pdf).
* [Essais adaptatifs avec sélection de traitement basée sur les données](http://www.mediana.us/MedianaDesigner/ADTreatSelFrench.pdf).
* [Essais adaptatifs avec sélection de population basée sur les données](http://www.mediana.us/MedianaDesigner/ADPopSelFrench.pdf).
* [Sélection optimale d'une règle d’arrêt pour futilité](http://www.mediana.us/MedianaDesigner/FutRuleFrench.pdf).
* [Prédiction d’événements en aveugle dans les essais basés sur les évènements](http://www.mediana.us/MedianaDesigner/EventPredFrench.pdf).
* [Essais adaptatifs avec randomisation adaptative à la réponse](http://www.mediana.us/MedianaDesigner/ADRandFrench.pdf).
* [Essais traditionnels avec des objectifs multiples](http://www.mediana.us/MedianaDesigner/MultAdjFrench.pdf).

# Étude de cas

Plusieurs études de cas ont été créées pour aider les utilisateurs à se familiariser avec le paquet. Ce manuel en ligne est mis à jour régulièrement et d’autres études de cas seront ajoutées dans un avenir proche.

## Module A : Essais adaptatifs avec réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données

Les études de cas suivantes illustrent le processus de design des essais de phase III avec une réestimation de la taille de l’échantillon ou du nombre d’événements basée sur les données à l’aide de la fonction `ADSSMod` :

* [Étude de cas A1](https://medianasoft.github.io/CaseStudyA1French).
* [Étude de cas A2](https://medianasoft.github.io/CaseStudyA2French).

## Module B : Essais adaptatifs avec sélection de traitement basée sur les données

L’étude de cas suivante illustre le processus de conception d’essais de phase III avec sélection de traitement basée sur les données à l’aide de la fonction `ADTreatSel` :

* [Étude de cas B1](https://medianasoft.github.io/CaseStudyB1French).

## Module C : Essais adaptatifs avec sélection de population basée sur les données

L’étude de cas suivante illustre le processus de conception d’essais de phase III avec sélection de population basée sur les données à l’aide de la fonction `ADPopSel` :

* [Étude de cas C1](https://medianasoft.github.io/CaseStudyC1French).

## Module D : Sélection optimale d’une règle d’arrêt pour futilité

L’étude de cas suivante montre comment mettre en place des règles d’arrêt pour futilité optimales à l’aide de la fonction `FutRule` :

* [Étude de cas D1](https://medianasoft.github.io/CaseStudyD1French).

## Module E : Prédiction d’événements en aveugle dans les essais basés sur les évènements

L’étude de cas suivante fournit une illustration de la fonction `EventPred` :

* [Étude de cas E1](https://medianasoft.github.io/CaseStudyE1French).

## Module F : Essais adaptatifs avec randomisation adaptative à la réponse

L’étude de cas suivante illustre le processus de conception d’un essai de phase II de détermination de la dose avec randomisation adaptative à la réponse à l’aide de la fonction `ADRand` :

* [Étude de cas F1](https://medianasoft.github.io/CaseStudyF1French).

## Module G : Essais traditionnels avec des objectifs multiples

Les études de cas suivantes illustrent les calculs de puissance dans les essais de phase III avec plusieurs comparaisons dose-contrôle et plusieurs critères d’évaluation utilisant la fonction `MultAdj` :

* [Étude de cas G1](https://medianasoft.github.io/CaseStudyG1French).
* [Étude de cas G2](https://medianasoft.github.io/CaseStudyG2French).
* [Étude de cas G3](https://medianasoft.github.io/CaseStudyG3French).
