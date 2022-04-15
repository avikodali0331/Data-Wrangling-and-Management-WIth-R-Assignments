Week 11 Assignment Part 1
================

``` r
spotify_url <- "https://spotifycharts.com/regional"
robotstxt::paths_allowed(spotify_url)
```

    ##  spotifycharts.com

    ## [1] TRUE

``` r
spotify_top200_raw <- spotify_url %>% 
  read_html() %>%
  html_table() %>%
  .[[1]]
```

``` r
spotify_top200_regional <- spotify_top200_raw %>% 
  select(Track, Streams) %>% 
  rename(Track_Artist = Track) %>% 
  mutate(Track = str_split(Track_Artist, "\n") %>% map_chr(1), 
         Artist = str_split(Track_Artist, "\n") %>% 
           map_chr(2) %>% 
           str_trim() %>% 
           str_split("by") %>% 
           map_chr(2) %>% 
           str_trim(), 
         Streams = parse_number(Streams)) %>% 
  select(Track, Artist, Streams)
```

``` r
spotify_top200_regional %>%
  slice_max(Streams, n = 10) %>%
  mutate(Track = fct_reorder(Track, Streams)) %>%
  ggplot(aes(Streams, Track)) +
  geom_col() +
  scale_x_continuous(labels = scales::comma) +
  labs(y = NULL) +
  theme_bw() + 
  ggtitle("Spotify Top 10")
```

![](Week-11--Assignment-Part-1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
