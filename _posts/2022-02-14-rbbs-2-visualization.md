---
layout: post
title: RBBS - 2 Visualizations
date: 2022-02-14
author: Aaron Chafetz
categories: [corps, rbbs]
tags: [r]
thumbnail: "20220214_rbbs_2-visualization.png"
---

Part 2 of the coRps R Building Blocks Series (RBBS). All content can be found on this blog under the `rbbs` category as well as on the [USAID-OHA-SI/coRps GitHub repo](https://github.com/USAID-OHA-SI/coRps).

## RBBS 2 - Visualization

For our R Building Blocks session today, we will be kicking everything
off and giving a quick run down of R and getting a sense of how plotting
with in R with the `ggplot` package. This session is modeled after
[Chapter 3 of R for Data
Science](https://r4ds.had.co.nz/data-visualisation.html).

### Learning Objectives

-   Become familiar with the RStudio IDE
-   Understand the basic framework to build a plot
-   Know what aesthetics are
-   Understand the difference between adding aesthetics within and
    outside of `aes()`
-   Able to create small multiples
-   Understand how to apply themes and styles

### Recording

You can use this link to access today’s recording.

### Setup

For these sessions, we’ll be using RStudio which is an IDE, “Integrated
development environment” that makes it easier to work with R. For help
on getting setup and installing packages, please reference [this
guide](https://usaid-oha-si.github.io/corps/rbbs/2022/01/28/rbbs-0-setup.html).

### Exploring RStudio IDE

RStudio may seem imposing at first, but in no time you’ll know where
everything is. Your RStudio will be broken down into 4 main components.

![RStudio IDE](../assets/img/posts/20220214_rstudio_ide.png)

-   Source: this box won’t be here when you first open RStudio. If you
    go to File > New File > R Script, you will see this box. This is
    where you can write/save or open your R scripts.
-   Console: below Source, you will have your console. This is where all
    your R code are excuted and where the outputs are displayed. If you
    want to run a line as a one off, you can write it here.
-   Environment - moving to the upper right hand corner, you have your
    environment tab. This box contains all the datasets or objects you
    have stored in your current working session.
-   Catch All - this last box has a lot of different features. You can
    see a number of tabs, which allow you to expore your
    directories/files, this is where your graphs will print, and where
    the help files are located.

### Loading packages

With a sense of the IDE, we’re ready to start working. Open up a new
script if you don’t have one open (File \> New File \> R Script or CTRL
+ SHIFT + N). In there, you will want to add the following code to load
the libraries that we’re going to use for this session (see [this
guide](https://usaid-oha-si.github.io/corps/rbbs/2022/01/28/rbbs-0-setup.html)
for help on installing packages). To run code from the console, you will
hit CTRL + ENTER. This will run the current line your cusor is on. To
run multiple lines, you will highlight those lines and then hit CTRL +
ENTER.

``` r
library(tidyverse) #install.packages("tidyverse")
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.6     v dplyr   1.0.7
    ## v tidyr   1.1.4     v stringr 1.4.0
    ## v readr   2.1.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(scales) #install.packages("scales")
library(glitr) #remotes::install_github("USAID-OHA-SI/glitr", build_vignettes = TRUE)
```

## Viewing the data

The dataset we’re using today is stored in the `glitr` package. It’s a
masked HFR dataset that has information for FY50 on multi-month
dispensing, MMD. Let’s take a look at the data using `glimpse()` and
then `View()`. \*If you are getting an error, make sure you reinstall
`glitr` using the code in the section about

``` r
#store the dataset as an object in your Global Environment
hfr_mmd <- hfr_mmd
```

``` r
#prints out a previous with your indicators vertically (with type) and data runs horizontally
glimpse(hfr_mmd)
```

    ## Rows: 243
    ## Columns: 12
    ## $ date              <date> 2049-10-01, 2049-10-01, 2049-10-01, 2049-10-01, 204~
    ## $ fiscal_year       <dbl> 2050, 2050, 2050, 2050, 2050, 2050, 2050, 2050, 2050~
    ## $ hfr_pd            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1~
    ## $ operatingunit     <chr> "Saturn", "Saturn", "Saturn", "Saturn", "Saturn", "S~
    ## $ snu1              <chr> "Region 1", "Region 1", "Region 1", "Region 1", "Reg~
    ## $ psnu              <chr> "District 13", "District 16", "District 5", "Distric~
    ## $ mech_code         <chr> "01705", "01563", "01705", "01705", "01339", "01563"~
    ## $ tx_curr           <dbl> 9250, 14830, 7020, 13220, 590, 5820, 31230, 30, 320,~
    ## $ tx_mmd.o3mo       <dbl> 8400, 13850, 6490, 11840, 320, 5540, 27180, 20, 240,~
    ## $ tx_mmd.u3mo       <dbl> 850, 980, 530, 1380, 270, 280, 4050, 10, 80, 0, 180,~
    ## $ tx_mmd_unk        <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 30, 0, 110, 0, 9~
    ## $ share_tx_mmd.o3mo <dbl> 0.91, 0.93, 0.92, 0.90, 0.54, 0.95, 0.87, 0.67, 0.75~

``` r
#your traditional tabular view
View(hfr_mmd)
```

Each row is a distinct reporting period (date:hfr_pd) with information
on where the data were reported (operatingunit:pnsu), by whom
(mech_code) and how much (tx_curr:share_tx_mmd.o3mo). For more
information on the dataset, we can use the `?` to see the documentation
through a “help” file. This function is extremely useful for getting
help files.

``` r
#get help with ?[function] or place your cursor on the function and hit F1
?hfr_mmd
```

### Plotting

Great, we have our data, now we can move onto start plotting. To plot
our data, we’re going to a package called `ggplot2` which is part of the
Tidyverse and installed/loaded with `tidyverse`.

Creating a plot with `ggplot2` is simply about providing some basic
information and building layers as you go. We always will start with
`ggplot()` to create a coordinate system and then add on a number of
mapping arguments or layers. So we are going to pass the dataset into
`ggplot()` and we will start by creating a scatter plot using
`geom_point()`. In addition to passing in the dataset, we need to
provide the function with what we want on the x and y axis.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-7-1.png" width="576" />

Et voila, we have a plot! Each of those points is a site in a given
month associated with the value provided.

#### Exercises

1.  How many rows are in `hfr_mmd`?
2.  Create a plot to explore the relationship between patient volume
    (`tx_curr`) and share on over 3 months of treatment dispensing
    (`share_tx_mmd.3mo`). Looking at the help file and the plot, what
    makes this plot not very telling?

### Aesthetic mapping

We have developed a basic plot with two lines of code. It’s not very
telling, but we can start to add layers of complexity through
aesthetics. Wickham and Grolemond describe aesthetics as “An aesthetic
is a visual property of the objects in your plot. Aesthetics include
things like the size, the shape, or the color of your points.”

On helpful aesthetic to apply to this plot is color. We can try adding
our `y` value as the `color` as well and see what happens.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-8-1.png" width="576" />

From this we can see that `ggplot` created a continuous scale ranging
from dark blue on the lower end of `share_tx_mmd.o3mo` to ligher blue on
the upper. We could also pass in a discrete scale, like `psnu`, which
will assign color each PSNU a distinct color.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, color = psnu))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-9-1.png" width="576" />

In addition to color, we can also pass in values to size our shapes.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-10-1.png" width="576" />

Now that the points are different sizes, a number of them have been
hidden because they are overlapped. We can adjust their transparency,
called `alpha`, where 1 is completely opaque and 0 is completely
transparent.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4)
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-11-1.png" width="576" />

What is interesting about the `alpha` aesthetic? It’s outside our
`aes()` mapping. What is the difference? By applying an aesthetic
outside of `aes()`, you are manually adjusting the aesthetic, applying
it to ALL the data values in that geom.

One last feature to flag is that we can also pass in logic into the
aesthetics. Maybe we want to highlight the data points below 60%.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo >= .6, size = tx_curr),
             alpha = ifelse(hfr_mmd$share_tx_mmd.o3mo >= .6, .2, 1))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-12-1.png" width="576" />

