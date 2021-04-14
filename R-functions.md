-   [R functions](#r-functions)
    -   [Exclude rows based on a set of criteria (or
        participants)](#exclude-rows-based-on-a-set-of-criteria-or-participants)
    -   [Count the arguments of a
        function](#count-the-arguments-of-a-function)
    -   [Call a function by a character
        string](#call-a-function-by-a-character-string)
    -   [Go through a list of items and select the ones that meet some
        criteria (contains rm in a
        loop)](#go-through-a-list-of-items-and-select-the-ones-that-meet-some-criteria-contains-rm-in-a-loop)
    -   [System Administration Tasks](#system-administration-tasks)
        -   [Creating, deleting, copying files and
            directories](#creating-deleting-copying-files-and-directories)
        -   [Get the script’s directory
            path](#get-the-scripts-directory-path)
        -   [Select characters that come before a
            symbol](#select-characters-that-come-before-a-symbol)
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
    ## 1             1   0.1010101
    ## 2             2   0.8585859
    ## 3             3   0.5252525
    ## 4             4   0.3131313
    ## 5             5   0.2222222
    ## 6             6   0.3232323
    ## 7             7   0.1919192
    ## 8             8   0.4444444
    ## 9             9   0.7676768
    ## 10           10   0.0000000

``` r
# Participants that we want to exclude
partExcl<-c(1,3,5)

# code to exclude participants
df<-df[!df$participants %in% partExcl, ]

df
```

    ##    participants performance
    ## 2             2   0.8585859
    ## 4             4   0.3131313
    ## 6             6   0.3232323
    ## 7             7   0.1919192
    ## 8             8   0.4444444
    ## 9             9   0.7676768
    ## 10           10   0.0000000

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

### Go through a list of items and select the ones that meet some criteria (contains rm in a loop)

This example is based on the BOSS dataset

``` r
# we need the readxl package to read the excel file with the object info
library(readxl)
# and dplyr for intabulating
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
# retrieve the file with info of the categories
category<-read_excel("SI1.xlsx", sheet = "Sheet2")

# change fourth and fifth names to include an underscore
names(category)[4:5]<-c("modal_categ", "cat_agreement")

# get the number of objects per category
table<-category %>% 
  group_by(modal_categ) %>% 
  tally()

# order by modal categ and name agreement
table<-table[order(table$n, decreasing=T),]

# show the first 11
head(table, n=11)
```

    ## # A tibble: 11 x 2
    ##    modal_categ                       n
    ##    <chr>                         <int>
    ##  1 Food                            168
    ##  2 Kitchen & utensil               117
    ##  3 Building infrastructure          96
    ##  4 Hand labour tool & accessory     95
    ##  5 Outdoor activity & sport item    94
    ##  6 Electronic device & accessory    91
    ##  7 Decoration & gift accessory      80
    ##  8 Gamestoy & entertainment         67
    ##  9 Clothing                         64
    ## 10 Vehicle                          64
    ## 11 Stationary & school supply       60

``` r
# select the categories
selCat<-c( "Outdoor activity & sport item", "Kitchen & utensil","Electronic device & accessory", "Hand labour tool & accessory")

# create a dataset with only the categories
dataSel<-category[category$modal_categ %in% selCat, ]

# select the the first 40 images with the highest category agreement level for each category, 
# assign them to the relative dataset
for (cat in 1:length(selCat)){
  # subset the database
  dataim<- dataSel[dataSel$modal_categ==selCat[cat],]
  # order it
  dataim<-dataim[order(dataim$cat_agreement, decreasing = T),]
  # select first 40 
  datapractsel<-dataim[1:40,]
  # assign to a dataset
  assign(paste(selCat[cat],"_dat",sep=""), datapractsel)
  
}

# merget the datasets 
tomerge<-ls(pattern = "*_dat")
dataim_all<-as.data.frame(NULL)

for (n in tomerge){
  dataim_all<-rbind(dataim_all, get(n) )
}

# delete the previously created single datasets
for (i in 1:length(tomerge)){
  name<-tomerge[i]
  rm(list=tomerge[i])
}
```

### System Administration Tasks

#### Creating, deleting, copying files and directories

``` r
# create a directory
dir.create("new_folder")

# create a file
file.create("new_text_file.txt")
file.create("new_word_file.docx")
file.create("new_csv_file.csv")

# create lots of files
sapply(paste0("file", 1:100, ".txt"), file.create)

# copy files
file.copy("source_file.txt", "destination_folder")


# list all CSV files non-recursively
list.files(pattern = ".csv")

# list all CSV files recursively through each sub-folder
list.files(pattern = ".csv", recursive = TRUE)

# read in all the CSV files
all_data_frames <- lapply(list.files(pattern = ".csv"), read.csv)
 
# stack all data frames together
single_data_frame <- Reduce(rbind, all_data_frames)

# remove files
file.remove("new_text_file.txt")

# check if a file exists
file.exists("new_text_file.txt")

# check if a folder exists
file.exists("new_folder")
```

#### Get the script’s directory path

Get the path to directory that contains the script and set it as the
working directory

``` r
# get the script directory
path<-rstudioapi::getSourceEditorContext()$path

# split the string into the names of the folders
names<-unlist(strsplit(path, split="/"))

# get number of carachters last name (file name)
charfile<-nchar(tail(names,1))

# subtract that to the path
path<-substr(path, 1,nchar(path) - charfile)

# set wd 
setwd(path)
```

#### Select characters that come before a symbol

Return the characters before a specific symbol

``` r
# take the first file from the previous example
setwd("testsel")
files<-list.files( pattern= ".csv$")
name<-files[1]

name
```

    ## [1] "myfile1.csv"

``` r
# Select the characters before the ".pdf"
sel<-sub("\\.pdf.*", "", name)

sel
```

    ## [1] "myfile1.csv"

#### Select files that meet a criterion

Selecting files that meets a criterion, or file estension

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
