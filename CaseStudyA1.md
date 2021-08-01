---
layout: page
title: MedianaDesigner
group: 
---

# Adaptive designs with data-driven sample size or event count re-estimation (Case study A1)

## Phase III trial with a time-to-event endpoint

The case study walks the user through the process of designing adaptive trials that support an option to modify the total number of patients or events based on the data available at an interim analysis. The general design of the QUAZAR AML-001 trial (Wei et al., 2020) is used in this case study. This Phase III clinical trial was conducted to investigate the efficacy and safety of oral azacitidine as a maintenance therapy in patients with acute myeloid leukemia. 

The efficacy objective of the trial was formulated in terms of assessing survival prolongation compared to control (best supportive care). A time-to-event endpoint (overall survival time) was used in this trial. The original trial employed a single interim analysis for futility after 30% of the events occurred. In this case study the original design is extended to an adaptive design with two interim looks:

* Interim analysis 1 supports early stopping for futility if the efficacy signal is too weak.
* Interim analysis 2 supports an option to increase the target number of events in the trial if the efficacy signal is weaker than anticipated.

The third decision point is Final analysis. 

For more information on the statistical methodology used in the `ADSSMod` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ADSSMod.pdf).

## Adaptive design with event count re-estimation using `ADSSMod` function

This section introduces key features of the `ADSSMod` function and illustrates the process of evaluating key operating characteristics of the adaptive design defined above. The design parameters and treatment effect assumptions presented in this section are based on the corresponding parameters of the QUAZAR AML-001 trial.

The first step is to load the MedianaDesigner package:

``` r
library(MedianaDesigner)
```

The parameters of the `ADSSMod` function, just like any other function in this package, are specified using a parameter list and the user needs to begin with an empty list:

``` r
parameters = list()
```

The required arguments of this parameter list are then populated as shown below.

First of all, the endpoint type needs to be defined:

``` r
parameters$endpoint_type = "Time-to-event"
```

Since a longer survival time is a more favorable outcome in this trial, the `direction` parameter needs to be set to "Higher", i.e.,

``` r
parameters$direction = "Higher"
```

The total number of enrolled patients was assumed to be 460 in the QUAZAR AML-001 trial and a 1:1 randomization ratio was used in the trial. Given this, the sample sizes in the control and treatment arms are specified as follows:

``` r
parameters$sample_size = c(235, 235)
```

In addition to the number of enrolled patients, the target number of events (deaths from any cause) at the final analysis also needs to be provided:

``` r
parameters$event_count = 330
``` 

This target corresponds to the original trial design, i.e., before event count re-estimation is applied at Interim analysis 2. The maximum number of events at the final analysis after event count re-estimation is defined below.

The magnitude of the treatment effect in the trial is specified using median survival times in the two trial arms (the times are measured in months):

``` r
parameters$control_time = 16
parameters$treatment_time = 22.9
```

The survival distribution is assumed to be exponential and, under this assumption, these median survival times correspond to a hazard rate of 0.7. It is worth noting that this assumption turned out to be conservative and a stronger treatment effect was established in the QUAZAR AML-001 trial. The median survival times in the control and treatment arms were 14.8 months and 24.7 months, respectively.

The next set of trial parameters defines the decision rules at the two interim looks. Beginning with information fractions, Interim analyses 1 and 2 will be conducted after 30% and 60% of the the events occurred, which means that the information fractions at these decision points are equal to 0.3 and 0.6. The information fraction at the original final analysis (before event count re-estimation) is simply 1 and the information fraction at the final analysis after event count re-estimation corresponds to a cap on the event count increase. Suppose that this cap is 30%, i.e., the number of events may not be adjusted by more than 30%. This means that the information fraction at the final analysis after event count re-estimation is 1.3.

The vector of the information fractions is then set up as follows:

``` r
parameters$info_frac = c(0.3, 0.6, 1, 1.3)
``` 

A futility assessment will be performed at Interim analyses 1 and the futility threshold for conditional power at this interim analyses is given by: 

``` r
parameters$futility_threshold = 0.1
``` 

The trial will be stopped due to futility at this decision point if conditional power is less than 10%.

The event count re-estimation rule at Interim analyses 2 is also defined in terms of conditional power. The target number of events will be increased to achieve a pre-defined target for conditional power (up to the 30% cap) if conditional power at this decision point falls within a promising interval. The promising interval for conditional power and target conditional power are specified as follows:

``` r
parameters$promising_interval = c(0.5, 0.9)
parameters$target_power = 0.9
``` 

Since the primary endpoint in this trial is a time-to-event endpoint, the user is required to define the details of the patient enrollment and dropout processes. The patient enrollment is defined using two parameters, namely, the length of the patient enrollment period and the median enrollment time (both are measured in months):

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
``` 

The patient enrollment is governed by a truncated exponential distribution and the median enrollment time corresponds to the time point by which 50% of the patients have been enrolled. With the median enrollment time of 24 months, it will take two years to enroll the first half of the patients and the remaining 50% will be enrolled during the third year. By the way, to specify uniform  patient accrual, the median enrollment time would need to be set to the mid-point of the patient enrollment period, i.e., to 18 months.

The annual patient dropout rate is set to 5%:

``` r
parameters$dropout_rate = 0.05
``` 

It is assumed that the time to patient dropout follows an exponential distribution.

The last design parameter is the one-sided Type I error rate in the trial. In confirmatory Phase III trials the one-sided rate is equal to 2.5%, i.e.,

``` r
parameters$alpha = 0.025
```

The adaptive design's characteristics will be evaluated via simulations based on 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

The resulting parameter list is ready to be passed to the `ADSSMod` function. The function will run simulations and save the results in the `results` object:

``` r
results = ADSSMod(parameters)
```

An important feature of the MedianaDesigner package is that it provides a convenient option to generate simulation reports (Microsoft Word documents) with a detailed summary of the adaptive design's parameters and simulation results. To create a simulation report, the user needs to call the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyA1.docx")
```

The simulation report will be saved in the current working directory. 

The report presents a summary of the design's key operating characteristics in Tables 8 and 9. Table 8 lists the following outcome probabilities:

* Probability of stopping for futility at Interim analysis 1.
* Probability of increasing the number of events at Interim analysis 2.
* Power of traditional design.
* Power of adaptive design.

The last two characteristics facilitate a comparison of the proposed adaptive design and a reference design. The reference design (traditional design) also employs a futility stopping rule at Interim analysis 1 but does not support an option to increase the target number of events at Interim analysis 2 in a data-driven manner.

A more informative comparison of the adaptive and traditional designs is presented in Table 9, which displays the conditional probability of success at the final analysis given that the treatment effect at Interim analysis 2  falls within each of the three pre-defined intervals (unfavorable interval, promising interval and favorable interval). The conditional probability of success is the same for the adaptive and traditional designs within the unfavorable and favorable intervals. However, the adaptive design tends to provide a tangible power advantage over the traditional design within the promising interval.

It is important to note that, since the trial is adequately powered in this particular case, the proposed adaptive design does not provide much power advantage over the traditional design. To better evaluate the performance of the adaptive design, it is recommended to consider scenarios with a weaker treatment effect, e.g., by assuming the hazard ratio of 0.75 and 0.8.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyA1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyA1.docx).

## References

Wei AH et al. Oral azacitidine maintenance therapy for acute myeloid leukemia in first remission. The New England Journal of Medicine. 2020; 383:2526-2537. DOI: 10.1056/NEJMoa2004444.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