In the plot above, we used boolean logic for the `color`, so TRUE and
FALSE created different colors. And for the alpha, we passed in the
vector from the dataframe to say if the value meets the threshold of
60%, use this value for `alpha`, otherwise use this other value.

#### Exercises

1.  Using a similar plot from above where `x = date` and
    `y = share_tx_mmd.o3mo`, change all the data points to size 6.
    Repeat this and change all the colors to of the points to purple.
2.  What is wrong with the code below? Why are the points not green?
    `ggplot(data = hfr_mmd) + geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, color = "green"))`
3.  Use the `shape` aesthetic to change the shape based on `snu1`.

### FACETING

In addition breaking out characteristics of the data with aesthetic
mapping, we can also separate the main plot in small multiples, or
facets. In one of the examples above, we applied a different color to
each PSNU to differentiate the values. Rather than having all of these
values on the same plot, we can break it into small multiples using
`facet_wrap()`.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo)) +
  facet_wrap(~psnu)
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-13-1.png" width="576" />
No instead of one jumbled plot, we can view the trends by each district.
The ordering here is alphabetically, which isn’t terribly meaningful
here. And the ordering is futher being messed up because it’s
interpreting Districts 10 and 11 coming after District 1 and before
District 2. We can assign ordering to our faceting using a package in
the `tidyverse` called `forcats`. A more meaningful ordering may be to
order the districts by their over all (sum) TX_CURR. To do this, we’ll
wrap `psnu` in the last line within the `fct_reorder()` function.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo)) +
  facet_wrap(~fct_reorder(.f = psnu, #what are we ordering?
                          .x = tx_curr, #by what variable are we ordering it?
                          .fun = sum, #how are we ordering it/what function?
                          na.rm = TRUE, #should we remove NA values?
                          .desc = TRUE #should we reverse the direction
                          )
             )
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-14-1.png" width="576" />
We can also use two variables to create the small multiples in a grid.
For instance, we could compare how different mechanisms compare in the
different regions they are in.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo)) +
  facet_grid(snu1 ~ mech_code)
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-15-1.png" width="576" />

