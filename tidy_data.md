Tidy Data
================

``` r
library(tidyverse)
```

    ## -- Attaching packages ----------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.1     v purrr   0.3.4
    ## v tibble  3.0.1     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts -------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## pivot\_longer

Load PULSE data

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

Wide format to long format:

``` r
pulse_data_tidy = 
  pulse_data %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
    )
```

rewrite, combine, and extend (add mutate step):

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
    ) %>% 
  relocate(id, visit) %>% 
  mutate(visit = recode(visit, "bl" = "00m"))
```

## pivot\_wider

Make up some data

``` r
analysis_result = 
  tibble(
    group = c("treatment","treatment", "placebo", "placebo"),
    time = c("pre", "pre", "post", "post"),
    mean = c(4, 8, 3.5, 4)
  )
analysis_result  %>%  
  pivot_wider(
    names_from = "time",
    values_from = "mean"
  )
```

    ## Warning: Values are not uniquely identified; output will contain list-cols.
    ## * Use `values_fn = list` to suppress this warning.
    ## * Use `values_fn = length` to identify where the duplicates arise
    ## * Use `values_fn = {summary_fun}` to summarise duplicates

    ## # A tibble: 2 x 3
    ##   group     pre       post     
    ##   <chr>     <list>    <list>   
    ## 1 treatment <dbl [2]> <NULL>   
    ## 2 placebo   <NULL>    <dbl [2]>
