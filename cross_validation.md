<center>
<font aspan size = '6'>Cross Validation in R</font>
</center>
================
<center>
Wyclife Agumba Oluoch <wyclifeoluoch@gmail.com>
</center>
<center>
2021-11-02 19:05:58
</center>

# Introduction

``` r
head(mtcars)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
    ## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1

``` r
cv <- crossv_kfold(mtcars, k = 5)
cv
```

    ## # A tibble: 5 x 3
    ##   train                test                .id  
    ##   <named list>         <named list>        <chr>
    ## 1 <resample [25 x 11]> <resample [7 x 11]> 1    
    ## 2 <resample [25 x 11]> <resample [7 x 11]> 2    
    ## 3 <resample [26 x 11]> <resample [6 x 11]> 3    
    ## 4 <resample [26 x 11]> <resample [6 x 11]> 4    
    ## 5 <resample [26 x 11]> <resample [6 x 11]> 5

``` r
models1 <- map(cv$train, ~lm(mpg ~ wt + cyl + hp, data = .))
models2 <- map(cv$train, ~lm(mpg ~ wt + qsec + am, data = .))
models3 <- map(cv$train, ~lm(mpg ~ wt + qsec + hp, data = .))
```

``` r
get_pred <- function(model, test_data){
  data <- as.data.frame(test_data)
  pred <- add_predictions(data, model)
  return(pred)
}
pred1 <- map2_df(models1, cv$test, get_pred, .id = 'Run')
pred2 <- map2_df(models2, cv$test, get_pred, .id = 'Run')
pred3 <- map2_df(models3, cv$test, get_pred, .id = 'Run')
```

``` r
MSE1 <- pred1 |> group_by(Run) |> summarise(MSE = mean((mpg - pred)^2))
MSE1
```

    ## # A tibble: 5 x 2
    ##   Run     MSE
    ##   <chr> <dbl>
    ## 1 1      8.07
    ## 2 2      5.39
    ## 3 3      4.14
    ## 4 4     12.2 
    ## 5 5      4.06

``` r
MSE2 <- pred2 |> group_by(Run) |> summarise(MSE = mean((mpg - pred)^2))
MSE1
```

    ## # A tibble: 5 x 2
    ##   Run     MSE
    ##   <chr> <dbl>
    ## 1 1      8.07
    ## 2 2      5.39
    ## 3 3      4.14
    ## 4 4     12.2 
    ## 5 5      4.06

``` r
MSE3 <- pred3 |> group_by(Run) |> summarise(MSE = mean((mpg - pred)^2))
MSE1
```

    ## # A tibble: 5 x 2
    ##   Run     MSE
    ##   <chr> <dbl>
    ## 1 1      8.07
    ## 2 2      5.39
    ## 3 3      4.14
    ## 4 4     12.2 
    ## 5 5      4.06

``` r
mean(MSE1$MSE)
```

    ## [1] 6.773016

``` r
mean(MSE2$MSE)
```

    ## [1] 6.831094

``` r
mean(MSE3$MSE)
```

    ## [1] 7.114941

# References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-R-rmarkdown" class="csl-entry">

Allaire, JJ, Yihui Xie, Jonathan McPherson, Javier Luraschi, Kevin
Ushey, Aron Atkins, Hadley Wickham, Joe Cheng, Winston Chang, and
Richard Iannone. 2021. *Rmarkdown: Dynamic Documents for r*.
<https://CRAN.R-project.org/package=rmarkdown>.

</div>

<div id="ref-R-purrr" class="csl-entry">

Henry, Lionel, and Hadley Wickham. 2020. *Purrr: Functional Programming
Tools*. <https://CRAN.R-project.org/package=purrr>.

</div>

<div id="ref-R-base" class="csl-entry">

R Core Team. 2021. *R: A Language and Environment for Statistical
Computing*. Vienna, Austria: R Foundation for Statistical Computing.
<https://www.R-project.org/>.

</div>

<div id="ref-R-modelr" class="csl-entry">

Wickham, Hadley. 2020. *Modelr: Modelling Functions That Work with the
Pipe*. <https://CRAN.R-project.org/package=modelr>.

</div>

<div id="ref-R-dplyr" class="csl-entry">

Wickham, Hadley, Romain François, Lionel Henry, and Kirill Müller. 2021.
*Dplyr: A Grammar of Data Manipulation*.
<https://CRAN.R-project.org/package=dplyr>.

</div>

<div id="ref-knitr2014" class="csl-entry">

Xie, Yihui. 2014. “Knitr: A Comprehensive Tool for Reproducible Research
in R.” In *Implementing Reproducible Computational Research*, edited by
Victoria Stodden, Friedrich Leisch, and Roger D. Peng. Chapman;
Hall/CRC. <http://www.crcpress.com/product/isbn/9781466561595>.

</div>

<div id="ref-knitr2015" class="csl-entry">

———. 2015. *Dynamic Documents with R and Knitr*. 2nd ed. Boca Raton,
Florida: Chapman; Hall/CRC. <https://yihui.org/knitr/>.

</div>

<div id="ref-R-knitr" class="csl-entry">

———. 2021. *Knitr: A General-Purpose Package for Dynamic Report
Generation in r*. <https://yihui.org/knitr/>.

</div>

<div id="ref-rmarkdown2018" class="csl-entry">

Xie, Yihui, J. J. Allaire, and Garrett Grolemund. 2018. *R Markdown: The
Definitive Guide*. Boca Raton, Florida: Chapman; Hall/CRC.
<https://bookdown.org/yihui/rmarkdown>.

</div>

<div id="ref-rmarkdown2020" class="csl-entry">

Xie, Yihui, Christophe Dervieux, and Emily Riederer. 2020. *R Markdown
Cookbook*. Boca Raton, Florida: Chapman; Hall/CRC.
<https://bookdown.org/yihui/rmarkdown-cookbook>.

</div>

</div>
