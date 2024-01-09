---
title: Data Structures
teaching: 40
exercises: 15
source: Rmd
---

::::::::::::::::::::::::::::::::::::::: objectives

- To be aware of the different types of data.
- To begin exploring data frames, and understand how they are related to vectors and factors.
- To be able to ask questions from R about the type, class, and structure of an object.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I read data in R?
- What are the basic data types in R?
- How do I represent categorical information in R?

::::::::::::::::::::::::::::::::::::::::::::::::::



One of R's most powerful features is its ability to deal with tabular data,
such as you may already have in a spreadsheet or a CSV file. Let's start by
downloading and reading in a file `casco_kelp_urchin.csv`. We will
save this data as an object named `casco_dmr`:


```r
casco_dmr <- read.csv("data/casco_kelp_urchin.csv")
```

The `read.table` function is used for reading in tabular data stored in a text
file where the columns of data are separated by punctuation characters such as
tabs (tab-delimited, sometimes with .txt or .tsv extensions) or commas (comma-delimited 
values, often with .csv extensions).
For convenience R provides 2 other versions of `read.table`. These are: `read.csv`
for files where the data are separated with commas and `read.delim` for files
where the data are separated with tabs. Of these three functions `read.csv` is
the most commonly used.  If needed it is possible to override the default
delimiting punctuation marks for both `read.csv` and `read.delim`.

:::::::::::::::::::::::::::::::::::::::::  callout

## Miscellaneous Tips

- Files can also be downloaded directly from the Internet into a local folder
  of your choice onto your computer using the `download.file` function. The
  `read.csv` function can then be executed to read the downloaded file from the
  download location, for example,


```r
download.file("https://cobalt-casco.github.io/r-intro-geospatial/data/casco_kelp_urchin.csv",
              destfile = "data/casco_kelp_urchin.csv")
casco_dmr <- read.csv("data/casco_kelp_urchin.csv")
```

- Alternatively, you can also read in files directly into R from the Internet
  by replacing the file paths with a web address in `read.csv`. One should
  note that in doing this no local copy of the csv file is first saved onto
  your computer. For example,


```r
casco_dmr <- read.csv("https://cobalt-casco.github.io/r-intro-geospatial/data/casco_kelp_urchin.csv") 
```

