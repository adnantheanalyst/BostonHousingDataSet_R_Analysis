# BostonHousingDataSet_R_Analysis
Start the R session and make sure there are no objects in the workspace

ls()
Eventually remove existing objects
rm(list=ls())

Upload the Boston dataset, that is inside library (or package) MASS.
library(MASS)
data(Boston)

The dataset contains information about 506 houses in the area of Boston. For other
information about the dataset we can use the help online

?Boston

or

help(Boston)

First look at the variables...start with the summary of the variables in the dataset (not
visualized here for space reasons)

summary(Boston)

Look at the information about the first 3 houses. We can access them through the square
brackets, that are used to access the elements of vectors, matrices, datasets.

Boston[1:3,]
crim zn indus chas nox rm age dis rad tax ptratio black lstat medv
1 0.00632 18 2.31 0 0.538 6.575 65.2 4.0900 1 296 15.3 396.90 4.98 24.0
2 0.02731 0 7.07 0 0.469 6.421 78.9 4.9671 2 242 17.8 396.90 9.14 21.6
3 0.02729 0 7.07 0 0.469 7.185 61.1 4.9671 2 242 17.8 392.83 4.03 34.7

dim(Boston)
[1] 506 14

For convenience we can assign the information about the number of houses n to an object

n <- nrow(Boston)
n
[1] 506

Consider only variables
 • medve: median values of the houses (1000 $)
 • lstat: lower status of the population (percent)
We want to evaluate whether and how the value medve can be predicted using lstat.
Start with some characteristics about the value

summary(Boston$medv)
Min. 1st Qu. Median Mean 3rd Qu. Max.
5.00 17.02 21.20 22.53 25.00 50.00

histogram of the distribution
xlab=graphical option to assign a label to the x-axis
main: title of the graph
hist(Boston$medv, prob=TRUE, xlab='Median value', main='Histogram')

![Medv Histogram Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/4b0d5f48-fbeb-470d-9048-682c5840a027)

boxplot of the distribution
boxplot(Boston$medv, xlab='Median value', main='Boxplot' )

![Boxplot Medv Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/6d9ad0ef-e2e9-4614-882a-88aef546b56e)

Graphical evaluation of the relationship between medv and lstat

plot(Boston$medv, Boston$lstat, main='Dispersion plot', xlab='% of lower status of the population', ylab='Median value', pch=19, cex=0.5)

![Dispersion plot Medv, lstat Rplot](https://github.com/adnantheanalyst/BostonHousingDataSet_R_Analysis/assets/16821246/169d243f-e348-4d54-98a4-f812f4eaeb2c)


