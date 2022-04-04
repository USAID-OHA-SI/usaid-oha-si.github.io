---
layout: post
title: Monday Data Viz - Action Oriented Data Viz
author: Aaron Chafetz
categories: [data viz]
tags: [vizualisation, Monday data viz]
thumbnail: "20220404_rapp_swd-cover.png"
---

I saw a blog post last week that filled my thoughts the rest of the week and wanted to share with you all. In [the post, Albert Rapp](https://albert-rapp.de/post/2022-03-29-recreating-the-swd-look/) draws on a [Storytelling With Data Makeover video](https://youtu.be/st7_vPjq0SU)) by Elizabeth Hardman Ricks and applies it to R. It's fascinating to watch the transformation process occur in R, but what really sticks with me is the end product compared with the original, which doesn't look terribly dissimilar to some of the PEPFAR graphics we frequently see. 

![Rapp original stacked bars](/assets/img/posts/20220404_rapp_swd-original.png)

![Rapp remake visuals of stacked bars](/assets/img/posts/20220404_rapp_swd-remake.png)

 The above visuals mimic the data and viz from the SWD video Ricks remakes. These two charts have exactly the same data and yet by moving away from program defaults in graphing the data, we went up with two vastly different plots. Ricks uses the following process in her remake. 

![image.png](/assets/img/posts/20220404_ricks_swd-process.png)

In the remake, Ricks starts by understanding the context and uses that to play into what graph makes the most sense to use. Next, she declutters the graph and focuses it through applying a number of different data viz principles: reoridents the graph (useful if the category labels were long, but we're just working with dummy numbered ids), orders the categories based on the error rates, uses color to strategically call attention to the particular areas of focus and not all the data, integrates the color legend into the text subtitles and annotations, and includes a baselines (average warehouse in this case).

The last step takes that graph to the next level with the story component. She weaves in contextual annotations and questions to make this graphic actionable when presented. Rather than having the audience have to make sense of the original stacked bar with no titles or use of color, she is able to craft the key questions needed to take action on base on the data. It's just fantastic.

I encourage you all to check out the SWD video (and Rapp's post on doing this in R) and think about how you can take these concepts and apply them to your own work.

Happy plotting!