- You can read directly from excel spreadsheets without
  converting them to plain text first by using the [readxl](https://cran.r-project.org/package=readxl) package.
  

::::::::::::::::::::::::::::::::::::::::::::::::::

We can begin exploring our dataset right away, pulling out columns by specifying
them using the `$` operator:


```r
casco_dmr$year
```

```{.output}
 [1] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 2002 2002 2002 2002 2002
[16] 2003 2003 2003 2003 2003 2003 2004 2004 2004 2004 2004 2004 2005 2005 2005
[31] 2005 2005 2005 2005 2005 2006 2006 2006 2006 2006 2006 2006 2006 2006 2006
[46] 2007 2007 2007 2007 2007 2007 2007 2008 2008 2008 2008 2008 2008 2009 2009
[61] 2009 2009 2009 2009 2009 2010 2010 2010 2010 2010 2010 2010 2010 2011 2011
[76] 2011 2011 2011 2011 2011 2011 2012 2014 2014 2014 2014 2014 2014 2014 2014
```

```r
casco_dmr$kelp
```

```{.output}
 [1]  92.5  59.0   7.7  52.5  29.2 100.0   0.8  87.5  13.0  86.5  96.5  65.0
[13]   5.0   0.0 100.0  64.0  81.0 100.0  56.0  19.5  31.5  55.0  80.5  68.5
[25]  43.0  50.0   9.5  30.0  49.0  79.5  44.5  46.5  24.0  50.5  30.5  42.0
[37]  49.5  51.0  39.0   0.5  71.0  11.0  33.5  75.0  82.5   0.5  35.0   8.5
[49]  55.5  26.0  87.0  32.5   5.0  16.5 100.0  22.0  97.0  39.0   1.5  71.0
[61]  10.5  63.0  73.5  70.5  67.5  17.5   7.5   7.0   0.0  69.5  13.5   2.0
[73]  41.5   7.5  74.5  62.5  76.5  74.5   4.5  71.0   9.5   0.5  46.5  41.5
[85]  55.0  11.0  63.5  14.5  25.5  31.0
```

We can do other operations on the columns. For example, if we discovered that our data were actually collected two years later:


```r
casco_dmr$year + 2
```

```{.output}
 [1] 2003 2003 2003 2003 2003 2003 2003 2004 2004 2004 2004 2004 2004 2004 2004
[16] 2005 2005 2005 2005 2005 2005 2006 2006 2006 2006 2006 2006 2007 2007 2007
[31] 2007 2007 2007 2007 2007 2008 2008 2008 2008 2008 2008 2008 2008 2008 2008
[46] 2009 2009 2009 2009 2009 2009 2009 2010 2010 2010 2010 2010 2010 2011 2011
[61] 2011 2011 2011 2011 2011 2012 2012 2012 2012 2012 2012 2012 2012 2013 2013
[76] 2013 2013 2013 2013 2013 2013 2014 2016 2016 2016 2016 2016 2016 2016 2016
```

As an aside, did this change the original year data? How would you check?

But what about:


```r
casco_dmr$year + casco_dmr$region
```

```{.error}
Error in casco_dmr$year + casco_dmr$region: non-numeric argument to binary operator
```

Understanding what happened here is key to successfully analyzing data in R.

## Data Types

::::::::::::::::::::::::::::::::::::::: instructor

- Learners will work with factors in the following lesson. Be sure to
  cover this concept.
  
:::::::::::::::::::::::::::::::::::::::

If you guessed that the last command will return an error because `2008` plus
`"Casco Bay"` is nonsense, you're right - and you already have some intuition for an
important concept in programming called *data classes*. We can ask what class of
data something is:


```r
class(casco_dmr$year)
```

```{.output}
[1] "integer"
```

There are 6 main types: `numeric`, `integer`, `complex`, `logical`, `character`, and `factor`.


```r
class(3.14)
```

```{.output}
[1] "numeric"
```

```r
class(1L) # The L suffix forces the number to be an integer, since by default R uses float numbers
```

```{.output}
[1] "integer"
```

```r
class(1+1i)
```

```{.output}
[1] "complex"
```

```r
class(TRUE)
```

```{.output}
[1] "logical"
```

```r
class('banana')
```

```{.output}
[1] "character"
```

```r
class(factor('banana'))
```

```{.output}
[1] "factor"
```
The types `numeric`, `integer`, and `complex` are all numbers, although they are stored differently
and have different mathematical properties. `logical` type data include only `TRUE` and `FALSE` values, 
while `character` type data can contain any kind of characters. Finally, `factor` is a special type
that was built to help us store categorical variables, variables that have a fixed and known set of 
possible values. We'll talk more about them in a little bit.

No matter how complicated our analyses become, all data in R is interpreted a specific
data class. This strictness has some really important consequences.

Let's say that a collaborator sends you an updated data file named `data/casco_kelp_urchin_2.csv`.

Load the new data file as `casco_dmr_2`, and check what class of data we find in the
`year` column:


```r
casco_dmr_2 <- read.csv("data/casco_kelp_urchin_2.csv")
class(casco_dmr_2$year)
```

```{.output}
[1] "character"
```

Oh no, our year data aren't the numeric type anymore! If we try to do the same math
we did on them before, we run into trouble:


```r
casco_dmr_2$year + 2
```

```{.error}
Error in casco_dmr_2$year + 2: non-numeric argument to binary operator
```

What happened? When R reads a csv file into one of these tables, it insists that 
everything in a column be the same class; if it can't understand *everything* 
in the column as numeric, then *nothing* in the column gets to be numeric. The 
table that R loaded our data into is something called a dataframe, and it is our 
first example of something called a *data structure*, that is, a structure 
which R knows how to build out of the basic data types.

We can see that it is a dataframe by calling the `class()` function on it:


```r
class(casco_dmr)
```

```{.output}
[1] "data.frame"
```

In order to successfully use our data in R, we need to understand what the basic
data structures are, and how they behave. Note: in this lesson we will not cover 
lists, which are a basic data structure in R. You can learn more about them [here] (https://r4ds.hadley.nz/rectangling.html#lists).

## Vectors and Type Coercion

To better understand this behavior, let's meet another of the data structures:
the vector.


```r
my_vector <- vector(length = 3)
my_vector
```

```{.output}
[1] FALSE FALSE FALSE
```

A vector in R is essentially an ordered list of things, with the special
condition that everything in the vector must be the same basic data type. If
you don't choose the data type, it'll default to `logical`; or, you can declare
an empty vector of whatever type you like.


```r
another_vector <- vector(mode = 'character', length = 3)
another_vector
```

```{.output}
[1] "" "" ""
```

You can check if something is a vector:


```r
str(another_vector)
```

```{.output}
 chr [1:3] "" "" ""
```

The somewhat cryptic output from this command indicates the basic data type
found in this vector (in this case `chr` or character), an indication of the
number of things in the vector (the indexes of the vector, in this
case: `[1:3]`), and a few examples of what's actually in the vector (in this case
empty character strings). If we similarly do:


```r
str(casco_dmr$year)
```

```{.output}
 int [1:90] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 ...
```

we see that `casco_dmr$year` is a vector, too! The columns of data we load into R
data frames are all vectors, and that's the root of why R forces everything in
a column to be the same basic data type.

::::::::::::::::::::::::::::::::::::::  discussion

## Discussion 1

Why is R so opinionated about what we put in our columns of data?
How does this help us?

:::::::::::::::  solution

## Discussion 1

By keeping everything in a column the same, we allow ourselves to make simple
assumptions about our data; if you can interpret one entry in the column as a
number, then you can interpret *all* of them as numbers, so we don't have to
check every time. This consistency is what people mean when they talk about
*clean data*; in the long run, strict consistency goes a long way to making
our lives easier in R.


:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

You can also make vectors with explicit contents with the combine function:


```r
combine_vector <- c(2, 6, 3)
combine_vector
```

```{.output}
[1] 2 6 3
```

We can see what is at a certain index of a vector using the `[]` notation. 
For example, what is the second element of `combine_vector`?


```r
combine_vector[2]
```

```{.output}
[1] 6
```

## Type Coercion

Given what we've learned so far, what do you think the following will produce?


```r
quiz_vector <- c(2, 6, '3')
```

This is something called *type coercion*, and it is the source of many surprises
and the reason why we need to be aware of the basic data types and how R will
interpret them. When R encounters a mix of types (here numeric and character) to
be combined into a single vector, it will force them all to be the same
type. Consider:


```r
coercion_vector <- c('a', TRUE)
coercion_vector
```

```{.output}
[1] "a"    "TRUE"
```

```r
another_coercion_vector <- c(0, TRUE)
another_coercion_vector
```

```{.output}
[1] 0 1
```

The coercion rules go: `logical` -> `integer` -> `numeric` -> `complex` ->
`character`, where -> can be read as *are transformed into*. You can try to
force coercion against this flow using the `as.` functions:


```r
character_vector_example <- c('0', '2', '4')
character_vector_example
```

```{.output}
[1] "0" "2" "4"
```

```r
character_coerced_to_numeric <- as.numeric(character_vector_example)
character_coerced_to_numeric
```

```{.output}
[1] 0 2 4
```

```r
numeric_coerced_to_logical <- as.logical(character_coerced_to_numeric)
numeric_coerced_to_logical
```

```{.output}
[1] FALSE  TRUE  TRUE
```

As you can see, some surprising things can happen when R forces one basic data
type into another! Nitty-gritty of type coercion aside, the point is: if your
data doesn't look like what you thought it was going to look like, type coercion
may well be to blame; make sure everything is the same type in your vectors and
your columns of data frames, or you will get nasty surprises!

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 1

Given what you now know about type conversion, look at the class of
data in `casco_dmr$year` and compare it with `casco_dmr_2$year`. Why are
these columns different classes?

:::::::::::::::  solution

## Solution


```r
str(casco_dmr$year)
```

```{.output}
 int [1:90] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 ...
```

```r
str(casco_dmr_2$year)
```

```{.output}
 chr [1:90] "year 2001" "2001" "2001" "2001" "2001" "2001" "2001" "2002" ...
```

The data in `casco_dmr_2$year` is stored as a character vector, rather than as
a numeric vector. This is because of the "year" character string in the first
data point.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

The combine function, `c()`, will also append things to an existing vector:


```r
ab_vector <- c('a', 'b')
ab_vector
```

```{.output}
[1] "a" "b"
```

```r
combine_example <- c(ab_vector, 'DC')
combine_example
```

```{.output}
[1] "a"  "b"  "DC"
```

You can also make series of numbers:


```r
my_series <- 1:10
my_series
```

```{.output}
 [1]  1  2  3  4  5  6  7  8  9 10
```

```r
seq(10)
```

```{.output}
 [1]  1  2  3  4  5  6  7  8  9 10
```

```r
seq(1,10, by = 0.1)
```

```{.output}
 [1]  1.0  1.1  1.2  1.3  1.4  1.5  1.6  1.7  1.8  1.9  2.0  2.1  2.2  2.3  2.4
[16]  2.5  2.6  2.7  2.8  2.9  3.0  3.1  3.2  3.3  3.4  3.5  3.6  3.7  3.8  3.9
[31]  4.0  4.1  4.2  4.3  4.4  4.5  4.6  4.7  4.8  4.9  5.0  5.1  5.2  5.3  5.4
[46]  5.5  5.6  5.7  5.8  5.9  6.0  6.1  6.2  6.3  6.4  6.5  6.6  6.7  6.8  6.9
[61]  7.0  7.1  7.2  7.3  7.4  7.5  7.6  7.7  7.8  7.9  8.0  8.1  8.2  8.3  8.4
[76]  8.5  8.6  8.7  8.8  8.9  9.0  9.1  9.2  9.3  9.4  9.5  9.6  9.7  9.8  9.9
[91] 10.0
```

We can ask a few questions about vectors:


```r
sequence_example <- seq(10)
head(sequence_example,n = 2)
```

```{.output}
[1] 1 2
```

```r
tail(sequence_example, n = 4)
```

```{.output}
[1]  7  8  9 10
```

```r
length(sequence_example)
```

```{.output}
[1] 10
```

```r
class(sequence_example)
```

```{.output}
[1] "integer"
```

Finally, you can give names to elements in your vector:


```r
my_example <- 5:8
names(my_example) <- c("a", "b", "c", "d")
my_example
```

```{.output}
a b c d 
5 6 7 8 
```

```r
names(my_example)
```

```{.output}
[1] "a" "b" "c" "d"
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 2

Start by making a vector with the numbers 1 through 26.
Multiply the vector by 2, and give the resulting vector
names A through Z (hint: there is a built in vector called `LETTERS`)

:::::::::::::::  solution

## Solution to Challenge 2


```r
x <- 1:26
x <- x * 2
names(x) <- LETTERS
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Factors

We said that columns in data frames were vectors:


```r
str(casco_dmr$year)
```

```{.output}
 int [1:90] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 ...
```

```r
str(casco_dmr$kelp)
```

```{.output}
 num [1:90] 92.5 59 7.7 52.5 29.2 100 0.8 87.5 13 86.5 ...
```

```r
str(casco_dmr$region)
```

```{.output}
 chr [1:90] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" ...
```

One final important data structure in R is called a "factor" (that special data 
type we mentioned above). Factors look like character data, but are used to 
represent data where each element of the vector must be one of a limited number 
of "levels". To phrase that another way, factors are an "enumerated" type where 
there are a finite number of pre-defined values that your vector can have. 

For example, let's make a character vector with all the sampling regions in the DMR
kelp data:


```r
maine_regions <- c("York", "Casco Bay", "Midcoast", "Penobscot Bay", "MDI", "Downeast")
maine_regions
```

```{.output}
[1] "York"          "Casco Bay"     "Midcoast"      "Penobscot Bay"
[5] "MDI"           "Downeast"     
```

```r
str(maine_regions)
```

```{.output}
 chr [1:6] "York" "Casco Bay" "Midcoast" "Penobscot Bay" "MDI" "Downeast"
```

We can turn a vector into a factor like so:


```r
categories <- factor(maine_regions)
class(maine_regions)
```

```{.output}
[1] "character"
```

```r
str(maine_regions)
```

```{.output}
 chr [1:6] "York" "Casco Bay" "Midcoast" "Penobscot Bay" "MDI" "Downeast"
```

Now R has noticed that there are 6 possible categories in our data, but it
also did something surprising. Instead of printing out the strings we gave it,
we got a bunch of numbers instead. R has replaced our human-readable categories
with numbered indices under the hood! This is necessary as many statistical
calculations utilize such numerical representations for categorical data:


```r
class(maine_regions)
```

```{.output}
[1] "character"
```

```r
class(categories)
```

```{.output}
[1] "factor"
```


:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 3

Convert the `region` column of our `casco_dmr` data frame to a factor. Then try
converting it back to a character vector. 

Now try converting `year` in our `casco_dmr` data frame to a factor, then back
to a numeric vector. What happens if you use `as.numeric()`?

Remember that you can always reload the `casco_dmr` data frame using 
`read.csv("data/casco_kelp_urchin.csv")` if you accidentally mess up your data!

:::::::::::::::  solution

## Solution to Challenge 3

Converting character vectors to factors can be done using the `factor()` 
function:


```r
casco_dmr$region <- factor(casco_dmr$region)
casco_dmr$region
```

```{.output}
 [1] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
 [8] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[15] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[22] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[29] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[36] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[43] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[50] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[57] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[64] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[71] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[78] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
[85] Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay Casco Bay
Levels: Casco Bay
```

You can convert these back to character vectors using `as.character()`:


```r
casco_dmr$region <- as.character(casco_dmr$region)
casco_dmr$region
```

```{.output}
 [1] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
 [7] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[13] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[19] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[25] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[31] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[37] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[43] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[49] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[55] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[61] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[67] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[73] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[79] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
[85] "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay" "Casco Bay"
```

You can convert numeric vectors to factors in the exact same way:


```r
casco_dmr$year <- factor(casco_dmr$year)
casco_dmr$year
```

```{.output}
 [1] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 2002 2002 2002 2002 2002
[16] 2003 2003 2003 2003 2003 2003 2004 2004 2004 2004 2004 2004 2005 2005 2005
[31] 2005 2005 2005 2005 2005 2006 2006 2006 2006 2006 2006 2006 2006 2006 2006
[46] 2007 2007 2007 2007 2007 2007 2007 2008 2008 2008 2008 2008 2008 2009 2009
[61] 2009 2009 2009 2009 2009 2010 2010 2010 2010 2010 2010 2010 2010 2011 2011
[76] 2011 2011 2011 2011 2011 2011 2012 2014 2014 2014 2014 2014 2014 2014 2014
Levels: 2001 2002 2003 2004 2005 2006 2007 2008 2009 2010 2011 2012 2014
```

But be careful -- you can't use `as.numeric()` to convert factors to numerics!


```r
as.numeric(casco_dmr$year)
```

```{.output}
 [1]  1  1  1  1  1  1  1  2  2  2  2  2  2  2  2  3  3  3  3  3  3  4  4  4  4
[26]  4  4  5  5  5  5  5  5  5  5  6  6  6  6  6  6  6  6  6  6  7  7  7  7  7
[51]  7  7  8  8  8  8  8  8  9  9  9  9  9  9  9 10 10 10 10 10 10 10 10 11 11
[76] 11 11 11 11 11 11 12 13 13 13 13 13 13 13 13
```

Instead, `as.numeric()` converts factors to those "numbers under the hood" we 
talked about. To go from a factor to a number, you need to first turn the factor
into a character vector, and _then_ turn that into a numeric vector:


```r
casco_dmr$year <- as.character(casco_dmr$year)
casco_dmr$year <- as.numeric(casco_dmr$year)
casco_dmr$year
```

```{.output}
 [1] 2001 2001 2001 2001 2001 2001 2001 2002 2002 2002 2002 2002 2002 2002 2002
[16] 2003 2003 2003 2003 2003 2003 2004 2004 2004 2004 2004 2004 2005 2005 2005
[31] 2005 2005 2005 2005 2005 2006 2006 2006 2006 2006 2006 2006 2006 2006 2006
[46] 2007 2007 2007 2007 2007 2007 2007 2008 2008 2008 2008 2008 2008 2009 2009
[61] 2009 2009 2009 2009 2009 2010 2010 2010 2010 2010 2010 2010 2010 2011 2011
[76] 2011 2011 2011 2011 2011 2011 2012 2014 2014 2014 2014 2014 2014 2014 2014
```

Note: new students find the help files difficult to understand; make sure to let them know
that this is typical, and encourage them to take their best guess based on semantic meaning,
even if they aren't sure.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

When doing statistical modelling, it's important to know what the baseline
levels are. This is assumed to be the first factor, but by default factors are
labeled in alphabetical order. You can change this by specifying the levels:


```r
treatment <- c("case", "control", "control", "case")
factor_ordering_example <- factor(treatment, levels = c("control", "case"))
str(factor_ordering_example)
```

```{.output}
 Factor w/ 2 levels "control","case": 2 1 1 2
```

In this case, we've explicitly told R that "control" should represented by 1,
and "case" by 2. This designation can be very important for interpreting the
results of statistical models!

To know what the levels map to, we can use `levels()` for factors. 
To do the same for characters, we can use `unique()`.


```r
levels(factor_ordering_example)
```

```{.output}
[1] "control" "case"   
```

```r
unique(mydata)
```

```{.error}
Error in eval(expr, envir, enclos): object 'mydata' not found
```

Note that the order is different - for `unique()` it's based on the order of 
observation in the vector. For levels, it's been set. If we want to sort from
`unique()`, which can be very useful, we can try


```r
non_alpha_vector <- c("b", "a", "c")

unique(non_alpha_vector)
```

```{.output}
[1] "b" "a" "c"
```

```r
sort(unique(non_alpha_vector) )
```

```{.output}
[1] "a" "b" "c"
```



:::::::::::::::::::::::::::::::::::::::: keypoints

- Use `read.csv` to read tabular data in R.
- The basic data types in R are numeric, integer, complex, logical, character, and factor.
- Dataframes store columns of the same data type as vectors.
- Use characters and factors to represent categories in R.

::::::::::::::::::::::::::::::::::::::::::::::::::


