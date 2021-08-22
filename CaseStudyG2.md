---
layout: page
title: MedianaDesigner
group: 
---

# Traditional trials with multiple endpoints (Case study G2)

## Phase II trial with two endpoints

This case study describes power calculations in Phase II clinical trials with several endpoints. In these proof-of-concept trials, multiple clinical endpoints are most often introduced to support an overall evaluation of the treatment effect across the selected endpoints. As a result, instead of multiplicity adjustment methods, global testing methods are applied to pool the evidence of effectiveness across the endpoints. 

As an illustration, consider a Phase II clinical trial in patients with osteoarthritis. The proof-of-concept trial will be conducted to evaluate the effects of an experimental treatment on two endpoints, the pain subscale and the physical function subscale of the Western Ontario and McMaster Universities (WOMAC) Osteoarthritis Index (Bellamy, 2002) versus placebo. The subscales are normally distributed and higher values indicate a favorable outcome. 

The efficacy evaluation for each endpoint will be performed using the mean change from baseline to Week 6. The overall treatment effect assessment will be conducted using the O'Brien global testing procedure based on the ordinary least squares estimate of the common effect size for the two endpoints. This global testing procedure will help the trial's sponsor assess the strength of evidence in favor of the global alternative hypothesis that the experimental treatment is uniformly better than placebo with respect to both endpoints. A power calculation in this case study is easy to perform using the `MultAdj` function.

For more information on the statistical methodology used in the `MultAdj` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/MultAdj.pdf).

## Power calculations using `MultAdj` function

Before going through the steps for running a power calculation in this case study, it is important to review the options supported by the `MultAdj` function. In trials with multiple dose-control comparisons, this function only considers multiplicity adjustment methods since overall assessments of the treatment effect across a range of doses are not conducted in clinical trials. By contrast, when trials with multiple endpoints are considered, this function lets the user choose between a multiple testing procedure or a global testing procedure. Multiple testing procedures should be considered in the context of confirmatory Phase III trials and global testing procedure are more relevant in proof-of-concept Phase II trials.

Returning to this case study, a power calculation will be aimed at estimating the probability of success for the O'Brien global testing procedure. In addition to this, marginal power will be computed for each individual endpoint.

To specify the design and other relevant parameters required for the power calculation, the first step is to load the MedianaDesigner package:

``` r
library(MedianaDesigner)
```

and then set up an empty list of the function's parameters:

``` r
parameters = list()
```

The common endpoint type for the pain and physical function subscales is specified first:  

``` r
parameters$endpoint_type = "Normal"
```

For both endpoints, higher values indicate a favorable outcome and thus the common direction of favorable outcome is specified as follows:

``` r
parameters$direction = "Lower"
```

A two-arm design with a single treatment-placebo comparison is employed in this trial and the efficacy profile of the experimental treatment is evaluated using two endpoints, which implies that

``` r
parameters$n_comparisons = 1
parameters$n_endpoints = 2
```

A balanced design with 180 patients per trial arm will be used in this trial:

``` r
parameters$sample_size = c(180, 180)
```

and only 5% of the patients are expected to drop out of the trial during the 6-week treatment period:

``` r
parameters$dropout_rate = 0.05
``` 

The treatment effect assumptions need to be specified for each endpoint, e.g., two mean values are required in the placebo arm  

``` r
parameters$control_mean = c(35, 110)
```

and similarly two mean values are required in the experimental arm  

``` r
parameters$treatment_mean = c(60, 200)
```

Common endpoint-specific standard deviations are assumed in the two trial arms:  

``` r
parameters$control_sd = c(100, 270)
parameters$treatment_sd = c(100, 270)
```

To request the O'Brien global testing procedure, the `mult_test` argument needs to be set to "O'Brien", i.e.,

``` r
parameters$mult_test = "O'Brien"
```

The one-sided Type I error rate will be set to 2.5% level, i.e.,

``` r
parameters$alpha = 0.025
```

however, in general, higher error rates may be used in proof-of-concept trials.

The power calculation for the global testing procedure will be run based on 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

The resulting parameter list will be passed to the `MultAdj` function with the simulation results saved in the `results` object:

``` r
results = MultAdj(parameters)
```

A detailed simulation report can be generated from this object by passing it to the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyG2.docx")
```

The simulation report will be saved in the current working directory. 

The most important characteristic of the trial, namely, the overall power based on the global testing procedure, is presented in Table 9. As supportive information, marginal power for each individual endpoints is displayed in Table 8. In general, due to a synergistic effect, the the overall power tends to be much higher than the marginal power values.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyG2.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyG2.docx).

## References

Bellamy, N. WOMAC Osteoarthritis Index User Guide. Version V. 2002. Brisbane, Australia.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

