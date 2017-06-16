---
title: Notes of Psyc 545A
categories: 
  - Course
tag:
  - Notes
---

## Sep 10
### Content
> 1 Background to correlation (a bivariate concept): Galton $\rightarrow$ Pearson
> 2 Uses of correlation in behavioral research
> 3 Terms and concepts in correlation
> 4 Precise statistical meaning of correlation

### Background
### The tools that Galton had
Central Tendancy 
> mean
> norm
> normal curve

Variability
> semi-interquartile range
> "Probable error"

Galton Q-score[^Latex]
$Q=\frac{X-Median}{semi-interquatile}$

|   $\quad$       | father   |  Son  | Q-score | Q-score |
| --------   | -----:  | :----:  |
| Pair        | X   |  Y  |  X   |  Y  |
| 1     | 63 | 69 | -1.5 | 0 |
| 2     | 64 | 65 | -1.25 | -1.0 |
| 3     | 65 | 64 | -1.0 | -1.25 |
| 4     | 67 | 63 | -0.5 | -1.50 |
| 5     | 69 | 67 | 0 | -0.5 |
| 6     | 69 | 75 | 0 | 1.5 |
| 7     | 71 | 71 | 0.5 | 0.5 |
| 8     | 73 | 69 | 1.0 | 0 |
| 9     | 74 | 73 | 1.25 | 1.0 |
| 10    | 75 | 74 | 1.50 | 1.25 |
$\bar{X}=69$ $\bar{Y}=69$
$S_x=4.64$ $S_y=4.64$
$Q_x=4.0$ $Q_y=4.0$

> Regression towards the mean

$Y'_Q=rX_Q$

#### Pearson
> * 1893 - standard deviation
> * Mean+SD $\rightarrow$ $r_{xy}$ -- Pearson product-moment correlation coefficient
> * less sampling fluctuation than Galton's meansure

### Uses of Correlation
> * Counterproductivity
> * Prediction

### Terms and Concepts
> * Bivariate Frequency Distribution (n x 2 data matrix)
> * Scatplot
> * least squares regression line of Y on X

### Statistic Meaning of Correlation
> * (Mean) Deviation Score: $X-\bar{X}$ (0 mean)
> * Standard Score: $\mathcal{z}-score= \frac{X-\bar{x}}{SD}$ (0 mean, 1 SD)
> * Product moment: $\sum_{i=1}^n \mathcal{z_x}\mathcal{z_y}$

#### Cauchy-Schwarg Inequality
$\sum_{i=1}^n \mathcal{z_x}\mathcal{z_y} \leq \sum_{i=1}^n \mathcal{z_x}^2=n-1$
$\sum_{i=1}^n \mathcal{z_x}\mathcal{z_y} \leq \sum_{i=1}^n\mathcal{z_y}^2=n-1$

$r_{xy}=\frac{\sum_{i=1}^n \mathcal{z_x}\mathcal{z_y}}{n-1}$

#### Covariance (unstandardized correlation coefficient)
$S_{xy}=\frac{\sum_{i=1}^n x_i y_i}{n-1}$
$r_{xy}=\frac{S_{xy}}{S_x S_y}$


## Sep 17
> * Some observations on interpretation of the magnitude of r
> * Theorems of the variance of sums and differences
> * Feature and phenomena that affect empirical correlations or how the true desired strength of relationship between X and Y can be distorted on obscured
(a) nonlinearity
(b) restriction on range
(c) dissimilarity of marginal X and Y distribution
(d) presence of disparate subgroups of subjects
(e) unreliability of measurement of the X and Y variables

1. Coefficient of Determination: $r^2$
represent the magnitude of correlation

2. Variable X and Y
    1. sum: X + Y
    2. Difference: X - Y

3. 1. $S_{X+Y}^2=S_X^2+S_Y^2+2S_{XY}$
$S_{X+Y}^2=S_X^2+S_Y^2+2r_{XY}S_XS_Y$
    2. $S_{X-Y}^2=S_X^2+S_Y^2-2S_{XY}$
$S_{X-Y}^2=S_X^2+S_Y^2-2r_{XY}S_XS_Y$
    3. $S_{XY}=r_{XY}S_XS_Y$


## Sep 22
### Factor effect, the magnitude of sample correlation coefficient (continued)
(a). disparate subgroups in the sample
(b). unreliable X, Y variables

### "Special" kinds of correlation (including one or more non-interval scale variables)
(a). ordinal variable, $r_{ranks}$
(b). one variable (x) dichotomous
    > * x a true (point) dichotomy
    > * x a artificially-imposed dichotomy

(c). both variable dichotomous
    > * x, y true dichotomous
    > * x, y artificially imposed dichotomies
    
#### Disparate subgroups
Big difference in subgroups -> "Between groups" correlation

What we are interested in is "within group" correlation

Solution: 
> * Group (gender) difference analysis: if there is significant difference of mean between subgroups
> * mean deviate

