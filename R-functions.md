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
    -   [select items in a dataset that start or end with a
        string](#select-items-in-a-dataset-that-start-or-end-with-a-string)
    -   [Select elements in a vector that start or end with a
        letter](#select-elements-in-a-vector-that-start-or-end-with-a-letter)
    -   [Looping](#looping)
        -   [Append dataframes in a loop](#append-dataframes-in-a-loop)
        -   [Error Handling](#error-handling)
    -   [Dplyr](#dplyr)
        -   [Calculate means with grouping factor with
            dplyr](#calculate-means-with-grouping-factor-with-dplyr)
        -   [Add trial number in a long dataset with
            dplyr](#add-trial-number-in-a-long-dataset-with-dplyr)
    -   [System Administration Tasks](#system-administration-tasks)
        -   [Creating, deleting, copying files and
            directories](#creating-deleting-copying-files-and-directories)
        -   [Get the script’s directory
            path](#get-the-scripts-directory-path)
        -   [Select files according to characters that come before a
            symbol](#select-files-according-to-characters-that-come-before-a-symbol)
        -   [Select files that meet a
            criterion](#select-files-that-meet-a-criterion)

# R functions

This is a collection of functions typically used in R, especially in
psychology.

For any question, feel free to drop me an email :
<pupillo@psych.uni-frankfurt.de>

### Exclude rows based on a set of criteria (or participants)

Useful code to exclude rows in a dataset that corresponds to
participants we decided to exclude.

``` r
# create fake dataframe with participants and performance columns
df<-data.frame(participants=seq(1:10), performance= sample(seq(0, 1, length.out = 100), 10, T))

df
```

    ##    participants performance
    ## 1             1  0.51515152
    ## 2             2  0.75757576
    ## 3             3  0.02020202
    ## 4             4  0.24242424
    ## 5             5  0.91919192
    ## 6             6  0.07070707
    ## 7             7  0.43434343
    ## 8             8  0.04040404
    ## 9             9  0.35353535
    ## 10           10  0.49494949

``` r
# Participants that we want to exclude
partExcl<-c(1,3,5)

# code to exclude participants
df<-df[!df$participants %in% partExcl, ]

df
```

    ##    participants performance
    ## 2             2  0.75757576
    ## 4             4  0.24242424
    ## 6             6  0.07070707
    ## 7             7  0.43434343
    ## 8             8  0.04040404
    ## 9             9  0.35353535
    ## 10           10  0.49494949

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

    ## # A tibble: 11 × 2
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

### select items in a dataset that start or end with a string

``` r
# select variables that start with lh or rh
 datasub<-data[, c( grep( "^lh_", names(data), value = TRUE), 
             grep( "^rh_", names(data), value = TRUE))]

# select items that ends with "thickness"
datasub<-data[, c(grep("_thickness$", names(data), value = TRUE))]
```

### Select elements in a vector that start or end with a letter

``` r
# create a vector
vect<-c("One", "Two", "Three", "Four", "Five")

# select only the elements that start with the letter "T"
Tvect<-vect[grep("T", vect)]

Tvect
```

    ## [1] "Two"   "Three"

``` r
# select only the elements that end with the letter "e"
Evect<-vect[grep("e$", vect)]

Evect
```

    ## [1] "One"   "Three" "Five"

### Looping

Functions that are used for looping through data \#### create a progress
bar

``` r
# using the previous example of the boss dataset

# make a progress bar
pb<-txtProgressBar(min=0, max=length(selCat), style =3)
```

    ##   |                                                                              |                                                                      |   0%

``` r
for (cat in 1:length(selCat)){
  # subset the database
  dataim<- dataSel[dataSel$modal_categ==selCat[cat],]
  # order it
  dataim<-dataim[order(dataim$cat_agreement, decreasing = T),]
  # select first 40 
  datapractsel<-dataim[1:40,]
  # assign to a dataset
  assign(paste(selCat[cat],"_dat",sep=""), datapractsel)
  
  #progress bar
  setTxtProgressBar(pb, i) 
}
```

    ##   |                                                                              |======================================================================| 100%

#### Append dataframes in a loop

``` r
# first, create a list
my_list<-list()


# loop through the dataframes
for (dataframe in 1:length(dataframes)){
  
  currdataframe<-dataframes[datamframe]
  # assign a dataframe to the list
  my_list[[dataframe]]<-currdataframe
  
  
}

# merge the files outside the loop
all_df<-do.call(rbind, my_list)
```

#### Error Handling

Don’t stop the loop when there is an error, but catch the error

``` r
# create a variable with random numbers, but with a NA
randVar<-c(1,2,3,4,NA, 6,7)

# loop through it, checking whether it is bigger than 5
for (num in randVar){
  if (num>5){
    print(paste(num, "is bigger than five"))
  } else {
    print(paste(num, "is smaller than five"))
  }
}

# create a variable to store missing cases
missing_cases<-vector()

# and a counter to store them
miss_count<-1
# as you can see, it stops when there is a NA
# let's use 'tryCatch'
for (num in randVar){
  tryCatch({
    
    if (num>5){
      print(paste(num, "is bigger than five"))
    } else {
      print(paste(num, "is smaller than five"))
    }
  },
  
  # we want to cathc the error and print something informative,
  # like at which number it crashed
  error= function(e) {print(paste("problem with number", num))
    
    print(paste(num, "is not a number"))
    
    # we could also do something for this case
    # we could assign to a list of missing cases, so we can count them
    missing_cases[miss_count]<<-num
    # we need the superassignment operator "<<-" to assign this to the 
    # global environment
    
    # update the counter
    miss_count<<-miss_count+1
  }
  )
}

# how many missing cases?
length(missing_cases)
```

### Dplyr

#### Calculate means with grouping factor with dplyr

``` r
library(dplyr)
meanGroup<- category %>%
  group_by(modal_categ) %>%
  slice( (1:10)) %>% # take only the first 10
  summarise(meanCateg = mean(cat_agreement),
            Dataset = unique(Dataset)) # keep the other variable
```

    ## `summarise()` has grouped output by 'modal_categ'. You can override using the
    ## `.groups` argument.

``` r
meanGroup
```

    ## # A tibble: 48 × 3
    ## # Groups:   modal_categ [30]
    ##    modal_categ             meanCateg Dataset        
    ##    <chr>                       <dbl> <chr>          
    ##  1 Bird                        0.979 BOSS-2014 (v.2)
    ##  2 Bodypart                    0.795 BOSS-2014 (v.2)
    ##  3 Building infrastructure     0.669 BOSS-2014 (v.2)
    ##  4 Building material           0.596 BOSS-2014 (v.2)
    ##  5 Building material           0.596 BOSS-2010 (v.1)
    ##  6 Canine                      0.806 BOSS-2014 (v.2)
    ##  7 Clothing                    0.848 BOSS-2014 (v.2)
    ##  8 Clothing                    0.848 BOSS-2010 (v.1)
    ##  9 Crustacean                  0.668 BOSS-2014 (v.2)
    ## 10 Crustacean                  0.668 BOSS-2010 (v.1)
    ## # … with 38 more rows
    ## # ℹ Use `print(n = ...)` to see more rows

#### Add trial number in a long dataset with dplyr

``` r
# create data frame 
trial_df <- data.frame('subject' = c(rep('101', 3), rep('102', 3)),
                  'result' = c(32, 33, 64, 12, 18, 14))

# group by subject and add a row number
# assumes your data frame is ordered by trial number for each subject
trial_df <- trial_df %>%
  group_by(subject) %>%
  mutate(trial_number = row_number())
```

### System Administration Tasks

#### Creating, deleting, copying files and directories

``` r
# create a directory
dir.create("new_folder")

# create a file
# file.create("new_text_file.txt")
# file.create("new_word_file.docx")
# file.create("new_csv_file.csv")

# assign a matrix to those files
fileSample<-matrix(data =0, nrow = 2, ncol = 2)
write.table(fileSample,"new_text_file.txt", row.names = F )
write.table(fileSample,"new_text_file.docx", row.names = F )
write.csv(fileSample,"new_csv_file.csv", row.names = F )


# create lots of files
sapply(paste0("new_folder/file", 1:100, ".txt"), file.create)

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

#### Select files according to characters that come before a symbol

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
