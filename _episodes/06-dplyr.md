---
title: Manipulating tibbles with dplyr
teaching: 40
exercises: 15
questions:
- "How can I manipulate tibbles without repeating myself?"
objectives:
- " To be able to use the six main `dplyr` data manipulation 'verbs' with pipes."
keypoints:
- "Use the `dplyr` package to manipulate tibbles."
- "Use `select()` to choose variables from a tibbles."
- "Use `filter()` to choose data based on values."
- "Use `group_by()` and `summarize()` to work with subsets of data."
- "Use `mutate()` to create new variables."
source: Rmd
---



We used the `readr` package to load tabular data into a tibble within R.  The `readr` package is part of a family of packages known as the   [tidyverse](http://tidyverse.org/).  As with `readr`, much of of the functionality of the `tidyverse` already exists in base R, or in other libraries.  What differentiates the tidyverse from a mixture of base R and ad-hoc libraries is that it its packages are designed to work well together.  They provide a modern and streamlined approach to data-analysis, and deal with some of the idiosyncrasies of base R.

If yuu are using a university PC the tidyverse should already be installed.  If not, it can be installed like any other R package:


~~~
install.packages("tidyverse")
~~~
{: .r}

We load the core libraries in the tidyverse with:


~~~
library("tidyverse")
~~~
{: .r}



~~~
Loading tidyverse: ggplot2
Loading tidyverse: tibble
Loading tidyverse: tidyr
Loading tidyverse: readr
Loading tidyverse: purrr
Loading tidyverse: dplyr
~~~
{: .output}



~~~
Conflicts with tidy packages ----------------------------------------------
~~~
{: .output}



~~~
filter(): dplyr, stats
lag():    dplyr, stats
~~~
{: .output}

There are other [libraries included](https://github.com/tidyverse/tidyverse) but these are less widely used, and must be loaded manually if they are required.  We won't cover most of these in the course. 

Let's dive in and look at how we can use the tidyverse to analyse and plot data from the [gapminder project](https://www.gapminder.org/).   Download the data from the [setup page]({{ page.root }}/setup), and decompress the contents to your data directory.  Take a look at it using a text editor such as notepad.   The first line contains variable names, and values are separated by commas.  Each record starts on a new line. 

As with the [lesson on data structures]({{ page.root }}/04-data-structures-part1) we use the `read_csv()` function to load data from a comma separated file:


~~~
gapminder <- read_csv("./data/gapminder-FiveYearData.csv")
~~~
{: .r}



~~~
Parsed with column specification:
cols(
  country = col_character(),
  year = col_integer(),
  pop = col_double(),
  continent = col_character(),
  lifeExp = col_double(),
  gdpPercap = col_double()
)
~~~
{: .output}

As we discussed in [lesson 4]({{ page.root }}/04-data-structures-part1/), variables in R can be character, integer, double, etc.   A tibble (and R's built in equivalent; the data-frame) require that all the values in a particular column have the same data type.  The `read_csv()` function will attempt to infer the data type of each column, and prints the column types it has guessed to the screen.  If the wrong column types have been generated, you can pass the `col_types=` option to `read_csv()`.  

For example, if we wanted to load `pop` as a character string, we would use:


~~~
gapminderPopChar <- read_csv("./data/gapminder-FiveYearData.csv", 
                             col_types = cols(
                               country = col_character(),
                               year = col_integer(),
                               pop = col_character(),
                               continent = col_character(),
                               lifeExp = col_double(),
                               gdpPercap = col_double()
) )
~~~
{: .r}

> ## Setting column types
> 
> Try reading a file using the `read_csv()` defaults (i.e. guessing column types).
> If this fails you can cut and paste the guessed column specification, and modify
> this with the correct column types.  It is good practice to do this anyway; it makes
> the data types of your columns explicit, and will help protect you if the format 
> of your data changes.
{: .callout}

## Manipulating data frames

Manipulation of data frames means many things to many researchers, we often
select certain observations (rows) or variables (columns), we often group the
data by a certain variable(s), or calculating summary statistics. 

## The `dplyr` package

The  [`dplyr`](https://cran.r-project.org/web/packages/dplyr/dplyr.pdf)
package is part of the tidyverse.  It provides a number of very useful functions for manipulating data frames
in a way that will reduce repetition, reduce the probability of making
errors, and probably even save you some typing. 

We will cover:

1. selecting variables with `select()`
2. subsetting observations with `filter()`
3. grouping observations with `group_by()`
4. generating summary statistics using `summarize()`
5. generating new variables using `mutate()`
6. chaining operations together using pipes `%>%` 

## Using select()

If, for example, we wanted to move forward with only a few of the variables in
our data frame we use the `select()` function. This will keep only the
variables you select.


~~~
year_country_gdp <- select(gapminder,year,country,gdpPercap)
~~~
{: .r}

![](../fig/13-dplyr-fig1.png)

## Using pipes and dplyr

The pipe operator `%>%` lets us pipe the output of one command into the next.   This allows us to build up a data-processing pipeline.  This approach has several advantages:

* We can build the pipeline piecemeal - building the pipeline step-by-step is easier than trying to 
perform a complex series of operations in one go
* It is easy to modify and reuse the pipeline
* We don't have to make temporary tibbles as the analysis progresses
* Although we don't cover it in this course, dplyr can interact with SQL databases; this can be very useful if we are working with data-sets that are too large to load in memory.

> ## Pipelines and the shell
>
> If you're familiar with the Unix shell, you may already have used pipes to
> pass the output from one command to the next.  The concept is the same, except
> we use the `|` character rather than R's pipe operator `%>%`
{: .callout}


> ## Keyboard shortcuts and getting help
> 
> The pipe operator can be tedious to type.  In Rstudio pressing `ctrl+shift+m` under
> Windows / Linux will insert the pipe operator.  On the mac, use FIXME xxxx.
>
> We can use tab completion to complete variable names when entering commands.
> This saves typing and reduces the risk of error.
> 
> RStudio includes a helpful "cheat sheet", which summarises the main functionality
> and syntax of `dplyr` and `tidyr` (covered in FIXME xxx lesson).  This can be accessed via the
> help menu --> cheatsheets --> data manipulation with dplyr, tidyr 
{: .callout}

Let's rewrite the previous command using the pipe operator:


~~~
year_country_gdp <- gapminder %>% select(year,country,gdpPercap)
~~~
{: .r}

To help you understand why we wrote that in that way, let's walk through it step
by step. First we summon the gapminder data frame and pass it on, using the pipe
symbol `%>%`, to the next step, which is the `select()` function. In this case
we don't specify which data object we use in the `select()` function since in
gets that from the previous pipe. 

## Using filter()

If we now wanted to move forward with the above, but only with European
countries, we can combine `select` and `filter`


~~~
year_country_gdp_euro <- gapminder %>%
    filter(continent=="Europe") %>%
    select(year,country,gdpPercap)
~~~
{: .r}

> ## Challenge 1
>
> Write a single command (which can span multiple lines and includes pipes) that
> will produce a dataframe that has the African values for `lifeExp`, `country`
> and `year`, but not for other Continents.  How many rows does your data frame
> have?
>
> > ## Solution to Challenge 1
> >
> >~~~
> >year_country_lifeExp_Africa <- gapminder %>%
> >                            filter(continent=="Africa") %>%
> >                            select(year,country,lifeExp)
> > nrow(year_country_lifeExp_Africa)
> >~~~
> >{: .r}
> >
> >
> >
> >~~~
> >[1] 624
> >~~~
> >{: .output}
> > As with last time, first we pass the gapminder dataframe to the `filter()`
> > function, then we pass the filtered version of the gapminder dataframe to the
> > `select()` function. **Note:** The order of operations is very important in this
> > case. If we used 'select' first, filter would not be able to find the variable
> > continent since we would have removed it in the previous step.
> {: .solution}
{: .challenge}

## Generating new variables

The `mutate()` function lets us add new variables to our tibble.  It will often be the case that these are variables we _derive_ from existing variables in the data-frame. 

As an example, the gapminder data contains the population of each country, and its GDP per capita.  We can use this to calculate the total GDP of each country:


~~~
gapminder_totalgdp <- gapminder %>% 
  mutate(gdp = gdpPercap * pop)
~~~
{: .r}

> ## Challenge 2
> 
> Create a tibble containing each country in Europe, its life expectancy in 2007
> and the rank of the country's life expectancy. Can you reverse the ranking order
> so that the country with the longest life expectancy gets the lowest rank?
>
> (It is not necessary to sort the countries by rank - we will sorting tibbles
> shortly)
> 
> Hint: First filter and then mutate the data.  The cheat-sheet contains useful
> functions you can use when you make new variables.
>
> > ## Solution to challenge 2
> > 
> > ~~~
> > europeLifeExp <- gapminder %>% 
> >   filter(continent == "Europe", year == 2007) %>% 
> >   select(country, lifeExp) %>% 
> >   mutate(rank = min_rank(lifeExp))
> > print(europeLifeExp, n=100)
> > ~~~
> > {: .r}
> > 
> > 
> > 
> > ~~~
> > # A tibble: 30 x 3
> >                   country lifeExp  rank
> >                     <chr>   <dbl> <int>
> >  1                Albania  76.423    11
> >  2                Austria  79.829    23
> >  3                Belgium  79.441    20
> >  4 Bosnia and Herzegovina  74.852     8
> >  5               Bulgaria  73.005     3
> >  6                Croatia  75.748    10
> >  7         Czech Republic  76.486    12
> >  8                Denmark  78.332    15
> >  9                Finland  79.313    17
> > 10                 France  80.657    26
> > 11                Germany  79.406    18
> > 12                 Greece  79.483    21
> > 13                Hungary  73.338     4
> > 14                Iceland  81.757    30
> > 15                Ireland  78.885    16
> > 16                  Italy  80.546    25
> > 17             Montenegro  74.543     6
> > 18            Netherlands  79.762    22
> > 19                 Norway  80.196    24
> > 20                 Poland  75.563     9
> > 21               Portugal  78.098    14
> > 22                Romania  72.476     2
> > 23                 Serbia  74.002     5
> > 24        Slovak Republic  74.663     7
> > 25               Slovenia  77.926    13
> > 26                  Spain  80.941    28
> > 27                 Sweden  80.884    27
> > 28            Switzerland  81.701    29
> > 29                 Turkey  71.777     1
> > 30         United Kingdom  79.425    19
> > ~~~
> > {: .output}
> > 
> > To reverse the order of the ranking, use the `desc` function, i.e.
> > `mutate(rank = min_rank(desc(lifeExp)))`
> {: .solution}
{: .challenge}

## Calculating summary statistics

We often wish to calculate a summary statistic (the mean, standard deviation, etc.)
for a variable.  We frequently want to calculate a separate summary statistic for several
groups of data (e.g. the experiment and control group).    We can calculate a summary statistic
for the whole data-set using the dplyr's `summarise()` function:


~~~
gapminder %>% 
  filter(year == 2007) %>% 
  summarise(meanlife = mean(lifeExp), medianlife = median(lifeExp))
~~~
{: .r}



~~~
# A tibble: 1 x 2
  meanlife medianlife
     <dbl>      <dbl>
1 67.00742    71.9355
~~~
{: .output}

To generate summary statistics for each value of another variable we use the 
`group_by()` function:


~~~
gapminder %>% 
  filter(year == 2007) %>% 
  group_by(continent) %>% 
  summarise(meanlife = mean(lifeExp), medianlife = median(lifeExp))
~~~
{: .r}



~~~
# A tibble: 5 x 3
  continent meanlife medianlife
      <chr>    <dbl>      <dbl>
1    Africa 54.80604    52.9265
2  Americas 73.60812    72.8990
3      Asia 70.72848    72.3960
4    Europe 77.64860    78.6085
5   Oceania 80.71950    80.7195
~~~
{: .output}

> ## Challenge 3
>
> Calculate the average life expectancy in each continent, for each year.
>
> > ## Solution to Challenge 3
> >
> >
> >~~~
> > lifeExp_bycontinentyear <- gapminder %>% 
> >    group_by(continent, year) %>% 
> >   summarise(mean_lifeExp = mean(lifeExp))
> > print(lifeExp_bycontinentyear)
> >~~~
> >{: .r}
> >
> >
> >
> >~~~
> ># A tibble: 60 x 3
> ># Groups:   continent [?]
> >   continent  year mean_lifeExp
> >       <chr> <int>        <dbl>
> > 1    Africa  1952     39.13550
> > 2    Africa  1957     41.26635
> > 3    Africa  1962     43.31944
> > 4    Africa  1967     45.33454
> > 5    Africa  1972     47.45094
> > 6    Africa  1977     49.58042
> > 7    Africa  1982     51.59287
> > 8    Africa  1987     53.34479
> > 9    Africa  1992     53.62958
> >10    Africa  1997     53.59827
> ># ... with 50 more rows
> >~~~
> >{: .output}
> {: .solution}
{: .challenge}

## count() and n()
**Not sure this is the best place for this- should perhaps be later**
A very common operation is to count the number of observations for each
group. The `dplyr` package comes with two related functions that help with this.

For instance, if we wanted to check the number of countries included in the
dataset for the year 2002, we can use the `count()` function. It takes the name
of one or more columns that contain the groups we are interested in, and we can
optionally sort the results in descending order by adding `sort=TRUE`:


~~~
gapminder %>%
    filter(year == 2002) %>%
    count(continent, sort = TRUE)
~~~
{: .r}



~~~
# A tibble: 5 x 2
  continent     n
      <chr> <int>
1    Africa    52
2      Asia    33
3    Europe    30
4  Americas    25
5   Oceania     2
~~~
{: .output}

If we need to use the number of observations in calculations, the `n()` function
is useful. For instance, if we wanted to get the standard error of the life
expectancy per continent:


~~~
gapminder %>%
    group_by(continent) %>%
    summarize(se_pop = sd(lifeExp)/sqrt(n()))
~~~
{: .r}



~~~
# A tibble: 5 x 2
  continent    se_pop
      <chr>     <dbl>
1    Africa 0.3663016
2  Americas 0.5395389
3      Asia 0.5962151
4    Europe 0.2863536
5   Oceania 0.7747759
~~~
{: .output}

## Using mutate()
*Make this an exercise? - combining summary stats and mutate*

We can also create new variables prior to (or even after) summarizing information using `mutate()`.

## Connect mutate with logical filtering: ifelse

*Not sure about this example - would ideally use if_else, but cannot for 1st one
since it returns NA for false.*

When creating new variables, we can hook this with a logical condition. A simple combination of 
`mutate()` and `ifelse()` facilitates filtering right where it is needed: in the moment of creating something new.
This easy-to-read statement is a fast and powerful way of discarding certain data (even though the overall dimension
of the data frame will not change) or for updating values depending on this given condition.


~~~
## keeping all data but "filtering" after a certain condition
# calculate GDP only for people with a life expectation above 25
gdp_pop_bycontinents_byyear_above25 <- gapminder %>%
    mutate(gdp_billion = ifelse(lifeExp > 25, gdpPercap * pop / 10^9, NA)) %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              sd_gdpPercap = sd(gdpPercap),
              mean_pop = mean(pop),
              sd_pop = sd(pop),
              mean_gdp_billion = mean(gdp_billion),
              sd_gdp_billion = sd(gdp_billion))

## updating only if certain condition is fullfilled
# for life expectations above 40 years, the gpd to be expected in the future is scaled
gdp_future_bycontinents_byyear_high_lifeExp <- gapminder %>%
    mutate(gdp_futureExpectation = ifelse(lifeExp > 40, gdpPercap * 1.5, gdpPercap)) %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              mean_gdpPercap_expected = mean(gdp_futureExpectation))
~~~
{: .r}


> ## Advanced Challenge
>
> Calculate the average life expectancy in 2002 of 2 randomly selected countries
> for each continent. Then arrange the continent names in reverse order.
> **Hint:** Use the `dplyr` functions `arrange()` and `sample_n()`, they have
> similar syntax to other dplyr functions.
>
> > ## Solution to Advanced Challenge
> >
> >~~~
> >lifeExp_2countries_bycontinents <- gapminder %>%
> >    filter(year==2002) %>%
> >    group_by(continent) %>%
> >    sample_n(2) %>%
> >    summarize(mean_lifeExp=mean(lifeExp)) %>%
> >    arrange(desc(mean_lifeExp))
> >~~~
> >{: .r}
> {: .solution}
{: .challenge}

## Other great resources

* [R for Data Science](http://r4ds.had.co.nz/)
* [Data Wrangling Cheat sheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
* [Introduction to dplyr](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html)
* [Data wrangling with R and RStudio](https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/)