---
layout: page
title: MedianaDesigner
group: 
---

# Traditional trials with cluster-randomized designs (Case study H1)

## Intervention study to assess the impact of rapid diagnostic malaria tests

This case study is loosely based on a recent study conducted in Ghana to examine the impact of providing rapid diagnostic tests for malaria on fever management in children and adults (Ansah et al., 2015). 

The ultimate goal of this study was to reduce overdiagnosis and overtreatment of malaria and the primary endpoint was a binary endpoint based on the proportion of negative malaria tests among subjects who received selected antimalarial treatments. A cluster-randomized design with two arms (treatment versus control) was employed in this study. Drug stores in multiple communities throughout a district of Ghana were used as clusters. The total number of clusters in the study was 24 (12 clusters per arm) and a balanced setting with an equal number of subjects across the clusters was considered at the study's design stage. 

Several simplifying assumptions are made in the case study, e.g., only a single population of subjects, namely, adults, is considered. The primary rate in assumed to be 60% and 40% in the control and treatment arms, respectively. The measure of within-cluster consistency commonly used in cluster-randomized studies, i.e., the intracluster correlation coefficient (ICC), is set to 0.1 in both study arms. This low value indicates that the subject outcomes are expected to be weakly correlated within each cluster.

The code presented below illustrates the use of the `ClustRand` function to evaluate the probability of success in the study and identify the number of completers (subjects who complete the study) required to achieve 80% power with the standard two-sided 5% significance level (or, equivalently, one-sided 2.5% significance level).

