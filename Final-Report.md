Final Report
================
Emery Lauer
2025-04-29

- [D’oh! Analyzing the Speech of The
  Simpsons](#doh-analyzing-the-speech-of-the-simpsons)
  - [Analyzing how popularity and complexity vary over
    time](#analyzing-how-popularity-and-complexity-vary-over-time)
  - [Background Information](#background-information)
  - [The Dataset](#the-dataset)
  - [Sources of Uncertainty in the
    Dataset](#sources-of-uncertainty-in-the-dataset)
  - [Guiding Question](#guiding-question)
  - [Sources of Uncertainty in our
    Methodology](#sources-of-uncertainty-in-our-methodology)
  - [Conclusions](#conclusions)

# D’oh! Analyzing the Speech of The Simpsons

### Analyzing how popularity and complexity vary over time

Data Science SP25 Final Project

Carter Harris, Swasti Jain, and Emery Lauer

## Background Information

*The Simpsons* is widely considered one of the most acclaimed and
successful TV series ever produced. Following the day-to-day life and
activities of the Simpson family, the animated sitcom chronicles and
satirizes American and western society, culture, and general family
dysfunction. The Simpson family - Homer, Marge, Bart, and Lisa - are far
from the only characters in this chronical, with an army of side
characters and celebrity “appearances” ranging from Albert Einstein to
Donald Trump. To this end, the show has garnered particular attention
for parodying current events and making zany predictions about the
future that sometimes end up coming true.

<figure>
<img src="images/3343.webp" width="483"
alt="The Simpsons family. From left to right: Maggie, Marge, Lisa, Homer, and Bart." />
<figcaption aria-hidden="true">The Simpsons family. From left to right:
Maggie, Marge, Lisa, Homer, and Bart.</figcaption>
</figure>

Having ran continuously for over 35 years, the show and the fictional
town of Springfield have become ubiquitous and universally-recognized
artifacts of modern media and popular culture. Beyond the entertainment
and comedic value, across three decades of production and writing teams,
the evolution of the show’s script, characters, plots, and settings
provides a rich window into the evolution and shifting of American
culture and family life as a whole. To this end, we wondered how the
script of The Simpsons has evolved through time and what it can tell us
about *The Simpsons’* viewers and the wider cultural influences of the
show.

## The Dataset

The dataset used for this analysis was originally compiled by the data
scientist and software engineer Todd Schneider for his article [“The
Simpsons by the
Data”](https://toddwschneider.com/posts/the-simpsons-by-the-data/)
posted to his website. This article served as a great starting place to
guide our research, alongside providing some important contextual
information to explain anomalies and strange trends in the data.

In this article, he links to a [GitHub
repository](https://github.com/toddwschneider/flim-springfield) that he
used for scraping this dataset. The code scrapes from several different
sources to compile the following information about *The Simpsons*
series:

- Primarily, the crawler scraped the “Simpsons’ World” website, which
  hosted all historical episodes of the show. From this source, the
  complete script for every episode, separated by character and
  individual line, was compiled. This represented a huge quantity of
  data across over 25 years of the show’s run, containing over 150,000
  lines. These script lines also provided a way to easily compile all
  characters in the show, ranging from the main characters in every
  episode to the one-off special appearances. This source also provided
  a way to compiled all locations in the show, although this data was
  not analyzed within this project.

- The second main source was [this Wikipedia
  article](https://en.wikipedia.org/wiki/List_of_The_Simpsons_episodes)
  which includes numerical data about every episode in the series. This
  source includes episode metadata (season, episode number, air date,
  etc). alongside original air date viewership data and ratings.

- Finally, the scraper also pulls data from
  [IMDb](https://www.imdb.com/title/tt0096697/episodes/), including
  ratings from IMDb’s voting system alongside supplemental episode
  information to that from Wikipedia.

Unfortunately, in late 2019, Disney (which acquired the rights to *The
Simpsons* in early 2019as part of its acquisition of 21st Century Fox)
moved all historical streaming episodes of *The Simpsons* from the
Simpsons’ World website to it’s Disney+ streaming subscription service.
As such, the original scraper no longer works for collecting the vast
array of data it previously provided. However, a user named [William
Cukierski](https://www.kaggle.com/wcukierski) on
[Kaggle](https://www.kaggle.com/), a website that hosts various data
science-related competitions, used the script to extract all episode
data through 2016. This dataset was further re-uploaded to the website
by user [Prashant Banerjee](https://www.kaggle.com/prashant111), which
is available here under the name [The Simpsons
Dataset](https://www.kaggle.com/datasets/prashant111/the-simpsons-dataset?resource=download).
An abbreviated version of this dataset was the February 24th, 2025
dataset for the weekly [Tidy Tuesday data science
project](https://github.com/rfordatascience/tidytuesday/tree/main/data/2025/2025-02-04),
which is where our team originally discovered it. However, to explore
the larger trends over time, all subsequent analysis was completed with
the complete 1989-2016 dataset available.

## Sources of Uncertainty in the Dataset

While the dataset offers a detailed look at The Simpsons’ episodes,
characters, and viewership over nearly three decades, there are several
sources of uncertainty that may affect the interpretation of the
<a href="%5Bdata:%5D(data:)%7B.uri%7D"
class="uri">[data:\\](data:){.uri}</a> As noted by Todd Schneider,
viewership data appears to spike around the year 2000, between seasons
11 and 12. This change is not due to a sudden increase in popularity but
rather a shift in the metric used. Wikipedia began reporting individual
viewers instead of households. This creates a discontinuity in the data
that may mislead trend analysis unless adjusted for or explicitly
acknowledged.  
Additionally, the dataset relies on scraping multiple sources, Simpsons
World, Wikipedia, and IMDb. There could be inconsistencies with these
sources. We know that Wikipedia data may be user-edited and not always
verified. However additionally IMDb ratings might reflect a
self-selected group of users and may not be representative of the
general audience.

## Guiding Question

How has the viewership of *The Simpsons* changed over time? Which
characters have been more or less popular at different points across the
show’s history?

#### Importing the data set into R

``` r
df_ep <- read.csv("data/simpsons_episodes.csv")
df_script <- read.csv("data/simpsons_script_lines.csv")

head(df_ep)
```

    ##   id
    ## 1  1
    ## 2  2
    ## 3  3
    ## 4  4
    ## 5  5
    ## 6  6
    ##                                                                              image_url
    ## 1         http://static-media.fxx.com/img/FX_Networks_-_FXX/617/479/Simpsons_01_08.jpg
    ## 2         http://static-media.fxx.com/img/FX_Networks_-_FXX/265/167/Simpsons_01_02.jpg
    ## 3         http://static-media.fxx.com/img/FX_Networks_-_FXX/621/883/Simpsons_01_03.jpg
    ## 4 http://static-media.fxx.com/img/FX_Networks_-_FXX/632/119/Simpsons_01_04__343617.jpg
    ## 5         http://static-media.fxx.com/img/FX_Networks_-_FXX/274/735/Simpsons_01_05.jpg
    ## 6         http://static-media.fxx.com/img/FX_Networks_-_FXX/631/642/Simpsons_01_06.jpg
    ##   imdb_rating imdb_votes number_in_season number_in_series original_air_date
    ## 1         8.2       3734                1                1        1989-12-17
    ## 2         7.8       1973                2                2        1990-01-14
    ## 3         7.5       1709                3                3        1990-01-21
    ## 4         7.8       1701                4                4        1990-01-28
    ## 5         8.1       1732                5                5        1990-02-04
    ## 6         7.6       1674                6                6        1990-02-11
    ##   original_air_year production_code season                             title
    ## 1              1989            7G08      1 Simpsons Roasting on an Open Fire
    ## 2              1990            7G02      1                   Bart the Genius
    ## 3              1990            7G03      1                   Homer's Odyssey
    ## 4              1990            7G04      1     There's No Disgrace Like Home
    ## 5              1990            7G05      1                  Bart the General
    ## 6              1990            7G06      1                      Moaning Lisa
    ##   us_viewers_in_millions                                       video_url  views
    ## 1                   26.7 http://www.simpsonsworld.com/video/273376835817 171408
    ## 2                   24.5 http://www.simpsonsworld.com/video/283744835990  91423
    ## 3                   27.5 http://www.simpsonsworld.com/video/273381443699  78072
    ## 4                   20.2 http://www.simpsonsworld.com/video/273392195780  67378
    ## 5                   27.1 http://www.simpsonsworld.com/video/300934723994  63129
    ## 6                   27.4 http://www.simpsonsworld.com/video/273391683535  55355

``` r
head(df_script)
```

    ##   id episode_id number
    ## 1  1          1      0
    ## 2  2          1      1
    ## 3  3          1      2
    ## 4  4          1      3
    ## 5  5          1      4
    ## 6  6          1      5
    ##                                                                                     raw_text
    ## 1                                               (Street: ext. street - establishing - night)
    ## 2                                                                    (Car: int. car - night)
    ## 3                                                        Marge Simpson: Ooo, careful, Homer.
    ## 4                                              Homer Simpson: There's no time to be careful.
    ## 5                                                                 Homer Simpson: We're late.
    ## 6 (Springfield Elementary School: Ext. springfield elementary school - establishing - night)
    ##   timestamp_in_ms speaking_line character_id location_id raw_character_text
    ## 1            8000         FALSE                        1                   
    ## 2            8000         FALSE                        2                   
    ## 3            8000          TRUE            1           2      Marge Simpson
    ## 4           10000          TRUE            2           2      Homer Simpson
    ## 5           10000          TRUE            2           2      Homer Simpson
    ## 6           24000         FALSE                        3                   
    ##               raw_location_text                   spoken_words
    ## 1                        Street                               
    ## 2                           Car                               
    ## 3                           Car           Ooo, careful, Homer.
    ## 4                           Car There's no time to be careful.
    ## 5                           Car                    We're late.
    ## 6 Springfield Elementary School                               
    ##                normalized_text word_count
    ## 1                                        
    ## 2                                        
    ## 3            ooo careful homer          3
    ## 4 theres no time to be careful          6
    ## 5                    were late          2
    ## 6

``` r
df_ep_new <- df_ep %>%
  select(id, imdb_votes, original_air_date, original_air_year, us_viewers_in_millions, views, season) %>%
  rename(
    episode_id = id
  )
```

#### Combining the two CSV files to get air dates, IMDB information, and words into the script information

``` r
get_word_counts_per_episode <- function(df_script) {
  df_script %>%
    filter(speaking_line == TRUE) %>%
    mutate(
      word_count = (as.numeric(word_count))
    ) %>%
    filter(!is.na(word_count)) %>%
    filter(word_count <= 1000) %>%
    group_by(episode_id, raw_character_text) %>%
    summarize(total_word_count = sum(word_count), .groups = "drop") %>%
    group_by(episode_id) %>%
    slice_max(total_word_count, n = 1, with_ties = FALSE) %>%
    arrange(episode_id, desc(total_word_count))
}

df_words_per_epsiode <-
  df_script %>%
  filter(speaking_line == TRUE) %>%
  mutate(word_count = as.numeric(word_count)) %>%
  filter(word_count <= 1000) %>%
  group_by(episode_id) %>%
  summarise(total_words_per_episode = sum(word_count)) %>%
  inner_join(df_ep_new, by = "episode_id")
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `word_count = as.numeric(word_count)`.
    ## Caused by warning:
    ## ! NAs introduced by coercion

``` r
episode_word_counts <-
  get_word_counts_per_episode(df_script) %>%
  inner_join(df_words_per_epsiode, by = "episode_id") %>%
  select(
    c(
      "episode_id",
      "raw_character_text",
      "total_word_count",
      "total_words_per_episode"
    )
  ) %>%
  mutate(percentage_of_script = total_word_count / total_words_per_episode)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `word_count = (as.numeric(word_count))`.
    ## Caused by warning:
    ## ! NAs introduced by coercion

``` r
df_simpsons <- df_ep_new %>%
  left_join(episode_word_counts, by = "episode_id")

head(df_simpsons)
```

    ##   episode_id imdb_votes original_air_date original_air_year
    ## 1          1       3734        1989-12-17              1989
    ## 2          2       1973        1990-01-14              1990
    ## 3          3       1709        1990-01-21              1990
    ## 4          4       1701        1990-01-28              1990
    ## 5          5       1732        1990-02-04              1990
    ## 6          6       1674        1990-02-11              1990
    ##   us_viewers_in_millions  views season raw_character_text total_word_count
    ## 1                   26.7 171408      1      Homer Simpson              900
    ## 2                   24.5  91423      1       Bart Simpson              619
    ## 3                   27.5  78072      1      Homer Simpson              852
    ## 4                   20.2  67378      1      Homer Simpson              868
    ## 5                   27.1  63129      1       Bart Simpson              795
    ## 6                   27.4  55355      1      Homer Simpson              586
    ##   total_words_per_episode percentage_of_script
    ## 1                    2903            0.3100241
    ## 2                    2730            0.2267399
    ## 3                    2337            0.3645700
    ## 4                    2699            0.3216006
    ## 5                    2541            0.3128689
    ## 6                    2395            0.2446764

#### Plotting US viewership and the character with the most words in an episode over time

``` r
# Bart|Homer|Marge|Lisa|Maggie|
df_simpsons %>%
  filter(grepl("Homer Simpson|Bart Simpson|Marge Simpson|Lisa Simpson", raw_character_text)) %>%
  ggplot(
    aes(x = `episode_id`, y = us_viewers_in_millions)
  ) +
  geom_point(aes(color = raw_character_text)) +
  scale_color_manual(values = character_colors) +
  geom_smooth(color = "black") +
  labs(
    title = "Which Simpsons Characters Are Most Popular Over Time?",
    x = "Episode Number (sequential over time)",
    y = "US Viewership in Millions",
    color = '"Star Character" of Each Episode',
    caption = "The Star Character is conisdered the character with the most spoken words in a given episode. \n Source: The Simpsons Dataset, Prashant Banerjee (via Kaggle)"
  )
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 2 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Final-Report_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

The above figure shows the overall trend between episode number and
views. There is a downward trend, except for around episode 300 where
the show appears to have revitalized itself and gain new viewers. In
reality, however, the algorithm for measuring viewers had simply changed
around that time. After this peak, the trend continue downwards as the
show loses popularity.

A notable outlier from this graph include episode 343, where it gained
23 million views. This episode, Homer and Ned’s Hail Mary Pass, played
directly after Super Bowl XXXIX, which garnered many more views than
previous episodes.

#### Excluding Homer, how popular are the other family members over time?

``` r
# Bart|Homer|Marge|Lisa|Maggie|
df_simpsons %>%
  filter(grepl("Bart Simpson|Marge Simpson|Lisa Simpson", raw_character_text)) %>%
  ggplot(
    aes(x = `episode_id`, y = us_viewers_in_millions)
  ) +
  geom_point(aes(color = raw_character_text)) +
  scale_color_manual(values = character_colors) +
  geom_smooth(color = "black", label = "Overall Trend") +
  geom_smooth(aes(color = raw_character_text)) +
  labs(
    title = "Excluding Homer, Which Simpsons Characters Are Most Popular Over Time?",
    x = "Episode Number (sequential over time)",
    y = "US Viewership in Millions",
    color = '"Star Character" of Each Episode',
    caption = "The Star Character is conisdered the character with the most spoken words in a given episode. \n Source: The Simpsons Dataset, Prashant Banerjee (via Kaggle)"
  )
```

    ## Warning in geom_smooth(color = "black", label = "Overall Trend"): Ignoring
    ## unknown parameters: `label`

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 2 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Final-Report_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

In the figure above, the Simpsons’ star characters, excluding Homer,
have interesting individual trends. Around episode 300, Lisa and Marge
have a sort of rise in popularity, while Bart’s viewership decreases.
This could be because he was in less episodes or maybe his writing
quality had decreased while Lisa and Marge’s writing improved. However,
there is no evidence that more female writers were brought onto the show
during that period.

Some outliers in this graph may affect the trendline, however. Episode
303, for example, where Lisa is the star character, is called “I’m
Spelling As Fast As I Can”, where Lisa gets to participate in the
Spellympics, getting the chance to win and get free college, while Homer
obsesses over a new sandwich made of unidentified meat. Lisa loses the
Spellympics, however the town carves her face in a mountain to
commemorate her achievement.

Another outlier, episode 474 titled “Moms I’d Like to Forget”, garnered
12.6 million views and covers how Bart has an identical scar on his hand
as someone else in his 5th grade class. He discovers that Marge was in a
club of 4 mothers she was in seven years prior. Marge plans to reunite
with the mothers and have Homer and Bart reunite with the fathers and
sons, respectively, where they learn the scar comes from fireworks
accidentally going everywhere and branding the boys’ hands.

#### Box plots of each character and their overall views

``` r
df_simpsons %>%
  filter(grepl("Homer Simpson|Bart Simpson|Marge Simpson|Lisa Simpson", raw_character_text)) %>%
  # group_by(raw_character_text) %>%
  ggplot(
    aes(x = raw_character_text, y = us_viewers_in_millions, fill = raw_character_text)
  ) +
  geom_boxplot() +
  scale_fill_manual(values = character_colors) +
  theme(axis.text.x = element_text(angle = 50, hjust = 1))
```

    ## Warning: Removed 2 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](Final-Report_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

The above box plots separate the characters to see the overall views of
each of them. Homer has the most episodes where he is the “star
character”, so he has the widest range. However, looking at the medians
of each character, Marge has the highest median viewer count in her
episodes while Lisa has the lowest.

#### Analyzing the complexity of the main character’s lines over the data set

``` r
unique_words <- function(line) {
  line <- iconv(line, from = "", to = "UTF-8", sub = " ")
  line %>%
    # Convert all characters to lowercase
    tolower() %>%
    # Remove all non-alphanumeric characters (ie punctuation)
    gsub("[^[:alnum:] ]", "", .) %>%
    # Split into individual words
    str_split(" +") %>%
    # Deal with weird list-in-a-list thing
    .[[1]] %>%
    # Count onlthe unique (non-repeat words)
    unique() %>%
    # Get total number of words
    length()
}

df_speech_complexity <-
  df_script %>%
  mutate(word_count = as.numeric(word_count)) %>%
  filter(word_count < 1000) %>%
  filter(
    grepl(
      "Simpson",
      raw_character_text
    )
  ) %>%
  group_by(raw_character_text) %>%
  summarise(
    all_words = paste0(normalized_text, collapse = " "),
    num_unique_words = unique_words(all_words),
    total_words = sum(word_count, na.rm = TRUE),
    speech_complexity = (num_unique_words / total_words) * 100
  ) %>%
  arrange(speech_complexity) %>%
  slice_max(total_words, n = 4, with_ties = FALSE)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `word_count = as.numeric(word_count)`.
    ## Caused by warning:
    ## ! NAs introduced by coercion

``` r
df_speech_complexity
```

    ## # A tibble: 4 × 5
    ##   raw_character_text all_words    num_unique_words total_words speech_complexity
    ##   <chr>              <chr>                   <int>       <dbl>             <dbl>
    ## 1 Homer Simpson      "theres no …            16684      271208              6.15
    ## 2 Marge Simpson      "ooo carefu…            10140      124710              8.13
    ## 3 Bart Simpson       "jingle bel…            10114      109120              9.27
    ## 4 Lisa Simpson       "but i real…            10664       99224             10.7

``` r
df_speech_complexity %>%
  mutate(raw_character_text = fct_reorder(raw_character_text, desc(speech_complexity))) %>%
  ggplot(aes(raw_character_text, speech_complexity, fill = raw_character_text)) +
  geom_col() +
  scale_fill_manual(values = character_colors) +
  theme(
    axis.text.x = element_text(angle = 50, hjust = 1),
    plot.caption = element_text(hjust = 0)
  ) +
  labs(
    x = "Character Name",
    color = "Character Name",
    y = "Series-Wide Speech Complexity (percentage)",
    caption = "Speech Complexity is defined as the number of unique words as a percentage \n of the total words spoken in a character's dialog across the entire series. \n Source: The Simpsons Dataset, Prashant Banerjee (via Kaggle)",
    title = "Which Simpsons Main Characters Have the Most Complex Speech?"
  )
```

![](Final-Report_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

The above figure shows the speech complexity per character.
Interestingly, this graph shows nearly the opposite of the earlier box
plot as Homer and Marge have the lowest speech complexity where Lisa has
the highest. Since Homer is the star character in the most episodes and
Marge has the highest median viewer count, recounting that the creator
chose the name Simpsons because it sounded like “simpleton”, this
analysis appears to confirm their suspicions that viewers are looking
for a simple funny show where they can more or less turn their brains
turn off to watch.

## Sources of Uncertainty in our Methodology

While our analysis provides insight into character prominence and
dialogue richness, the methods used come with important limitations:

We define speech complexity as the number of unique words spoken by a
character divided by their total word count. While this captures
vocabulary variety, it might not actually represent character
intelligence.

We define a “Star Character” of each episode as the one who speaks the
most words. However, this may not always align with narrative or
audience perception of who the episode is really “about.”

## Conclusions

We see that Homer has the least complex speech across the series and he
is well represented as the “Star Character” throughout the series. *It
is possible that this association points to a choice by the writers to
make the main character deliberately consistent and non-complex.* There
is certainly a lot of benefit to creating a character that is consistent
and familiar especially in the genre of sitcom television. It ensures
the viewers on what sort of entertainment they are going to receive.
That consistency can lead to comfort and preference for that character.

We performed more analysis on the favored characters excluding Homer
since he is often considered the main protagonist. We discovered that
Bart was favored in earlier seasons however in later seasons Lisa and
Marge also fluctuate for the top spot.

It is difficult to tell whether these star characters were representing
genuine popularity since there are so many sources of uncertainty. As
aforementioned there is uncertainty that the imdb voting is a true
representation of all viewers of the Simpsons. There is also uncertainty
that our chosen method of denoting the ‘star character’ as the most
prominent character is not the most effective measurement of
“popularity”.

*It is inconclusive to say whether viewers of the Simpsons prefer to
watch non-complex characters <u>in general</u>.* Especially since the
data analysis presented does not account popularity of an episode based
on inter-character dynamics or historical and pop culture influences
separate from the characters.
