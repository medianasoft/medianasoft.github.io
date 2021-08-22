---
layout: page
title: MedianaDesigner
group: 
---

# Traditional trials with multiple dose-placebo comparisons and multiple endpoints (Case study G3)

## Phase III trial with three dose-placebo comparisons and two multiple endpoints

To introduce a general process for performing power calculations in clinical trials with complex multiplicity problems, e.g., trials with two sources of multiplicity, a Phase III trial in acutely psychotic patients with schizophrenia (Nasrallah et al., 2013) will be used. This trial investigated the efficacy profile of three doses of lurasidone (40 mg/day, 80 mg/day and 120 mg/day) versus placebo. The efficacy evaluations were performed with respect to two prospectively defined endpoints (primary and key secondary endpoints):

* Endpoint 1 (primary): Change from baseline to Week 6 in the Positive and Negative Syndrome Scale (PANSS) total score.
* Endpoint 2 (key secondary endpoint): Change from baseline to Week 6 in the Clinical Global Impressions Severity Scale (CGI-S) score.

Both endpoints are normally distributed and the correlation between them is assumed to be 0.5. It is important to note that lower values of the PANSS total score and CGI-S score are associated with improvement and indicate a beneficial treatment effect.

The same general setting will be assumed in this case study. The dose levels of the experimental treatment will be labeled as Dose L, Dose M and Dose H. The three dose-placebo comparisons serve as the first source of multiplicity and the evaluation of the primary and secondary endpoints defines the second source of multiplicity. This means that the efficacy evaluation involves six null hypotheses of no effect. The null hypotheses are defined as follows:

* H1: Null hypothesis of no difference between Dose L and placebo with respect to Endpoint 1.
* H2: Null hypothesis of no difference between Dose M and placebo with respect to Endpoint 1.
* H3: Null hypothesis of no difference between Dose H and placebo with respect to Endpoint 1.
* H4: Null hypothesis of no difference between Dose L and placebo with respect to Endpoint 2.
* H5: Null hypothesis of no difference between Dose M and placebo with respect to Endpoint 2.
* H6: Null hypothesis of no difference between Dose H and placebo with respect to Endpoint 2.

The null hypotheses are naturally grouped into two families based on the underlying endpoint:

Family 1 (primary family): Hypotheses H1, H2 and H3.
Family 2 (secondary family): Hypotheses H4, H5 and H6.

The families will be tested sequentially beginning with the primary family. The hypotheses in the primary family impose logical restrictions on the secondary hypotheses in the sense that a comparison between a dose and placebo on the key secondary endpoint will be carried out only if a significant treatment effect is established at the same dose in the primary family. This implies that the secondary hypothesis H4 can be tested only if the corresponding primary hypothesis H1 is rejected, otherwise this secondary hypothesis is accepted without testing.

To account for the two sources of multiplicity, a multiplicity adjustment based on a gatekeeping procedure will be utilized in this case study. This gatekeeping procedure applies Hochberg-type component procedures in each family:

* A truncated Hochberg procedure is used in Family 1 since this family serves as a gatekeeper for the secondary family. Due to the use of a truncated procedure in the primary family, the gatekeeping procedure can proceed to Family 2 even if only one hypothesis is rejected in the Family 1, i.e., only one dose is statistically significant on the primary endpoint. The truncated Hochberg procedure is defined using a pre-specified truncation parameter of 0.8.
* The regular Hochberg procedure is used in Family 2 since it is the last family in the sequence.

As in [Case study G1](https://medianasoft.github.io/CaseStudyG1), the Hochberg component procedures ensure strong Type I error rate control within each family since the test statistics corresponding to the dose-placebo comparisons follow a multivariate normal distribution with positive pairwise correlations.

Power calculations for the Hochberg-based gatekeeping procedure will be performed using the `MultAdj` function.

For more information on the statistical methodology used in the `MultAdj` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/MultAdj.pdf).

## Power calculations using `MultAdj` function

The process of evaluating most important characteristics of the specified multiplicity adjustment, e.g., the overall probability of success in the trial as well as the probability of rejecting at least one null hypothesis in the secondary family, will be implemented using the `MultAdj` function. 

The process begins with loading the MedianaDesigner package and initiating an empty list of parameters:

``` r
library(MedianaDesigner)
parameters = list()
```

As in other case studies, the first parameter to be populated is the endpoint type. In this case, two normally distributed endpoints will be specified:

