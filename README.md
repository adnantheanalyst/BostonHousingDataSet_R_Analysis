# BostonHousingDataSet_R_Analysis
First I checked there were any objects in the workspace<br>

ls()<br>
Eventually removed existing objects<br>
rm(list=ls())<br>

Uploaded the Boston dataset, that is inside library (or package) MASS.<br>
library(MASS)<br>
data(Boston)<br>

The dataset contained information about 506 houses in the area of Boston. For other information about the dataset I used the help online<br>

?Boston<br>

or<br>

help(Boston)<br>

First looked at the variables...started with the summary of the variables in the dataset (not visualized here for space reasons)<br>

summary(Boston)<br>

Looked at the information about the first 3 houses. Then I accessed them through the square brackets, that were used to access the elements of vectors, matrices, datasets.<br>

Boston[1:3,]<br>
crim zn indus chas nox rm age dis rad tax ptratio black lstat medv<br>
1 0.00632 18 2.31 0 0.538 6.575 65.2 4.0900 1 296 15.3 396.90 4.98 24.0<br>
2 0.02731 0 7.07 0 0.469 6.421 78.9 4.9671 2 242 17.8 396.90 9.14 21.6<br>
3 0.02729 0 7.07 0 0.469 7.185 61.1 4.9671 2 242 17.8 392.83 4.03 34.7<br>

dim(Boston)<br>
[1] 506 14<br>

For convenience I assigned the information about the number of houses n to an object<br>

n <- nrow(Boston)<br>
n<br>
[1] 506<br>

Considered only variables<br>
 • medve: median values of the houses (1000 $)<br>
 • lstat: lower status of the population (percent)<br>
After that I wanted to evaluate whether and how the value medve can be predicted using lstat.<br>
Started with some characteristics about the value<br>

summary(Boston$medv)<br>
Min. 1st Qu. Median Mean 3rd Qu. Max.<br>
5.00 17.02 21.20 22.53 25.00 50.00<br>

histogram of the distribution<br>
hist(Boston$medv, prob=TRUE, xlab='Median value', main='Histogram')<br>

![Medv Histogram Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/4b0d5f48-fbeb-470d-9048-682c5840a027)

boxplot of the distribution<br>
boxplot(Boston$medv, xlab='Median value', main='Boxplot' )<br>

![Boxplot Medv Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/6d9ad0ef-e2e9-4614-882a-88aef546b56e)

Graphical evaluation of the relationship between medv and lstat<br>

plot(Boston$medv, Boston$lstat, main='Dispersion plot', xlab='% of lower status of the population', ylab='Median value', pch=19, cex=0.5)<br>

![Dispersion plot Medv, lstat Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/169d243f-e348-4d54-98a4-f812f4eaeb2c)

The plot showed an inverse relationship between the variables.<br>
Then checked correlation between the variables<br>

cor(Boston$medv, Boston$lstat)<br>
[1] -0.7376627<br>

Then I tried to estimate a simple linear regression model<br>

medv = β0 + β1lstat + ε<br>

Constructed it step by step<br>

beta1 <- cov(Boston$medv, Boston$lstat)/var(Boston$lstat)<br>
beta1<br>
[1] -0.9500494<br>
beta0 <- mean(Boston$medv) - beta1* mean(Boston$lstat)<br>
beta0<br>
[1] 34.55384<br>

The variance of lstat<br>

mean((Boston$lstat- mean(Boston$lstat))^2)/n<br>
[1] 0.100581<br>

was equal to<br>
mean(Boston$lstat^2)-(mean(Boston$lstat)^2)<br>
[1] 50.89398<br>

and note that it was equal to<br>
var(Boston$lstat)*(n-1)/n<br>
[1] 50.89398<br>

as R computes variances and covariances by dividing them by n − 1 instead of n in order to provide unbiased estimates (it works at a sample level, not at the population level). The R function needed to fit linear regression models is lm()<br>

model <- lm(medv ~ lstat, data=Boston)<br>

