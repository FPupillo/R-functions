-   [R functions](#r-functions)
    -   [Exclude rows based on a set of criteria (or
        participants)](#exclude-rows-based-on-a-set-of-criteria-or-participants)
    -   [Count the arguments of a
        function](#count-the-arguments-of-a-function)
    -   [Call a function by a character
        string](#call-a-function-by-a-character-string)
    -   [Select files that meet a
        criterion](#select-files-that-meet-a-criterion)

R functions
===========

This is a collection of functions typically used in R, especially in
psychology.

For any question, feel free to drop me an email :
<a href="mailto:pupillo@psych.uni-frankfurt.de" class="email">pupillo@psych.uni-frankfurt.de</a>

### Exclude rows based on a set of criteria (or participants)

Useful code to exclude rows in a dataset that corresponds to
participants we decided to exclude.

``` r
# create fake dataframe with participants and performance columns
df<-data.frame(participants=seq(1:10), performance= sample(seq(0, 1, length.out = 100), 10, T))

df
```

    ##    participants performance
    ## 1             1  0.63636364
    ## 2             2  0.73737374
    ## 3             3  1.00000000
    ## 4             4  0.51515152
    ## 5             5  0.39393939
    ## 6             6  0.07070707
    ## 7             7  0.35353535
    ## 8             8  0.43434343
    ## 9             9  0.01010101
    ## 10           10  0.34343434

``` r
# Participants that we want to exclude
partExcl<-c(1,3,5)

# code to exclude participants
df<-df[!df$participants %in% partExcl, ]

df
```

    ##    participants performance
    ## 2             2  0.73737374
    ## 4             4  0.51515152
    ## 6             6  0.07070707
    ## 7             7  0.35353535
    ## 8             8  0.43434343
    ## 9             9  0.01010101
    ## 10           10  0.34343434

### Count the arguments of a function

Function that returns the number of arguments in a function

``` r
# let's create a function that calculate the means between three dimenions (x, y ,z)

foo<-function(x, y, z){
  sum (x, y, z)/ length(c(x, y,z))
}

# now I want to assign the number of the arguments of this function to a vector. 
# I can use "formals" to store the argumens

fooArgs<-formals(foo)

fooArgs
```

    ## $x
    ## 
    ## 
    ## $y
    ## 
    ## 
    ## $z

``` r
# then I can extract the number of the arguments with "length"
numfooArgs<-length(fooArgs)

numfooArgs
```

    ## [1] 3

### Call a function by a character string

How to assign a name of a function to a character string and then call
it later? By using “get”.

``` r
# Create a function. This one calculate the average of three dimensions

foo<-function(x, y, z){
  sum (x, y, z)/ length(c(x, y,z))
}

# Now create a carachter string with the name of the formula

foostr<-"foo"

foostr
```

    ## [1] "foo"

``` r
# use "get" to call the function

myfunction<-get(foostr)

# now we can call our function
myfunction(3,4,5)
```

    ## [1] 4

``` r
numfooArgs
```

    ## [1] 3

### Select files that meet a criterion

Selecting files that meets a criterion, or file estention

``` r
# create a vector with names that vary in different ways
files<-vector()

for (i in 1:10){
  # the first five are .csv, the others are .pdf
  if (i<6){
  files[i]<- paste("myfile", i, ".csv", sep="")
  } else{
  files[i]<- paste("myfile", i, ".pdf", sep="")
}
}

# plus some random files
files[11:13]<-c("random1.pdf", "test2.csv", "dunno.rnd")

# create the files in a subfolder
# dir.create("testsel")

 setwd("testsel")

# create files
for (f in 1:length(files)){
  write(0, file = files[f])
}



# select all the files that are .csv
selCsv<-list.files( pattern= ".csv$")
selCsv
```

    ## [1] "myfile1.csv" "myfile2.csv" "myfile3.csv" "myfile4.csv" "myfile5.csv"
    ## [6] "test2.csv"

``` r
# now, select files that are .csv OR that start with "myfile"
selCsv<-list.files(pattern= c("myfile", ".csv$"))
selCsv
```

    ##  [1] "myfile1.csv"  "myfile10.pdf" "myfile2.csv"  "myfile3.csv"  "myfile4.csv" 
    ##  [6] "myfile5.csv"  "myfile6.pdf"  "myfile7.pdf"  "myfile8.pdf"  "myfile9.pdf"

``` r
# now, select files that are .csv AND that start with "myfile"
selCsv<-list.files(pattern= c("myfile.*.csv$"))
selCsv
```

    ## [1] "myfile1.csv" "myfile2.csv" "myfile3.csv" "myfile4.csv" "myfile5.csv"
