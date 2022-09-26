---
layout: post
title: Monday Data Viz - The Urban Exodus
author: Aaron Chafetz
categories: [data viz]
tags: [vizualisation, Monday data viz]
thumbnail: "20220919_vandam_us-county-change.png"
---

 I'm teleworking today and writing this from home, which is pretty typical of about a third of American workers since about mid 2021 (down from its peak of an estimated +60% in 2020). These facts from Andrew Van Dam's [Washington Post](https://www.washingtonpost.com/business/2022/08/19/remote-work-hybrid-employment-revolution/) article from last month reporting on a [recent paper](https://digitaleconomy.stanford.edu/wp-content/uploads/2022/03/Measurement_of_remote_work_MARCH22_2022.pdf) by a few economists, polling data from the Post and Gallup, and Census Bureau data. 

Van Dam's article provides quite a number of data points. which comes as no surprise since this is part of his weekly column - [The Department of Data](https://www.washingtonpost.com/business/2022/06/29/dept-of-data/), but what stuck with me still weeks after the article was published was the latter half which focuses on estimating the geographic effects of remote work. To do this, Van Dam plotted Census data to show what counties lost/gained populations (25-49) between 2020 and 2021. Rather than going the choropleth map route (coloring counties by their gains/losses), Van Dam depicts the size of the population trend through symbols (arrows).  

![US map showing change by county between 2020-21 in working population](/assets/img/posts/20220919_vandam_us-county-change.png)


The largest cities in America had pretty large exoduses, leaving to work from small metro areas, exurbs, and more rural places as well. In counties with cities like New York, LA, Chicago, and San Francisco, we see pretty sizable drops that stand out compared with the rest of the country. 

![Map zooming into a few large metro area showing change by county between 2020-21 in working population](/assets/img/posts/20220919_vandam_select-states-county-change.png)

In the [map I shared last week](https://usaid-oha-si.github.io/data%20viz/2022/09/12/mdv-importance-of-context.html), I talked about how a choropleth map can "lie" by giving more weight/space to areas that are less populated density. In this map, that issue is being resolved by using symbology so each county has the same size (width) of their arrows; this map however is making the tradeoff by showing population counties rather than density. This dramatically highlights those larger cities, but those places had larger populations to begin with. I really like this map a lot, but think it would also have been interesting to see paired with one that also shows these losses/gains as a share of the county's population.

As you are mapping your own data, consider the fact that in order to map the world, you have to make tradeoffs and you need to think carefully about what you want to show and what you're giving up.

Happy plotting!