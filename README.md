# BostonHousingDataSet_R_Analysis
First I checked there were any objects in the workspace

ls()
Eventually removed existing objects
rm(list=ls())

Uploaded the Boston dataset, that is inside library (or package) MASS.
library(MASS)
data(Boston)

The dataset contained information about 506 houses in the area of Boston. For other
information about the dataset I used the help online

?Boston

or

help(Boston)

First looked at the variables...started with the summary of the variables in the dataset (not
visualized here for space reasons)

summary(Boston)

Looked at the information about the first 3 houses. Then I accessed them through the square
brackets, that were used to access the elements of vectors, matrices, datasets.

Boston[1:3,]
crim zn indus chas nox rm age dis rad tax ptratio black lstat medv
1 0.00632 18 2.31 0 0.538 6.575 65.2 4.0900 1 296 15.3 396.90 4.98 24.0
2 0.02731 0 7.07 0 0.469 6.421 78.9 4.9671 2 242 17.8 396.90 9.14 21.6
3 0.02729 0 7.07 0 0.469 7.185 61.1 4.9671 2 242 17.8 392.83 4.03 34.7

dim(Boston)
[1] 506 14

For convenience I assigned the information about the number of houses n to an object

n <- nrow(Boston)
n
[1] 506

Considered only variables
 • medve: median values of the houses (1000 $)
 • lstat: lower status of the population (percent)
After that I wanted to evaluate whether and how the value medve can be predicted using lstat.
Started with some characteristics about the value

summary(Boston$medv)
Min. 1st Qu. Median Mean 3rd Qu. Max.
5.00 17.02 21.20 22.53 25.00 50.00

histogram of the distribution
hist(Boston$medv, prob=TRUE, xlab='Median value', main='Histogram')

![Medv Histogram Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/4b0d5f48-fbeb-470d-9048-682c5840a027)

boxplot of the distribution
boxplot(Boston$medv, xlab='Median value', main='Boxplot' )

![Boxplot Medv Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/6d9ad0ef-e2e9-4614-882a-88aef546b56e)

Graphical evaluation of the relationship between medv and lstat

plot(Boston$medv, Boston$lstat, main='Dispersion plot', xlab='% of lower status of the population', ylab='Median value', pch=19, cex=0.5)

![Dispersion plot Medv, lstat Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/169d243f-e348-4d54-98a4-f812f4eaeb2c)

The plot showed an inverse relationship between the variables.
Then checked correlation between the variables

cor(Boston$medv, Boston$lstat)
[1] -0.7376627

Then I tried to estimate a simple linear regression model

medv = β0 + β1lstat + ε

Constructed it step by step

beta1 <- cov(Boston$medv, Boston$lstat)/var(Boston$lstat)
beta1
[1] -0.9500494
beta0 <- mean(Boston$medv) - beta1* mean(Boston$lstat)
beta0
[1] 34.55384

The variance of lstat

mean((Boston$lstat- mean(Boston$lstat))^2)/n
[1] 0.100581

was equal to
mean(Boston$lstat^2)-(mean(Boston$lstat)^2)
[1] 50.89398

and note that it was equal to
var(Boston$lstat)*(n-1)/n
[1] 50.89398

as R computes variances and covariances by dividing them by n − 1 instead of n in order to provide unbiased estimates (it works at a sample level, not at the population level). The R function needed to fit linear regression models is lm()

model <- lm(medv ~ lstat, data=Boston)

The output provided an object (model) with many details.

basic information: estimate of the coefficients
model

Much of the information were visualised through command summary
summary(model)

The output contained:

• information about residuals
• estimate, standard error, significance test on the parameters
• information about the accuracy of the model
• test F for the significance of all the parameters

Other information in model

names(model)
[1] "coefficients" "residuals" "effects" "rank" "fitted.values"
[6] "assign" "qr" "df.residual" "xlevels" "call"
[11] "terms" "model"

Then I  accessed the components of the model as follows:

model$coefficients
 (Intercept) lstat
 34.5538409 -0.9500494
 
Model-based estimated fitted values

est.values <- fitted(model)

Observations, model-based estimated values and linear regression fit

plot(Boston$lstat, Boston$medv, pch=19, cex=0.5, xlab='% of lower status of the population', ylab='Median value')

points(Boston$lstat, est.values, pch='x', col='green')

abline(coef(model)[1], coef(model)[2], lty=2, col='red', lwd=3)

![Model based estimated values   regression fit Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/3da626b4-5832-4819-bf12-561550bcffb1)

Confidence interval at level 0.95 for β1

variance/covariance matrix associated to the parameter estimates
vcov(model)
(Intercept) lstat
(Intercept) 0.31654954 -0.018983106
lstat -0.01898311 0.001500278
standard error
se <- sqrt(diag(vcov(model)))
se
(Intercept) lstat
0.56262735 0.03873342

for beta1
beta1-qt(0.975, df=n-2)*se[2]
lstat
-1.026148
beta1+qt(0.975, df=n-2)*se[2]
lstat
-0.8739505
or using the operator c()
c(beta1-qt(0.975, df=n-2)*se[2], beta1+qt(0.975, df=n-2)*se[2])
lstat lstat
-1.0261482 -0.8739505

