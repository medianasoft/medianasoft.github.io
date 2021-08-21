---
layout: page
title: MedianaDesigner
group: 
---

# Adaptive trials with response-adaptive randomization (Case study F1)

## Dose-finding Phase II trial

A simulation-based approach to designing trials with response-adaptive randomization will be illustrated using a Phase II trial in patients with partially responsive major depressive disorder. This trial will be conducted to study the dose-response profile of a novel treatment. Four doses of this treatment (100 mg/day, 200 mg/day, 400 mg/day and 800 mg/day) will be compared to placebo. The primary efficacy analysis will be performed using a continuous endpoint, i.e., the mean reduction from baseline to 4 weeks in the MADRS (Montgomery-Asberg Depression Rating Scale) total score. The endpoint is normally distributed and, without a treatment, the mean and standard deviation of the change at 4 weeks are expected to be -10 and 14, respectively.

A four-stage design will be employed in this clinical trial with 50 patients enrolled in each stage. Patients in Stage 1 will be equally randomized to the four doses of the experimental treatment or placebo and, starting with Stage 2, a response-adaptive randomization approach will be applied. At the end of each stage the posterior probabilities of achieving target efficacy will be computed in each of the four dosing arms and the randomization scheme for the next stage will be updated appropriately (note that the randomization ratio in the placebo arm will be fixed at 20%). The ultimate goal of the response-adaptive design is to treat more patients at the most effective doses. 

The steps required to perform a simulation-based evaluation of this response-adaptive design using the `ADRand` function are presented below. For more information on the statistical methodology used in this function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ADRand.pdf).

## Response-adaptive randomization using `ADRand` function

Key features of the `ADRand` function and the evaluation process for the dose-finding Phase II trial are presented below. 

The MedianaDesigner package needs to be loaded first, i.e.,

``` r
library(MedianaDesigner)
```

and an empty list of function parameters needs to be created:

``` r
parameters = list()
```

The function's parameters will be set up beginning with the endpoint parameters. This includes the endpoint type, i.e.,

``` r
parameters$endpoint_type = "Normal"
```

and the direction of favorable effect for the primary endpoint. A larger reduction in the MADRS total score indicates a beneficial effect and therefore the `direction` parameter will be set to "Lower", i.e.,

``` r
parameters$direction = "Lower"
```

The next set of parameters defines the trial design and treatment effect assumptions. First of all, the dose levels to be examined in the trial are specified (with 0 corresponding to the placebo arm):

``` r
parameters$dose_levels = c(0, 100, 200, 400, 800)
```

A four-stage design will be employed in the trial and 50 patients are expected to be enrolled in each stage. The stage-specific numbers of enrolled patients are specified as follows:

``` r
parameters$stage_sample_size = c(50, 50, 50, 50)
```

It is expected that it will take about 36 weeks to enroll the 200 patients into the trial. The patient enrollment will follow a non-uniform pattern, specifically a truncated exponential distribution will be assumed with the median enrollment time of 24 weeks (the median enrollment time corresponds to the time point by which 50% of the patients will be enrolled into the trial). The two parameters of the patient enrollment distribution are set up as follows:

``` r
parameters$enrollment_period = 36
parameters$enrollment_parameter = 24
```

The length of the treatment period in this trial is 4 weeks (the primary efficacy evaluation will be performed at 4 weeks after randomization):

``` r
parameters$treatment_period = 4
```

Given that the treatment period is quite short, only 5% of the patients are expected to be lost to follow up, which leads to the following value of the patient dropout rate:

``` r
parameters$dropout_rate = 0.05
```

Since the primary endpoint follows a normal distribution, the treatment effect assumptions will be defined by specifying the true mean and standard deviation of the arm-specific normal distributions. The parameters of the primary endpoint's distribution in the placebo arm are set up first:

``` r
parameters$control_mean = -10
parameters$control_sd = 14
```

and then the corresponding parameters are defined in the four dosing arms:

``` r
parameters$treatment_mean = c(-12, -14, -16, -18)
parameters$treatment_sd = c(14, 14, 14, 14)
```

With any response-adaptive design, it is common to fix the randomization ratio in the placebo arm. In this trial this ratio will be set to 20%:

``` r
parameters$ratio_placebo = 0.2
```

As explained in the technical manual, the class of response-adaptive designs for dose-finding trials implemented in this module relies on a model-averaging approach based on the MCPMod method. This method requires that a set of dose-response functions that describe plausible shapes of the dose-response relationship should be prospectively defined. The current implementation of the method assumes four candidate dose-response models, namely, linear, exponential, Emax and logistic models. The non-linear parameters of the exponential, Emax and logistic models are specified as follows:

``` r
# Non-linear parameter of the exponential model (delta)
parameters$exponential_model_parameter = 400

# Non-linear parameter of the Emax model (ED50)
parameters$emax_model_parameter = 600

# Non-linear parameters of the logistic model (ED50 and delta)
parameters$logistic_model_parameters = c(400, 50)
```

It is important to note that no parameter needs to be specified for the linear model. 

For more information on the MCPMod methodology and its implementation, see the [MCPModPack package](https://cran.r-project.org/web/packages/MCPModPack/index.html). 

After the model-averaging approach has been applied to the dose-response data to estimate the mean effects across the five trial arms, the posterior probability of achieving target efficacy is computed for each dose of the experimental treatment. The target efficacy profile is defined using a threshold that corresponds to a clinically relevant improvement over placebo:

``` r
parameters$delta = -6
```

The probability of meeting the target efficacy profile plays a key role in the adaptive randomization procedure. This probability is computed for each dose at the end of each stage and directly affects the dose-specific randomization ratios in the next stage. Note that a negative value of the threshold is required in this case since a reduction in the MADRS total score indicates beneficial effect.

The last parameter related to the adaptive randomization procedure is the balance parameter. This parameter controls the degree of balance for patient allocation in the next cohort. If a smaller value is chosen, the patient allocation across the dosing arms is more likely to be balanced and, with a larger value of this parameter, the allocation will be increasingly imbalanced. In this case study, the balance parameter will be set to 2 to assign most of the patients to the most promising doses:

``` r
parameters$balance = 2
```

The statistical significance of the dose-response relationship in the response-adaptive design, as well as a traditional design with equal randomization, will be evaluated at a  one-sided alpha of 2.5%, i.e.,

``` r
parameters$alpha = 0.025
```

Key operating characteristics of the adaptive and traditional designs will be estimated using 1,000 simulation runs:

``` r
parameters$nsims = 1000
```

The resulting list of parameters (`parameters`) will be passed to the `ADRand` function to compute the operating characteristics:

``` r
results = ADRand(parameters)
```

It is important to point out that this function relies on Bayesian calculations and it will take several minutes to run the simulations.

The `GenerateReport` function need to be invoked to create a simulation report. The object created by the `ADRand` function, i.e., `results`, needs to be passed to the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyF1.docx")
```

This function will generate a Word-based simulation report with the specified file name and save it in the working directory. The report includes a summary of the trial parameters as well as the adaptive design's key operating characteristics such as a sample size summary by trial arm and a probability of success summary based on the significance of the dose-response relationship.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyF1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyF1.docx).

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).





