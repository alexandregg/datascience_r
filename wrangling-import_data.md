Data-Wrangling: Import Data
================
Alexandre Galiani Garmbis
18/07/2018

Manipulando Caminhos e Arquivos no R
------------------------------------

``` r
(mypath <- getwd())
```

    ## [1] "/Users/alexandregg/github/Data-Science-R"

``` r
(filepath <- system.file("extdata", package = "dslabs"))
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.5/Resources/library/dslabs/extdata"

``` r
(files <- list.files(filepath))
```

    ## [1] "fertility-two-countries-example.csv"                    
    ## [2] "life-expectancy-and-fertility-two-countries-example.csv"
    ## [3] "murders.csv"

``` r
(origin <- file.path(filepath, files[3]))
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.5/Resources/library/dslabs/extdata/murders.csv"

``` r
file.copy(origin, file.path(mypath, "data"))
```

    ## [1] FALSE

``` r
dir("data")
```

    ## [1] "murders.csv"          "murders2.csv"         "Week_3_lab_data.xlsx"

``` r
file.exists("data/murders.csv")
```

    ## [1] TRUE

Importando dados com `readr` e `readxl`
---------------------------------------

### Carregando o Tidyverse:

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.5
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(readr)
library(readxl)
```

### CSV - Separado por virgulas

``` r
read_lines("data/murders.csv", n_max = 3)
```

    ## [1] "state,abb,region,population,total" "Alabama,AL,South,4779736,135"     
    ## [3] "Alaska,AK,West,710231,19"

``` r
read_csv("data/murders.csv") %>% print
```

    ## Parsed with column specification:
    ## cols(
    ##   state = col_character(),
    ##   abb = col_character(),
    ##   region = col_character(),
    ##   population = col_integer(),
    ##   total = col_integer()
    ## )

    ## # A tibble: 51 x 5
    ##    state                abb   region    population total
    ##    <chr>                <chr> <chr>          <int> <int>
    ##  1 Alabama              AL    South        4779736   135
    ##  2 Alaska               AK    West          710231    19
    ##  3 Arizona              AZ    West         6392017   232
    ##  4 Arkansas             AR    South        2915918    93
    ##  5 California           CA    West        37253956  1257
    ##  6 Colorado             CO    West         5029196    65
    ##  7 Connecticut          CT    Northeast    3574097    97
    ##  8 Delaware             DE    South         897934    38
    ##  9 District of Columbia DC    South         601723    99
    ## 10 Florida              FL    South       19687653   669
    ## # ... with 41 more rows

``` r
url <- "https://raw.githubusercontent.com/rafalab/dslabs/master/inst/extdata/murders.csv"
data1 <- read_csv(url)

# OR

download.file(url, "data/murders2.csv")
data2 <- read_csv("data/murders2.csv")

# OR

temp <- tempfile()
download.file(url, temp)
data3 <- read_csv(temp)
file.remove(temp)
```

    ## [1] TRUE

``` r
setequal(data1, data2, data3)
```

    ## TRUE

### Excel

``` r
excel_sheets("data/Week_3_lab_data.xlsx")
```

    ## [1] "Sheet1"

``` r
read_excel("data/Week_3_lab_data.xlsx", sheet = "Sheet1")
```

    ## # A tibble: 101 x 3
    ##    `2.830.1x`             `Week 3`           X__1              
    ##    <chr>                  <chr>              <chr>             
    ##  1 Lab data for Problem 4 <NA>               <NA>              
    ##  2 <NA>                   Bending            <NA>              
    ##  3 <NA>                   High Depth         Turning           
    ##  4 Thermoforming          Low Thickness      (Outer_           
    ##  5 Average Dia. (mn)      Angle (º)          (mm)              
    ##  6 50.302999999999997     76.924999999999997 10.79             
    ##  7 50.307000000000002     76.314999999999998 10.781000000000001
    ##  8 50.35                  76.00333333333333  10.798999999999999
    ##  9 50.292999999999999     75.521666666666661 10.816000000000001
    ## 10 50.286999999999999     75.056666666666672 10.791            
    ## # ... with 91 more rows