#### Exercises

1.  Read the help for `facet_wrap()` (`?facet_wrap`) and figure out how
    you would make the output from our PSNU small multiples above into 1
    row? Would you you plot it in one column?
2.  How would you reorder `mech_code` order in the second small
    multiples graphic so that they were ordered by the partner who had
    the highest (max) TX_CURR to the lowest?

### Transformation

So far we have been working with points, but can do things more
sophisticated with the data. For the scatter plots we’ve been using, we
have taken the the x and the y mapping directly from our dataset. We can
also used `ggplot` to transform our data, say creating a count or a sum,
and displaying the output as a bar chart or histogram.

``` r
ggplot(data = hfr_mmd) +
  geom_bar(mapping = aes(x = date))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-16-1.png" width="576" />

By using `geom_bar` we are getting a count of the number of rows that
exist in the dataset. This could be useful for various purposes, but of
more import to us is being able to sum up the values patients.

``` r
ggplot(data = hfr_mmd) +
  geom_bar(mapping = aes(x = date, y = tx_curr), stat = "identity")
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-17-1.png" width="576" />

A slightly simpler alternative to `geom_bar` is to use `geom_col`, which
defaulted to `stat = "identity"` that you would have to otherwise
specify to not get a count when using `geom_bar`

``` r
ggplot(data = hfr_mmd) +
  geom_col(mapping = aes(x = date, y = tx_curr))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-18-1.png" width="576" />

We can also quickly tranform this into a stacked bar chart by applying a
color fill.

``` r
ggplot(data = hfr_mmd) +
  geom_col(mapping = aes(x = date, y = tx_curr, fill = snu1))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-19-1.png" width="576" />
Stacked bar charts aren’t ideal. We could create a small multiples plot
like we did above, which is preferable, but could also just dodge the
columns by changing the `position`.

