---
layout: page
title: MedianaDesigner
group: 
---

# Traditional trials with multiple dose-placebo comparisons (Case study G1)

## Phase III trial with three dose-placebo comparisons

Power calculations in clinical trials with multiple dose-placebo comparisons will be illustrated using a case study based on the saxagliptin trial in treatment-naive patients with Type 2 diabetes (Rosenstock et al., 2009). Three doses of saxagliptin (2.5 mg/day, 5 mg/day and 10 mg/day) were tested in this Phase III trial compared to placebo. The primary efficacy evaluation was performed using the hemoglobin A1c (HbA1c) change from baseline to Week 24. 

Multiplicity is induced in this trial by a single factor, i.e., by the evaluation of three dose-placebo comparisons with respect to a single primary endpoint. A family of three null hypotheses of no effect will be defined based on the comparison of each dose to placebo. A multiplicity adjustment based on the Hochberg procedure will be applied in this case study to protect the overall Type I error rate at a pre-defined level (one-sided 0.025 level). The Hochberg procedure is an efficient multiple testing procedure, which is known to be uniformly more powerful than the Bonferroni or Holm procedures. The Hochberg procedure may not always preserve the Type I error rate but it does provide strong Type I error rate control in this setting since the test statistics corresponding to the dose-placebo comparisons follow a multivariate normal distribution with positive pairwise correlations. Also, the Hochberg procedure could incorporate information on the relative importance of the individual hypotheses but, in this trial, equal hypothesis weight will be assumed. A power calculation will be performed using the `MultAdj` function.

For more information on the statistical methodology used in the `MultAdj` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/MultAdj.pdf).

## Power calculations using `MultAdj` function

The `MultAdj` function will be used in this section to illustrate the process of computing power characteristics in this trial such as the probability of rejecting each individual null hypothesis as well as the overall probability of success in the trial, i.e., the probability of rejecting at least one hypothesis in the family. 

As the first step, the MedianaDesigner package needs to be loaded:

``` r
library(MedianaDesigner)
```

and an empty list of the function's parameters needs to be created:

``` r
parameters = list()
```

To begin populating the parameter list, a normality assumption will be made for the primary efficacy endpoint (HbA1c change from baseline) in the trial:

``` r
parameters$endpoint_type = "Normal"
```

A reduction in the HbA1c level corresponds to a more favorable outcome and therefore the `direction` parameter will be set to "Lower", i.e.,

``` r
parameters$direction = "Lower"
```

The type of the multiplicity problem in this trial is defined by specifying the number of dose-placebo comparisons and number of endpoints:

``` r
parameters$n_comparisons = 3
parameters$n_endpoints = 1
```

The total sample size of 400 patients (100 patients per trial arm) used in the saxagliptin trial will be assumed in this case study. A balanced design will be considered and, consequently, the sample sizes in the four trial arms will be specified as follows:

``` r
parameters$sample_size = c(100, 100, 100, 100)
```

The patient dropout rate over the 24-week treatment period will be set to 30%:

``` r
parameters$dropout_rate = 0.30
``` 

Power calculations will be performed using the following set of treatment effect assumptions. First of all, a slight increase in the mean HbA1c level will be assumed in the placebo arm (0.1%), i.e.,

``` r
parameters$control_mean = 0.1
```

The mean HbA1c levels will be assumed to decrease in the three dosing arms as follows

``` r
parameters$treatment_mean = c(-0.4, -0.45, -0.5)
```

A common standard deviation of 1.5% will be assumed across the trial arms, i.e.,

``` r
parameters$control_sd = 1.5
parameters$treatment_sd = c(1.5, 1.5, 1.5)
```

A multiplicity adjustment based on the Hochberg procedure is specified as follows:

``` r
parameters$mult_test = "Hochberg"
```

Since the null hypotheses of interest are assumed to be equally weighted, the vector of hypothesis weights, i.e., `parameters$weights`, does not need to be explicitly specified.

The overall one-sided Type I error rate in this Phase III trial will be controlled at a 2.5% level, i.e.,

``` r
parameters$alpha = 0.025
```

Power calculations will be run using 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

The resulting parameter list will be passed to the `MultAdj` function and the simulation results will be saved in the `results` object:

``` r
results = MultAdj(parameters)
```

To generate a detailed simulation report with a summary of power calculations, the `GenerateReport` function will be invoked:

``` r
GenerateReport(results, 
               "CaseStudyG1.docx")
```

The simulation report will be saved in the current working directory. 

Two key characteristics are presented in the simulation report. A summary of marginal power calculations can be found in Table 8. Marginal power for each hypothesis, i.e., the probability of rejecting each hypothesis or the probability of establishing a significant effect at each dose level, is computed before and after the Hochberg-based multiplicity adjustment. To define overall power, two approaches are considered:

* Disjunctive power is defined as the probability of rejecting at least one hypothesis, i.e., the probability that at least one dose provides a statistically significant improvement over placebo in the trial, after the specified multiplicity adjustment. The overall probability of success in a trial is typically defined based on this quantity.

* Conjunctive power is defined as the probability of simultaneously rejecting all hypotheses after the specified multiplicity adjustment.

The overall power values are presented in Table 9.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyG1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyG1.docx).

## References

Rosenstock et al. Effect of saxagliptin monotherapy in treatment-naive patients with type 2 diabetes. Current Medical Research And Opinion. 2009; 25:2401-2411. DOI: 10.1185/03007990903178735.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

