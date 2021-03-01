---
layout: page
title: MedianaDesigner
group: 
---

# Adaptive designs with data-driven population selection (Case study C1)

## Phase III trial with a time-to-event endpoint

Adaptive designs with decision rules aimed at identifying the most promising patient population or populations will be illustrated using a case study based on the SATURN trial for the treatment of advanced non-small-cell lung cancer (Cappuzzo et al., 2010). The primary efficacy endpoint was progression-free survival and this trial was conducted to evaluate the effects of erlotinib in two pre-specified patient populations:

* Overall population of patients with advanced non-small-cell lung cancer.
* Subpopulation of patients with a positive biomarker status, i.e., a positive EGFR (epidermal growth factor receptor) immuno-histochemistry status. 

The multi-population approach was employed to better characterize erlotinib's efficacy profile in this patient population and improve the probability of success in the trial. 

The SATURN trial relied on a group-sequential design with a single interim efficacy assessment. This case study will present an extension of the SATURN trial's design that retains the multi-population approach but employs two interim analyses with data-driven decision rules: 

* Interim analysis 1 supports a futility assessment in the overall population, i.e., the trial will be terminated for futility if the overall treatment effect is too weak.
* Interim analysis 2 supports an option to select the best population/populations in the trial.

The adaptive population-selection design considered in this case study assumes that the trial's sponsor plans to pursue three distinct effectiveness claims in the trial: 

* Effectiveness claim in the overall population.
* Effectiveness claim in the pre-defined subpopulation (subpopulation of biomarker-positive patients).
* Effectiveness claim in both populations.

The effectiveness claims define the null hypotheses of no treatment effect to be tested at the third decision point (Final analysis). These hypotheses are chosen at Interim analysis 2. For example, the enrollment of biomarker-negative patients may be discontinued after Interim analysis 2, in which case only the null hypothesis of no effect in the biomarker-positive subpopulation will be tested at the final analysis. The efficacy evaluation could also be performed in the overall population and biomarker-positive subpopulation, i.e., the two corresponding null hypotheses will be tested simultaneously.

For more information on the statistical methodology used in the `ADPopSel` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ADPopSel.pdf).

## Designing an adaptive population-selection trial using `ADPopSel` function

The adaptive population-selection trial outlined above will be designed based on the parameters from the SATURN trial. Key operating characteristics of the adaptive design will be evaluated using the `ADPopSel` function. 

The MedianaDesigner package will be loaded first, i.e.,

``` r
library(MedianaDesigner)
```

and the list of parameters for the `ADPopSel` function will be initialized, i.e.,

``` r
parameters = list()
```

The required arguments of this parameter list include those that are quite similar to the arguments of the `ADSSMod` and `ADTreatSel` functions as well as parameters specific to `ADPopSel`, e.g., parameters of the population selection rule that will be applied at Interim analysis 2. 

Since the primary efficacy endpoint is a survival-type endpoint, the time-to-event endpoint type needs to be requested:

``` r
parameters$endpoint_type = "Time-to-event"
```

A balanced design will be used in the trial and the overall patient numbers in the control and treatment arms are given by

``` r
parameters$sample_size = c(445, 445)
```

The patient numbers within the biomarker-positive subpopulation are set up by specifying the prevalence of biomarker-positive patients in the overall population. The prevalence is assumed to be 50%, i.e., 

``` r
parameters$prevalence = 0.5
``` 

which means that there will be approximately 222 patients with a biomarker-positive status in each trial arm.

Since there are two patient populations in the trial, population-specific treatment effect assumptions need to be made. A common median PFS time will be assumed within the biomarker-negative and biomarker-positive subpopulations in the control arm, namely, the median time of 11 months, i.e., 
 
``` r
parameters$control_time = c(11, 11)
``` 

When specifying the median PFS times in the treatment arm, a differential treatment effect will be assumed, i.e., a weaker effect in the subset of biomarker-negative patients and a stronger effect in biomarker-positive patients. The median PFS times in these two subpopulations (measured in months) are given by 

``` r
parameters$treatment_time = c(13, 15.7)

``` 

These treatment effects roughly correspond to the hazard ratios of 0.85 in the biomarker-negative subpopulation and 0.7 in the biomarker-positive subpopulation.