``` r
ggplot(data = hfr_mmd) +
  geom_col(mapping = aes(x = date, y = tx_curr, fill = snu1),
           position = "dodge")
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-20-1.png" width="576" />

In mentioning the position, it may be useful to back to our scatter
plots from above. In the plots, many of our points were overlapping and
couldn’t be seen without adjusting the shapes’ opacity. Another option
would have been to adjust the placement of the points by jittering them
slightly to help with the overplotting. We can adjust the position by
using `position = jitter`).

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4,
             position = "jitter")
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-21-1.png" width="576" />

You can even refine the radius of the jittering by using a function,
`position_jitter()`

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4,
             position = position_jitter(width = 5, height = 0, seed = 42))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-22-1.png" width="576" />

#### Exercises

1.  Plot a bar graph of TX_CURR over time. Replace `fill = snu1` with
    `color = snu1` in the aesthetics. What changes in your plot?
2.  Using `geom_bar` graph the number of observations for each period
    (`date`).

### Additional Thoughts

Before closing the book on the basics of plotting using `ggplot`, I
wanted to delve hit on a few things.

First up is structure. So far, we have passed in data to `geom_` and
then mapped aesthetics. The great thing is that you can keep using that
simple structure and layering on more and more geoms and aesthetics. For
example, we could add in a `geom_line` to connect the points and even
add a static threshold line, `geom_vline`, or even an area to highlight
a particular period.

``` r
ggplot(data = hfr_mmd,
       mapping = aes(x = date, y = share_tx_mmd.o3mo)) +
  geom_line(mapping = aes(group = mech_code), alpha = .4) +
  geom_point(alpha = .4) +
  geom_hline(yintercept = .6, color = "red", linetype = "dashed") +
  facet_wrap(~psnu)
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-23-1.png" width="576" />

In addition to layer on geoms and facets, we can also clean up the x and
y scales as well as adding titles and captions.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4) +
  scale_x_date(date_breaks = "1 month", date_labels = "%b") +
  scale_y_continuous(labels = percent) +
  scale_size(labels = comma) +
  scale_color_continuous(type = "viridis", labels = percent, guide = "none") +
  labs(x = "Reporting Period",
       y = "Patients on 3+ months of Rx",
       size = "TX_CURR\n volume",
       title = "LARGERS DISTRICTS HAVE MORE PATIENTS ON +3 MONTHS OF RX",
       caption = "Source: HFR FY50")
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-24-1.png" width="576" />

We can also start adjusting the style and theme.

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4) +
  scale_x_date(date_breaks = "1 month", date_labels = "%b") +
  scale_y_continuous(labels = percent) +
  scale_size(labels = comma) +
  scale_color_continuous(type = "viridis", labels = percent, guide = "none") +
  labs(x = "Reporting Period",
       y = "Patients on 3+ months of Rx",
       size = "TX_CURR\n volume",
       title = "LARGERS DISTRICTS HAVE MORE PATIENTS ON +3 MONTHS OF RX",
       caption = "Source: HFR FY50") +
  theme_minimal() +
  theme(legend.position = "none",
        plot.title.position = "plot",
        axis.text = element_text(color = "gray60"),
        plot.title = element_text(face = "bold"))
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-25-1.png" width="576" />

So far we have just been using `glitr` package to access the `hfr_mmd`
data, but the package’s function is to apply the [OHA Style
Guide](https://issuu.com/achafetz/docs/oha_styleguide) on top of
`ggplot`. Since part of the style is a non-standard R font, I am going
to load an extra package to load the font. For more information on how
to use `extrafont` the first time and install Source Sans Pro, see [this
reference](https://usaid-oha-si.github.io/glitr/index.html#what-the-fnt).

``` r
library(extrafont) #install.packages("extrafont")
```

    ## Registering fonts with R

``` r
ggplot(data = hfr_mmd) +
  geom_point(mapping = aes(x = date, y = share_tx_mmd.o3mo, 
                           color = share_tx_mmd.o3mo, size = tx_curr),
             alpha = .4) +
  scale_x_date(date_breaks = "1 months", date_labels = "%b") +
  scale_y_continuous(labels = percent) +
  scale_size(labels = comma) +
  scale_color_si(palette = "scooters", guide = "none") +
  labs(x = "Reporting Period",
       y = "Patients on 3+ months of Rx",
       size = "TX_CURR volume",
       title = "LARGERS DISTRICTS HAVE MORE PATIENTS ON +3 MONTHS OF RX",
       caption = "Source: HFR FY50") +
  si_style_ygrid()
```

<img src="2022-02-14-rbbs-2-visualization_files/figure-gfm/unnamed-chunk-27-1.png" width="576" />

For more information on using the OHA styles and colors in glitr, check
out [this
guide](https://usaid-oha-si.github.io/glitr/articles/adorn-your-plots.html).
And for a good guide on ggplot, see [this
cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization.pdf)
from RStudio
