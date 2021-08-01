---
layout: page
title: MedianaDesigner
group: 
---

# Online manual

This package supports efficient simulation-based power and sample size calculations for a broad class of late-stage clinical trials, including Phase II trials, seamless Phase II/III trials and Phase II/III trials. 

The following modules are currently included in the package:

* Module A: Adaptive trials with data-driven sample size or event count re-estimation.
* Module B: Adaptive trials with data-driven treatment selection.
* Module C: Adaptive trials with data-driven population selection.
* Module D: Optimal selection of a futility stopping rule.
* Module E: Event prediction in event-driven trials.
* Module F: Adaptive trials with response-adaptive randomization (experimental module).

# Development version

As noted on the main page, the latest stable version of the package can be downloaded from [CRAN](https://cran.r-project.org/web/packages/MedianaDesigner/index.html) and the latest development version is available on [Github](https://github.com/medianasoft/MedianaDesigner). This version is easy to install directly from GitHub using the `devtools` package. After installing this package, submit the following code to install the latest version of the MedianaDesigner package on your computer:

``` r
devtools::install_github("medianasoft/MedianaDesigner")
```

# Technical manuals

The technical manuals with a detailed description of the statistical methodology implemented in each module can also be found on Mediana's web site: 

* [Adaptive trials with data-driven sample size or event count re-estimation](http://www.mediana.us/MedianaDesigner/ADSSMod.pdf).
* [Adaptive trials with data-driven treatment selection](http://www.mediana.us/MedianaDesigner/ADTreatSel.pdf).
* [Adaptive trials with data-driven population selection](http://www.mediana.us/MedianaDesigner/ADPopSel.pdf).
* [Optimal selection of a futility stopping rule](http://www.mediana.us/MedianaDesigner/FutRule.pdf).
* [Event prediction in event-driven trials](http://www.mediana.us/MedianaDesigner/EventPred.pdf).
* [Adaptive trials with response-adaptive randomization](http://www.mediana.us/MedianaDesigner/ADRand.pdf).

# Case studies

Multiple case studies have been created to help users come up to speed with the package. This online manual is updated on a regular basis and more case studies will be added in the near future.

## Module A: Adaptive trials with data-driven sample size/event count re-estimation 

The following case studies illustrate the process of designing Phase III trials with data-driven sample size/event count re-estimation using the `ADSSMod` function:

* [Case study A1](https://medianasoft.github.io/CaseStudyA1).
* [Case study A2](https://medianasoft.github.io/CaseStudyA2).

## Module B: Adaptive trials with data-driven treatment selection 

The following case studies illustrate the process of designing Phase III trials with data-driven treatment selection using the `ADTreatSel` function:

* [Case study B1](https://medianasoft.github.io/CaseStudyB1).

## Module C: Adaptive trials with data-driven population selection 

The following case studies illustrate the process of designing Phase III trials with data-driven population selection using the `ADPopSel` function:

* [Case study C1](https://medianasoft.github.io/CaseStudyC1).

## Module D: Optimal selection of a futility stopping rule 

The following case studies demonstrate how to set up optimal futility stopping rules using the `FutRule` function:

* [Case study D1](https://medianasoft.github.io/CaseStudyD1).

## Module E: Event prediction in event-driven trials 

The following case study provides an illustration for the `EventPred` function:

* [Case study E1](https://medianasoft.github.io/CaseStudyE1).

## Module F: Adaptive trials with response-adaptive randomization

The following case study illustrates the process of designing a dose-finding Phase II trial with response-adaptive randomization using the `ADRand` function:

* [Case study F1](https://medianasoft.github.io/CaseStudyF1).