Similarly, the target number of events at the final analysis needs to be set up separately in the overall population and biomarker-positive subpopulations:

``` r
parameters$event_count = c(600, 300)
``` 

If the efficacy data at Interim analysis 2 support the decision to perform the final analysis in the overall population, the trial will continue until 600 events have been accrued in this population. However, if the null hypothesis of no effect in the biomarker-positive subpopulation is chosen, the trial's duration will be determined by the time it takes to accrue 300 events within this subpopulation.

The information fractions at Interim analysis 1, Interim analysis 2 and Final analysis are defined as follows:

``` r
parameters$info_frac = c(0.4, 0.6, 1)
``` 

These fractions are expressed in terms of the number of events (number of patients with disease progression) in the overall patient population. In particular, the futility assessment at Interim analysis 1 will be performed after 40% of the events in the overall population and the population selection rule will be applied at Interim analysis 2 after 60% of the overall number of events.

Continuing to the decision rules at the two interim looks, the futility threshold at Interim analysis 1 is given by

``` r
parameters$futility_threshold = 0.1
``` 

This means that the trial will be terminated for futility if conditional power in the overall population is below 10%.

The next two parameters are the key components of the population selection rule at Interim analysis 2. The parameters, known as the influence and interaction thresholds, define the influence and interaction conditions that guide the process of identifying the most appropriate patient populations. The influence condition states that an effectiveness claim in the overall population can be pursued only if an overall  beneficial effect is not confined to the biomarker-positive subpopulation. If this condition is met, the interaction condition is evaluated. This condition states that, to conclude that the treatment is simultaneously effective in both populations, the treatment effect in the biomarker-positive subpopulation should be appreciably greater than the effect in the biomarker-negative subpopulation.
The influence and interaction thresholds are set to the following values in this trial:

``` r
parameters$influence = 0.1
parameters$interaction = 1.3
``` 

The patient enrollment and dropout specifications are aligned with the assumptions used in the SATURN trial. The length of the patient enrollment period is set to 18 months. The patient enrollment distribution is a truncated exponential distribution and the median enrollment time is 13.5 months, which means that 50% of the patients will be enrolled prior to the 13.5-month milestone and the remaining 50% after this milestone.  Additionally, 5% of the patients are expected to be lost to follow up on an annual basis, assuming an exponential dropout distribution. These parameters are passed to the `ADPopSel` function as follows:

``` r
parameters$enrollment_period = 18
parameters$enrollment_parameter = 13.5
parameters$dropout_rate = 0.05
``` 

The one-sided Type I error rate in the adaptive trial equals 2.5%, i.e.,

``` r
parameters$alpha = 0.025
```

and 10,000 simulations will be run to compute the trial's operating characteristics:

``` r
parameters$nsims = 10000
```

Operating characteristics of the adaptive population-selection design with the parameters listed above are evaluated by calling the `ADPopSel` function:

``` r
results = ADPopSel(parameters)
```

The simulation results will be saved in the `results` object and it is easy to create a detailed simulation report in a Microsoft Word format by calling the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyC1.docx")
```

The simulation report generated by this function will be saved in the working directory.  

The report includes the following summaries of the simulation results. Table 8 presents key characteristics of the futility stopping and population selection rules at the two interim looks:

* Probability of futility stopping in the overall population at Interim analysis 1.
* Probability of selecting only the overall population at Interim analysis 2.
* Probability of selecting only the biomarker-positive subpopulation at Interim analysis 2.
* Probability of selecting both populations at Interim analysis 2.

Tables 9 and 10 evaluate the performance of the proposed adaptive population-selection design compared to a reference design (traditional design). The reference design also supports futility stopping at Interim analysis 1 but population selection is not enabled, i.e., this design assesses the significance of the treatment effect only in the overall patient population at the final analysis.


## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyC1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyC1.docx).

## References

Cappuzzo F. et al. Erlotinib as maintenance treatment in advanced non-small-cell lung cancer: A multicentre, randomised, placebo-controlled Phase 3 study. Lancet Oncology. 2010; 11:521-529. DOI: 10.1016/S1470-2045(10)70112-1.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).


