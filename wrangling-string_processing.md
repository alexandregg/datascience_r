Data-Wrangling: String Processing
================
Alexandre Galiani Garmbis
18/07/2018

String: `""` ou `''`
--------------------

``` r
a <- "foo"
b <- "2'"
c <- '20"'
cat(a,b,c)
```

    ## foo 2' 20"

Escape: `\`
-----------

``` r
d <- "foo 2'20\""
cat(d)
```

    ## foo 2'20"

Pacote stringr no tidyverse
---------------------------

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.5
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(stringr)
murders_raw %>% summarize_all(function(x) any(str_detect(x, pattern=",")))
```

    ## # A tibble: 1 x 9
    ##   state population total murders gun_murders gun_ownership total_rate
    ##   <lgl> <lgl>      <lgl> <lgl>   <lgl>       <lgl>         <lgl>     
    ## 1 FALSE TRUE       TRUE  TRUE    TRUE        FALSE         FALSE     
    ## # ... with 2 more variables: murder_rate <lgl>, gun_murder_rate <lgl>

``` r
murders <- mutate_at(murders_raw, 2:3, parse_number)
murders
```

    ## # A tibble: 51 x 9
    ##    state     population total murders gun_murders gun_ownership total_rate
    ##    <chr>          <dbl> <dbl> <chr>   <chr>               <dbl>      <dbl>
    ##  1 Alabama      4853875   348 3[a]    3[a]                 48.9        7.2
    ##  2 Alaska        737709    59 57      39                   61.7        8  
    ##  3 Arizona      6817565   309 278     171                  32.3        4.5
    ##  4 Arkansas     2977853   181 164     110                  57.9        6.1
    ##  5 Californ…   38993940  1861 1,861   1,275                20.1        4.8
    ##  6 Colorado     5448819   176 176     115                  34.3        3.2
    ##  7 Connecti…    3584730   117 107     73                   16.6        3.3
    ##  8 Delaware      944076    63 63      52                    5.2        6.7
    ##  9 District…     670377   162 162     121                  25.9       24.2
    ## 10 Florida     20244914  1041 — [b]   — [b]                32.5        5.1
    ## # ... with 41 more rows, and 2 more variables: murder_rate <chr>,
    ## #   gun_murder_rate <chr>

Heights
-------

``` r
data("reported_heights")
problems <- reported_heights %>% as_tibble() %>%
  mutate(height_dbl = as.numeric(height), na = is.na(height_dbl)) %>%
  filter(na == TRUE | !between(height_dbl, 50, 84))
```

    ## Warning in evalq(as.numeric(height), <environment>): NAs introduzidos por
    ## coerção

``` r
problems
```

    ## # A tibble: 292 x 5
    ##    time_stamp          sex    height   height_dbl na   
    ##    <chr>               <chr>  <chr>         <dbl> <lgl>
    ##  1 2014-09-02 15:16:23 Male   6               6   FALSE
    ##  2 2014-09-02 15:16:28 Male   "5' 4\""       NA   TRUE 
    ##  3 2014-09-02 15:16:32 Female 5.3             5.3 FALSE
    ##  4 2014-09-02 15:16:37 Female 165cm          NA   TRUE 
    ##  5 2014-09-02 15:16:41 Male   511           511   FALSE
    ##  6 2014-09-02 15:16:46 Male   6               6   FALSE
    ##  7 2014-09-02 15:16:50 Female 2               2   FALSE
    ##  8 2014-09-02 15:16:52 Male   5'7            NA   TRUE 
    ##  9 2014-09-02 15:16:56 Male   >9000          NA   TRUE 
    ## 10 2014-09-02 15:16:56 Male   "5'7\""        NA   TRUE 
    ## # ... with 282 more rows

REGEX
=====

``` r
pattern <- "^[4-7]'\\d{1,2}\"$"
problems %>% filter(str_detect(height, pattern))
```

    ## # A tibble: 14 x 5
    ##    time_stamp          sex    height   height_dbl na   
    ##    <chr>               <chr>  <chr>         <dbl> <lgl>
    ##  1 2014-09-02 15:16:56 Male   "5'7\""          NA TRUE 
    ##  2 2014-09-02 15:17:09 Female "5'3\""          NA TRUE 
    ##  3 2014-10-19 13:08:30 Male   "5'5\""          NA TRUE 
    ##  4 2014-10-24 10:59:48 Female "5'2\""          NA TRUE 
    ##  5 2015-01-26 11:40:33 Female "5'3\""          NA TRUE 
    ##  6 2015-04-05 13:18:44 Female "5'2\""          NA TRUE 
    ##  7 2016-01-25 08:15:45 Female "5'8\""          NA TRUE 
    ##  8 2016-01-25 22:02:03 Male   "5'11\""         NA TRUE 
    ##  9 2016-01-26 09:49:15 Male   "5'7\""          NA TRUE 
    ## 10 2016-01-26 10:02:26 Male   "6'1\""          NA TRUE 
    ## 11 2016-02-11 11:49:33 Male   "5'8\""          NA TRUE 
    ## 12 2016-04-02 12:48:06 Male   "6'3\""          NA TRUE 
    ## 13 2016-05-22 08:02:42 Male   "5'7\""          NA TRUE 
    ## 14 2017-02-06 09:57:53 Male   "6'4\""          NA TRUE

