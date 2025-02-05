ggplot1
================
Keyi Wang
10/7/2019

#### create the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/wangkeyi/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-10-07 13:18:52

    ## file min/max dates: 1869-01-01 / 2019-10-31

    ## file path:          /Users/wangkeyi/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-10-07 13:19:11

    ## file min/max dates: 1965-01-01 / 2019-10-31

    ## file path:          /Users/wangkeyi/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-10-07 13:19:18

    ## file min/max dates: 1999-09-01 / 2019-10-31

## create a ggplot

``` r
ggplot(weather_df,aes(x = tmin,y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

alternate way of making this plot

``` r
weather_df %>%
  ggplot(aes(x = tmin,y = tmax)) + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
weather_df %>% filter(name == "CentralPark_NY")
```

    ## # A tibble: 365 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 355 more rows

``` r
##adding color
weather_df %>%
  ggplot(aes(x = tmin,y = tmax)) + 
  geom_point(aes(color = name),alpha = 0.4) 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

``` r
##alpha for transparency  , different color for different location
```

why do aes positions matter?

first

``` r
weather_df %>%
  ggplot(aes(x = tmin,y = tmax)) + 
  geom_point(aes(color = name),alpha = 0.4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> vs

``` r
weather_df %>%
  ggplot(aes(x = tmin,y = tmax,color = name)) + ## color now is applying in everywehre
  geom_point(alpha = 0.4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

facet–seperate it into three panels based on location

``` r
weather_df %>%
  ggplot(aes(x = tmin,y = tmax,color = name)) +
  geom_point(alpha = 0.4) +
  geom_smooth(se = FALSE)  +
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x= date, y=tmax,color = name))+
  geom_point(aes(size=prcp)) + ###include precp information
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x= date, y=tmax,color = name))+
  geom_point(aes(size=prcp), alpha = 0.35) + ###include precp information
  geom_smooth(size=2)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](vis_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

2d density

``` r
## install.package("hexbin")
weather_df %>%
  ggplot(aes(x= tmin, y=tmax)) +
  geom_bin2d()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_bin2d).

![](vis_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## more kinds of plots\!

``` r
weather_df %>%
  ggplot(aes(x = tmax,fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](vis_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmax,fill = name)) +
  geom_histogram()+
  facet_grid(~name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](vis_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

``` r
### density plots
weather_df %>%
  ggplot(aes(x = tmax,fill = name)) +
  geom_density(alpha = 0.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](vis_files/figure-gfm/unnamed-chunk-10-3.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmax)) +
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](vis_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

ridge plots

``` r
weather_df %>%
  ggplot(aes(x = tmax,y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](vis_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

saving plots

``` r
ggplot_ridge_temp =
  weather_df %>%
  ggplot(aes(x = tmax,y = name)) +
  geom_density_ridges()
ggsave("ggplot_temp_ridge.pdf",ggplot_ridge_temp)
```

    ## Saving 7 x 5 in image

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).
