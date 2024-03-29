---
layout: post
title: "RBBS - 8 Strings & Dates"
author: "Karishma Srikanth"
date: '2022-04-25'
categories:
- corps
- rbbs
tags: r
thumbnail: "20220425_rbbs_8-strings.png"
---

Part 8 of the coRps R Building Blocks Series (RBBS). All content can be found on this blog under the `rbbs` category as well as on the [USAID-OHA-SI/coRps GitHub repo](https://github.com/USAID-OHA-SI/coRps).

## RBBS 8 - Strings & Dates

Over the last few sessions, we’ve explored how to bring data into R for
manipulation and covered the principles of tidy data and transformations
using base R and the Tidyverse. Another key component of transformations
that we will explore is how to manipulate strings and dates using tools
in the tidyverse and regular expressions (regexps).

For our R Building Blocks session today, we will focus on understanding
what regular expressions are, how to utilize them and the `stringr`
package in R to manipulate strings, and finally how to wrangle dates in
R using the `lubridate` package.

This session is modeled after [Chapter 14 and Chapter 16 of R for Data
Science](https://r4ds.had.co.nz/data-visualisation.html).

### Learning Objectives

    - Understand what regular expressions are and how to use them to manipulate strings
    - Gain the ability to use the `stringr` package to manipulate strings
    - Learn how to work with Date formatting in R

### Recording

USAID staff can use [this
link](https://drive.google.com/file/d/1bRe8ikYmZk6N2QRfhxvhWbtbgIHbRti4/view?usp=sharing)
to access today’s recording (not available to external users).

### Setup

For these sessions, we’ll be using RStudio which is an IDE, “Integrated
development environment” that makes it easier to work with R. For help
on getting setup and installing packages, please reference [this
guide](https://usaid-oha-si.github.io/corps/rbbs/2022/01/28/rbbs-0-setup.html).

### Materials

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vRUMy-1qVMgIoeF55Mz1WS6KS8oz1HFU8rkmlcARM5mETOkg6Uk_BvZVt29I9zkGfxC404n-2BWW6SI/embed?start=false&loop=false&delayms=3000" frameborder="0" width="960" height="569" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>

### Load Packages

Let’s get started by loading some important packages. When we load the
`tidyverse`, you’ll notice that a couple other packages are being loaded
as well, including `stringr`, the main set of tools to manipulate
strings in the Tidyverse. Note that the `lubridate` package is not part
of the core tidyverse packages, so if you are working with dates/times,
you’ll want to load this package as well.

``` r
library(tidyverse) #install.packages("tidyverse")
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.6     v dplyr   1.0.8
    ## v tidyr   1.2.0     v stringr 1.4.0
    ## v readr   2.1.2     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(lubridate)
```

## String Theory 101

Let’s start with a brief review of what strings are and how we can
create them in R. Most of the time, we’ll be working with strings that
are pre-defined in our dataset, but the intuition of how to create
strings can help us better understand how to manipulate them.

Any value that is written within a pair of single or double quotes in R
is treated as a string. Unlike most other coding languages, R does not
make a distinction between the single or double quotes when creating a
string, but it’s important to note that R will always store strings
within double quotes. As such, it is generally best practice to use
double quotes when creating strings.

``` r
a <- "Starting and ending string with double quote"
print(a)
```

    ## [1] "Starting and ending string with double quote"

``` r
b <- 'Starting and ending with a single quote'
print(b)
```

    ## [1] "Starting and ending with a single quote"

Another rule to keep in mind is that the quote at the beginning of the
string has to match the quote at the end of the string. If they are
mixed, it will throw an error.

``` 
  e <- "Mixed quotes'
  print(e)

Error: unexpected symbol in: "print(e)
```

If you forget to close a quote, you’ll see `+`, the continuation
character:

    > "This is a string without a closing quote
    + 
    + 
    + HELP I'M STUCK

Lastly, you can store multiple strings in a character vector, using
`c()`

``` r
c("storing", "multiple", "strings")
```

    ## [1] "storing"  "multiple" "strings"

### `stringr` Functions

While base R has numerous functions that are useful to working with
strings, they can be inconsistent and difficult to remember. For this
session, we’ll focus on functions in the `stringr` package in the
Tidyverse. All of these functions begin with the `str` naming
convention, making them more intuitive to use and remember.

#### String Length

To count the number of characters in a string (including spaces), use
`str_length()`

``` r
str_length(c("OHA", "r building block", NA))
```

    ## [1]  3 16 NA

#### Concatenating Strings

To combine two or more strings, use `str_c()`

``` r
str_c("a", "b", "c")
```

    ## [1] "abc"

We can also use the `sep` argument to control how the strings will be
separated when they are combined.

``` r
str_c("x", "y", sep = ",")
```

    ## [1] "x,y"

`str_c()` is also vectorized, meaning that it will output shorter
vectors based on a character vector. Let’s take a look at an example
that we might see with PEPFAR data.

Let’s say that we are working with some country-level data and we want
to create a naming convention so that each output file has a consistent
naming convention.

We’d first want to store the countries that we are working with as a
character vector. We can then use `str_c()` and generate a unique string
vector output for each file name.

``` r
country <- c("Mozambique", "Nigeria", "Malawi")
str_c("output/", country, "-data-report.pdf")
```

    ## [1] "output/Mozambique-data-report.pdf" "output/Nigeria-data-report.pdf"   
    ## [3] "output/Malawi-data-report.pdf"

Another key rule to note is that elements of length 0 are dropped in the
concatenation. Let’s take this a step further - let’s say we were
working with semi-annual data that is only released in Q2 and Q4, like
OVC data.

If we include an `if()` statement in this `str_c()` function for OVC
(where OVC is TRUE in only Q2 and Q4), what will the output look like?
What would it look like if we changed the period to Q1? Try changing the
code for the period and see what happens\!

``` r
ou <- "Tanzania"
period <- "Q2"
ovc <- ifelse(period == "Q2" | period == "Q4", TRUE, FALSE)

str_c(
  "output/", ou, "-", period, "-data-report",
  if(ovc) "-with-ovc",
  ".csv"
)
```

    ## [1] "output/Tanzania-Q2-data-report-with-ovc.csv"

#### Subsetting Strings

To extract parts of a string, use `str_sub()` using the `start` and
`end` arguments to assign the position of the string you want to subset.

Here’s an example of how we’d use this in our work, by slicing just the
fiscal year from the period variable.

``` r
period <- c("FY19Q4", "FY20Q4", "FY21Q4")

str_sub(period, start = 1, end = 4)
```

    ## [1] "FY19" "FY20" "FY21"

#### Changing the case

To manipulate the case of the strings, we can use:

1.  `str_to_lower()` - changes all characters to lowercase
2.  `str_to_upper()` - changes all characters to uppercase
3.  `str_to_title()` - changes first letter of all words to uppercase
4.  `str_to_sentence()` - changes first letter of first words to
    uppercase

<!-- end list -->

``` r
str_to_upper("change lower to upper")
#> [1] "CHANGE LOWER TO UPPER"
str_to_lower("CHANGE UPPER tO LOwER")
#> [1] "change upper to lower"
str_to_title("mAke strinGs tITLe-like")
#> [1] "Make Strings Title-Like"
str_to_sentence("tuRN string INTO sENTence")
#> [1] "Turn string into sentence"
```

## Regular Expressions

You can think of regular expressions as the language that describes the
patterns in strings. They are constructed in a similar manner to
arithmetic expressions, by using special characters and various
operators to combine smaller expressions. While these can be daunting to
learn at first, they can ultimately be very useful to your ability to
wrangle and manipulate strings.

In R, regular expressions are written as strings - however, some
characters can be be represented directly as an R string, and some are
instead represented as **special characters**, which are patterns that
hold specific meaning:

Let’s start with some basic matching, using `str_view()`. In its
simplest form, we can use regular expressions to match exact strings.
You’ll notice here that the “io” pattern in each string is highlighted
using `str_view()`

``` r
x <- c("WH Region", "Ethiopia", "Asia Region")
str_view(x, "io")
```

<img src="rbb-strings_files/figure-gfm/unnamed-chunk-12-1.png" width="576" />

#### Period (`.`) - matches any character

Time to introduce our first special character\! In regular expressions,
the **period** matches any character. In the example below, we are
asking R to match the characters one to the right and one to the left of
the “o”.

``` r
x <- c("WH Region", "Ethiopia", "Asia Region.")
str_view(x, ".o.")
```

<img src="rbb-strings_files/figure-gfm/unnamed-chunk-13-1.png" width="576" />

#### Escape (`\`) - backslash used to escape special behavior

You might be wondering, if the period notation matches any characters,
how do I match the character “.” specifically? In these cases, we can
use “escapes” to tell the regular expression to match the character
exactly, rather than special character behavior.

As such, we’ll need the regexp `\.`. Let’s see what happens when we try
to match the explicit pattern “a.”

``` r
x <- c("WH.Region", "Ethiopia", "Asia.Region.")
str_view(x, "a\.")
```

    ## Error: '\.' is an unrecognized escape in character string starting ""a\."

We see here that R is unable to recognize the escape character - since
we use strings to represent regular expressions, and the backslash is
also used as an escape for strings, we need to use the string `\\.` as
our escape character.

``` r
x <- c("WH.Region", "Ethiopia", "Asia.Region")
str_view(x, "a\\.")
```

<img src="rbb-strings_files/figure-gfm/unnamed-chunk-15-1.png" width="576" />

And it worked\! From now on, we can think of the strings that represent
regular expressions as `\\.`

*Bonus question* - We can use a similar logic to match specific `\` -
using what you know about regular expressions so far, how many escapes
do you need to match one `\`?

#### Anchors - anchoring at the start or end of the string

  - Use `^` to match the **start** of the string
  - Use `$` to match the **end** of the string

<!-- end list -->

``` r
indicator <- c("HTS_TST", "TX_CURR", "HTS_TST_POS")
str_view(indicator, "^HTS")
```

<img src="rbb-strings_files/figure-gfm/unnamed-chunk-16-1.png" width="576" />

``` r
ou <- c("WH.Region", "Ethiopia", "Asia.Region")
str_view(ou, "Region$")
```

<img src="rbb-strings_files/figure-gfm/unnamed-chunk-16-2.png" width="576" />

#### Additional Characters

There are countless special characters that match to more than one
character. Here’s an abbreviated list of some helpful characters to
know:

**Character Classes**

  - Use `\d` to match the any digit
  - Use `\s` to match white-space
  - Use `[x,y,z]` to match one of x, y, or z
  - Use `[^x,y,z]` to match anything but x, y, or z

**Pattern Repetition**

  - Use `?` to match 0 or 1 times
  - Use `+` to match 1 or more times
  - Use `*` to match 0 or more times

**Specify number of matches**

  - Use `{n}` exactly n matches
  - Use `{n,}` n or more matches
  - Use `{,m}` at most m matches
  - Use `{n,m}` between n and m matches

At this point, your head may be spinning with regular expressions. Let’s
do a bonus example to practice our regular expression skills once more.

### *Bonus Example*

    "C:/Users/ksrikanth/Documents/Data/MER_Structured_Datasets_PSNU_IM_FY19-22_20211217_v2_1_Zambia.zip"

Let’s say I wanted to match this specific pattern in the above filepath
exactly - how would I go about constructing a regular expression to to
match this? (see slides for a breakdown of the answer)

### Useful Stringr Tools

Now that we have a solid foundation for understanding regular
expressions, let’s see if we can apply this logic to some real-life
situations that we would run into with our own data using some **helpful
stringr tools**.

#### Detecting Matches

We can use `str_detect()` to determine if a character vector matches a
certain pattern or not. `str_detect()` will return a logical vector of
the same length as the original input.

Let’s take a look at the example we used earlier - if we wanted to
identify the strings that match with just the HTS (testing) indicators,
we could use `str_detect()` and the anchoring regular expression `^` to
match every string that starts with the pattern “HTS”.

``` r
indicator <- c("HTS_TST", "TX_CURR", "HTS_TST_POS")
str_detect(indicator, "^HTS")
```

    ## [1]  TRUE FALSE  TRUE

The nice thing about `str_detect()` is that we can easily combine it
with our `dplyr` functions to filter and tidy our dataset. For instance,
we could add this function to a `filter()` command to filter a dataframe
to just HTS indicators.

``` 
 df %>% 
 filter(str_detect(indicator, "^HTS"))
 
```

#### Replacing Matches

To replace matches with new strings, we can use `str_replace()`. The
syntax for `str_replace()` is as follows:

`str_replace(vector, "old pattern", "new pattern")`

Based on this logic, what do you think the following output will look
like?

``` r
fy <- c("FY19", "FY20", "FY21")
str_replace(fy, "FY", "20")
```

    ## [1] "2019" "2020" "2021"

Using `str_replace()` allows us to quickly manipulate our fiscal year
variable into the calendar year.

#### Splitting Matches

We can also use `str_split()` to split a string into different pieces -
this is especially useful for tidying your data after a pivot and is
similar to the `tidyr::separate()` function.

Let’s take an example of data being encoded into indicator names, like
below (with age, sex, and the statistic type written out in the string
name). We can use to `str_split()` to split on the "\_" pattern to
isolate each string into distinct pieces.

``` r
x <- c("prev_15-49_female_estimate", "deaths_all_all_high", "plhiv_15+_male_low")

x %>% str_split("_")
```

    ## [[1]]
    ## [1] "prev"     "15-49"    "female"   "estimate"
    ## 
    ## [[2]]
    ## [1] "deaths" "all"    "all"    "high"  
    ## 
    ## [[3]]
    ## [1] "plhiv" "15+"   "male"  "low"

Phew, we made it\! Regular expressions can be incredibly daunting but
deeply useful for analytics. While this may not be a comprehensive
overview of everything that regular expressions and `stringr` have to
offer, hopefully, you feel a little more equipped to tackle strings head
on.

Nevertheless, as you start working through regexp, function
documentation (`?function()`), the [OHA/SI Github
site](https://github.com/USAID-OHA-SI), and Google are your best
friends\!

## Dates and Times

Now that we are experts in working with strings, let’s apply some of
that logic to working with dates and times in R. In day-to-day data use,
we may come across multiple different formats for date/time data that
can complicate our analyses. While we can use base R to do some of these
manipulations, the `lubridate` package is a helpful and user-friendly
set of functions that make date/time manipulation much easier. As a
reminder, `lubridate` is not a part of the core tidyverse, so you will
need to load it separately whenever you need it.

``` r
library(lubridate)
```

### Creating dates/times

There are 3 main types of data and time data:

  - **Date**: printed as `<date>`
  - **Time**: printed as `<time>`
  - **Date-time**: date plus a time, printed as `<dttm>`

Let’s illustrate this using two functions from the `lubridate` package:
`today()` which tells you the current day and `now()` which tells you
the current day and time. These functions can be tremendous useful when
you are trying to perform date/time calculations as a part of your
analysis.

``` r
today()
#> [1] "2022-04-26"
now()
#> [1] "2022-04-26 19:17:05 EDT"
```

There are 3 main ways to create a date/time variable:

#### From a string

Let’s focus first on how to parse a string into a date format, since
that is often the format that we get date/time data in. We can use some
helpful functions from `lubridate` to automatically populate the date
format once we specify the order of the string.

For example, let’s say we had the string **“April 25, 2022”**. This
string is in “Month Day, Year” format - as such, we can use `mdy()` to
parse this string into a proper date-format.

Similarly, we can use `ymd()` for strings with “Year Month Day” format
or `dmy()` for strings with “Day Month Year”.

These functions also work with unquoted numbers if you have numeric data
instead:

``` r
ymd(20220426)
#> [1] "2022-04-26"
```

To add the date-time element, we can add the suffix "\_hms" for “hours,
months, seconds” to these parsing functions (`ymd_hms()`). To specify
the timezone, use the `tz` argument. To return a list of valid time
zones, use `OlsonNames()`.

``` r
mdy_hms("04/26/22 18:36:59", tz = "EST")
#> [1] "2022-04-25 18:36:59 EST"
```

#### From individual columns in the dataset

Sometimes, we’ll see individual components of a date spread across
multiple columns - we can manipulate this into a date format using
`make_date()` or `make_date_time()`.

Let’s create an example tibble of 3 dates spread out across multiple
columns.

``` r
date_df <- data.frame(year = c(2003, 2022, 1998),
                   month = c(1, 4, 10),
                   day = c(19, 25, 26),
                   hour = c(10, 6, 16),
                   minute = c(50, 35, 22))

date_df
```

    ##   year month day hour minute
    ## 1 2003     1  19   10     50
    ## 2 2022     4  25    6     35
    ## 3 1998    10  26   16     22

Since we have columns for year, month, day, hour, and minute, we’ll use
`make_date_time()` to include the time component.

``` r
date_df2 <- date_df %>% 
  mutate(date = make_datetime(year, month, day, hour, minute))

date_df2
```

    ##   year month day hour minute                date
    ## 1 2003     1  19   10     50 2003-01-19 10:50:00
    ## 2 2022     4  25    6     35 2022-04-25 06:35:00
    ## 3 1998    10  26   16     22 1998-10-26 16:22:00

And now we have a date-time variable\!

#### Other types

To switch between a date-time format and a date format, you can use
`as_date()` and `as_date_time()`:

``` r
as_datetime(today())
#> [1] "2022-04-26 UTC"
as_date(now())
#> [1] "2022-04-26"
```

Sometimes, R will read date/time variables as a numeric value that shows
the number of seconds that have elapsed since January 1, 1970 (POSIX
time). If you are dealing with dates in this format, you can also use
`as_date()`.

As an example, let’s use the `unclass()` to return to the raw data
format of the date value from `today()`. From this, we can see that the
number of seconds from January 1, 1970 to April 26, 2022 is 19108. Then,
let’s call the `as_date()` function on that number as see what happens.

``` r
unclass(today())
#> [1] 19108

as_date(19108)
#> [1] "2022-04-26"
```

### Identifying components of dates

Using `lubridate`, you can also extract parts of the date with the
following functions:

  - `year()`
  - `month()`
  - `mday()` - day of the month
  - `yday()` - day of the year
  - `wday()` - day of the week
  - `hour()`
  - `minute()`
  - `second()`

<!-- end list -->

``` r
datetime <- ymd_hms("2019-01-12 23:34:56")

year(datetime)
#> [1] 2019
month(datetime)
#> [1] 1
mday(datetime)
#> [1] 12
yday(datetime)
#> [1] 12
wday(datetime)
#> [1] 7
```

### Calculations with Dates

Once you have your date variables formatted correctly, it becomes really
intuitive to start performing calculations with them.

When we subtract two dates, we get a “difftime” object. Let’s see what
happens when we have an individual’s birthday and want to calculate
their age.

When we simply subtract, we get an age in units of days, which is not
very intuitive. However, we can use the function `as.duration()` from
the `lubridate` package which reports the time in seconds, as well as a
more intuitive rounded figure.

``` r
age <- today() - ymd(20120210)

age
as.duration(age)

#> Time difference of 3728 days
#> [1] "322099200s (~10.21 years)"
```

## Additional Resources

For more reading on working with strings and dates, check out Chapter 14
and 16 of [R for Data Science](https://r4ds.had.co.nz/index.html).

For a good guide on how to use the `stringr` functions, see [this
cheatsheet](https://evoldyn.gitlab.io/evomics-2018/ref-sheets/R_strings.pdf).

For a good guide on how to use the `lubridate` functions, see [this
cheatsheet](https://evoldyn.gitlab.io/evomics-2018/ref-sheets/R_lubridate.pdf).
