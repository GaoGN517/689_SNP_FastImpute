
# SNPFastImpute

<!-- badges: start -->
<!-- badges: end -->

The goal of SNPFastImpute is to impute missing values in SNP data files. 

## Installation

You can install the development version of SNPFastImpute from github using devtools:

``` r
devtools::install_github("GaoGN517/689_SNP_FastImpute")
```
(currently private.)

## Basic Example

This is a basic example which shows you how to solve a common problem:

``` r
library(SNPFastImpute)

## Read a vcf file as a matrix
## filename <- "data/Test.vcf" ## your file path
## output_df <- vcf2df(filename) ## convert to a matrix

## Introduce some missing values into the original matrix to test the performance
data("SNP_orig_sub")
## this is the SNP matrix with the original SNP types.
## original NA ratio is 0.018

## Make the final missing ratio to be 20%
SNP_NA_df02 <- NA_Generator(SNP_orig_sub, 0.2)
## This is an object list of four elements, 
## SNP_NA_df, 
## NA_percent_orig,
## NA_percent_generate,
## NP_generate_positions

## Make another object with final missing ratio to be 5%
SNP_NA_df005 <- NA_Generator(SNP_orig_sub, 0.05)

## Now we have the original matrix, the two objects with additional missing values.
ls()
# [1] "SNP_NA_df005" "SNP_NA_df02"  "SNP_orig_sub"

## We can use the following function to create an object that can be used in the imputation 
## function for each SNP in the matrix.
Create_Single_SNP_Object(SNP_NA_df005$SNP_NA_df, 2, size = 20)

## The main function of this package is to use the imputation function to fill in the missing
## values.

## We can just do the filling for the original matrix
predict_df <- Impute_GenoType_XGBoost(SNP_orig_sub, size = 10)
## about 30 seconds.

## We can also perform the filling on the matrix which we introduced additional 
## missing values. And then see how our method performed on predictions. 
df_fill02 <- Impute_GenoType_XGBoost(SNP_NA_df02$SNP_NA_df)
df_fill005 <- Impute_GenoType_XGBoost(SNP_NA_df005$SNP_NA_df)

NA_positions02 <- SNP_NA_df02$NP_generate_positions
NA_positions005 <- SNP_NA_df005$NP_generate_positions

classification_error(SNP_orig_sub, df_fill02, NA_positions02)
classification_error(SNP_orig_sub, df_fill005, NA_positions005)
```

