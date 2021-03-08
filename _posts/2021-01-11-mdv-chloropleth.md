---
layout: post
title: Monday Data Viz - Be wary of chloropleth maps
date: 2021-01-11
author: Aaron Chafetz
categories: [data viz]
tags: [vizualisation, Monday data viz]
thumbnail: "20210111-map.gif"
---

To kick off the year, I wanted to share a [nifty gif](https://twitter.com/DavidZumbach/status/1344547411985911808) on twitter the other week. This gif is a take on the [“land doesn’t vote, people do” visual](https://twitter.com/karim_douieb/status/1181934417650040832?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1181934417650040832%7Ctwgr%5E%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fthemorningnews.org%2Fp%2Fthe-history-of-the-map-behind-land-doesnt-vote-people-do) from 2019 depicting the US.

![map](/assets/img/posts/20210111-map.gif)

The fist image in the new gif is a map of Switzerland showing the results of votes for the Hunting Act Amendment in Sept 2020. The choropleth map shows votes by municipality, appearing that the act passed by a large majority. But, "land doesn't vote, people do"; land area isn't representative of how many people live there. The amendment in fact didn't pass.

As expressed by one of my favorite [xkcd comics](https://xkcd.com/1138/), it's easy to "lie" or misinterpret maps when you don't account for the underlying population.

![map](/assets/img/posts/20210111-xkcd.png)

As you map out treatment or testing by districts in a country, make sure you don't fall into this trap without understanding (or trying to incorporate) the denominator - PLHIV or population.