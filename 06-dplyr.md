---
title: Data frame Manipulation with dplyr
teaching: 30
exercises: 10
source: Rmd
---

::::::::::::::::::::::::::::::::::::::: objectives

- To be able to use the six main dataframe manipulation 'verbs' with pipes in  `dplyr`.
- To understand how `group_by()` and `summarize()` can be combined to summarize datasets.
- Be able to analyze a subset of data using logical filtering.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I manipulate dataframes without repeating myself?

::::::::::::::::::::::::::::::::::::::::::::::::::

Let's begin by loading loading the Maine DMR kelp-urchin data for the whole 
coastline.


```r
dmr <- read.csv("data/dmr_kelp_urchin.csv") 
```

Manipulation of dataframes means many things to many researchers, we often
select certain observations (rows) or variables (columns), we often group the
data by a certain variable(s), or we even calculate summary statistics. We can
do these operations using the normal base R operations:


```r
mean(dmr[dmr$year == 2001, "kelp"])
```

```{.output}
[1] 41.36417
```

```r
mean(dmr[dmr$year == 2008, "kelp"])
```

```{.output}
[1] 55.89091
```

```r
mean(dmr[dmr$year == 2014, "kelp"])
```

```{.output}
[1] 39.21705
```

But this isn't very efficient, and can become tedious quickly because there is a
fair bit of repetition. Repeating yourself will cost you time, both now and
later, and potentially introduce some nasty bugs.

## The `dplyr` package

:::::::::::::::::::::::::::::::::::::::  instructor

- Introduce the `dplyr` package as a simpler, more intuitive way of doing
  subsetting.
- Unlike other SWC and DC R lessons, this lesson does **not** include data
  reshaping with `tidyr` as it isn't used in the rest of the workshop.

:::::::::::::::::::::::::::::::::::::::

