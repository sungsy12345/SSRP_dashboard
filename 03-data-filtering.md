---
title: "Data Cleaning"
author: "Fernando Hoces de la Guardia"
output: html_document
editor_options: 
  chunk_output_type: console
---



<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
#tidy_dfs <- list.files(paste0(clean_path,"/processed/"))
#file.copy(paste0(clean_path,"/processed/",tidy_dfs), 
#          paste0("./processed/",tidy_dfs),
#          overwrite = TRUE)
```

### First: basic clean up  
- load data  
- clean some DOIs  
- rename the title of a reproduction  
- generate a variable that takes the same value for titles that sound similar  
- generate unique identifier for each reproduction  
- Identify the maximum number of claims and display items reproduced across all reproduction attempts

<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
# dis_df <- read_csv("./processed/tidy_di_df.csv") %>%
#     mutate(paper_title= ifelse(
#         test = # Fixing titles
#             (paper_title == "[Fernando] Railroads of the Raj: Estimating the Impact of Transportation Infrastructure"),
#             yes = "Railroads of the Raj: Estimating the Impact of Transportation Infrastructure",
#             no = paper_title),
#         similar_title = phonetic(paper_title)
#     ) %>%
#     filter(!is.na(repro_score))
```

<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
# claims_df <- read_csv("./processed/tidy_claim_df.csv") %>%
#     mutate(paper_title= ifelse(
#         test = # Fix Fernando's title
#             (
#                 paper_title == "[Fernando] Railroads of the Raj: Estimating the Impact of Transportation Infrastructure"
#             ),
#             yes = "Railroads of the Raj: Estimating the Impact of Transportation Infrastructure",
#             no = paper_title
#         ),
#         similar_title = phonetic(paper_title)
#     ) # 98 reproductions (no abandoned)
```

<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
    # abandoned_df <- read_csv("./processed/tidy_abandoned_df.csv") %>%
    # mutate(paper_title= ifelse(
    #     test = # Fix Fernando's title
    #         (
    #             paper_title == "[Fernando] Railroads of the Raj: Estimating the Impact of Transportation Infrastructure"
    #         ),
    #         yes = "Railroads of the Raj: Estimating the Impact of Transportation Infrastructure",
    #         no = paper_title
    #     ),
    #     similar_title = phonetic(paper_title)
    # )  # 104 reproductions total
```

# Make sure there's at least one scored Display Item

The rationale for this filter is that papers should have some sort of reproducibility score associated with them to be counted.

<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
# has_score <- dis_df %>%
#     select(reproduction_id) %>%
#     unique()
# 
# claims_df <- claims_df %>%
#     inner_join(has_score,
#                by="reproduction_id")
```

# Future Filters

This section explains how to add a filter in the future. Additional features should have a title (e.g. "Make sure there's at least one estimate associated with each claim") followed by a description of the reason we want this filter.

Code for the filter should reduce the number of observations in `claims_df`, `dis_df`, and/or `abandoned_df`, writing over the old df.

# Temporary

FIXME: This section uses the clean data set used for working paper. Mimics all cleaning above.
One change is that, particularly given the data from ssrp_analysis do not contain and use paper titles, but rather doi,
codes are changed to use doi also.

<div class="knitr-options" data-fig-width="576" data-fig-height="460"></div>

``` r
# Load clean data
di_cl_df <- read.csv("data(temp)/di_cl_df_20240401.csv")
tidy_all_repro_df <- read.csv("data(temp)/tidy_all_repro_df_20240401.csv")
tidy_reproducer_df <- read.csv("data(temp)/tidy_reproducer_df_20240401.csv")


# Check if there is any missing (NA or "") in ID variables
di_cl_id_check <- di_cl_df %>% 
    filter((is.na(claim_N) | claim_N == "" | is.na(DI_N) | DI_N == "") & abandoned != 1) %>% 
    nrow()
stopifnot(di_cl_id_check == 0)

repro_paper_id_check <- tidy_all_repro_df %>% 
    filter(is.na(reproduction_id) | reproduction_id == "" | is.na(doi) | doi == "") %>% 
    nrow() 
stopifnot(repro_paper_id_check == 0)
    
# How many observations? (Using data from APril 1, 2024)
nrow(di_cl_df) #840 obs
```

```
[1] 840
```

``` r
nrow(tidy_all_repro_df) #260 obs
```

```
[1] 260
```

``` r
# How many papers, reproductions, claims, and DIs?

    # DI from completed repo (830)
    di_cl_df %>% 
        filter(abandoned == 0) %>%
        select(reproduction_id, claim_N, DI_N) %>%
        unique() %>% 
        nrow()
```

```
[1] 830
```

``` r
    # DI from abandoned repo (10)
    di_cl_df %>% 
        filter(abandoned == 1) %>%
        select(reproduction_id, claim_N, DI_N) %>%
        unique() %>% 
        nrow()
```

```
[1] 10
```

``` r
    # Claims from completed repo (500)
    di_cl_df %>% 
        filter(abandoned == 0) %>%
        select(reproduction_id, claim_N) %>%
        unique() %>% 
        nrow()
```

```
[1] 500
```

``` r
    # Claims from abandoned repo (10)
    di_cl_df %>% 
        filter(abandoned == 1) %>%
        select(reproduction_id, claim_N) %>%
        unique() %>% 
        nrow()    
```

```
[1] 10
```

``` r
    # Completed Reproductions (250)
    tidy_all_repro_df %>%
        filter(abandoned == 0) %>%
        select(reproduction_id) %>%
        unique() %>% 
        nrow()    
```

```
[1] 250
```

``` r
    # Abandoned Reproductions (10)
    tidy_all_repro_df %>%
        filter(abandoned == 1) %>%
        select(reproduction_id) %>%
        unique() %>% 
        nrow()       
```

```
[1] 10
```

``` r
    # Papers from completed reproductions (178)
    tidy_all_repro_df %>%
        filter(abandoned == 0) %>%
        select(doi) %>%
        unique() %>% 
        nrow()    
```

```
[1] 178
```

``` r
    # Papers from abandoned reproductions (8)
    tidy_all_repro_df %>%
        filter(abandoned == 1) %>%
        select(doi) %>%
        unique() %>% 
        nrow()     
```

```
[1] 8
```

``` r
    # Papers from all reproductions (186) - This doesn't need to necessarily be additive of above two.
    tidy_all_repro_df %>%
        select(doi) %>%
        unique() %>% 
        nrow()    
```

```
[1] 186
```