``` r
pattern1 <- "^\\d\\s*'\\s*\\d{1,2}\\.*\\d*'*\"*$"
problems %>% filter(str_detect(height, pattern1))
```

    ## # A tibble: 56 x 5
    ##    time_stamp          sex    height   height_dbl na   
    ##    <chr>               <chr>  <chr>         <dbl> <lgl>
    ##  1 2014-09-02 15:16:28 Male   "5' 4\""         NA TRUE 
    ##  2 2014-09-02 15:16:52 Male   5'7              NA TRUE 
    ##  3 2014-09-02 15:16:56 Male   "5'7\""          NA TRUE 
    ##  4 2014-09-02 15:17:09 Female "5'3\""          NA TRUE 
    ##  5 2014-09-02 15:19:48 Male   5'11             NA TRUE 
    ##  6 2014-09-04 00:46:45 Male   5'9''            NA TRUE 
    ##  7 2014-09-04 10:29:44 Male   5'10''           NA TRUE 
    ##  8 2014-09-18 21:40:23 Male   5' 10            NA TRUE 
    ##  9 2014-10-19 13:08:30 Male   "5'5\""          NA TRUE 
    ## 10 2014-10-24 10:59:48 Female "5'2\""          NA TRUE 
    ## # ... with 46 more rows

``` r
pattern2 <- "^[4-6]\\s*[\\.|,]\\s*([0-9]|10|11)$"
problems %>% filter(str_detect(height, pattern2))
```

    ## # A tibble: 62 x 5
    ##    time_stamp          sex    height height_dbl na   
    ##    <chr>               <chr>  <chr>       <dbl> <lgl>
    ##  1 2014-09-02 15:16:32 Female 5.3           5.3 FALSE
    ##  2 2014-09-03 21:43:00 Male   5.5           5.5 FALSE
    ##  3 2014-09-04 06:31:03 Male   6.5           6.5 FALSE
    ##  4 2014-09-06 17:01:32 Male   5.8           5.8 FALSE
    ##  5 2014-09-07 14:35:35 Female 5.6           5.6 FALSE
    ##  6 2014-09-09 20:00:38 Male   5,3          NA   TRUE 
    ##  7 2014-09-15 07:56:56 Male   5.9           5.9 FALSE
    ##  8 2014-09-15 14:38:58 Male   6,8          NA   TRUE 
    ##  9 2014-09-19 05:04:16 Male   5.5           5.5 FALSE
    ## 10 2014-09-30 21:44:18 Male   6.2           6.2 FALSE
    ## # ... with 52 more rows

``` r
problems %>% filter(between(height_dbl/2.54, 54, 81))
```

    ## # A tibble: 91 x 5
    ##    time_stamp          sex    height height_dbl na   
    ##    <chr>               <chr>  <chr>       <dbl> <lgl>
    ##  1 2014-09-04 09:24:41 Female 150           150 FALSE
    ##  2 2014-09-07 16:33:46 Male   175           175 FALSE
    ##  3 2014-09-07 18:18:31 Male   177           177 FALSE
    ##  4 2014-09-19 11:01:10 Male   178           178 FALSE
    ##  5 2014-09-26 05:34:34 Female 163           163 FALSE
    ##  6 2014-10-05 03:41:48 Male   175           175 FALSE
    ##  7 2014-10-13 03:09:39 Male   178           178 FALSE
    ##  8 2014-10-15 16:46:17 Female 165           165 FALSE
    ##  9 2014-10-21 18:26:06 Female 165           165 FALSE
    ## 10 2014-10-23 12:15:48 Male   180           180 FALSE
    ## # ... with 81 more rows

### OR "|"