The output provided an object (model) with many details.<br>

basic information: estimate of the coefficients<br>
model<br>

Much of the information were visualised through command summary<br>
summary(model)<br>

The output contained:<br>

• information about residuals<br>
• estimate, standard error, significance test on the parameters<br>
• information about the accuracy of the model<br>
• test F for the significance of all the parameters<br>

Other information in model<br>

names(model)<br>
[1] "coefficients" "residuals" "effects" "rank" "fitted.values"<br>
[6] "assign" "qr" "df.residual" "xlevels" "call"<br>
[11] "terms" "model"<br>

Then I  accessed the components of the model as follows:<br>

model$coefficients<br>
 (Intercept) lstat<br>
 34.5538409 -0.9500494<br>
 
Model-based estimated fitted values<br>

est.values <- fitted(model)<br>

Observations, model-based estimated values and linear regression fit<br>

plot(Boston$lstat, Boston$medv, pch=19, cex=0.5, xlab='% of lower status of the population', ylab='Median value')<br>

points(Boston$lstat, est.values, pch='x', col='green')<br>

abline(coef(model)[1], coef(model)[2], lty=2, col='red', lwd=3)<br>

![Model based estimated values   regression fit Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/3da626b4-5832-4819-bf12-561550bcffb1)

Confidence interval at level 0.95 for β1<br>

variance/covariance matrix associated to the parameter estimates<br>
vcov(model)<br>
(Intercept) lstat<br>
(Intercept) 0.31654954 -0.018983106<br>
lstat -0.01898311 0.001500278<br>
standard error<br>
se <- sqrt(diag(vcov(model)))<br>
se<br>
(Intercept) lstat<br>
0.56262735 0.03873342<br>

for beta1<br>
beta1-qt(0.975, df=n-2)*se[2]<br>
lstat<br>
-1.026148<br>
beta1+qt(0.975, df=n-2)*se[2]<br>
lstat<br>
-0.8739505<br>
or using the operator c()<br>
c(beta1-qt(0.975, df=n-2)*se[2], beta1+qt(0.975, df=n-2)*se[2])<br>
lstat lstat<br>
-1.0261482 -0.8739505<br>

Given the large values of n, the standard normal approximation can be used as well<br>

c(beta1-qnorm(0.975)*se[2], beta1+qnorm(0.975)*se[2])<br>
lstat lstat<br>
-1.0259655 -0.8741333<br>

Using R functionalities<br>

confint(model)<br>

2.5 % 97.5 %<br>
(Intercept) 33.448457 35.6592247<br>
lstat -1.026148 -0.8739505<br>
changed the confidence level, for example 90%<br>
confint(model, level=0.90)<br>
5 % 95 %<br>
(Intercept) 33.626697 35.4809847<br>
lstat -1.013877 -0.8862212<br>

Hypothesis test on H0 : β1 = −1 against H1 : β1 6 = −1 at significance level 0.05<br>
statistic.t <- (beta1-(-1))/se[2]<br>
statistic.t<br>
lstat<br>
1.289601<br>

qt(0.025, df=n-2)<br>
[1] -1.964682<br>

There was no empirical evidence against H0 at significance level 0.05.<br>

p-value of the test<br>
2*min(pt(statistic.t, n-2), 1-pt(statistic.t, n-2))<br>
[1] 0.1977807<br>

Then I confirmed the previous result.<br>
Predictions on a new dataset<br>

predict(model, newdata=data.frame(list(lstat=c(5, 10, 25))))<br>

1 2 3<br>
29.80359 25.05335 10.80261<br>

Predictions with prediction interval<br>
predict(model, newdata=data.frame(list(lstat=c(5, 10, 25))), interval='prediction')<br>

Then I considered the residuals in order to judge the model.<br>
res <- residuals(model)<br>

Graphical evaluation of the residuals<br>

