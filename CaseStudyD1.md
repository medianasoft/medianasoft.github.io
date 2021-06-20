---
layout: page
title: MedianaDesigner
group: 
---

# Optimal selection of a futility stopping rule (Case study D1)

## Phase III trial with a time-to-event endpoint

The case study defines the steps that need to be taken to derive a futility stopping rule with optimal properties in a clinical trial with a time-to-event endpoint. The trial to be considered in this case study is the QUAZAR AML-001 trial in patients with acute myeloid leukemia, see [Case study A1](https://medianasoft.github.io/CaseStudyA1). The primary analysis in this trial relies on the comparison of overall survival times in the treatment and control arms.

The trial is assumed to employ a single interim analysis that will support a futility assessment. The interim look is planned to be taken after 50% of the events have been accrued. The futility stopping rule will be defined using conditional power and a recommendation to stop the trial for futility will be issued if conditional power at the interim analysis is below a pre-defined threshold. The trial's sponsor is interested in finding a threshold for conditional power that provides an optimal balance between the sensitivity and specificity of the underlying decision rule. 

For more information on the statistical methodology used in the `FutRule` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/FutRule.pdf).

## Optimal selection of a futility stopping rule using `FutRule` function

An optimal futility stopping rule is easy to derive in this Phase III trial by invoking the `FutRule` function. 

After loading the MedianaDesigner package, i.e.,

``` r
library(MedianaDesigner)
```

the user needs to initialize the list of parameters that will be passed to the `FutRule` function, i.e.,

``` r
parameters = list()
```

and then populate the individual arguments. The required arguments defined below are very similar to those used by the adaptive design functions, i.e., `ADSSMod`, `ADTreatSel` and `ADPopSel`.

The endpoint type needs to be specified first:
``` r
parameters$endpoint_type = "Time-to-event"
```

As in [Case study A1](https://medianasoft.github.io/CaseStudyA1), a longer survival time is a more favorable outcome in this trial and therefore the `direction` parameter needs to be set to "Higher", i.e.,

``` r
parameters$direction = "Higher"
```

The number of patients to be enrolled in the control and treatment arms as well as the target number of events at the final analysis are specified as follows:

``` r
parameters$sample_size = c(230, 230)
parameters$event_count = 330
```

As the next step, the median survival times in the control and treatment trial arms (measured in months) are defined:

``` r
parameters$control_time = 16
parameters$treatment_time = 22.9
```

As in the adaptive design functions, the time to event is assumed to be exponentially distributed in the `FutRule` function.

The information fraction, expressed in terms of the number of events, is set to 50%: 

``` r
parameters$info_frac = 0.5
``` 

The parameters of the patient enrollment and dropout processes are required in this particular case because the primary endpoint is a time-to-event endpoint. These parameters are set to the same values as in [Case study A1](https://medianasoft.github.io/CaseStudyA1), i.e.,

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
parameters$dropout_rate = 0.05
``` 

In other words, the length of the patient enrollment period is 36 months and 50% of the patients are expected to be enrolled into the trial by 24 months. The annual patient dropout rate, under an exponential dropout distribution, is assumed to be 5%.

The one-sided Type I error rate in this Phase III trial is set to 2.5%, i.e.,

``` r
parameters$alpha = 0.025
```

The simulation-based algorithm for computing an optimal threshold for conditional power at the interim analysis will rely on 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

To run the calculations, the list of trial parameters is passed to the `FutRule` function:

``` r
results = FutRule(parameters)
```

The simulation results will be saved in the `results` object and it is easy to create a detailed simulation report in a Microsoft Word format by calling the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyD1.docx")
```

The simulation report, which will be saved in the working directory, presents plots of the sensitivity, specificity and accuracy rates as a function of the threshold for conditional power (see Figures 1, 2 and 3). The sensitivity and specificity rates are defined as the probability of correctly continuing the trial and the probability of correctly stopping the trial due to futility at the interim analysis, respectively. An optimal value of the threshold is expected to simultaneously improve the sensitivity and specificity rates. This is accomplished by finding the threshold that maximizes the accuracy rate, i.e., the average of the sensitivity and specificity rates. The optimal threshold is identified in Figure 3. In addition to the optimal threshold, the `FutRule` function also computes a set of nearly optimal values, known as a 95% optimal interval. Optimal intervals of this kind tend to be wide if the optimization function, i.e. the accuracy rate as a function of the threshold, is fairly flat around its maximum.  

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyD1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyD1.docx).

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).