For more information on the statistical methodology used in this function, download the [technical manual](http://www.mediana.us/MedianaDesigner/ClustRand.pdf).

## Power calculations using `ClustRand` function

The `ClustRand` function enables simulation-based power calculations in a broad class of studies and trials with cluster-randomized designs. To perform power calculations, the MedianaDesigner package needs to be loaded first:

``` r
library(MedianaDesigner)
```

and an empty list of the function's parameters needs to be created:

``` r
parameters = list()
```

In what follows, the parameter list will be populated using the information summarized above. Beginning with the primary endpoint, the endpoint's type needs to be specified:

``` r
parameters$endpoint_type = "Binary"
```

The study's primary hypothesis states that the proportion of negative malaria tests is expected to be reduced in the treatment arm compared to the control arm and thus the `direction` parameter needs to be set to "Lower", i.e.,

``` r
parameters$direction = "Lower"
```

The specifications for the expected proportions of negative malaria tests in the two study arms, i.e., 60% in the control arm and 40% in the treatment arm, are set up as follows:

``` r
parameters$control_rate = 0.6
parameters$treatment_rate = 0.4

```

The data will be collected from 24 clusters (drug stores) and the clusters will be randomly assigned to the two study arms.  It is anticipated that 960 subjects will complete the study in each arm, i.e., 

``` r
parameters$sample_size = c(960, 960)

```

This implies a common cluster size of 80 subjects. To define the distribution of subjects across the clusters, the following two vectors need to be set up:

``` r
parameters$control_cluster_size = rep(80, 12)
parameters$treatment_cluster_size = rep(80, 12)
```

According to this specification, there will be 12 clusters with 80 subjects each in the control arm as well as the treatment arm. This setup corresponds to a fixed clustering scheme:

``` r
parameters$cluster_scheme = "Fixed"
```

This is the most commonly used setting in cluster-randomized trials. The `ClustRand` function also supports settings with random cluster sizes. This setting is often considered in the context of sensitivity assessments, see below.

A common intracluster correlation coefficient of 0.1 will be assumed in the two study arms:

``` r
parameters$control_icc = 0.1
parameters$treatment_icc = 0.1

```

Finally, the methodology for analyzing correlated outcomes within each cluster needs to be chosen by the user. The `ClustRand` function supports the GEE (generalized estimating equations) method and GLMEM (generalized linear mixed effects model) method. For example, the GEE method can be requested as follows:

``` r
parameters$method_type = "GEE"
```

The comparison of the proportions of negative malaria tests in the control and treatment arms will be carried out using a one-sided 2.5% significance level, i.e.,

``` r
parameters$alpha = 0.025
```

and power calculations will be performed using 1,000 simulation runs:

``` r
parameters$nsims = 1000
```

It is important to note that the number of simulation runs can be easily increased to 10,000 runs for the GEE method. However, the current implementation of the GLMEM method is much less efficient than that for the GEE method and will likely result in much longer simulation run times. For this reason, it is highly recommended to take advantage of parallel calculations supported by the `ClustRand` function. The user can specify the number of cores:

``` r
parameters$ncores = 4
```

and the simulations will be run in parallel across these cores and then seamlessly combined at the end.   

In general, the seed for the random number generation algorithm could be explicitly specified, e.g., 

``` r
parameters$random_seed = 50219
```

If the seed is not specified, the default value (49283) will be used in this function.

Another optional parameter is `descriptive_statistics`. If this option is enabled, i.e.,

``` r
parameters$descriptive_statistics = TRUE
``` 

key descriptive statistics, such as the proportion of negative malaria tests in each study arm, will be computed and saved for each simulation run. By default, the function does not compute the descriptive statistics.

Now that the required parameters have been defined, the `parameters` list needs to be passed to the `ClustRand` function to run simulations:

``` r
results = ClustRand(parameters)
```

The simulation results will be saved in the `results` object and, to create a simulation report with a summary of power calculations, this object is passed to the `GenerateReport` function:

``` r
GenerateReport(results, 
               "CaseStudyH1.docx")
```

The resulting simulation report (Microsoft Word document) will be saved in the current working directory. 

The report presents a summary of the design parameters saved in the `parameters` list and a single table (Table 8) with power calculation results. An important feature of the analysis method used in this case study (GEE) is that the standard version of this method is rather unreliable when the number of clusters is small, e.g., 24 clusters, and is known to lead to inflated Type I error rates. Alternative formulations of the GEE method, known as bias corrections, are recommended for settings with smaller cluster counts. In this case the assumed sample size and cluster counts guarantee 80% power for both bias-corrected analysis methods (Kauermann-Carroll correction and Mancl-DeRouen correction).

## Sensitivity assessments using `ClustRand` function

As stated in the [technical manual](http://www.mediana.us/MedianaDesigner/ClustRand.pdf), it is recommended to perform sensitivity assessments to determine the impact of key parameters on the probability of success in the study and ultimately ensure a robust trial design. The sensitivity assessments are often conducted with respect to the following factors:

* ICC misspecification.
* Cluster size variability.
* Small number of clusters.

As an example, the user could examine the impact of changes in cluster sizes on the probability of success in the study. To accomplish this, a random clustering scheme needs to be enabled:

``` r
parameters$cluster_scheme = "Random"
```

In this case the number of clusters in each study arm is fixed but the cluster sizes are random. The cluster sizes are generated from a generalized Bernoulli distribution with pre-defined sets of relative cluster sizes. For example, the following specification in the control arm

``` r
parameters$control_cluster_proportion = rep(1 / 12, 12)
```

defines a vector of relative cluster sizes. On average, the size of each of the 12 clusters is 960 / 12 = 80 subjects.

A vector of relative cluster sizes in the treatment arm can be specified in a similar way:

``` r
parameters$control_treatment_proportion = rep(1 / 12, 12)
```

Power calculations for this case study can now be run using the random clustering scheme and the sample size could be selected to ensure 80% power under this scheme.

## Code and simulation report

The code and simulation report can be downloaded from Mediana's web site:

* [R code](http://www.mediana.us/MedianaDesigner/CaseStudyH1.r).
* [Simulation report](http://www.mediana.us/MedianaDesigner/CaseStudyH1.docx).

## References

Ansah et al. The impact of providing rapid diagnostic malaria tests on fever management in the private retail sector in Ghana: A cluster randomized trial. British Medical Journal. 2015; 350:h1019. DOI: 10.1136/bmj.h1019.

# Online manual

Back to the [online manual](https://medianasoft.github.io/MedianaDesigner).

