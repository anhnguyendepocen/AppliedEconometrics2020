---
title: 'Program Evaluation (Causal Inference) 2: Regression Discontinuity Design'
author: "Instructor: Yuta Toyama"
date: "Last updated: 2020-03-30"
fig_width: 6 
fig_height: 4 
output: 
  html_document:
    theme: lumen
    highlight: haddock 
    #code_folding: show
    toc: yes
    number_sections: true
    toc_depth: 2
    toc_float: true
    keep_md: true
    df_print: paged
  beamer_presentation:
    theme: "Madrid"
    colortheme: "lily"
    slide_level: 2
    includes:
      in_header: "../beamer_header.tex"
    df_print: tibble
---

# Introduction

## Introduction

-   Regression Discontinuity Design

    -   Exploit the discontinuous change in treatment status to estimate
        the causal effect.

    -   Example:

        -   Threshold of test score for college admission

        -   Eligibility of policy due to age.

        -   Geographic boundary of two regions.

-   Pros: Strong internal validity

    -   Assumption for identification is weak.

-   Cons: Very little external validity

    -   What we estimate is the effect on people at the boundary.

## Idea in Figure

## Reference

-   Angrist and Pischke "Mostly harmless econometrics" Chapter 6

-   R packages:
    [https://sites.google.com/site/rdpackages/rdrobust ](https://sites.google.com/site/rdpackages/rdrobust )

# Framework

## Framework

-   $Y_{i}$: observed outcome for person $i$

-   Define *potential outcomes*

    -   $Y_{1i}$: outcome for $i$ when she is treated (treatment group)

    -   $Y_{0i}$: outcome for $i$ when she is not treated (control
        group)

-   $D_{i}$: treatment status is deterministically determined (sharp RD
    design) $$D_{i}=\mathbf{1}\{W_{i}\geq\bar{W}\}$$

    -   $W_{i}$: running variable (forcing variable).

    -   Probabilistic assignment is allowed (fuzzy RD design)

## Example: Incumbent Advantage

-   Consider the two-candidate elections

    -   $D_{i}$: dummy for incumbent in the election

    -   $Y_{i}$: whether the candidate win in the election

    -   $W_{i}:$ the vote share in the previous election.

-   The incumbent status is defined as
    $$D_{i}=\mathbf{1}\{W_{i}\geq0.5\}$$

-   Idea of RD:

    -   Suppose that you won with 51%.

    -   You are similar to the guy who lose at 49% (main assumption of
        RD).

    -   If you focus on these people, $D_{i}$ is as if it were randomly
        assigned.

## Framework cont.d

-   Note that $D_{i}=\mathbf{1}\{W_{i}\geq\bar{W}\}$ implies the
    unconfoundedness $$(Y_{1i},Y_{0i})\perp D_{i}|W_{i}$$

-   But the overlap assumption does not hold
    $$P(D_{i}=1|W_{i}=w)=\begin{cases}
    1 & if\ w\geq\bar{W}\\
    0 & if\ w<\bar{W}
    \end{cases}$$

-   To compare people with and without treatment, we need to rely on
    some sort of extrapolation around the threshold.

Linear approach

-   Suppose for a moment that $$\begin{aligned}
    Y_{1i} & =\rho+Y_{0i}\\
    E[Y_{0i}|W_{i}=w] & =\alpha_{0}+\beta_{0}w\end{aligned}$$

-   This leads to a regression
    $$Y_{i}=\alpha+\beta W_{i}+\rho D_{i}+\eta_{i}$$

    -   $\rho$ is the causal effect.

-   This approach relies on linear extrapolation. May not be good.

    -   What if $E[Y_{0i}|W_{i}=w]$ is nonlinear?

---

![image](figure_table/Figure6_1_1.png)

---

## A more general approach

-   Allowing for nonlinear effect of the running variable $W_{i}$
    $$Y_{i}=f(W_{i})+\rho\mathbf{1}\{W_{i}\geq\bar{W}\}+\eta_{i}$$

-   A function $f(\cdot)$ might be a $p$th order polynomial.
    $$f(W_{i})=\beta_{1}W_{i}+\beta_{2}W_{i}^{2}+\cdots+\beta_{p}W_{i}^{p}$$

    -   nonparametric approach later.

Implementation in Regression

-   Consider $$\begin{aligned}
    E[Y_{0i}|W_{i}=w] & =f_{0}(W_{i}-\bar{W})\\
    E[Y_{1i}|W_{i}=w] & =\rho+f_{1}(W_{i}-\bar{W})\end{aligned}$$

    -   $\tilde{W}_{i}=W_{i}-\bar{W}$ is a normalization.

-   Then the regression equation is (See page 255 in Angrist and
    Pischke) $$\begin{aligned}
    Y_{i} & =\alpha+\beta_{01}\tilde{W_{i}}+\cdots+\beta_{0p}\tilde{W}_{i}^{p}\\
     & +\rho D_{i}+\beta_{1}^{*}D_{i}\tilde{W}_{i}+\cdots+\beta_{p}^{*}D_{i}\tilde{W}_{i}^{p}+\eta_{i}\end{aligned}$$

    -   $\rho$ is the causal effect.

-   When running regression, need to focus on the sample around
    threshold.

    -   How close the sample should be to the threshold can be taken
        care by statistical procedure.

# Example

## From Mastering Metrics Sec 4.1: Effects of the minimum age drinking law

![image](figure_table/MMfig41.pdf)

---

![image](figure_table/MMfig42.pdf)

---

![image](figure_table/MMfig44.pdf)

---

![image](figure_table/MMfig45.pdf)

---

# Formal Analysis

## Formal Identification Analysis

-   Key: continuity assumptions: Both $E[Y_{1i}|W_{i}=w]$ and
    $E[Y_{0i}|W_{i}=w]$ are continuous at the threshold $w=\bar{W}$.

    -   This is not directly testable assumption (because we cannot
        observe $Y_{1i}$ below the threshold).

    -   Will discuss several validating approaches.

-   To see how this works, notice that $$\begin{aligned}
    E[Y_{i}|W_{i}=w]= & E[Y_{0i}|W_{i}=w]\\
     & +\mathbf{1}\{w\geq\bar{W}\}\left(E[Y_{1i}|W_{i}=w]-E[Y_{0i}|W_{i}=w]\right)\end{aligned}$$

-   Taking the limit of $w$ to $\bar{W}$ from above and below
    $$\begin{aligned}
    \lim_{w\uparrow\bar{W}}E[Y_{i}|W_{i} & =w]=\lim_{w\uparrow\bar{W}}E[Y_{0i}|W_{i}=w]=E[Y_{0i}|W_{i}=\bar{W}]\\
    \lim_{w\downarrow\bar{W}}E[Y_{i}|W_{i} & =w]=\lim_{w\downarrow\bar{W}}E[Y_{1i}|W_{i}=w]=E[Y_{1i}|W_{i}=\bar{W}]\end{aligned}$$

    -   Notice that we use continuity in the second equalities!

```{=html}
<!-- -->
```
-   Remember that $$\begin{aligned}
    \lim_{w\uparrow\bar{W}}E[Y_{i}|W_{i} & =w]=\lim_{w\uparrow\bar{W}}E[Y_{0i}|W_{i}=w]=E[Y_{0i}|W_{i}=\bar{W}]\\
    \lim_{w\downarrow\bar{W}}E[Y_{i}|W_{i} & =w]=\lim_{w\downarrow\bar{W}}E[Y_{1i}|W_{i}=w]=E[Y_{1i}|W_{i}=\bar{W}]\end{aligned}$$

-   So, we have
    $$E[Y_{1i}-Y_{0i}|W_{i}=\bar{W}]=\lim_{w\downarrow\bar{W}}E[Y_{i}|W_{i}=w]-\lim_{w\uparrow\bar{W}}E[Y_{i}|W_{i}=w]$$

    -   LHS: Average treatment effect at the threshold

    -   RHS: We can observe from the data.

        -   Conditional expectation near the threshold.

## Nonparametric Implementation

-   Too much details for this course. I will skip.

-   The package 'rddrobust" will take care for all the details.

# Validation of Assumptions

## Validation of Assumptions

-   The key assumptions : Both $E[Y_{1i}|W_{i}=w]$ and
    $E[Y_{0i}|W_{i}=w]$ are continuous at the threshold $w=\bar{W}$.

-   This is not directly testable because we cannot observe $Y_{1i}$
    below the threshold.

-   There are two common approaches that support this assumption:

    1.  Covariate test

    2.  Density test (no bunching in the running variable).

## Covariate Test

-   The underlying idea of RDD: Comparing outcomes right above and right
    below $\bar{W}$ provides a comparison of treated and control agents
    who are similar due to the assumed continuity in conditional
    distributions

-   If this is a valid comparison, then we would expect that covariates
    $X$ also change smoothly as we pass through the threshold.

    -   Run the RDD on the covariate $X$.

-   If we found the discontinuity, it suggests that the conditional
    expectation of $Y$ on $W$ may not be continuous either.

-   If $X$ has a direct effect on $Y$, the discontinuity in $E[Y_{i}|W]$
    at $\bar{W}$ will confound the treatment effect.

-   Example:

    -   $Y$ hours worked,

    -   $D$: older-than-65 discounts,

    -   $W$: age, $X$: social security benefit (non-work income)

## Density Test, or No Bunching

-   There is also a concern about manipulation if agents know about the
    institutional details

    -   For example, if schools scoring lower than w = 50 on
        standardized tests get labeled as dysfunctional, we might expect
        a lot of schools to be right above 50

-   In this case, we observe a bunching around the threshold.

    -   Agents are "manipulating" treatment assignment around the
        threshold.

    -   Density of $W_{i}$ is discontinuous at $\bar{W}$

-   We would expect that $E[Y_{1i}|W_{i}=w]$ would be also
    discontinuous.

-   McCrary (2008) suggests a test of the null hypothesis that the
    density of $W_{i}$ is continuous at $\bar{W}$.

-   Note: Bunching itself is an interesting economic phenomenon. It can
    be used to analyze a different question.

## Ito and Sallee (2018, REStat)

![image](figure_table/ItoSallee.png)

## Bunching Estimator

![image](figure_table/ItoSallee2.png)

-   Policy causes excess bunching at 1520.