``` r
parameters$endpoint_type = "Normal"
```

As indicated above, lower levels of both endpoints correspond to a more favorable outcome:

``` r
parameters$direction = "Lower"
```

A "bivariate" multiplicity problem with two source of multiplicity arises in this cases study due to the fact that three dose-placebo comparisons will be performed with respect to two endpoints:

``` r
parameters$n_comparisons = 3
parameters$n_endpoints = 2
```

The sample size vector with an equal randomization of 460 patients to the four trial arms is specified as follows:

``` r
parameters$sample_size = c(115, 115, 115, 115)
```

The trial duration is quite short in this case study and 10% of the patients are expected to be lost to follow up by the end of the 6-week treatment period:

``` r
parameters$dropout_rate = 0.1
``` 

The treatment effect assumptions need to specified using vectors of distributional parameters in the placebo arm and matrices in each dosing arm. Beginning with the placebo arm, the expected mean changes and the corresponding standard deviations for the PANSS total score and CGI-S score are given by

``` r
parameters$control_mean = c(-15, -1)
parameters$control_sd = c(20, 1)
```

To capture the mean changes for the same two endpoints across the three dosing arms, the following matrix needs to be set up:

``` r
parameters$treatment_mean = matrix(c(-20, -21, -23,
                                     -1.35, -1.40, -1.45), 2, 3, byrow = TRUE)

```

The `MultAdj` function expects a 2 by 3 matrix since the rows correspond to the endpoints and the columns correspond to the dose-placebo comparisons. To make sure that the matrix is filled by rows, the `byrow` needs to be set to TRUE.

The standard deviations are defined in a similar way:

``` r
parameters$treatment_sd = matrix(c(20, 20, 20,
                                     1, 1, 1), 2, 3, byrow = TRUE)

```

The last distributional parameter to be specified is the correlation matrix for the primary and key secondary endpoints. Since the correlation between the endpoints is set to be 0.5, the matrix is given by

``` r
parameters$endpoint_correlation = matrix(c(1, 0.5, 
                                           0.5, 1), 2, 2)
```

To define the parameters of the gatekeeping procedure, the component procedure needs to be specified first: 

``` r
parameters$mult_test = "Hochberg"
```

The hypothesis rejection rules in this gatekeeping procedure will be constructed using the modified mixture method:

``` r
parameters$mult_method = "Modified"
```

The truncation parameter for the truncated Hochberg procedure in the primary family is set to 0.8 and, since the regular Hochberg procedure is planned to be used in the secondary family, the corresponding truncation parameter is equal to 1. This implies that

``` r
parameters$mult_test_gamma = c(0.8, 1)
```

The overall one-sided Type I error rate is to be controlled at a 2.5% level in this Phase III trial and therefore 

``` r
parameters$alpha = 0.025
```

Power calculations will be run using 10,000 simulation runs:

``` r
parameters$nsims = 10000
```

This parameter list now needs to be passed to the `MultAdj` function:

``` r
results = MultAdj(parameters)
```

and the simulation results will be saved in the `results` object. Using this object, a detailed simulation report with a summary of power calculations is easily generated using the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyG3.docx")
```

The simulation report will be saved in the current working directory. 

The key power characteristics of the specified multiplicity adjustment are summarized in Tables 8 and 9. As in [Case study G1](https://medianasoft.github.io/CaseStudyG1), Table 8 provides a summary of marginal power calculations. Marginal power, i.e., the probability of rejecting each hypothesis, is computed before and after the multiplicity adjustment. Table 9 presents disjunctive and conjunctive power within each family. The family-specific disjunctive and conjunctive power are defined as follows: 

* Disjunctive power is defined as the probability of rejecting at least one hypothesis within each family. 
* Conjunctive power is defined as the probability of rejecting all hypotheses within each family.

In both cases, power calculations are performed after the specified multiplicity adjustment is applied.

Disjunctive power in the primary family defines the overall probability of success in the trial and disjunctive power in the secondary family helps the trial's sponsor determine the probability of making additional effectiveness claims based on the selected key secondary endpoint. 

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyG3.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyG3.docx).

## References

Nasrallah et al. Lurasidone for the treatment of acutely psychotic patients with schizophrenia: A 6-week, randomized, placebo-controlled study. Journal of Psychiatric Research. 2013; 47:670-677. DOI: 10.1016/j.jpsychires.2013.01.020.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