Luckily, the [`dplyr`](https://dplyr.tidyverse.org) package provides a number of
very useful functions for manipulating dataframes in a way that will reduce the
above repetition, reduce the probability of making errors, and probably even
save you some typing. As an added bonus, you might even find the `dplyr` grammar
easier to read.

Here we're going to cover 6 of the most commonly used functions as well as using
pipes (`|>`) to combine them.

1. `select()`
2. `filter()`
3. `group_by()`
4. `summarize()`
5. `count()` and `n()`
6. `mutate()`

If you have have not installed this package earlier, please do so:


```r
install.packages('dplyr')
```

Now let's load the package:


```r
library("dplyr")
```

## Using `select()`

If, for example, we wanted to move forward with only a few of the variables in
our dataframe we could use the `select()` function. This will keep only the
variables you select.


```r
dmr_kelp <- select(dmr, year, region, kelp)
```

![](fig/13-dplyr-fig1.png){alt='Illustration of selecting two columns from a dataframe'}

If we open up `dmr_kelp` we'll see that it only contains the year,
region and kelp.

## The Pipe

Above we used 'normal' grammar, but the strengths of
`dplyr` lie in combining several functions using pipes. Since the pipes grammar
is unlike anything we've seen in R before, let's repeat what we've done above
using pipes.


```r
dmr <- dmr |> select(year, region, kelp)
```

To help you understand why we wrote that in that way, let's walk through it step
by step. First we summon the `dmr` data frame and pass it on, using the
pipe symbol `|>`, to the next step, which is the `select()` function. In this
case we don't specify which data object we use in the `select()` function since
in gets that from the previous pipe. Pipes can be used for more than just `dplyr`
functions. For example, what if we wanted the unique region names in `dmr`?


```r
dmr$region |> unique()
```

```{.output}
[1] "York"          "Casco Bay"     "Midcoast"      "Penobscot Bay"
[5] "MDI"           "Downeast"     
```

We can also chain pipes together. What if we wanted those unique region names
sorted?


```r
dmr$region |> unique() |> sort()
```

```{.output}
[1] "Casco Bay"     "Downeast"      "MDI"           "Midcoast"     
[5] "Penobscot Bay" "York"         
```

To make our code more readable when we chain operations together, we often
separate each step onto it's own line of code. 


```r
dmr$region |>
  unique() |>
  sort()
```

This has the secondary benefit of, if you're puzzing through a particularly 
difficult workflow, you can write it out in comments, with one step on each 
line, and then figure out what functions to use. Like as follows


```r
#----
# Step 1: write our what you want to do

# start with regions
  # get the unique values
  # sort them


#----
# Step 2. fill in the first bit

# start with regions
dmr$region
  # get the unique values
  # sort them

#----
# Step 3. fill in the second step after looking up ??unique

# start with regions
dmr$region |>
  # get the unique values
  unique()
  # sort them

#----
# Step 4. Bring it home after googling "how to make alphabetical in R"

# start with regions
dmr$region |>
  # get the unique values
  unique() |>
  # sort them
  sort()
```

:::::::::::::::::::::::::::::::::::::::::  callout

**Fun Fact**: You may have encountered
pipes before in the shell. In R, a pipe symbol is `|>` while in the shell it is
`|` but the concept is the same! 

The `|>` operator is relatively new in R as part of the base language. But, the
use of pipes has been around for longer. `dplyr` and many `tidyverse` libraries
adopted the pipe from the [magrittr](https://magrittr.tidyverse.org/) library
Danish data scientist Stefan Milton Bache and first released in 2014. You might
see `%>%` in code from experienced R uses who still use the older style pipe.
There are some subtle differences between the two, but for what we will be 
doing, `|>` will work just fine. Also, Hadley Wickham (the original author 
of dplyr and much of the tidyverse who popularized `%>%`) is [pro-base-pipe](https://twitter.com/hadleywickham/status/1519313737773158401).
:::::::::::::::::::::::::::::::::::::::::  


## Using `filter()`

If we now wanted to move forward with the above, but only with data from 2011
and kelp data, we can combine `select` and `filter`


```r
dmr_2011 <- dmr |>
  filter(year == 2011) |>
  select(year, region, kelp)
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 1

Write a single command (which can span multiple lines and includes pipes) that
will produce a dataframe that has the Midcoast values for `kelp`, `urchin`
and `year`, but not for other regions  How many rows does your dataframe
have and why?

:::::::::::::::  solution

## Solution to Challenge 1


```r
year_kelp_urchin_dmr <- dmr |>
                           filter(region=="Midcoast") |>
                           select(year,kelp,urchin)
```

```{.error}
Error in `select()`:
! Can't subset columns that don't exist.
✖ Column `urchin` doesn't exist.
```

```r
nrow(year_kelp_urchin_dmr)
```

```{.error}
Error in eval(expr, envir, enclos): object 'year_kelp_urchin_dmr' not found
```

249 rows, as they are only the Midcoast samples

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

As with last time, first we pass the dmr dataframe to the `filter()`
function, then we pass the filtered version of the dmr data frame to the
`select()` function. **Note:** The order of operations is very important in this
case. If we used 'select' first, filter would not be able to find the variable
continent since we would have removed it in the previous step.

## Using `group_by()` and `summarize()`

Now, we were supposed to be reducing the error prone repetitiveness of what can
be done with base R, but up to now we haven't done that since we would have to
repeat the above for each region Instead of `filter()`, which will only pass
observations that meet your criteria (in the above: `region`=="Midcoast"`), we
can use `group_by()`, which will essentially use every unique criteria that you
could have used in filter.


```r
class(dmr)
```

```{.output}
[1] "data.frame"
```

```r
dmr |> group_by(region) |> class()
```

```{.output}
[1] "grouped_df" "tbl_df"     "tbl"        "data.frame"
```

You will notice that the structure of the dataframe where we used `group_by()`
(`grouped_df`) is not the same as the original `dmr` (`data.frame`). A
`grouped_df` can be thought of as a `list` where each item in the `list`is a
`data.frame` which contains only the rows that correspond to the a particular
value `continent` (at least in the example above).

![](fig/13-dplyr-fig2.png){alt='Illustration of multiple dataframes created by piping a dataframe to group\_by'}

To see this more explicitly, try `str()` and see the information about groups 
at the end.


```r
dmr |> group_by(region) |> str()
```

```{.output}
gropd_df [1,478 × 3] (S3: grouped_df/tbl_df/tbl/data.frame)
 $ year  : int [1:1478] 2001 2001 2001 2001 2001 2001 2001 2001 2001 2001 ...
 $ region: chr [1:1478] "York" "York" "York" "York" ...
 $ kelp  : num [1:1478] 1.9 0 18.5 0.6 63.5 92.5 59 7.7 52.5 29.2 ...
 - attr(*, "groups")= tibble [6 × 2] (S3: tbl_df/tbl/data.frame)
  ..$ region: chr [1:6] "Casco Bay" "Downeast" "MDI" "Midcoast" ...
  ..$ .rows : list<int> [1:6] 
  .. ..$ : int [1:90] 6 7 8 9 10 11 12 92 93 94 ...
  .. ..$ : int [1:483] 65 66 67 68 69 70 71 72 73 74 ...
  .. ..$ : int [1:342] 44 45 46 47 48 49 50 51 52 53 ...
  .. ..$ : int [1:249] 13 14 15 16 17 18 19 20 21 22 ...
  .. ..$ : int [1:272] 32 33 34 35 36 37 38 39 40 41 ...
  .. ..$ : int [1:42] 1 2 3 4 5 90 91 170 171 172 ...
  .. ..@ ptype: int(0) 
  ..- attr(*, ".drop")= logi TRUE
```


## Using `summarize()`

The above was a bit on the uneventful side but `group_by()` is much more
exciting in conjunction with `summarize()`. This will allow us to create new
variable(s) by using functions that repeat for each of the continent-specific
data frames. That is to say, using the `group_by()` function, we split our
original dataframe into multiple pieces, then we can run functions
(e.g. `mean()` or `sd()`) within `summarize()`.


```r
kelp_by_region <- dmr |>
  group_by(region) |>
  summarize(mean_kelp = mean(kelp))

kelp_by_region
```

```{.output}
# A tibble: 6 × 2
  region        mean_kelp
  <chr>             <dbl>
1 Casco Bay          44.1
2 Downeast           67.0
3 MDI                46.9
4 Midcoast           40.5
5 Penobscot Bay      41.1
6 York               19.4
```

![](fig/13-dplyr-fig3.png){alt='illustration of creating a summary dataframe from grouped data'}

That allowed us to calculate the mean gdpPercap for each continent, but it gets
even better.

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 2

Calculate the average urchin abundance per country. Which region has the most 
urchins on average and which has the least?

:::::::::::::::  solution

## Solution to Challenge 2


```r
urchin_by_region <- dmr |>
   group_by(region) |>
   summarize(mean_urchins = mean(urchin))
```

```{.error}
Error in `summarize()`:
ℹ In argument: `mean_urchins = mean(urchin)`.
ℹ In group 1: `region = "Casco Bay"`.
Caused by error:
! object 'urchin' not found
```

```r
urchin_by_region |>
   filter(mean_urchins == min(mean_urchins) | mean_urchins == max(mean_urchins))
```

```{.error}
Error in eval(expr, envir, enclos): object 'urchin_by_region' not found
```

Another way to do this is to use the `dplyr` function `arrange()`, which
arranges the rows in a data frame according to the order of one or more
variables from the data frame.  It has similar syntax to other functions
from the `dplyr` package. You can use `desc()` inside `arrange()` to sort in
descending order.


```r
urchin_by_region |>
   arrange(mean_urchins) |>
   head(1)
```

```{.error}
Error in eval(expr, envir, enclos): object 'urchin_by_region' not found
```

```r
urchin_by_region |>
   arrange(desc(mean_urchins)) |>
   head(1)
```

```{.error}
Error in eval(expr, envir, enclos): object 'urchin_by_region' not found
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

The function `group_by()` allows us to group by multiple variables. Let's group by `year` and `region`.


```r
kelp_region_year <- dmr |>
  group_by(region, year) |>
  summarize(mean_kelp = mean(kelp))
```

```{.output}
`summarise()` has grouped output by 'region'. You can override using the
`.groups` argument.
```

That is already quite powerful, but it gets even better! You're not limited to defining 1 new variable in `summarize()`.


```r
kelp_urchin_region_year <- dmr |>
  group_by(region, year) |>
  summarize(mean_kelp = mean(kelp),
            mean_urchin = mean(urchin),
            sd_kelp = sd(kelp),
            sd_urchin = sd(urchin))
```

```{.error}
Error in `summarize()`:
ℹ In argument: `mean_urchin = mean(urchin)`.
ℹ In group 1: `region = "Casco Bay"`, `year = 2001`.
Caused by error:
! object 'urchin' not found
```

## `count()` and `n()`

A very common operation is to count the number of observations for each group.
The `dplyr` package comes with two related functions that help with this.

For instance, if we wanted to check the number of countries included in the
dataset for the year 2002, we can use the `count()` function. It takes the name
of one or more columns that contain the groups we are interested in, and we can
optionally sort the results in descending order by adding `sort=TRUE`:


```r
dmr |>
    filter(year == 2002) |>
    count(region, sort = TRUE)
```

```{.output}
         region  n
1      Downeast 25
2           MDI 17
3 Penobscot Bay 15
4      Midcoast 13
5     Casco Bay  8
6          York  2
```

If we need to use the number of observations in calculations, the `n()` function
is useful. For instance, if we wanted to get the standard error of urchins per region:


```r
dmr |>
    group_by(region) |>
    summarize(se_urchin = sd(urchin)/sqrt(n()))
```

```{.error}
Error in `summarize()`:
ℹ In argument: `se_urchin = sd(urchin)/sqrt(n())`.
ℹ In group 1: `region = "Casco Bay"`.
Caused by error:
! object 'urchin' not found
```


## Using `mutate()`

We can also create new variables prior to (or even after) summarizing information using `mutate()`. For example, if we wanted to create a `total_fleshy_algae`
column.


```r
dmr |>
  mutate(total_fleshy_algae = kelp + understory)
```

You can also create a second new column based on the first new column within the same call of `mutate()`:


```r
dmr |>
  mutate(total_fleshy_algae = kelp + understory,
         total_algae = total_fleshy_algae + crust)
```

## Using `mutate()` and `group_by()` together

In some cases, you might want to use `mutate()` in conjunction with `group_by()`
to get summarized properties for groups, but not lose your original data. for 
example, let's say we wanted to calculate how kelp differed from average 
conditions within a region each year. This is called an anomaly. In essence
we want to get the average amount of kelp in a region over time, and then
calculate a `kelp_anomaly_regional` where we subtract the amount of kelp from
the regional average over time.  This makes it easier to separate regional 
variation from variation over time.

This requires putting `mutate()` and `group_by()` together.


```r
dmr |>
  group_by(region) |>
  mutate(mean_kelp_regional = mean(kelp),
         kelp_anomaly_regional = kelp - mean_kelp_regional)
```

```{.output}
# A tibble: 1,478 × 5
# Groups:   region [6]
    year region     kelp mean_kelp_regional kelp_anomaly_regional
   <int> <chr>     <dbl>              <dbl>                 <dbl>
 1  2001 York        1.9               19.4               -17.5  
 2  2001 York        0                 19.4               -19.4  
 3  2001 York       18.5               19.4                -0.876
 4  2001 York        0.6               19.4               -18.8  
 5  2001 York       63.5               19.4                44.1  
 6  2001 Casco Bay  92.5               44.1                48.4  
 7  2001 Casco Bay  59                 44.1                14.9  
 8  2001 Casco Bay   7.7               44.1               -36.4  
 9  2001 Casco Bay  52.5               44.1                 8.35 
10  2001 Casco Bay  29.2               44.1               -14.9  
# ℹ 1,468 more rows
```

This worked, but, uh oh. Why is the data frame still grouped? Leaving a data 
frame grouped will have consequences, as any other mutate will be done 
**by region** instead of on the whole data frame. So, for example, if we wanted 
to then get a *global* anomaly - i.e., the difference between kelp and the 
average amount of kelp for the entire data set, we couldn't just do another set
of mutates. 

Fortunately, we can resolve this with `ungroup()` - a useful verb to insert any
time you want to remove all grouping structure, and are worried `dplyr` is not
doing it for you.


```r
dmr |>
  group_by(region) |>
  mutate(mean_kelp_regional = mean(kelp),
         kelp_anomaly_regional = kelp - mean_kelp_regional) |>
  ungroup() |>
  head()
```

```{.output}
# A tibble: 6 × 5
   year region     kelp mean_kelp_regional kelp_anomaly_regional
  <int> <chr>     <dbl>              <dbl>                 <dbl>
1  2001 York        1.9               19.4               -17.5  
2  2001 York        0                 19.4               -19.4  
3  2001 York       18.5               19.4                -0.876
4  2001 York        0.6               19.4               -18.8  
5  2001 York       63.5               19.4                44.1  
6  2001 Casco Bay  92.5               44.1                48.4  
```

Note, it does convert your data frame into a `tibble`, but if you really want a 
data frame back, you can use `as.data.frame()`.

<!-- 
:::::::::::::::::::::::::::::::::::::::  challenge

## Extra Challenge 4

Let's look at inerannual and spatial variability a bit. Calculate the urchin 
anomaly by  `year` and `exposure.code` (1 is fully protected from the open 
ocean, 5 is fully exposed). To figure out the most variable year and exposure,
calculate the difference between the biggest anomaly and smallest anomaly by 
year and exposure. What year and exposure code had the biggest difference?

One way to start this is to write out in comments what you want to do

```
# start with data
# group by year and exposure.code
# calculate urchin anomaly
# summarize min and max anomaly
# calculate difference between the two
# get the row with the biggest difference
```

:::::::::::::::  solution

## Solution to Challenge 2


```r
dmr |> 
  
  group_by(year, exposure.code) |>
  
  mutate(mean_urchins_exposure_year = mean(urchin),
         urchin_anomaly_exposure_year = urchin - mean_urchins_exposure_year) |>
  
  summarize(max_anomaly = max(urchin_anomaly_exposure_year),
            min_anomaly = min(urchin_anomaly_exposure_year),
            diff = max_anomaly - min_anomaly) |>

  ungroup() |>
  
  filter(diff == max(diff))
```

```{.error}
Error in `group_by()` at dplyr/R/mutate.R:146:2:
! Must group by variables found in `.data`.
✖ Column `exposure.code` is not found.
```


:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

-->

## Other great resources

- [R for Data Science](https://r4ds.had.co.nz/)
- [Data Wrangling Cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
- [Introduction to dplyr](https://cran.r-project.org/web/packages/dplyr/vignettes/dplyr.html)
- [Data wrangling with R and RStudio](https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/)

:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the `dplyr` package to manipulate dataframes.
- Use `select()` to choose variables from a dataframe.
- Use `filter()` to choose data based on values.
- Use `group_by()` and `summarize()` to work with subsets of data.
- Use `count()` and `n()` to obtain the number of observations in columns.
- Use `mutate()` to create new variables.

::::::::::::::::::::::::::::::::::::::::::::::::::