``` r
problems %>% filter(str_detect(height, "cm|inches|feet"))
```

    ## # A tibble: 8 x 5
    ##   time_stamp          sex    height                 height_dbl na   
    ##   <chr>               <chr>  <chr>                       <dbl> <lgl>
    ## 1 2014-09-02 15:16:37 Female 165cm                          NA TRUE 
    ## 2 2014-09-02 15:18:00 Male   5 feet and 8.11 inches         NA TRUE 
    ## 3 2014-10-08 19:19:33 Female Five foot eight inches         NA TRUE 
    ## 4 2015-05-27 08:03:32 Male   5 feet 7inches                 NA TRUE 
    ## 5 2016-01-26 15:19:32 Male   5ft 9 inches                   NA TRUE 
    ## 6 2016-01-26 15:19:51 Male   5 ft 9 inches                  NA TRUE 
    ## 7 2016-03-18 00:45:39 Male   5 feet 6 inches                NA TRUE 
    ## 8 2017-06-19 04:20:32 Male   170 cm                         NA TRUE

### Digits "\\d"

``` r
problems %>% filter(str_detect(height, "cm|inches|feet"), !str_detect(height, "\\d"))
```

    ## # A tibble: 1 x 5
    ##   time_stamp          sex    height                 height_dbl na   
    ##   <chr>               <chr>  <chr>                       <dbl> <lgl>
    ## 1 2014-10-08 19:19:33 Female Five foot eight inches         NA TRUE

### str\_view e str\_view\_all

``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view("\\d")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-281c87dd7506aacf3228">{"x":{"html":"<ul>\n  <li><span class='match'>1<\/span>65cm<\/li>\n  <li><span class='match'>5<\/span> feet and 8.11 inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li><span class='match'>5<\/span> feet 7inches<\/li>\n  <li><span class='match'>5<\/span>ft 9 inches<\/li>\n  <li><span class='match'>5<\/span> ft 9 inches<\/li>\n  <li><span class='match'>5<\/span> feet 6 inches<\/li>\n  <li><span class='match'>1<\/span>70 cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view_all("\\d\\d")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-c64e2203e2754fe4a2c1">{"x":{"html":"<ul>\n  <li><span class='match'>16<\/span>5cm<\/li>\n  <li>5 feet and 8.<span class='match'>11<\/span> inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li>5 feet 7inches<\/li>\n  <li>5ft 9 inches<\/li>\n  <li>5 ft 9 inches<\/li>\n  <li>5 feet 6 inches<\/li>\n  <li><span class='match'>17<\/span>0 cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### Character classes

``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view_all("[59]")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-4bb7f0a25d68e0b7513b">{"x":{"html":"<ul>\n  <li>16<span class='match'>5<\/span>cm<\/li>\n  <li><span class='match'>5<\/span> feet and 8.11 inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li><span class='match'>5<\/span> feet 7inches<\/li>\n  <li><span class='match'>5<\/span>ft <span class='match'>9<\/span> inches<\/li>\n  <li><span class='match'>5<\/span> ft <span class='match'>9<\/span> inches<\/li>\n  <li><span class='match'>5<\/span> feet 6 inches<\/li>\n  <li>170 cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view_all("[5-7]")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-124c032dd735f74ab712">{"x":{"html":"<ul>\n  <li>1<span class='match'>6<\/span><span class='match'>5<\/span>cm<\/li>\n  <li><span class='match'>5<\/span> feet and 8.11 inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li><span class='match'>5<\/span> feet <span class='match'>7<\/span>inches<\/li>\n  <li><span class='match'>5<\/span>ft 9 inches<\/li>\n  <li><span class='match'>5<\/span> ft 9 inches<\/li>\n  <li><span class='match'>5<\/span> feet <span class='match'>6<\/span> inches<\/li>\n  <li>1<span class='match'>7<\/span>0 cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### Anchors

``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view_all("^\\d*cm$")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-51d8a134d603eeddf981">{"x":{"html":"<ul>\n  <li><span class='match'>165cm<\/span><\/li>\n  <li>5 feet and 8.11 inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li>5 feet 7inches<\/li>\n  <li>5ft 9 inches<\/li>\n  <li>5 ft 9 inches<\/li>\n  <li>5 feet 6 inches<\/li>\n  <li>170 cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### Classifiers

``` r
problems %>% filter(str_detect(height, "cm|inches|feet")) %>% .$height %>% str_view_all("^\\d{2,3}")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-cd89ebcbb054b107f6bc">{"x":{"html":"<ul>\n  <li><span class='match'>165<\/span>cm<\/li>\n  <li>5 feet and 8.11 inches<\/li>\n  <li>Five foot eight inches<\/li>\n  <li>5 feet 7inches<\/li>\n  <li>5ft 9 inches<\/li>\n  <li>5 ft 9 inches<\/li>\n  <li>5 feet 6 inches<\/li>\n  <li><span class='match'>170<\/span> cm<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
