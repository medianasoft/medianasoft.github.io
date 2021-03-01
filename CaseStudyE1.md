---
layout: page
title: MedianaDesigner
group: 
---

# Event prediction in event-driven trials (Case study E1)

## Phase III trial and `EventPred` function

Event predictions play an important role in clinical trials with time-to-event endpoints. To illustrate the process of event forecasting, consider a Phase III trial in an oncology population, e.g., the Phase III trial in patients with acute myeloid leukemia used in [Case study A1](https://medianasoft.github.io/CaseStudyA1). The trial is conducted to investigate the efficacy of an experimental treatment versus a control (best supportive care). The primary endpoint is overall  survival (time from patient enrollment to death from any cause). 

To support the internal decision-making process, predictions will be run to estimate the average number of events at future time points. The predictions are based on blinded data, including patient enrollment, patient dropout and time to event information. 

It will be assumed that the current snapshot of the blinded trial data is included in the `EventPredData` data set, which comes with the `MedianaDesigner` package. This data set is a data frame with patient enrollment, patient dropout and time to event information on 150 patients at 12 months after the study start. The blinded data, along with relevant prior information, will be used to predict the average number of events and define a 95% predictive interval at each pre-defined time point using the `EventPred` function.

An important feature of the event forecasting algorithm in the `EventPred` function is that it relies on a Bayesian approach and prior distributions need to be defined for the following parameters:

* Event hazard rate.
* Patient dropout hazard rate.
* Patient enrollment rate. 

The process of specifying the prior distributions is illustrated below.

For more information on the statistical methodology used in the `EventPred` function, download the [technical manual](http://www.mediana.us/MedianaDesigner/EventPred.pdf).

## Event prediction using `EventPred` function

This section illustrates the steps to run event predictions in this Phase III trial using the `EventPred` function. 

As always, assume that the package has been loaded:

``` r
library(MedianaDesigner)
```

After that, the function's parameters will be specified using a parameter list:

``` r
parameters = list()
```

The event forecasting will be performed using the available blinded patient enrollment, patient dropout and event data stored in the `EventPredData` data set. It is worth  noting that `EventPredData` is a built-in data set, which is loaded with the package (no additional steps are required to load this data set). The name of this data set needs to be assigned to the `data_set` parameter:

``` r
parameters$data_set = EventPredData
``` 

If the user would like to perform event forecasting using another data set, it will be important to ensure that this data set meets the following requirements. First of all, this needs to be a data frame with a single record per patient. Secondly, the following four variables should be included in the data frame:

* `enrollment`: Time of the patient's enrollment relative to the study start.
* `time`: Time to the event of interest, e.g., patient's death, if the event occurred or the last contact if the event is censored. The time to event or last contact is defined relative to the patient's enrollment.
* `event`: Event indicator (1 if the event of interest occurred and 0 otherwise).
* `dropout`: Patient dropout indicator (1 if the patient drops out of the trial/is lost to follow up and 0 otherwise).

The pre-defined time points at which the event predictions will be computed, i.e., 12, 13, ..., 24 months after the study start, need to be defined as follows:

``` r 
parameters$time_points = seq(from = 12, to = 24, by = 1)
```

The next set of parameters deals with the specification of the prior distributions for the key components of the underlying model, i.e., the event hazard rate, patient dropout hazard rate and patient enrollment rate. These parameters are assumed to follow gamma distributions. The shape and rate parameters of the gamma distributions could be specified directly or, alternatively, the user could rely on the `EventPredPriorDistribution` function. This function facilitates the process of prior specifications by allowing the user to provide the expected value of a parameter of interest, e.g., the event hazard rate, as well as the uncertainty parameter:

* `expected`: Expected value of the hazard rate, e.g., the event hazard rate, patient dropout hazard rate or patient enrollment rate. 
* `uncertainty`: Uncertainly parameter (coefficient of variation). 

Since the uncertainty parameter is the coefficient of variation, it quantifies the amount of variability around the expected value. As a general guideline, the uncertainty parameter of 0.1 defines a high-confidence prior distribution, whereas 0.3 and 0.5 correspond to medium-confidence and low-confidence prior distributions, respectively. 

To illustrate, consider the specification of a prior distribution from the gamma family for the event hazard rate. The expected value will be derived under the assumption that the median survival time is 15 months. Note that the median time of 15 months needs to be transformed to compute the hazard rate, i.e., the hazard rate is equal to log(2) / 15. To set up a medium-confidence prior distribution for the event hazard rate, the uncertainly parameter will be equal to 0.3. The shape and rate parameters of this gamma distribution are computed as follows:

``` r
parameters$event_prior_distribution = 
    EventPredPriorDistribution(expected = log(2) / 15, uncertainty = 0.3)
```

It is easy to verify that the shape and rate parameters equal 11.1 and 240.5, respectively.

The prior distributions for the other two parameters, i.e., the patient dropout hazard rate and patient enrollment rate, are defined in a similar way:

``` r
parameters$dropout_prior_distribution = 
    EventPredPriorDistribution(expected = log(2) / 80, uncertainty = 0.3)

parameters$enrollment_prior_distribution = 
    EventPredPriorDistribution(expected = 35, uncertainty = 0.3)
```

Both of these specifications assume a medium-confidence prior with the uncertainty parameter set to 0.3. The expected value of the patient dropout hazard rate is computed assuming the median dropout time of 80 months and the patient enrollment rate is assumed to be 35 patients per month.

The event predictions will be run using 1,000 simulation runs:

``` r
parameters$nsims = 1000
```

The last step is to call the `EventPred` function to predict the average number of events and compute the predictive intervals at 12, 13, ..., 24 months:

``` r
results = EventPred(parameters)
```

A comprehensive simulation report is generated by calling the `GenerateReport` function. The object created by the `EventPred` function, i.e., `results`, needs to be passed to the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyE1.docx")
```

This function will create a Microsoft Word document with the name specified in the function call and will save it in the working directory. The document includes a summary of the trial parameters as well as tabular and graphical summaries of the event prediction at the pre-defined time points.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyE1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyE1.docx).

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).





