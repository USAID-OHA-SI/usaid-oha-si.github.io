---
layout: post
title: Monday Data Viz - Slope chart + small multiples
author: Aaron Chafetz
categories: [data viz]
tags: [vizualisation, Monday data viz]
thumbnail: "20220425_lu-sun_covid-death-rates-before-after-by-ethnicity.png"
---

As PEPFAR, we've been collecting quarterly MER data that runs back to FY15, so trend data is a staple for our work. Armed with ample data, our first stop when plotting trend over time is a line chart, but do we always need 6 or 16 quarters to plot as a line or bar graph? Sometimes the purpose is just to show change from a particular point in time to now (e.g. year over year change). Denise Lu and Albert Sun of the New York Times do a great job exemplifying this in an [article on COVID deaths from last December](https://www.nytimes.com/interactive/2021/12/28/us/covid-deaths.html).

In their article, they show the COVID death rate for different group before and after all adults became eligible for the vaccine. They utilize slope charts, which are just simplified line charts that help demonstrate the change (via the slope) between two points. From the visual below, we can see that the COVID death rate for 85+ fell from 15% to just 7%, compared with the complete opposite with 25-44 year olds, going from 7% up to 13%.

![COVID death rate slope charts before and after vaccine by age group](/assets/img/posts/20220425_lu-sun_covid-death-rates-before-after.png)

Lu and Sun use an inline legend with color, showing the "after" share in red to really emphasize these data points. They also call out the age groups where the death rate rose after the vaccine, with a light red shade as the plot's background. And finally, they utilize small multiples, replicating the slope chart by race/ethnicity and age to be able to compare all of these groups without creating a messy spaghetti plot.

![COVID death rate slope charts before and after vaccine by age group](/assets/img/posts/220220425_lu-sun_covid-death-rates-before-after-by-ethnicity.png)

The next time you want to show change overtime, consider using slope charts (and small multiples) using Lu and Sun as a great, informative, and clean example of how to do this.

Happy plotting!