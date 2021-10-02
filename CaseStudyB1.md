---
layout: page
title: MedianaDesigner
group: 
---

# Adaptive designs with data-driven treatment selection (Case study B1)

## Phase III trial with a binary endpoint

The design of adaptive trials with data-driven treatment selection will be illustrated using a case study based on the Phase III trial in infants with proliferating infantile hemangioma (Leaute-Labrze et al., 2015). The trial was conducted to evaluate the efficacy and safety profiles of four propranolol regimens (1 or 3 mg of propranolol base per kilogram of body weight per day for 3 or 6 months) compared to placebo. The primary efficacy evaluation was based on a binary endpoint, namely, the success rate (rate of complete or nearly complete resolution of the target hemangioma) at the end of the 6-month period. An adaptive approach was employed in the trial and the best propranolol regimen was chosen at an interim analysis.

In this case study, an extended version of the trial design will be considered. An adaptive design with two interim looks will be assumed: 

* A futility stopping rule will be applied at Interim analysis 1 to drop the ineffective regimens.
* A treatment selection rule will be utilized at Interim analysis 2 to identify the best regimen.

At the final analysis, the treatment effect will be evaluated in the selected regimen arm compared to placebo.

For more information on the statistical methodology used in the `ADTreatSel` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ADTreatSel.pdf).

## Adaptive treatment-selection design using `ADTreatSel` function

The proposed adaptive trial with futility and treatment selection rules will be designed using the `ADTreatSel` function. The trial parameters and treatment effect assumptions will be loosely based on the propranolol trial. 

As the first step, the MedianaDesigner package will be loaded:

``` r
library(MedianaDesigner)
```

The list of the function's parameters will be initialized, i.e.,

``` r
parameters = list()
```

and the arguments of this parameter list will be populated as described below. 

A binary primary endpoint will be specified in this trial:

``` r
parameters$endpoint_type = "Binary"
```

The binary proportion endpoint represents the success rate and a higher values indicates a more favorable outcome. The `direction` parameter needs to be set to "Higher", i.e.,

``` r
parameters$direction = "Higher"
```

A balanced allocation of patients across the five arms (placebo and four regimens) is assumed with 90 enrolled patients in each arm, i.e., 

``` r
parameters$sample_size = c(90, 90, 90, 90, 90)
```

The magnitude of the treatment effect is specified using the success rate at 6 months in the placebo and four regimen arms:

``` r
parameters$control_rate = 0.1
parameters$treatment_rate = c(0.30, 0.30, 0.35, 0.35)
```

As a starting point, a constant success rate of 30% is assumed for each regimen but, of course, it will be advisable to examine the adaptive design's performance under multiple plausible sets of success rates across the four regimens.

The information fractions at Interim analyses 1 and 2 will be defined in terms of the number of patients who complete the 6-month treatment period. Early interim looks, corresponding to the 20% and 50% information fractions, will be utilized in the trial to reduce the total sample size:

``` r
parameters$info_frac = c(0.2, 0.5, 1)
``` 

The threshold for conditional power at Interim analysis 1 will be set to 30%, i.e.,

``` r
parameters$futility_threshold = 0.3
``` 

This threshold corresponds to a fairly liberal stopping rule in the sense that the individual regimens are likely to be dropped due to lack of efficacy unless the treatment effect is rather strong, specifically unless conditional power is over 30%. The trial will be terminated at this decision point if all regimens are simultaneously dropped.

A fairly high patient dropout rate of 15% is assumed due to a longer duration of the trial:

``` r
parameters$dropout_rate = 0.15
``` 

The next two parameters define the treatment selection rule that will be applied at Interim analysis 2. First of all, the single best treatment corresponding to the largest observed effect size will be chosen at this decision point: 

``` r
parameters$treatment_count = 1
```

It is worth noting that, due to the discrete nature of the primary endpoint, there is a non-trivial chance that the same effect size could be observed in multiple regimen arms. In this case, the regimen with the lowest overall dose will be chosen.

Since data-driven treatment selection induces multiplicity, a multiplicity adjustment (multiple testing  procedure) needs to be prospectively defined. The Hochberg procedure will be used in the trial:

``` r
parameters$mult_test = "Hochberg"
```

The one-sided Type I error rate in this Phase III trial is equal to 2.5%:

``` r
parameters$alpha = 0.025
```

The adaptive design's operating characteristics will be evaluated using 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

Operating characteristics of the adaptive population-selection design with the parameters listed above are evaluated by calling the `ADTreatSel` function:

``` r
results = ADTreatSel(parameters)
```

The simulation results will be saved in the `results` object. A report with a detailed summary of simulation results is generated by calling the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyB1.docx")
```

This report will be saved in the working directory. 

The following summaries are included in the simulation report. The probability of dropping each regimen and an overall probability of terminating the trial due to futility (if all regimens are simultaneously ineffective) at Interim analysis 1 are displayed in Table 7. Table 8 presents the probabilities of treatment selection at Interim analysis 2, i.e., the probability that each regimen is selected for the final analysis. Tables 9 and 10 compare the probability of success in the proposed adaptive trial to that of reference trials (traditional trials). In the context of adaptive treatment selection, it is natural to define multiple reference trials. Each reference trial assumes a simple two-arm design where each individual regimen is compared with placebo with a futility assessment at a single interim look. 

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyB1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyB1.docx).

## References

Leaute-Labreze C et al. A randomized, controlled trial of oral propranolol in infantile hemangioma. The New England Journal of Medicine. 2015; 372:735-746. DOI: 10.1056/NEJMoa1404710.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).