Given the large values of n, the standard normal approximation can be used as well

c(beta1-qnorm(0.975)*se[2], beta1+qnorm(0.975)*se[2])
lstat lstat
-1.0259655 -0.8741333

Using R functionalities

confint(model)

2.5 % 97.5 %
(Intercept) 33.448457 35.6592247
lstat -1.026148 -0.8739505
changed the confidence level, for example 90%
confint(model, level=0.90)
5 % 95 %
(Intercept) 33.626697 35.4809847
lstat -1.013877 -0.8862212

Hypothesis test on H0 : β1 = −1 against H1 : β1 6 = −1 at significance level 0.05
statistic.t <- (beta1-(-1))/se[2]
statistic.t
lstat
1.289601

qt(0.025, df=n-2)
[1] -1.964682

There was no empirical evidence against H0 at significance level 0.05.

p-value of the test
2*min(pt(statistic.t, n-2), 1-pt(statistic.t, n-2))
[1] 0.1977807

Then I confirmed the previous result.
Predictions on a new dataset

predict(model, newdata=data.frame(list(lstat=c(5, 10, 25))))

1 2 3
29.80359 25.05335 10.80261

Predictions with prediction interval
predict(model, newdata=data.frame(list(lstat=c(5, 10, 25))), interval='prediction')

Then I considered the residuals in order to judge the model.
res <- residuals(model)

Graphical evaluation of the residuals

par(mfrow=c(2,2))
hist(res, prob=TRUE)
plot(res, pch=19, cex=0.5, ylab='Residuals')
abline(h=0, lty=2)
plot(est.values, res, pch=19, cex=0.5, xlab='Estimated values', ylab='Residuals')
abline(h=0, lty=2)
plot(Boston$lstat, res, ylab='Residuals', xlab='% of lower status of the population', pch=19, cex=0.5)
abline(h=0, lty=2)

![Residuals Boston Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/06bb7bf4-9fb0-4c40-9a1c-9822043b3406)

Graphical evaluation of the standardized residuals

par(mfrow=c(2,2))
standard.res <- rstandard(model)
hist(standard.res, prob=TRUE, xlab='Standardized residuals')
plot(standard.res, pch=19, cex=0.5, ylab='Standardized residuals')
abline(h=0, lty=2)
plot(est.values, standard.res, pch=19, cex=0.5, xlab='Estimated values', ylab='Standardized residuals')
abline(h=0, lty=2)
plot(Boston$lstat, standard.res, ylab='Standardized residuals', xlab='% of lower status of the population', pch=19, cex=0.5)
abline(h=0, lty=2)

![Standardized Residuals Boston RplotRplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/a49b262f-12e7-4956-9a50-110a43dad6ae)



Graphical evaluation of the accuracy of the model provided by R
par(mfrow=c(2,2))
plot(model)

![Model Accuracy Check Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/f0e60cc5-452d-4630-981c-617194323027)

plot(model, 4)

![Boston Cook's Distance Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/7d0a3489-8737-4615-991e-66b5afcd1fae)

There were "suspicious" values that R indicates through the corresponding row number in the dataset, but they were not anomalous on the basis of the Cook’s distance (contour is zero).

## 1.1 Multiple linear regression model

Considered variable crim that include the information about per capita crime rate by town.
Relationship between crim and medv

plot(Boston$crim, Boston$medv, ylab='Median value', xlab='Crime', pch=19, cex=0.5)


![Multiple Linear Regression Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/95d8bfdd-30c1-47b5-adac-5db6912c907a)


Estimation of the model
                      medv = β0 + β1lstat + β2crim + ε
                      
model.mv <- lm(medv ~ lstat + crim, data=Boston)
summary(model.mv)

The significance of β2 was questionable.

## 1.2 Model with polynomials

Considered the model without crim. Given the dispersion plot between medv and lstat I tried to insert a quadratic term, that is, I estimated model
                     medv = β0 + β1lstat + β2lstat2 + ε
                     
model2 <- lm(medv ~ lstat + I(lstat^2), data=Boston)
or
model2 <- update(model, .~.+I(lstat^2))
summary(model2)

The new covariate had an associated coefficient significantly different from 0.
Compared the two models, with and without the quadratic term, using the F statistic

rss0 <- (6.216^2)*504
or
sum(model$residuals^2)
rss <- (5.524^2)*503
f <- (rss0 - rss)/rss * (503/1)
f
[1] 135.183
qf(0.95, 1, 503)
[1] 3.860012
There is empirical evidence against H0 that suggests
to move to the simplest model with a single covariate
p-value
1-pf(f, 1, 503)
[1] 0
the p-value confirms the rejection of H0

In R I used function anova()

anova(model, model2)

Note that in this case statistic F corresponded to the square of statistic t for the significance of the coefficient associated to the square of lstat in model2.
Residuals of the updated model

par(mfrow=c(2,2))
plot(model2)



![Residauls of polynomial model 2 Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/52df728b-2506-4d77-9234-8ae0907b6c22)


