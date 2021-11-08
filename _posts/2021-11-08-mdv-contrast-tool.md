---
layout: post
title: Monday Data Viz - Color Contrast Tool
author: Aaron Chafetz
categories: [data viz]
tags: [vizualisation, Monday data viz]
thumbnail: "20211108_adobe_contrast-tool.png"
---

[A couple weeks back](https://usaid-oha-si.github.io/data%20viz/2021/10/25/mdv-visual-hierarchy-1.html), I brought up the need to check contrast when using grey elements in a graph. As we decrease the saturation and brightness of a color to reduce its prominence in a visual, we also need to be sure that the elements we are using - text, shapes, lines - are visible enough for the audience. This principle is especially true for gray, but is also true with the other colors you're using. The ability for the reader to clearly see the element depends not just on the color used for the element, but also the size and background color. 

In the past, I've used an older tool for checking this, but just found out last week that Adobe has added this feature to their accessible page on [Adobe Color](https://color.adobe.com/create/color-contrast-analyzer) (before it was just a color blind checker, which is also great). While  [the OHA Style Guide](https://issuu.com/achafetz/docs/oha_styleguide) calls for contrast of 2.5 for larger text and at least 4 for smaller text, Adobe uses WCAG standards which are slightly higher, calling for a contrast of 3 for larger text (18pt and above / 14pt bold and above) and 4.5 for regular text (17pt and below). 

Adobe makes the check extremely easy to run and the output is extremely clear. In the screenshot below, we can see that the overall contrast of yellow on green does not pass the standard, as a result of a low contrast for small text. Adobe even provides alternatives on the right that would meet the contrast ratio.

![image.png](/assets/img/posts/20211108_adobe_contrast-tool.png)

Accessibility is important in our own products. For an upcoming presentation, the title slide below was created based on a pre-existing template. As you can quickly tell, the USAID Red against the USAID Blue is not at all readable and likely almost certainly doesn't pass the contrast rate.

![image.png](/assets/img/posts/20211108_usaid_default-slide.png)

We can check this with the Adobe Accessible tool, dropping in the text and background colors. We have an overall contrast rate of 1.96, which is below the minimum 4 for large text (18pt).

![image.png](/assets/img/posts/20211108_usaid_default-slide-contrast.png)

When creating graphics or presentations, I would highly recommend checking your work to ensure that all of your text and other elements are readable to your audience. Adobe Color makes this extremely easy to do.

Happy plotting!