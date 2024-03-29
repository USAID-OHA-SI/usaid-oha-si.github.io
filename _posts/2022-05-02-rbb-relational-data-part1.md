---
layout: "post"
title: "RBBS - 9 Relational Data - Part I"
date: "2022-05-02"
author: "Baboyma Kagniniwa"
categories: [corps, rbbs]
tags: [r]
thumbnail: "20220502_rbbs_9-relational.png"
output: github_document
---




Relational Data - Part one: Combining data sets through R/Dplyr Joins.

## Introduction

For today's session, we are covering **Relational Data**.

We will be using primary `dplyr` from `tidyverse` to demonstrate how to work with relational data and will also touch on other packages demonstrated in previous RBB sessions. 

This session covers a bit of the Computer Science / Information Technology concepts and much of the content comes from [Chapter 13 of R for Data Science Book](https://r4ds.had.co.nz/relational-data.html).

## Learning Objectives

Part 1

  - Relational Database Management Concepts
  - Handling duplicates / Checking Uniqueness
  - Joins - left, right, inner, full, 

## Recordings and Materials

Today's [recordings](https://drive.google.com/file/d/1bppRmdZBjUIyo5UUCYxBXO875ocFCR0m/view?usp=sharing) (for USAID staff only), [code](https://github.com/USAID-OHA-SI/coRps/tree/main/2022-05-02), and materials will be shared through the `coRps Presentations & Recordings` Google Spreadsheet, accessible through coRps calendar invite.

## Relational Data?

What is `Relational Data`?

Most data analyses, if not all, involve more than 1 data set and a lot of cleaning / munging.
This process will also lead to combining a lot data sets / tables, conduct your analysis in order to answer an analytic question. Because the information needed is usually spread among multiple tables of data [hence the need to combine them], so collectively they are called `relational data`.

A good example of a relational data are `MER Structured Dataset (MSD)`

**Note**: MSDs are outputs (products) of a process of combining relational data from a DATIM's Database Tables.

## Database (DB)

What's a database (DB) to you?

```
In computing, a database is an organized collection of data stored and accessed electronically. ~ Wikipedia
```

Small databases can be stored on a `file system` (eg: MS Access Database, SQLite, MangoDB, etc.), while large databases are hosted on computer clusters (MS SQL Server, Oracle, Postgres, etc.) or cloud storage (AWS S3, GCP, etc.). 

## Database Management System (DBMS)

A DBMS is a `software` that interacts with end users, applications, and the database itself to capture and analyze data. The software also includes the core functionality needed to administer the database. 

The most common DBMS is a relational (RDBMS). RDBMS model data as `columns and rows` in a series of related `tables`, and uses `Structured Query Language (SQL)` for writing and querying data.

**Note**: There are also non-relational databases (DBMS). RDBMS model data in a `non-tabular relations` way, and uses different query language. These database systems are currently known as `NoSQL` databases.


## R Packages & Data

We will use `dplyr` and `magrittr`. You can also use `tidyverse`. 

Our sample dataset is PEPFAR's `Site by IM MSD`

```
MER_Structured_Datasets_Site_IM_FY20-22_20220318_v2_1_<operatingunit>.zip
```

Let's reverse engineer the MSD and look at different data subsets and how they are related.

## Load Libraries 


```r
library(tidyverse) # Data munging
library(magrittr)
library(glamr)     # OHA Utilities 
library(gophr)     # OHA MSD Functions
```

## Load Data


```r
dir_msds <- si_path(type = "path_msd")

file_site_im_msd <- return_latest(
  folderpath = file.path("..", dir_msds),
  pattern = "^MER_.*_Site_IM_FY20-22_\\d{8}_.*_Nigeria.zip")

df_msd <- read_msd(file = file_site_im_msd)
```

## Explore MSD Structure


```r
  str_msd_sites <- list(
    # WHEN
    "periods" = c(
      "fiscal_year"
    ),
    # WHERE
    "orgunits" = c(
      "orgunituid",                 # ID
      "sitename",
      "operatingunit",
      "operatingunituid",
      "countryname",
      "snu1",
      "snu1uid",
      "psnu",
      "psnuuid",
      "snuprioritization",
      "typemilitary",
      "dreams",
      "communityuid",
      "community",
      "communityprioritization",
      "facilityuid",
      "facility",
      "facilityprioritization",
      "sitetype"
    ),
    # WHAT: dataElements
    "indicators" = c(
      "indicator",                  # ID
      "numeratordenom",
      "indicatortype",
      "disaggregate",
      "standardizeddisaggregate",
      "categoryoptioncomboname",
      "source_name"
    ),
    # WHAT: CategoryOptionsCombo
    "disaggregates" = c(
      "disaggregate",               # ID
      "standardizeddisaggregate",   # ID
      "categoryoptioncomboname",    # ID
      "ageasentered",
      "trendsfine",
      "trendssemifine",
      "trendscoarse",
      "sex",
      "statushiv",
      "statustb",
      "statuscx",
      "hiv_treatment_status",
      "otherdisaggregate",
      "otherdisaggregate_sub",
      "modality"
    ),
    # WHO: Mechs - AttributesOptionsCombo
    "mechanisms" = c(
      "fundingagency",
      "award_number",
      "mech_code",                  # ID
      "mech_name",
      "pre_rgnlztn_hq_mech_code",
      "primepartner",
      "prime_partner_duns"
    ),
    # VALUE
    "values" = c(
      "targets",
      "qtr1",
      "qtr2",
      "qtr3",
      "qtr4",
      "cumulative"
    ),
    # Slimmed Version of MSD,
    # Similar to Flat Files exported from EMRs
    "data" = c(
      "fiscal_year",  # => Used to reshape reshape / pivot
      "orgunituid",   # => Join to Org Hierarchy
      "mech_code",    # => Join to AttributesOptionsCombo tbl
      "indicator",
      "disaggregate",
      "standardizeddisaggregate",
      "categoryoptioncomboname",
      "targets",
      "qtr1",
      "qtr2",
      "qtr3",
      "qtr4",
      "cumulative"
    )
  )
```

## Prepare data sets (reverse engineering)


```r
# Org H.
df_orgs <- df_msd %>% 
  filter(fundingagency != "Dedup") %>% 
  select(all_of(str_msd_sites$orgunits)) %>% 
  distinct()

# Mechs
df_mechs <- df_msd %>% 
  filter(fundingagency != "Dedup") %>% 
  select(all_of(str_msd_sites$mechanisms)) %>% 
  distinct()

# Indicators
df_inds <- df_msd %>% 
  filter(fundingagency != "Dedup") %>% 
  select(all_of(str_msd_sites$indicators)) %>% 
  distinct()

df_disaggs <- df_msd %>% 
  filter(fundingagency != "Dedup") %>% 
  select(all_of(str_msd_sites$disaggregates)) %>% 
  distinct()

# Data / Reports -> Note no distinct used here
df_report_wide <- df_msd %>% 
  filter(fundingagency != "Dedup") %>% 
  select(all_of(str_msd_sites$data)) 

df_report_long <- df_report_wide %>% 
  pivot_longer(
    cols = c(starts_with("qtr"), cumulative, targets),
    names_to = "period",
    values_to = "value"
  ) %>% 
  mutate(
    value_type = case_when(
      str_detect(period, "qtr") ~ "qtr_results",
      period == "cumulative" ~ "results",
      period == "targets" ~ "targets",
      TRUE ~ NA_character_
    ),
    period = case_when(
      str_detect(period, "qtr") ~ paste0("FY", str_sub(fiscal_year, 3, 4), "Q", str_sub(period, 4)),
      TRUE ~ paste0("FY", str_sub(fiscal_year, 3, 4))
    )) %>% 
  relocate(period, .after = fiscal_year) %>% 
  relocate(value_type, .before = value)
```

## Review data sets 

WHERE, WHO, WHAT, WHEN + Data

## Org. Hierarchy


```r
#glimpse(df_orgs)
# Rows: 3,074
# Columns: 19
# $ orgunituid              <chr> "SSqrxMy7RHB", "X8smKtvqUob", "q~
# $ sitename                <chr> "Data reported above Site level"~
# $ operatingunit           <chr> "Nigeria", "Nigeria", "Nigeria",~
# $ operatingunituid        <chr> "PqlFzhuPcF1", "PqlFzhuPcF1", "P~
# $ countryname             <chr> "Nigeria", "Nigeria", "Nigeria",~
# $ snu1                    <chr> "Rivers", "Gombe", "Adamawa", "B~
# $ snu1uid                 <chr> "SSqrxMy7RHB", "XzSGf4ZxlR6", "e~
# $ psnu                    <chr> "Rivers", "Gombe", "Adamawa", "B~
# $ psnuuid                 <chr> "SSqrxMy7RHB", "XzSGf4ZxlR6", "e~
# $ snuprioritization       <chr> "1 - Scale-Up: Saturation", "4 -~
# $ typemilitary            <chr> "N", "N", "N", "N", "N", "N", "N~
# $ dreams                  <chr> "N", "N", "N", "N", "N", "N", "N~
# $ communityuid            <chr> "?", "VQgRF8xzLJH", "FlBE3mL1gu7~
# $ community               <chr> "Data reported above Community L~
# $ communityprioritization <chr> "Missing", "4 - Sustained", "4 -~
# $ facilityuid             <chr> "~", "X8smKtvqUob", "qY7ZkVcos1O~
# $ facility                <chr> "Data reported above Facility le~
# $ facilityprioritization  <chr> "Not Available", "4 - Sustained"~
# $ sitetype                <chr> "Above Site", "Facility", "Facil~
```

## Mechanisms


```r
# Mechs
#glimpse(df_mechs)
# Rows: 28
# Columns: 7
# $ fundingagency            <chr> "HHS/CDC", "HHS/CDC", "USAID", ~
# $ award_number             <chr> "18675", "5NU2GGH002097", "7200~
# $ mech_code                <chr> "18675", "18677", "81858", "818~
# $ mech_name                <chr> "ACTION to Control HIV Epidemic~
# $ pre_rgnlztn_hq_mech_code <chr> "18675", "18677", "81858", "818~
# $ primepartner             <chr> "INSTITUTE OF HUMAN VIROLOGY", ~
# $ prime_partner_duns       <chr> "850470568", "850492314", "6180~
```

## Indicators


```r
# Indicators
# glimpse(df_inds)
# Rows: 4,870
# Columns: 7
# $ indicator                <chr> "CXCA_SCRN", "CXCA_SCRN", "CXCA~
# $ numeratordenom           <chr> "N", "N", "N", "N", "N", "N", "~
# $ indicatortype            <chr> "DSD", "DSD", "DSD", "DSD", "DS~
# $ disaggregate             <chr> "Total Numerator", "Age/Sex/HIV~
# $ standardizeddisaggregate <chr> "Total Numerator", "Age/Sex/HIV~
# $ categoryoptioncomboname  <chr> "default", "30-34, Female, Posi~
# $ source_name              <chr> "Derived", "DATIM", "DATIM", "D~
```

## Disaggregations


```r
# glimpse(df_disaggs)
# Rows: 4,870
# Columns: 7
# $ indicator                <chr> "CXCA_SCRN", "CXCA_SCRN", "CXCA~
# $ numeratordenom           <chr> "N", "N", "N", "N", "N", "N", "~
# $ indicatortype            <chr> "DSD", "DSD", "DSD", "DSD", "DS~
# $ disaggregate             <chr> "Total Numerator", "Age/Sex/HIV~
# $ standardizeddisaggregate <chr> "Total Numerator", "Age/Sex/HIV~
# $ categoryoptioncomboname  <chr> "default", "30-34, Female, Posi~
# $ source_name              <chr> "Derived", "DATIM", "DATIM", "D~
```
## Data - Wide Format


```r
# Data / Reports Wide Format
# glimpse(df_report_wide)
# Rows: 3,176,597
# Columns: 13
# $ fiscal_year              <int> 2021, 2021, 2021, 2021, 2020, 2~
# $ orgunituid               <chr> "SSqrxMy7RHB", "X8smKtvqUob", "~
# $ mech_code                <chr> "18675", "18677", "81858", "818~
# $ indicator                <chr> "CXCA_SCRN", "CXCA_SCRN", "CXCA~
# $ disaggregate             <chr> "Total Numerator", "Age/Sex/HIV~
# $ standardizeddisaggregate <chr> "Total Numerator", "Age/Sex/HIV~
# $ categoryoptioncomboname  <chr> "default", "30-34, Female, Posi~
# $ targets                  <dbl> 3689, NA, NA, NA, NA, NA, NA, N~
# $ qtr1                     <dbl> NA, NA, NA, NA, NA, NA, NA, NA,~
# $ qtr2                     <dbl> NA, 1, NA, 10, NA, NA, 3, NA, 1~
# $ qtr3                     <dbl> NA, NA, NA, NA, NA, NA, NA, NA,~
# $ qtr4                     <dbl> NA, NA, 8, 32, 2, 83, 18, 2, NA~
# $ cumulative               <dbl> NA, 1, 8, 42, 2, 83, 21, 2, 1, ~
```

## Data - Long format


```r
# Data / Reports Wide Format
# glimpse(df_report_long)
# Rows: 19,059,582
# Columns: 10
# $ fiscal_year              <int> 2021, 2021, 2021, 2021, 2021, 2~
# $ period                   <chr> "FY21Q1", "FY21Q2", "FY21Q3", "~
# $ orgunituid               <chr> "SSqrxMy7RHB", "SSqrxMy7RHB", "~
# $ mech_code                <chr> "18675", "18675", "18675", "186~
# $ indicator                <chr> "CXCA_SCRN", "CXCA_SCRN", "CXCA~
# $ disaggregate             <chr> "Total Numerator", "Total Numer~
# $ standardizeddisaggregate <chr> "Total Numerator", "Total Numer~
# $ categoryoptioncomboname  <chr> "default", "default", "default"~
# $ value_type               <chr> "qtr_results", "qtr_results", "~
# $ value                    <dbl> NA, NA, NA, NA, NA, 3689, NA, 1~
```

## Relationship Keys

How are the MSDs put together?
How are the different data sets related to one another?

Relational data rely on `keys`, uniquely identifiable variable, to connect tables.
There are 2 types of keys, a `Primary Key` (uniquely identify a record in a table) and `Foreign Key` (uniquely identify a record in another table)

Keys can be used to test the uniqueness of records / observation in a table.

Based on the image below, how will you draw similar diagram for your MSD?

![ERD - Entity Relationship Diagram](erd.png)

## Duplicates, Uniqueness checks

The best way to check duplicates / uniqueness is to count the number of records by key. 
In R / Tidyverse world you will use `dplyr::count` function.


```r
df_orgs %>% 
  count(sitename) %>% 
  filter(n > 1)
```

## Duplicates, Uniqueness checks - Sitename


```r
df_orgs %>% 
  count(operatingunit, countryname, 
        psnu, community, sitetype, sitename) %>% 
  filter(n > 1)
```
## Duplicates, Uniqueness checks - Orgunituid


```r
df_orgs %>% 
  count(operatingunit, countryname, 
        psnu, community, sitetype, sitename, orgunituid) %>% 
  filter(n > 1)

df_orgs %>% 
  count(orgunituid) %>% 
  filter(n > 1)
```

## Combining data sets - Joins

How can we combine related data set? 

Data sets are related when the have 1 or multiple variables in common and they can be combined using the common variables. These variables are usually Primary and Foreign Keys of their respective tables.

Tables are combined using `Join`, and one can only combine 2 tables at the time. 

There are multiple type of join: 

`Inner Join` matches pairs of observations whenever their keys are equal
`Outer Join` keeps observations that appear in at least one of the table. There are 3 types of outer joins: left, right and full joins.
`Left Join` keeps all the observations in the left side table
`Right Join` Keeps all the observations in right side table
`Full Join` keeps all the observations in both tables

## Combining data sets - Joins Venn Diagram

![Joins Venn Diagram](joins2.png)

## Combining data sets - Joins

![Joins Graphics](joins.png)

## Practice time - inner join


```r
df_report_long %>% 
  filter(fiscal_year == 2022,
         value_type == "results",
         str_detect(mech_code, "^81")) %>% #distinct(mech_code)
  inner_join(x = ., 
             y = df_mechs %>% filter(mech_code == 81862), 
             by = "mech_code")
```

## Practice time - left join


```r
df_report_long %>% 
  filter(fiscal_year == 2022,
         value_type == "results",
         str_detect(mech_code, "^81")) %>% 
  left_join(x = ., 
            y = df_orgs, 
            by = "orgunituid")
```

## Practice time - right join


```r
df_report_long %>% 
  filter(fiscal_year == 2022,
         value_type == "results",
         str_detect(mech_code, "^81")) %>% 
  right_join(x = ., 
             y = df_orgs, 
             by = "orgunituid") %>% filter(is.na(fiscal_year))
```

## Practice time - full join


```r
df_report_long %>% 
  filter(fiscal_year == 2022,
         value_type == "results",
         str_detect(mech_code, "^81")) %>% 
  full_join(x = ., 
            y = df_orgs %>% filter(psnu == "Akwa Ibom"), 
            by = "orgunituid") %>% filter(is.na(fiscal_year) | is.na(psnu))
```
