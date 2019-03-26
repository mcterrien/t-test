t-test
================
CT
March 26, 2019

### t-test

##### It is used to determine whether the means of two groups are equal to each other. The assumption for the test is that both groups are sampled from normal distributions with equal variances

------------------------------------------------------------------------

#### Dataset

##### <https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/ToothGrowth.html>

##### ToothGrowth is a built-in R dataset that examined the Effect of Vitamin C on Tooth Growth in Guinea Pigs. A data frame with 60 observations on 3 variables.

-   \[,1\] len numeric Tooth length
-   \[,2\] supp factor Supplement type (VC or OJ).
-   \[,3\] dose numeric Dose in milligrams/day

##### The response is the length of odontoblasts (cells responsible for tooth growth) in 60 guinea pigs. Each animal received one of three dose levels of vitamin C (0.5, 1, and 2 mg/day) by one of two delivery methods, orange juice or ascorbic acid (a form of vitamin C and coded as VC).

------------------------------------------------------------------------

#### Research Question

##### Whether the mean of group A (mA) is equal to the mean of group B (mB)?

##### Whether the Guinea Pigs treated with Supplement type VC’s average Tooth length is equal to Guinea Pigs treated with Supplement type OJ?

-   Null hypothesis (H0): H0:mA=mB
-   Alternative hypotheses (Ha): Ha:mA≠mB (different)

------------------------------------------------------------------------

#### Loading libraries

``` r
library(dplyr)
library(ggplot2)
library(knitr)
library(ggpubr)
```

------------------------------------------------------------------------

#### Reading data

``` r
data(ToothGrowth)
my_data <- ToothGrowth
```

------------------------------------------------------------------------

#### Exploring data

``` r
# Checking data
str(my_data)
```

    ## 'data.frame':    60 obs. of  3 variables:
    ##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
    ##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...

``` r
summary(my_data)
```

    ##       len        supp         dose      
    ##  Min.   : 4.20   OJ:30   Min.   :0.500  
    ##  1st Qu.:13.07   VC:30   1st Qu.:0.500  
    ##  Median :19.25           Median :1.000  
    ##  Mean   :18.81           Mean   :1.167  
    ##  3rd Qu.:25.27           3rd Qu.:2.000  
    ##  Max.   :33.90           Max.   :2.000

``` r
# Summary statistics by Supplement type:
group_by(my_data, supp) %>%
  summarise(
    count = n(),
    mean = mean(len, na.rm = TRUE),
    sd = sd(len, na.rm = TRUE)
  )
```

    ## # A tibble: 2 x 4
    ##   supp  count  mean    sd
    ##   <fct> <int> <dbl> <dbl>
    ## 1 OJ       30  20.7  6.61
    ## 2 VC       30  17.0  8.27

``` r
# Plotting length by Supplement type
ggboxplot(my_data, x = "supp", y = "len", 
          color = "supp", palette = c("#00AFBB", "#E7B800"),
          ylab = "Tooth length", xlab = "Supplement type") +
          ggtitle("Length by Supplement type") +
          scale_fill_discrete(name = "Supplement type") +
          theme(legend.position = "right")
```

![](t-test_files/figure-markdown_github-ascii_identifiers/explore-1.png)

------------------------------------------------------------------------

#### Assumptions

-   Assumption 1: Are the two samples independents? \#\#\#\#\# Yes, the samples from Supplement type VC and OJ are not related.

-   Assumtion 2: Are the data from each of the 2 groups follow a normal distribution? \#\#\#\#\# Null hypothesis (H0): the data are normally distributed \#\#\#\#\# Alternative hypotheses (Ha): the data are not normally distributed

``` r
# Plotting the data  
gg <- ggplot(my_data, aes(len)) + 
  geom_density(kernel = "gaussian") +
  facet_grid(. ~ supp) +
  labs(subtitle="Length distribution")
plot(gg)

# Confirming with Shapiro-Wilk normality test

# Shapiro-Wilk normality test for VC's length
with(my_data, shapiro.test(len[supp == "VC"]))# p-value = 0.4284
# Shapiro-Wilk normality test for OJ's length
with(my_data, shapiro.test(len[supp == "OJ"])) # p-value = 0.02359
```

##### From the output, the two p-values are bigger than the significance level 0.05

##### implying that the distribution of the data are not significantly different from the normal

##### distribution. So, we can assume the normality.

------------------------------------------------------------------------

#### Test

``` r
# Performing a t-test
res <- t.test(len ~ supp, data = my_data, var.equal = TRUE)
res
```

    ## 
    ##  Two Sample t-test
    ## 
    ## data:  len by supp
    ## t = 1.9153, df = 58, p-value = 0.06039
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.1670064  7.5670064
    ## sample estimates:
    ## mean in group OJ mean in group VC 
    ##         20.66333         16.96333

------------------------------------------------------------------------

#### Conclusion

##### The p-value of the test is 0.06039, which is more than the significance level alpha = 0.05. We can conclude that the Guinea Pigs treated with Supplement type VC’s average Tooth length is NOT significantly different from Guinea Pigs treated with Supplement type OJ’s average Tooth length with a p-value = 0.06039

------------------------------------------------------------------------

#### Source

##### <http://www.sthda.com/english/wiki/unpaired-two-samples-t-test-in-r>
