---
layout: post
title: SI Standard Style with ggplot
date: 2020-04-24
author: Baboyma Kagniniwa
categories: tutorial
tags: [ggplot, vizualisation]
thumbnail: "iris_scatter_plot_si_style.png"
---

How to create standard visualizations across SI team with USAID's alternate fonts, Sans Source Pro. 

This font is not only not native to R, nor is it a standard to Windows.

### glitr
**glitr** is SI graphics [package](https://github.com/USAID-OHA-SI/glitr) to adorn your plots with ggplot

### Installation
glitr is not on CRAN, so you will have to install it directly from Github using devtools.

Follow the installation guide from the [github repo](glitr is not on CRAN, so you will have to install it directly from Github using devtools.)

Make sure install _Sans Source Pro_ font as well

### Use cases

- Load required R Packages

```{r}
library(tidyverse)
library(glitr)
```

- Make sure iris sample data is loaded

```{r}
iris %>% 
  glimpse()
```

```{r}
iris %>% head()
```
  
- Create a scatter plot with with default ggplot options

```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data")
  
ggsave("assets/img/tutorials/iris_scatter_plot_default.png")
```

![ggplot default theme](/assets/img/tutorials/iris_scatter_plot_default.png)


- Create a scatter plot with with ggplot minimal theme


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  theme_minimal()
  
ggsave("assets/img/tutorials/iris_scatter_plot_minimal.png")
```

![ggplot minimal theme](/assets/img/tutorials/iris_scatter_plot_minimal.png)


- Create a scatter plot with with SI Strandard Styles

*si_style()* function extends ggplot _minimal theme_ along with a re-adjustement of the plot title, subtitle and capture.

```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style.png")
```

![ggplot SI Style](/assets/img/tutorials/iris_scatter_plot_si_style.png)


*si_style_xgrid* displays only the _x axes_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_xgrid()

ggsave("assets/img/tutorials/iris_scatter_plot_si_style_xgrid.png")
```

![ggplot SI Style xgrid](/assets/img/tutorials/iris_scatter_plot_si_style_xgrid.png)


*si_style_ygrid* displays only the _y axes_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_ygrid()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style_ygrid.png")
```

![ggplot SI Style ygrid](/assets/img/tutorials/iris_scatter_plot_si_style_ygrid.png)


*si_style_xline* displays only the _x line_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_xline()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style_xline.png")
```

![ggplot SI Style xline](/assets/img/tutorials/iris_scatter_plot_si_style_xline.png)


*si_style_yline* displays only the _y line_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_yline()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style_yline.png")
```

![ggplot SI Style yline](/assets/img/tutorials/iris_scatter_plot_si_style_yline.png)


*si_style_xyline* displays only the _x and y lines_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_xyline()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style_xyline.png")
```

![ggplot SI Style xyline](/assets/img/tutorials/iris_scatter_plot_si_style_xyline.png)


*si_style_nolines* displays only the _no lines_


```{r}
iris %>% 
  ggplot(aes(Sepal.Length, y = Sepal.Width, colour = Species)) +
  geom_point() +
  labs(x="Length", y="Width",
      title = "IRIS Flowers Study",
      subtitle = "Sepal Dimensions",
      caption = "source: Edgar Anderson's Iris Data") +
  si_style_nolines()
  
ggsave("assets/img/tutorials/iris_scatter_plot_si_style_nolines.png")
```
![ggplot SI Style no lines](/assets/img/tutorials/iris_scatter_plot_si_style_nolines.png)


**Note**: These SI Styles are still under construction and all feedback / comments are welcome.
