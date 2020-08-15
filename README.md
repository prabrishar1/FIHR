
<!-- README.md is generated from README.Rmd. Please edit that file -->

FIHR
====

<!-- badges: start -->
<!-- badges: end -->

The goal of FIHR is to provide inference for linear and quadratic
functionals in high-dimensional linear and logistic regression models.
It computes bias-corrected estimators and corresponding standard errors
for the linear and quadratic functionals.

Installation
------------

You can install the released version of FIHR from
[CRAN](https://CRAN.R-project.org) with:

    install.packages("FIHR")

And the development version from [GitHub](https://github.com/) with:

    # install.packages("devtools")
    devtools::install_github("prabrishar1/FIHR")

Example
-------

These are basic examples which show how to solve the common
high-dimensional inference problems:

    library(FIHR)

Inference for linear functional in high-dimensional linear regression
model


    library(MASS)
    A1gen <- function(rho,p){
      A1=matrix(0,p,p)
      for(i in 1:p){
        for(j in 1:p){
          A1[i,j]<-rho^(abs(i-j))
        } 
      }
      A1
    }
    mu <- rep(0,400)
    mu[1:5] <- c(1:5)/5
    rho = 0.5
    Cov <- (A1gen(rho,400))/2
    beta <- rep(0,400)
    beta[1:10] <- c(1:10)/5
    X <- MASS::mvrnorm(100,mu,Cov)
    y = X%*%beta + rnorm(100)
    Est = FIHR::LF(X = X, y = y, loading = c(1,rep(0,399)), intercept = TRUE)
    #> [1] 3
    #> [1] "step is 3"
    Est$prop.est
    #>           [,1]
    #> [1,] 0.6120607
    Est$se
    #> [1] 0.04910674

Inference for linear functional in high-dimensional logistic regression
model

    library(MASS)
    A1gen <- function(rho,p){
      A1=matrix(0,p,p)
      for(i in 1:p){
        for(j in 1:p){
          A1[i,j]<-rho^(abs(i-j))
        } 
      }
      A1
    }
    mu <- rep(0,400)
    rho = 0.5
    Cov <- (A1gen(rho,400))/2
    beta <- rep(0,400)
    beta[1:10] <- c(1:10)/5
    X <- MASS::mvrnorm(100,mu,Cov)
    exp_val <- X%*%beta
    prob <- exp(exp_val)/(1+exp(exp_val))
    y <- rbinom(100,1,prob)
    Est = FIHR::LF_logistic(X = X, y = y, loading = c(1,rep(0,399)), intercept = TRUE, weight = rep(1,100))
    #> [1] 3
    #> [1] "step is 3"
    Est$prop.est
    #> [1] 0.3313114
    Est$se
    #> [1] 0.4169103

Inference for quadratic functional in high-dimensional linear model

    library(MASS)
    A1gen <- function(rho,p){
      A1=matrix(0,p,p)
      for(i in 1:p){
        for(j in 1:p){
          A1[i,j]<-rho^(abs(i-j))
        } 
      }
      A1
    }
    rho = 0.6
    Cov <- (A1gen(rho,400))
    mu <- rep(0,400)
    mu[1:5] <- c(1:5)/5
    beta <- rep(0,400)
    beta[25:50] <- 0.08
    X <- MASS::mvrnorm(100,mu,Cov)
    y <- X%*%beta + rnorm(100)
    test.set <- c(30:100)

    ## Inference for Quadratic Functional with Population Covariance Matrix in middle

    Est = FIHR::QF(X = X, y = y, test.set=test.set)
    Est$prop.est
    #>           [,1]
    #> [1,] 0.5345461
    Est$se
    #> [1] 0.1209907

    ## Inference for Quadratic Functional with known matrix A in middle

    Est = FIHR::QF(X = X, y = y, test.set=test.set, A = diag(1:400,400))
    #> Warning in if (A == "sigma") {: the condition has length > 1 and only the first
    #> element will be used
    #> Warning in if (A == "sigma") {: the condition has length > 1 and only the first
    #> element will be used
    Est$prop.est
    #>          [,1]
    #> [1,] 8.880047
    Est$se
    #> [1] 0.3381186

    ## Inference for square norm of regression vector

    Est = FIHR::QF(X = X, y = y, test.set=test.set, A = diag(1,400))
    #> Warning in if (A == "sigma") {: the condition has length > 1 and only the first
    #> element will be used

    #> Warning in if (A == "sigma") {: the condition has length > 1 and only the first
    #> element will be used
    Est$prop.est
    #>           [,1]
    #> [1,] 0.2277406
    Est$se
    #> [1] 0.141658
