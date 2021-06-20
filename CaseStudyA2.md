---
layout: page
title: MedianaDesigner
group: 
---

# Adaptive designs with data-driven sample size or event count re-estimation (Case study A2)

## Phase III trial with a continuous endpoint

The case study complements [Case study A1](https://medianasoft.github.io/CaseStudyA1) by providing a brief description of the process of designing trials with adaptive sample size re-estimation when the primary efficacy endpoint is continuous and follows a normal distribution. 

This case study is loosely based on the Phase III trial for the treatment of schizophrenia (Meltzer et al., 2011). This clinical trial was conducted to evaluate the efficacy and safety profiles of lurasidone in patients with acute schizophrenia. It will be important to clarify that this particular trial included four arms (two doses of lurasidone, placebo and active control). This example assumes a simplified set up with a single dose of the experimental treatment versus placebo.

Three decision points will be employed in the trial:

* Interim analysis 1 (futility stopping rule).
* Interim analysis 2 (sample size re-estimation rule).
* Final analysis. 

For more information on the statistical methodology used in the `ADSSMod` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ADSSMod.pdf).

## Adaptive design with sample size re-estimation using `ADSSMod` function

As in [Case study A1](https://medianasoft.github.io/CaseStudyA1), the `ADSSMod` function will be used to evaluate key operating characteristics of the proposed adaptive design. 

The MedianaDesigner package needs to be loaded first:

``` r
library(MedianaDesigner)
```

and an empty list of the function parameters needs to be set up:

``` r
parameters = list()
```

The primary endpoint in this trial is the change from baseline to 6 weeks in the Positive and Negative Syndrome Scale (PANSS) total score. The endpoint type needs to be defined as follows:

``` r
parameters$endpoint_type = "Normal"
```

A larger reduction in the PANSS total score corresponds to a favorable outcome and thus the `direction` parameter will be set to "Lower", i.e.,

``` r
parameters$direction = "Lower"
```

Two hundred patients per arm will be initially enrolled in the trial and randomized using a 1:1 scheme to receive placebo or experimental treatment. This means that the original patient numbers in the placebo and treatment arms need to be specified as follows:

``` r
parameters$sample_size = c(200, 200)
```

The sample size may be adjusted at Interim analysis 2, as explained below.

As in many other neuroscience trials, a fairly high patient dropout rate is expected in the trial. The rate will be set set to 20%:

``` r
parameters$dropout_rate = 0.2
``` 

The treatment effect assumptions in the placebo and treatment arms will be defined using the mean and standard deviation for the primary endpoint: 

``` r
parameters$control_mean = -15
parameters$control_sd = 25
parameters$treatment_mean = -22.5
parameters$treatment_sd = 25
```

The interim analysis parameters will be defined as follows. The information fractions at the two interim looks are set to 30% and 70%, respectively. The sample size at Interim analysis 2 may be increased up to a prospectively defined cap, which is set at 30%. As a consequence, the vector of the information fractions is given by

``` r
parameters$info_frac = c(0.3, 0.7, 1, 1.3)
``` 

The futility stopping rule at Interim analyses 1 will rely on a 10% threshold (which means that trial will be terminated for futility if conditional power is no greater than 10%): 

``` r
parameters$futility_threshold = 0.1
``` 

The sample size re-estimation rule at Interim analyses 2 will be defined using the lower and upper limits of the promising interval as well as the target conditional power for increasing the number of patients: 

``` r
parameters$promising_interval = c(0.5, 0.9)
parameters$target_power = 0.9
``` 

Finally, the Type I error rate and number of simulation runs need to be specified. The one-sided Type I error rate will be set to 2.5% in this Phase III trial:

``` r
parameters$alpha = 0.025
```

Also, 10,000 simulation runs will be used in this simulation exercise:

``` r
parameters$nsims = 10000
```

The parameter list needs to be passed to the `ADSSMod` function, the function will run simulations to compute key operating characteristics and save the results in the `results` object:

``` r
results = ADSSMod(parameters)
```

A comprehensive simulation report is easy to create by calling the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyA2.docx")
```

The simulation report will be saved in the current working directory. 

Just like in [Case study A1](https://medianasoft.github.io/CaseStudyA1), the simulation report provides a detailed summary of the design's operating characteristics, including the probability of stopping for futility at Interim analysis 1 and the probability of adjusting the sample size at Interim analysis 2. The report also provides probability of success comparisons between the adaptive design and a traditional design without an option to increase the sample size.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyA2.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyA2.docx).

## References

Meltzer HY et al. (2011). Lurasidone in the treatment of schizophrenia: A randomized, double-blind, placebo- and olanzapine-controlled study. American Journal of Psychiatry. 168, 957-967. DOI: 10.1176/appi.ajp.2011.10060907.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