par(mfrow=c(2,2))<br>
hist(res, prob=TRUE)<br>
plot(res, pch=19, cex=0.5, ylab='Residuals')<br>
abline(h=0, lty=2)<br>
plot(est.values, res, pch=19, cex=0.5, xlab='Estimated values', ylab='Residuals')<br>
abline(h=0, lty=2)<br>
plot(Boston$lstat, res, ylab='Residuals', xlab='% of lower status of the population', pch=19, cex=0.5)<br>
abline(h=0, lty=2)<br>

![Residuals Boston Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/06bb7bf4-9fb0-4c40-9a1c-9822043b3406)

Graphical evaluation of the standardized residuals<br>

par(mfrow=c(2,2))<br>
standard.res <- rstandard(model)<br>
hist(standard.res, prob=TRUE, xlab='Standardized residuals')<br>
plot(standard.res, pch=19, cex=0.5, ylab='Standardized residuals')<br>
abline(h=0, lty=2)<br>
plot(est.values, standard.res, pch=19, cex=0.5, xlab='Estimated values', ylab='Standardized residuals')<br>
abline(h=0, lty=2)<br>
plot(Boston$lstat, standard.res, ylab='Standardized residuals', xlab='% of lower status of the population', pch=19, cex=0.5)<br>
abline(h=0, lty=2)<br>

![Standardized Residuals Boston RplotRplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/a49b262f-12e7-4956-9a50-110a43dad6ae)



Graphical evaluation of the accuracy of the model provided by R<br>
par(mfrow=c(2,2))<br>
plot(model)<br>

![Model Accuracy Check Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/f0e60cc5-452d-4630-981c-617194323027)

plot(model, 4)<br>

![Boston Cook's Distance Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/7d0a3489-8737-4615-991e-66b5afcd1fae)

There were "suspicious" values that R indicates through the corresponding row number in the dataset, but they were not anomalous on the basis of the Cook’s distance (contour is zero).<br>

## 1.1 Multiple linear regression model<br>

Considered variable crim that include the information about per capita crime rate by town.<br>
Relationship between crim and medv<br>

plot(Boston$crim, Boston$medv, ylab='Median value', xlab='Crime', pch=19, cex=0.5)<br>


![Multiple Linear Regression Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/95d8bfdd-30c1-47b5-adac-5db6912c907a)


Estimation of the model<br>
                      medv = β0 + β1lstat + β2crim + ε<br>
                      
model.mv <- lm(medv ~ lstat + crim, data=Boston)<br>
summary(model.mv)<br>

The significance of β2 was questionable.<br>

## 1.2 Model with polynomials<br>

Considered the model without crim. Given the dispersion plot between medv and lstat I tried to insert a quadratic term, that is, I estimated model<br>
                     medv = β0 + β1lstat + β2lstat2 + ε<br>
                     
model2 <- lm(medv ~ lstat + I(lstat^2), data=Boston)<br>
or<br>
model2 <- update(model, .~.+I(lstat^2))<br>
summary(model2)<br>

The new covariate had an associated coefficient significantly different from 0.<br>
Compared the two models, with and without the quadratic term, using the F statistic<br>

rss0 <- (6.216^2)*504<br>
or<br>
sum(model$residuals^2)<br>
rss <- (5.524^2)*503<br>
f <- (rss0 - rss)/rss * (503/1)<br>
f<br>
[1] 135.183<br>
qf(0.95, 1, 503)<br>
[1] 3.860012<br>
There is empirical evidence against H0 that suggests<br>
to move to the simplest model with a single covariate<br>
p-value<br>
1-pf(f, 1, 503)<br>
[1] 0<br>
the p-value confirms the rejection of H0<br>

In R I used function anova()<br>

anova(model, model2)<br>

Note that in this case statistic F corresponded to the square of statistic t for the significance of the coefficient associated to the square of lstat in model2.<br>
Residuals of the updated model<br>

par(mfrow=c(2,2))<br>
plot(model2)<br>



![Residauls of polynomial model 2 Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/52df728b-2506-4d77-9234-8ae0907b6c22)