#### Unreliable variables
$X=T+E$,
where $X$ is the obtained score, $T$ is the true score, $E$ is error score.

> * $r_{T,E}=0$ -> $S_{X+Y}=S_X^2+S_Y^2$

Generic Definition of Reliability:
$r_{tt}=\frac{S_T^2}{S_X^2}$

Some practical ways to get the reliability:
> * alpha?
> * test retest
> * ultimate forms

Relation between reliablity and correlation
$r_{max}=\sqrt{r_{tt,x}r_{tt,y}}$

## Sep 24
> * Correlations involving non-interval scale variables
    1. X and Y ordinal-scale: $r_{ranks}$
    2. X dichotomous Y interval scale
        (a). X a true point dichotomy: $r_{pb}$ (point biserial)
        (b). X an artificially-imposed dichotomy: $r_{bis}$
    3. Both X and Y dichotomous
        (a). both point dicotomies
        (b). both artificially-imposed dichotomies
    4. [other more-recent developments](http://link.springer.com/article/10.1007%2fbf02889768)
    
### X dichotomous, Y interval scale
X = true point dichotomy
Y = interval-scale variable
Point-Biserial: $r_{pb}=\frac{\bar{Y_1}-\bar{Y_0}}{S_Y}\sqrt{\frac{n_0n_1}{n(n-1)}}$

X = artificial-imposed dichotomy
Y = 
$b_{bis}=\frac{\bar{Y_1}-\bar{Y_0}}{S_Y}\frac{n_1n_0}{un^2}$
where $u$ is height (ordinal) of the standard normal distribution at the point of the dichotomy.

### True point dichotomies
X=0, Y=1: A
X=1, Y=1: B
X=0, Y=0: C
X=1, Y=1: D
$r_{\phi}=\frac{BC-AD}{\sqrt{(A+B)(C+D)(A+C)(B+D)}}$

## Sep 29
> * Tetrachoric and other special correlations
> * Regression-Prediction Y from X
    (a). standard-scare prediction equation
    (b). raw-score prediction equation
    (c). entities and assumptions in prediction
    (d). solving prediction problems
    
### Dichotomy artificially imposed (underlying normal distribution on X)
interval-scale Y variable
Tetrachoric correlation, $r_{tet}=\frac{BC-AD}{u_xu_yn^2}$
$|r_\Phi|\leq|r_{tet}|$

### Other special correlations
$r_{polyserial}$:
X=polytomous
Y=interval scale
$r_{polychoric r}$:
X=polytomous
Y=polytomous

### Regression-Prediction Y from X
standard-score prediction equation
$\hat{z_y}=r_{xy}z_x$
$z_y=r_{xy}z_x+e$

### Raw-score prediction equation
$Z_{X_{i}=\frac{X_i-\bar{X}}{S_X}}$
$Z_{Y_{i}=\frac{Y_i-\bar{Y}}{S_Y}}$
$\frac{\hat{Y}-\bar{Y}}{S_Y}=r_{XY}\frac{X-\bar{X}}{S_X}$
$\hat{Y}-\bar{Y}=r_{XY}\frac{S_Y}{S_X}(X-\bar{X})$
$\hat{Y}=r_{XY}\frac{S_Y}{S_X}(X-\bar{X})+\bar{Y}$
$\hat{Y}=b_{Y\cdot X}(X-\bar{X})+\bar{Y}$
$\hat{Y}=b_{Y\cdot X}X+\bar{Y}-b_{Y\cdot X}\bar{X}=b_{Y\cdot X}X+C$
$Y=b_{Y\cdot X}X+C+e$
where $b_{Y\cdot X}$ is called regression coefficient, $b_{Y\cdot X}=r_{XY}\frac{S_Y}{S_X}$

$Y-\bar{Y}=e$: "residual" about the regression line
$\bar{e}=0$
$Var(e)=\sum_{i=1}^n\frac{(Y_i-\bar{Y})}{n-1}$
$S_e^2=S_Y^2(1-r_{XY})=S_{Y\cdot X}^2$
$S_e=S_Y\sqrt{1-r_{XY}}$
$\hat{\sigma}_{Y\cdot X}=S_{Y\cdot X}\frac{\sqrt{n-1}}{\sqrt{n-2}}$

Assumptions:
> * Linearity
> * bivariate normality (marginal and conditional distributions are normal, looks like a hat)
> * Homoscedasticity (equal variability)

## Oct 1
> * Solving prediction problems
> * The bivariate regression model
    (a). key entities
    (b). relationships among the entities
    (c). residual scores
    (d). use of residual scores to refine correlational result
        (i). partial correlation
        (ii). part (semi-partial) correlation

### Interpretation of predicted score
The predicted score $\hat{Y}$ is the mean of the conditioned distribution given $X$.

The standard deviation of the conditioned distribution given $X$ is 
$S_{Y\cdot X}=S_Y\sqrt{1-r_{XY}^2}$

The standardized Y in the unconditioned distribution is $z_Y=\frac{Y-\hat{Y}}{S_{Y\cdot X}}$

###
$S_{Y}^2=S_{\hat{Y}}^2+S_e^2+2Cov(\hat{Y}, e)$ $,\quad( S_{X+Y}^2=S_X^2+S_Y^2+2Cov(X, Y))$
$S_{Y}^2=S_{\hat{Y}}^2+S_e^2+2Cov(X, e)$$,\quad( \hat{Y}=b_{Y\cdot X}X+C)$
$S_{Y}^2=S_{\hat{Y}}^2+S_e^2$ $,\quad (Cov(X, e)=0)$
$S_{\hat{Y}}^2=Var(b_{Y\cdot X}X+C)=Var(b_{Y\cdot X}X)+Var(C)$
$S_{\hat{Y}}^2=b_{Y\cdot X}^2Var(X)$ $,\quad (Cov(C)=0)$
$S_{\hat{Y}}^2=b_{Y\cdot X}^2S_X^2=r_{XY}^2\frac{S_Y^2}{S_X^2}S_X^2=r_{XY}^2S_Y^2$
$r_{XY}^2=\frac{S_{\hat{Y}}^2}{S_Y^2}=1-\frac{S_{Y\cdot X}^2}{S_Y^2}$

## Oct 6
> * Use of residualized score to refine/adjust correlational results
    (a). partial correlation
    (b). part (semi-partial) correlation
> * Multiple regression analysis (MRA)
    (a). Brief digression: linear combination of variables;
    (b). The optimal linear combination of prediction variables (to maximize $r_{Y, \hat{Y}}$)
    (c). The multiple correlation coefficient, $R_{Y\cdot R?}$
    (d). Multiple regression weights

### Partial correlation
Pairwise correlated variables $A$, $B$, $C$.

The partial correlation $r_{AB\cdot C}$ of $A$ on $B$ is calculated using $A-\hat{A}$ and $B-\hat{B}$. Where $\hat{A}$ and $\hat{B}$ are predicted by variable $C$. This is call first-order partial correlation. Remove the effect of the third variable ($C$) on the variables of interest ($A$, $B$).

$r_{AB\cdot C}=\frac{r_{AB}-r_{AC}r_{BC}}{\sqrt{(1-r_{AC}^2)(1-r_{BC}^2)}}$

Second-order partial correlation: partial two factors out

### Part (semi-partial) correlation
Variables $A$, $B$, $C$. Correlation exists between $A$ and $B$, $B$ and $C$, but not $A$ and $C$.

The part correlation $r_{A(B\cdot C)}$ of $A$ on $B$ is calculated using $A$ and $B-\hat{B}$. Where $\hat{B}$ are predicted by variable $C$. Remove the effect of the third variable ($C$) on one of variables of interest ($B$).

The reason why we don't partial $A$ on $C$ is the lack of correlation.

$r_{A(B\cdot C)}=\frac{r_{AB}-r_{AC}r_{BC}}{\sqrt{(1-r_{BC}^2)}}$

### Multiple regression analysis
#### Linear combination of variables
$Y=\sum_{i=1}^m w_iX_i$

the weight a certain variable carries is equal to the SD. Typical, we transform all variables to the $z$-scores before doing weighted combination.

On predictor variables, $X_1$, $X_2$, $...$, $X_m$,
find $w_1$, $w_2$, $...$, $w_m$,
$\hat{Y}=\sum_{i=1}^m w_iX_i$,
maximize $r_{Y, \hat{Y}}$.

$\hat{Z_Y}=\sum_{i=1}^m w_iZ_{X_i}$

#### For 2 predictor variable, $m=2$
$r_{Y1}=0.45, r_{Y2}=0.35, r_{12}=0.4$
$R_{Y\cdot 12}=\sqrt{\frac{r_{Y1}^2+r_{Y2}^2-2r_{Y1}r_{Y2}r_{12}}{1-r_{12}^2}}$

## Oct 8
> * Multiple regression analysis
    (a). beta weight
    (b). raw-score weight
> * some entities used in understanding MRA result
> * Bias in $R_{Y\cdot 12...m}$
    (a). Wishart's result
    (b). population validity and population cross-validity
        (i). analytic methods
        (ii). empirical methods

### partial regression
$Y$: criterion
$X_1, X_2, ..., X_m$: predictors
$m$: number of predictors
$R_{Y\cdot 12...m}$: partial regression, the increase of $R_{Y\cdot 12...m}$ decreases as the number of predictors $m$ increases

**standardized regression weight**
$\hat{\beta}_1=\frac{r_{Y1}-r_{Y2}r_{12}}{1-r_{12}^2}$
$\hat{\beta}_2=\frac{r_{Y2}-r_{Y1}r_{12}}{1-r_{12}^2}$
$\hat{Z}_Y=\hat{\beta}_1Z_{X1}+\hat{\beta}_2Z_{X2}$
$Z_Y=\hat{\beta}_1Z_{X1}+\hat{\beta}_2Z_{X2}+e$
$r_{Z_Y\hat{Z}_Y}$ is maxmized

Also named **semi-partial correlation coefficient**: $\hat{\beta}_1 = r_{Y(1\cdot 2)}$

Bivariate case ($r_{12}=0$):
$\hat{Z}_Y=r_{XY}Z_X$

**raw score weights**
$b_1=\hat{\beta}_1\frac{s_Y}{s_{X1}}$
$b_2=\hat{\beta}_2\frac{s_Y}{s_{X2}}$
$C=\bar{Y}-b_1\bar{X}_1-b_2\bar{X}_2$: intercept 
$\hat{Y}=b_1X_1+b_2X_2+C$

weights applied to unstandadized values, no interpretations

### understanding MRA result
1. $R_{Y\cdot 12...m}^2=\sum_{i=1}^m\hat{\beta}_ir_{Yi}$
2. If you drop predictor 1, the $R_{Y\cdot 12...m}^2$ is gonna drop $r_{Y(12)}^2$. Likewise, if you drop predictor 2, the $R_{Y\cdot 12...m}^2$ is gonna drop $r_{Y(21)}^2$. A good measure of how important the predictor is. Another way is to look at the $\beta$ weights.

### Bias
MRA: captalize chance

$Wishart(1931)=E(R_{Y\cdot 12...m}^2|\rho^2=0)=\frac{m}{n-1}$
where $E$ is the expect value, $n$ is the sample size, and $m$ the number of predictors.

**population validity** ($R_{adj}$ or $_{adj}R$): what the multiple correlation coefficient would be if you take the whole population. Typically larger than $\hat{\rho}_c$.

**population cross-validity** ($\hat{\rho}_c$): You take the $\beta$ weights you developed from the development sample and apply it to other samples or population.

### Oct 13

> * Empirical methods of estimated population cross-validity
> * Reporting MRA result
> * Suppressor variables
> * Sequential MRA techniques
    (a). Stepwise MRA
    (b). Hierarchical MRA
> * How to take a multiple-choice exam

### 没有仔细听

### Suppressor variables
characterized by a lack of predictive validity $r_{XY}=0$ and high redundancy with other predictors, $r_{X_1X_2}$. It's extreamly rare.

### Sequential MRA techniques
**Stepwise MRA (forward solution)**
    > * adding the first variable (if any) that improves the model the most.
    > * what is the highest semi-partial correlation while the added predictors are held fixed, this should be the newly added variable.
    > * stops when none predictor improves the model. The result is a subset of the predictors.

## Oct 20
> Statistical Inference 1: Parametric Estimation
    (1). Motivation
    (2). Sampling distribution
    (3). Some definition of parameter estimation
    (4). The central limit theorem
    (5). Interval estimate of parameter
    (6). Properties of estimates
  
### Estimator & Parameter
estimator: $\bar{X}$ or $\hat{\mu}$, $S^2$, $S$, $r_{XY}$, $P$, $t$, $\hat{\beta}$. **Sample**
parameter: $\mu$, $\sigma^2$, $\sigma$, $\rho_{XY}$, $\pi$, $\theta$, $\beta$. the corresponding value in the **population**

### Sampling Distribution
Parent Population

Frequency Distribution

Sampling distribution: the distribution of some sample statistic. The sampling distribution of means has less variability that that of the parent population. The sampling distribution of SD is positively skewed.

**Sampling error**: $t_i-\theta$

**Expected value of a statistic**: E(t), mean of the sampling distribution of $t$, often equals to the parameter of the population, but not necessarily always. $E(\bar{X})=\mu$, $E(S^2)=\sigma^2$, $E(S)\neq \sigma$, $E(r)\neq p$

**variance of $t$**: $\sigma_t^2$, variance of sampling distribution of $t$.

**standard error of $t$**: $\sigma_t$, standard deviation of sampling distribution of $t$.

### Central Limit Theorem
as it applies to $\bar{X}$ as an estimator of $\mu$.

1. As $n$ increases the sampling distribution of $\bar{X}$ approaches normality regardless of the shape of the parent population frequency distribution
2. $\sigma_{\bar{X}}=\frac{\sigma}{n}$

$Pr[\frac{\bar{X}-\mu}{\sigma_{\bar{X}}}<-1.96]=0.025$
$Pr[\frac{\bar{X}-\mu}{\sigma_{\bar{X}}}>1.96]=0.025$
$Pr[\mu-1.96\sigma_{\bar{X}}<\bar{X}<\mu+1.96\sigma_{\bar{X}}]=0.95$
$Pr[-1.96\sigma_{\bar{X}}<\bar{X}-\mu<1.96\sigma_{\bar{X}}]=0.95$
$Pr[\bar{X}-1.96\sigma_{\bar{X}}<\mu<\bar{X}+1.96\sigma_{\bar{X}}]=0.95$
**more general form**
$Pr[\bar{X}-z_{1-\frac{\alpha}{2}}\sigma_{\bar{X}}<\mu<\bar{X}+z_{1-\frac{\alpha}{2}}\sigma_{\bar{X}}]=1-\alpha$
where $z_{1-\frac{\alpha}{2}}=100[1-\frac{\alpha}{2}]\%$ de? point of the standard normal distribution.
**interval estimate of a parameter**
$(1-\alpha)$ confidence interval for $\mu$ is given by $\bar{X}\pm z_{1-\frac{\alpha}{2}}\sigma_{\bar{X}}$: the probability that interval constructed this way $(\bar{X}- z_{1-\frac{\alpha}{2}}\sigma_{\bar{X}}, \bar{X}+ z_{1-\frac{\alpha}{2}}\sigma_{\bar{X}})$ will capture the parameter is $1-\alpha$.

## Oct 22
> * $(1-\alpha)$ CIs (confidence interval) for $\mu$
> * Properties of estimators
    (a). Unbiasedness
    (b). Consistency
    (c). Relative efficiency
> * Statistical Inference 2: Hypothesis testing
    (a). Types of hypotheses

### Properties of estimators
**Unbiasedness**: $t$ is an unbiased estimator of $\theta$ if $E(t)=\theta$.

**Consistency**: $Pr(|t-\theta|)\rightarrow0$ as $n\rightarrow \infty$

**Relative efficiency**: If $t_1$ and $t_2$ are both estimators of $\theta$, the relative efficiency of $t_1$ to $t_2$ is: $\frac{\sigma_{t_2}^2}{\sigma_{t_1}^2}$. The most important one.

### Scientific and Statistical Hypothesis
**Null Hypothesis $H_0$**: nothing is happening other than sampling error.

**Neyson-Pearson Hypothesis-test Logic**:
If the probability that we get the data we got when the Null Hypo is true is below a threshold, then we reject the Null Hypo.

## Oct 27
> * Brief review of hypothesis testing
> * Decision and errors in hypothesis testing
    (a). decision matrix
    (b). some examples
> * Conventions regarding reporting of statistical results
> * Some fallacies involving the null hypothesis significance test (the "NHST")

### Decision Matrix
decision matrix
![decision matrix](/assets/images/decision_matrix.png)

Diagnostic Testing
![diagnostic testing](/assets/images/diagnostic testing.png)

1. Sensitivity: $\frac{D}{B+D}$
2. Specificity: $\frac{A}{A+C}$
3. Positive predictive power: $\frac{D}{C+D}$
4. Negative predictive power: $\frac{A}{A+D}$
5. False positive rate: $\frac{C}{A+C}$
6. False negative rate: $\frac{B}{B+D}$

### Examples
$\alpha$: level of significance

## Oct. 29
> * Example of hypothesis test and power with test of difference between means
> * Convention regarding reporting of statistical result
> * Directional and non-directional hypothesis testing
> * several fallacies involving the NHST(null hypothesis statistic testing)
> * Single-sample inference with means


### Test of difference between means
$\sigma_{\hat{X}}=\frac{\sigma}{\sqrt{n}}$
$\sigma_{\hat{x_1}-\hat{x_2}}=\sqrt{\sigma^2(\frac{1}{n_1}+\frac{1}{n_2})}$, assumption: $\sigma_1^2=\sigma_2^2=\sigma^2$

$H_0: \mu_1 = \mu_2$, estimated by $X_1-X_2$
$H_0: \mu_1 - \mu_2 = 0$
$H_1: \mu_1 - \mu_2 \neq 0$
Linear combination of the unbiased pamameters is still unbiased.

### Conventions of reporting results
significance level $\alpha$: 
$p$-value: the empirical result, refelcts the probility of the data we got would have occured if the null is true.

1.
Rejection, $<.05, .01, .005, ..., .001$
Retaintion?, $> .05, .10, .25, .50$
2. Exact values: $p=.0357$(example)

### Directional and non-directional

### fallacies
1. inverse probability fallacy: given the null is true, the prob of getting the data is $P$; given the data you have, the prob of the null is true is $P$. These two are different.
2. attach too much importance to $p$-value. difference between statistical and practical(clinical) significance
3. .05 level isn't adjustable

##Nov 3

### Single-Sample Inference with Means
1. testing this $H_0$: $\mu = k$ or test $H_1$: $\mu \neq k$
2. $(1-\alpha)CI_0$ for $\mu$

Given $X \sim N(\mu, \sigma_{\bar{X}}^2)$
Linear transformation of $X$: $Z=\frac{\bar{X}-\mu}{\sigma_{\bar{X}}}$
Then, $Z \sim N(0, 1)$
$Pr[z_{1-\frac{\alpha}{2}} < Z < z_{1+\frac{z}{2}}]=1-\alpha$
$Pr[\bar{X}-z_{1-\frac{z}{2}}\sigma_{\bar{X}} <\mu < \bar{X}+z_{1-\frac{z}{2}}\sigma_{\bar{X}}]=1-\alpha$


In practice, $\sigma$ is unknow:

use estimate of $\sigma$: s
student's t-test: $\frac{\bar{X}-\mu}{s_{\bar{X}}} \sim t(0, \frac{df}{df-2})$. 0 mean, $\frac{df}{df-2}$ SD.

$Pr[\bar{X}-_{df}t_{1-\frac{\alpha}{2}}<\mu<\bar{X}+_{df}t_{1-\frac{\alpha}{2}}]<1-\alpha$

where $s_{\bar{X}}=\frac{s}{\sqrt{n}}$, because $s_{\bar{X}}$ is not a constant, this transformation is not normally distributeed but asymptotically normal, and not a linear transformation of $\bar{X}$. Main difference between t- and normal distribution is that the former has fat tails.


### Degrees of freedom
$s^2=\sum_{i=1}{n}\frac{(X_i-\mu)^2}{n}$
$s^2=\sum_{i=1}{n}\frac{(X_i-\bar{X})^2}{n-1}$

because even we know the population parameter $\mu$, the sample value $X_i$ is free to vary. thus $df=5$. And in the second equation, when $\bar{X}$ and $n-1$ samples are known, the last sample is fixed, thus $df=n-1$

### Two-Sample Tests of Means
$H_0: \mu_1=\mu_2$ or $\mu_1-\mu_2=0$

$\sigma_{\bar{X_1}-\bar{X_2}}=\sqrt{\sigma^2(\frac{1}{n_1}+\frac{1}{n_2})}$

$Z=\frac{(\bar{X_1}-\bar{X_2})-(\mu_1-\mu_2)}{\sigma_{\bar{X_1}-\bar{X_2}}}$

$\sigma$ is unknown, use $S$ as an estimate
student's t distribution: $\frac{(\bar{X_1}-\bar{X_2})-(\mu_1-\mu_2)}{S_{\bar{X_1}-\bar{X_2}}}\sim_{df}t_{1-\frac{\alpha}{2}}$

where $df=n_1+n_2-2$

##Nov 5
> * The t-test of difference between two means:
Features of the data:
1. Case 1
2. Case 2
3. Case 3
4. Assumptions underlying the t-test of two means

### Case 1
The most important features: Independent samples, sample size could be unequal, $n_1\neq n_2$, homegeneous variances assumed ($H_0:\sigma_1^2=\sigma_2^2$ is retained).

| Sample 1  | Sample 2  |
| :-------: | :------:  |
| $X_1$     | $X_2$     |
| $S_1^2$   | $S_2^2$   |
| $n_1$     | $n_2$     |

$S_1^2$ and $S_2^2$ are estimates of $\sigma^2$

$\hat{\sigma}^2=\frac{(n_1-1)S_1^2+(n_2-1)S_2^2}{n_1+n_2-2}$

$S_{\bar{X_1}-\bar{X_2}}=\sqrt{\hat{\sigma}^2(\frac{1}{n_1}+\frac{1}{n_2})}$

$t=\frac{\bar{X_1}-\bar{X_2}}{S_{\bar{X_1}-\bar{X_2}}}$

Test against the table-$t$ with $n_1+n_2-2$ $df$.

**Raw Effect Size**: $\bar{X_1}-\bar{X_2}$
**Standardized Effect Size**: $\hat{\bigtriangleup}=\frac{\bar{X_1}-\bar{X_2}}{\hat{\sigma}}$

### Case 2
Independent samples, $n_1=n_2=n$, no need for homogenous variance.

$S_{\bar{X_1}-\bar{X_2}}=\sqrt{\frac{S_1^2+S_2^2}{n}}$

**Standardized Effect Size**: $\hat{\bigtriangleup}=\frac{\bar{X_1}-\bar{X_2}}{\hat{\sigma}}=\frac{\bar{X_1}-\bar{X_2}}{\sqrt{\frac{S_1^2+S_2^2}{2}}}$

### Case 3
Dependent Samples

| subject pair  | Condition 1 | Condition 2 | Diff |
| :-------:     | :------:    | :------:    | :------:    |
| 1             | $X_{11}$    | $X_{12}$    |$X_{11}-X_{12}$|
| 2             | $X_{21}$    | $X_{22}$    |$X_{21}-X_{22}$|
| $\vdots$      | $\vdots$    | $\vdots$    |$\vdots$    |
| n             | $X_{n1}$    | $X_{n2}$    |$X_{n1}-X_{n2}$|

$S_{\bar{X_1}-\bar{X_2}}^2=S_{\bar{X_1}}^2+S_{\bar{X_2}}^2-2r_{12}S_{\bar{X_1}}S_{\bar{X_2}}$
$=\frac{S_1^2}{n}+\frac{S_2^2}{n}-2r_{12}\frac{S_1}{\sqrt{n}}\frac{S_2}{\sqrt{n}}$
$=\frac{S_1^2+S_2^2-2r_{12}S_1S_2}{n}$

**Standardized Effect Size**: $\hat{\bigtriangleup}=\frac{\bar{X_1}-\bar{X_2}}{\hat{\sigma}}=\frac{\bar{X_1}-\bar{X_2}}{\sqrt{\frac{S_1^2+S_2^2}{2}}}$

## Nov 10
> * Review of Case 3
> * Assumptions underlying the t-test of mean differences
    (a). The 3 assumptions
    (b). how we assess robustness
    (c). HOV in independent-samples, unequal-n case
        (i). two conditions
        (ii). assessing HOV in this case
> * Case 4: Welch's method

### Case 3 Example
$S_{\bar{D}}=S_{\bar{X_1}-\bar{X_2}}=\sqrt{\frac{S_1^2+S_2^2-2r_{12}S_1S_2}{n}}=\frac{S_D}{\sqrt{n}}$
$t=\frac{\bar{X_1}-\bar{X_2}}{S_{\bar{X_1}-\bar{X_2}}}=\frac{\bar{X_D}}{S_{\bar{D}}}$
where $D=X_1-X_2$

### Assumptions
1. Independence: Independent both within and between groups
2. Normality: the samples are drawn from normally distributed population.
3. Homogeneity of Variance (HOV): the variances (SD) of the populations from which the samples are drawn, are the same.

Nominal $\alpha$: 0.05
Actual $\alpha$: what the actually probability of making a type I error. 0.04-0.06.

#### Heterogeneous variance, unequal-n

| $\quad$    | 1   |  2  |
| --------   | -----: | :----:  |
| n          | 10     |   50    |
| $\sigma^2$ |   20   |   100   |

positive condition
conservative based in t-test
Nominal $\alpha$: 0.05
Actual $\alpha$: 0.01.

| $\quad$    | 1   |  2  |
| --------   | -----: | :----: |
| n          | 10     |   50   |
| $\sigma^2$ |   100  |   20   |

negative condition
Liberal bias
Nominal $\alpha$: 0.05
Actual $\alpha$: 0.10-0.15

### HOV
$H_0: \sigma_1^2=\sigma_2^2$
1. Traditional test: $F=\frac{S_1^2}{S_2^2}$, referenced to central F-distribution (2-parameter distribution, $df_1 = n_1-1$, $df_2(row) = n_2-1$, put larger over smaller)

## Nov 12

> * Assessing HOV with independent samples
    (a). the traditional test (F-test)
    (b). newer test
    (c). bias in tests on variances
    (d). Accuracy
> * Case 4 - the welch procedure

### Traditional test (F-test)
|$\quad$| group 1    | group 2   |
|:------:| :------:   | :----:  |
|| 10     |   8   |
|| 12     |   8   |
|| 14     |   9   |
|| 16     |   9   |
||        |   9   |
||        |   9   |
||        |   10  |
||        |   10  |
|$n$|   4    |   8  |
|$\bar{X}$|  13    |   9  |
|$S^2$|  6.6666    |   .5714  |

$F=\frac{6.6666}{.5714}=11.666$ larger over smaller
$df: 3, 7$

### Newer tests
#### Levene test
Take absolute values about the mean: $|X_{ij}-\bar{X_j}|=Y_{ij}$. Not a very good test.
|$\quad$| group 1    | group 2   |
|:------:| :------:   | :----:  |
|| 3     |   1   |
|| 1     |   1   |
|| 1     |   0   |
|| 3     |   0   |
||        |   0   |
||        |   0   |
||        |   1  |
||        |   1  |
|$n$|   4    |   8  |
|$\bar{X}$|  2    |   0.5  |
|$S$|  1.1547    |   .5345|
|$S^2$|  1.3333| .2857|

$t=\frac{2.0-0.5}{\sqrt{\frac{3*1.333+7*.2857}{4+8-2}}(\frac{1}{4}+\frac{1}{8})}=3.162, P=0.0101$

$F(3, 7)=\frac{1.3333}{.2857}=4.667, P=0.0857$

**Brown-Forsyth**: recommended
Take the absolute values about the median:  $|X_{ij}-Md_j|=Y_{ij}$

**O'Brien**
### Case 4 (Welch' method)
Independent samples, $n_1\neq n_2$, HOV rejected.

|$\quad$| group 1    | group 2   |
|:------:| :------:   | :----:  |
|$\bar{X}$|  25    |   20  |
|$S$|  8.5    |   2|
|$S^2$|  15| 5|

Assume $F(14, 4)=18.06, P<0.5, P=.0064$

$t'=\frac{\bar{X_1}-\bar{X_2}}{\sqrt{S_{X_1}^2+S_{X_2}^2}}=\frac{\bar{X_1}-\bar{X_2}}{\sqrt{\frac{S_1^2}{n_1}+\frac{S_2^2}{n_2}}}$

$df'=\frac{(\frac{S_1^2}{n_1}+\frac{S_1^2}{n_2})^2}{\frac{(\frac{S_1^2}{n_1})^2}{n_1+1}+\frac{(\frac{S_1^2}{n_2})^2}{n_2+1}}-2$

## Nov 17
> * Estimation of Effect size in Research comparing two means
> * Raw Effect Size
    (a). Definition and uses
    (b). (1-$\alpha$) CIs for parameter raw effect size
> * Standardized Effect Size
    (a). Definition and uses
    (b). (1-$\alpha$) CIs for parameter standardized effect size

### Raw effect size
$\bigtriangleup_r=\mu_1-\mu_2$
estimated by 
$\hat{\bigtriangleup}_r=\bar{X}_1-\bar{X}_2$

$t_{obt}=\frac{\bar{X}_1-\bar{X}_2}{S_{\bar{X}_1-\bar{X}_2}}$

$(1-\alpha)$ CI for $\bigtriangleup_r$: $(\bar{X}_1-\bar{X}_2\pm t_{df}S_{\bar{X}_1-\bar{X}_2})$
where $t_{df}$ is "tabled" $t$ with $df$

#### An example
$\bar{X}_1=45, S_1^2=100, n_1=10$
$\bar{X}_2=55, S_2^2=150, n_2=20$

**Two-sided CI**

**One-sided CI**: "Credibility Value"
$\hat{\bigtriangleup}_r=10$

One-sided CI for $\hat{\bigtriangleup}_r$: $\hat{\bigtriangleup}_r+_{df}t_{1-\alpha}S_{\bar{X}_1-\bar{X}_2}$

### Standardized Effect Size
$\bigtriangleup=\frac{\mu_1-\mu_2}{\sigma}$
$\hat{\bigtriangleup}=\frac{\bar{X}_1-\bar{X}_2}{\hat{\sigma}}=\frac{\bar{X}_1-\bar{X}_2}{\sqrt{\frac{S_1^2+S_2^2}{2}}}$ (last term: Case 4)

Distribution of $\frac{\bar{X}_1-\bar{X}_2}{S_{\bar{X}_1-\bar{X}_2}}$

#### Case 1
$\hat{\bigtriangleup}=\frac{\bar{X}_1-\bar{X}_2}{\hat{\sigma}}$

$t=\frac{\bar{X}_1-\bar{X}_2}{\sqrt{\hat{\sigma}^2(\frac{1}{n_1}+\frac{1}{n_2})}}$ (case 1: $S_{\bar{X}_1-\bar{X}_2}=\sqrt{\hat{\sigma}^2(\frac{1}{n_1}+\frac{1}{n_2})}$)
$=\frac{\bar{X}_1-\bar{X}_2}{\hat{\sigma}\sqrt{(\frac{1}{n_1}+\frac{1}{n_2})}}$
$=\frac{\hat{\bigtriangleup}}{\sqrt{(\frac{1}{n_1}+\frac{1}{n_2})}}$
$=\frac{\hat{\bigtriangleup}}{\sqrt{\frac{n_1+n_2}{n_1n_2}}}$

$\hat{\bigtriangleup}=t_{obt}\sqrt{\frac{n_1+n_2}{n_1n_2}}$
$=\delta M_1$

#### An Example
$\bar{X}_1=62.5, S_1^2=110, n_1=30$
$\bar{X}_2=56.4, S_2^2=70, n_2=20$
$t(48)=2.18, P=.0342,$ non-directional; $\hat{\bigtriangleup}=.63$; 0.95 CI for $\bigtriangleup:(.05, 1.21)$
$\hat{\delta}_L=.161544, \hat{\delta}_U=4.176828$
$\hat{\bigtriangleup}_L=\hat{\delta}_LM_1, \hat{\bigtriangleup}_U=\hat{\delta}_UM_1$

## Nov 19
> * Inference with Correlation Coefficient
I. Testing $H_0: \rho=0$
    (a). Pearson r-family coefficient
    (b). Non-Pearson-r bivariate coefficient
    (c). Multivariate coefficient
    (d). Reporting correlation result

### Inference with Correlation Coefficient
$H_0: \rho_{xy}=0$

$\sigma_r=\frac{1-\rho^2}{\sqrt{n-1}}$

1. $Z=\frac{r_{xy}}{\sigma_r}=\frac{r_{xy}}{\frac{1}{\sqrt{n-1}}}$
2. $t=\frac{r_{xy}}{\sqrt{\frac{1-r_{xy}^2}{n-2}}}$; $df=n-2$
3. $(1-\alpha)$ CI for $\rho$; Normal distribution $df:(n-3)$

#### Pearson-r Family
1. $r_{XY}$ (Pearson $r_{XY}$)
2. $r_{Ranks}$ (Spearman)
3. $r_{pb}$
4. $r_{\phi}$

#### Non-Pearson-r Correlations
1. $r_{bis}$
2. $r_{Tet}$

#### Multiple, Part and Partial Correlations