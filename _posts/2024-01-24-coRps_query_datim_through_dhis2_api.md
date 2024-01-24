---
title: "Query DATIM through DHIS2 API"
author: "Baboyma Kagniniwa"
date: "2024-01-24"
output: html_document
---



![](https://unsplash.com/photos/FPK6K5OUFVA/download?ixid=M3wxMjA3fDB8MXxzZWFyY2h8MTV8fEFQSSUyMGVuZHBvaW50fGVufDB8fHx8MTcwNjEwMTAzOHww&force=true&w=1920)

[Tool used to build, test, publish APIs ...](https://www.postman.com/)

## Objectives

We are using this session as a walk through some of the recent changes we've made to `grabr`. Some of the generic functions we used to process `HFR` data have been migrated to `grabr` + additional functions that were used as utilities functions in various projects like `lastmile`, `gisr`, `MerQL`, `ff-check`, `ddc_validation`, etc.

We will cover 2 main group of functions and look at some use cases:

1.  DATIM Look up functions
2.  DATIM Query functions
3.  Use cases

Most of what we will be going through today is also located on the `grabr` [package site](https://usaid-oha-si.github.io/grabr/index.html)

Note: We took full advantage of PEPFAR/DATIM's [DHIS2 Web API](https://docs.dhis2.org/archive/en/2.32/developer/html/dhis2_developer_manual.html), [DHIS2 Documentation](https://docs.dhis2.org/en/use/user-guides/dhis-core-version-237/understanding-the-data-model/about-data-dimensions.html), and [DATIM Support Resources](https://help.datim.org/hc/en-us/articles/115002334246-DATIM-Data-Import-and-Exchange-Resources)

## Background / Refresher

API stands for `Application Programming Interface` and is a set of definitions and protocols designed to allow at least 2 pieces of software to **interact/interface** with each other.

There are multiple types of APIs: `REST`, `SOAP`, `RPC`, `Event-driven`, etc. These are software architectural styles in API design.

REST (Representational State Transfer) API, most popular and used by DHIS2, is designed to take advantage of HTTP methodologies. These methods (`GET`, `POST`, `PUT`, `DELETE`, etc) are also made available to REST APIs. For DATIM Queries, we mainly use the **GET** method to extract data from specific resources. Most, if not all, of our implementing partners use the **POST** method to submit data.

**APIs** use **endpoints**, which are digital locations, to expose resources to users and other application. An endpoint is a location where an API receives requests for a specific resource. Each endpoint is represented by URL (Universal Resource Locator) and can be accessed through pre-define HTTP Methods. GET is in most cases the default method and available for read only requests.

## DATIM API

Most of us access DATIM through the web application where the user interface allows us to execute specific tasks.

A simple example is the login page that uses GET and POST methods to grant us access to the content.

A great example is the `resources` endpont of the DATIM API where users can extract and view all available resources.


```r
  library(httr)

  #help("GET")
  
  GET(url = "https://final.datim.org")
```

```
## Response [https://final.datim.org/dhis-web-commons/security/login.action]
##   Date: 2024-01-24 15:48
##   Status: 200
##   Content-Type: text/html;charset=UTF-8
##   Size: 13.5 kB
## <!DOCTYPE HTML>
## <html class="loginPage" dir="ltr">
## <head>
##     <title>final</title>
##     <meta name="description" content="DHIS 2">
##     <meta name="keywords" content="DHIS 2">
##     <meta http-equiv="Content-Type" content="text/htm...
##     <link rel="shortcut icon" href="../../favicon.ico"/>
##     <script type="text/javascript" src="../javascript...
##     <script nonce="$cspNonce">
## ...
```

```r
  GET(url = "https:/final.datim.org/api/resources")
```

```
## Response [https://final.datim.org/dhis-web-commons/security/login.action]
##   Date: 2024-01-24 15:48
##   Status: 200
##   Content-Type: text/html;charset=UTF-8
##   Size: 13.5 kB
## <!DOCTYPE HTML>
## <html class="loginPage" dir="ltr">
## <head>
##     <title>final</title>
##     <meta name="description" content="DHIS 2">
##     <meta name="keywords" content="DHIS 2">
##     <meta http-equiv="Content-Type" content="text/htm...
##     <link rel="shortcut icon" href="../../favicon.ico"/>
##     <script type="text/javascript" src="../javascript...
##     <script nonce="$cspNonce">
## ...
```

```r
  GET(url = "https://final.datim.org/api/resources", 
      authenticate(user = glamr::datim_user(), 
                   password = glamr::datim_pwd()))
```

```
## Response [https://final.datim.org/api/resources]
##   Date: 2024-01-24 15:48
##   Status: 200
##   Content-Type: application/json;charset=UTF-8
##   Size: 12.8 kB
```

```r
  GET(url = "https://final.datim.org/api/resources", 
      authenticate(user = glamr::datim_user(), 
                   password = glamr::datim_pwd())) |>
    content("text") |>
    jsonlite::fromJSON()
```

This is a bit long and a bit too much information for most of us. We just want to be able to extract the data and move on to the analytics and visualization.

## `grabr` package to the rescue

To make our lives easier, we've wrapped these steps under a couple of functions that are in turn used in other query functions.

1.  `datim_execute_query()`
2.  `datim_process_query()`


```r
  library(grabr)

  res_url <- "https://final.datim.org/api/resources"

  datim_execute_query(url = res_url,
                      username = glamr::datim_user(), 
                      password = glamr::datim_pwd())

  # For those that have the credential setup, it's just 1 line
  
  datim_execute_query(res_url)
```

The trick here is build your view and get the URL through `Data Visualiser > Download > Advanced > json` and pass it to the function.

### Look up and query functions

Look up functions

-   `get_ouuid()` provides the uid for the specified OU
-   `get_ouorglabel()` provides the label of the org level. Eg. Zambia's psnu is at level #5
-   `get_ouorglevel()` is the opposite of get_ouorglabel. Provides the level of the orgunit label
-   etc ...

Query functions

-   `get_outable()` returns PEPFAR OU/Countries along with their UIDs and Code
-   `get_levels()` returns organizational hierarchy levels
-   `get_ouorgs()` returns OU's Orgunits at a specific level
-   `datim_dimension()`
-   `datim_query()`
-   `datim_sqlviews()` returns list of sqlviews, or uid/data of a specific view
-   `datim_orgunits()` returns organisation units
-   `datim_mechs()` returns implementing mechanisms
-   etc ...


```r
  library(tidyverse)

  cntry <- "Nigeria"
  
  cntries <- c("Mozambique", "Zambia")
  
  # We could extract country uid by a simple filter
  glamr::pepfar_country_list %>% 
    filter(country == cntry) %>% 
    pull(country_uid)
```


```r
  # or user one of the lookup functions
  get_ouuid(operatingunit = cntry) 
```


```r
  cntries %>% map(get_ouuid) %>% unlist()
```

The above code is simple enough because PEPFAR country list data has already been same in `glamr` package. Nothing sensitive here. What happens when the table is not saved?


```r
  # Look up org levels
  get_ouorglabel(operatingunit = cntry, org_level = 5)
```

```
## [1] "community"
```

```r
  1:5 %>% map(~get_ouorglabel(operatingunit = cntry, org_level = .x)) %>% unlist()
```

```
## [1] "global"         "region"         "country"       
## [4] "prioritization" "community"
```

```r
  #get_ouorglabel
  
  #get_levels
  
  # Look up org types
  get_ouorglevel(operatingunit = cntry, org_type = "prioritization")
```

```
## [1] 4
```

```r
  c("community", "facility") %>% 
    map(~get_ouorglevel(operatingunit = cntry, org_type = .x)) %>% unlist()
```

```
## [1] 5 6
```

Some of us are great at memorizing uids, levels, and other complicated strings. Lucky you. For those like me, enjoy the look up functions. But seriously though, these look up functions comes in handy when you need to query data with multiple parameters.


```r
  # extract orgunits for different levels
  cntry <- "Mozambique"

  cntry_uid <- get_ouuid(cntry)
  
  my_orgs <- c("community", "prioritization", "country")
  
  # Look up levels
  my_orgs %>% 
    map(~get_ouorglevel(operatingunit = cntry, org_type = .x)) %>% 
    unlist()
```

```
## [1] 5 4 3
```

```r
  # Extract orgunits for specific levels
  my_orgs %>% 
    map(~get_ouorglevel(operatingunit = cntry, org_type = .x)) %>% 
    unlist() %>% 
    map(~get_ouorgs(cntry_uid, level = .x)) %>% 
    bind_rows()
```

```r
  # Extract orgunits for specific levels & append the look details to the final output
  my_orgs %>% 
    map(~get_ouorglevel(operatingunit = cntry, org_type = .x)) %>% 
    unlist() %>% 
    map2(my_orgs, function(.x, .y) {
      get_ouorgs(cntry_uid, level = .x) %>% 
        mutate(
          level = .x,
          label = .y
        )
    }) %>% 
    bind_rows()
```

DHIS2 leverage `dimensions` to build complex datasets. Let's look at what DATIM has to offer.

```r
  # List all dimensions
  df_dims <- datim_dimensions()

  df_dims %>% glimpse()
```


```r
  df_dims %>% head()
```

```r
  # filter age specific dimensions
  df_dims %>% filter(str_detect(dimension, "Age"))
```

```r
  # List options available within a dimension
  datim_dim_items(dimension = "Age: FY24 Target Age Bands")
```

```r
  datim_dim_items(dimension = "Funding Agency")
```


```r
  # Get specific dimension uid
  datim_dim_item(dimension = "Funding Agency", name = "USAID")
```

Knowing how to look up element of specific dimensions can help you build a complex query.


```r
  # SI Backstop country
  cntry <- "Malawi"  

  # More detailed requests with dimension names (converted into uid)
  datim_query(
    ou = cntry,                    # Operating unit
    level = "prioritization",      # org level
    pe = "2023Oct",                # periods
    ta = "PLHIV",                  # From dimension: Technical Area
    value = "MER Targets",         # From dimension: Targets / Results
    disaggs = "Age/Sex/HIVStatus", # From dimension: Disaggregation Type
    dimensions = c("Sex", "Age: Semi-fine age"),         # Additional dimension: Sex
    baseurl = "https://final.datim.org/",
    verbose = TRUE                 # display notification
  )
```

```r
  # Simplified query PLHIV number
  datim_pops(ou = cntry)
```


There are also similar version of these datasets pre-processed and stored as sqlviews. We've also wrap the sqlview queries under `datim_sqlviews()`.


```r
  # SI Backstop country 

  cntry <- "Nigeria"

  cntry_iso <- glamr::pepfar_country_list %>% 
    filter(country == cntry) %>% 
    pull(country_iso)
  
  # List of all sqlviews
  
  df_views <- datim_sqlviews()

  df_views %>% glimpse()
```

```r
  df_views %>% head()
```

```r
  # Filter specific views
  df_views %>% filter(str_detect(name, "Data Exchange"))
```

```r
  # check the uid of Organisation Units
  datim_sqlviews(view_name = "Data Exchange: Organisation Units", dataset = FALSE)
```

```r
  # Extract Organisation Units data for a specific country - All levels with child / parent links
  df_orgs <- datim_sqlviews(view_name = "Data Exchange: Organisation Units", 
                             dataset = TRUE,
                             query = list(type = "variable", params = list("OU" = cntry_iso)))
  
  df_orgs %>% glimpse()
```

```r
  # Extract mechanisms
  df_mechs <- datim_sqlviews(view_name = "Mechanisms partners agencies OUS Start End", 
                             dataset = TRUE,
                             query = list(type = "field", params = list("OU" = cntry)))
  
  df_mechs %>% glimpse()
```

To make simple for users, we've wrapped these into specific query functions

```r
  # Extract Organisation Units data for a specific country
  datim_orgunits(cntry = "Cote d'Ivoire") %>% glimpse()
```

```r
  # Extract Organisation Units data for a specific country - expand child / parent relationship from facility to OU
  # Note some missing levels may be filled in or duplicated in for the the reshaping to work
  datim_orgunits(cntry = "Cote d'Ivoire", reshape = TRUE) %>% glimpse()
```

ENJOY!

WHEN IN TROUBLE ALWAYS USE HELP FUNCTION OR ASK FOR HELP :-)
