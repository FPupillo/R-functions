-   [R functions](#r-functions)
    -   [Exclude rows based on a set of criteria (or
        participants)](#exclude-rows-based-on-a-set-of-criteria-or-participants)

R functions
===========

This is a collection of functions typically used in R, especially in
psychology.

For any question, feel free to drop me an email :
<a href="mailto:pupillo@psych.uni-frankfurt.de" class="email">pupillo@psych.uni-frankfurt.de</a>

### Exclude rows based on a set of criteria (or participants)

Useful code to exclude rows in a dataset that corresponds with
participants we decided to exclude.

``` r
# create fake dataframe with participants and performance columns
df<-data.frame(participants=seq(1:10), performance= sample(seq(0, 1, length.out = 100), 10, T))

df
```

    ##    participants performance
    ## 1             1  0.54545455
    ## 2             2  0.02020202
    ## 3             3  0.19191919
    ## 4             4  0.23232323
    ## 5             5  0.74747475
    ## 6             6  0.17171717
    ## 7             7  0.55555556
    ## 8             8  0.18181818
    ## 9             9  0.74747475
    ## 10           10  0.85858586

``` r
# Participants that we want to exclude
partExcl<-c(1,3,5)

# code to exclude participants
df<-df[!df$participants %in% partExcl, ]

df
```

    ##    participants performance
    ## 2             2  0.02020202
    ## 4             4  0.23232323
    ## 6             6  0.17171717
    ## 7             7  0.55555556
    ## 8             8  0.18181818
    ## 9             9  0.74747475
    ## 10           10  0.85858586
