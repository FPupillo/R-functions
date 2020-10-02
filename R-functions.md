-   [R functions](#r-functions)
    -   [Exclude rows based on a set of criteria (or
        participants)](#exclude-rows-based-on-a-set-of-criteria-or-participants)

R functions
===========

This is a collection of functions typically used in R, especially in
psychology.

For any question, feel free to drop me an
[email](pupillo@psych.uni-frankfurt.de)

### Exclude rows based on a set of criteria (or participants)

Useful code to exclude rows in a dataset that corresponds with
participants we decided to exclude.

``` r
# create fake dataframe with participants and performance columns
df<-data.frame(participants=seq(1:10), performance= sample(seq(0, 1, length.out = 100), 10, T))

View(df)
# Participants that we want to exclude
partExcl<-c(1,3,5)

# code to exclude participants
df<-df[!df$participants %in% partExcl, ]

View(df)
```
