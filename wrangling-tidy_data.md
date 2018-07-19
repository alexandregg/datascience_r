Data-Wrangling: Tidy Data
================
Alexandre Galiani Garmbis
18/07/2018

Web Scraping
------------

### Murders dataset

``` r
library(tidyverse)
library(rvest)
url <- "https://en.wikipedia.org/wiki/Murder_in_the_United_States_by_state"
h <- read_html(url)
murders_web <- h %>% html_nodes("table") %>% .[[2]] %>% html_table() %>% as_tibble()
colnames(murders_web) <- c("state", "population", "total", "murders", "gun_murders",
                           "gun_ownership", "total_rate", "murder_rate", "gun_murder_rate")
murders_web
```

    ## # A tibble: 51 x 9
    ##    state     population total murders gun_murders gun_ownership total_rate
    ##    <chr>     <chr>      <chr> <chr>   <chr>               <dbl>      <dbl>
    ##  1 Alabama   4,853,875  348   3[a]    3[a]                 48.9        7.2
    ##  2 Alaska    737,709    59    57      39                   61.7        8  
    ##  3 Arizona   6,817,565  309   278     171                  32.3        4.5
    ##  4 Arkansas  2,977,853  181   164     110                  57.9        6.1
    ##  5 Californ… 38,993,940 1,861 1,861   1,275                20.1        4.8
    ##  6 Colorado  5,448,819  176   176     115                  34.3        3.2
    ##  7 Connecti… 3,584,730  117   107     73                   16.6        3.3
    ##  8 Delaware  944,076    63    63      52                    5.2        6.7
    ##  9 District… 670,377    162   162     121                  25.9       24.2
    ## 10 Florida   20,244,914 1,041 — [b]   — [b]                32.5        5.1
    ## # ... with 41 more rows, and 2 more variables: murder_rate <chr>,
    ## #   gun_murder_rate <chr>

### Receitas de cozinha

``` r
url <- "http://foodnetwork.com.br/receita/pao-chipa/"

read_recipe <- function(url) {
  j <- read_html(url)
  recipe <- j %>% html_node(".secondary-title") %>% html_text()
  rend <- j %>% html_nodes(".result-rend") %>% html_text() %>% .[1]
  preptime <- j %>% html_nodes(".result-rend") %>% html_text() %>% .[2]
  ingredients <- j %>% html_nodes(".texto-receita.letter-simple.capitalize") %>% 
    html_children() %>% html_children() %>% html_text()
  return(list(recipe = recipe, rend = rend, preptime = preptime, ingredients = ingredients))
}

read_recipe(url)
```

    ## $recipe
    ## [1] "Pão Chipa"
    ## 
    ## $rend
    ## [1] "30 pães"
    ## 
    ## $preptime
    ## [1] "20 minutos"
    ## 
    ## $ingredients
    ## [1] "4 xícaras de polvilho"    "2 ovos"                  
    ## [3] "100 g de manteiga"        "250 g de queijo Sardo"   
    ## [5] "250 g de queijo holandês" "1 xícara de leite morno"
