
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
deaths <- av %>%
  pivot_longer(cols = starts_with("Death"), names_to = "Time", values_to = "Death") %>%
  filter(Death != "") %>%
  mutate(Time = parse_number(Time))
```

Similarly, deal with the returns of characters.

``` r
returns <- av %>%
  pivot_longer(cols = starts_with("Return"), names_to = "Time", values_to = "Return") %>%
  filter(Return != "") %>%
  mutate(Time = parse_number(Time))

returns
```

    ## # A tibble: 89 × 18
    ##    URL                Name.Alias Appearances Current. Gender Probationary.Introl
    ##    <chr>              <chr>            <int> <chr>    <chr>  <chr>              
    ##  1 http://marvel.wik… "Henry Jo…        1269 YES      MALE   ""                 
    ##  2 http://marvel.wik… "Janet va…        1165 YES      FEMALE ""                 
    ##  3 http://marvel.wik… "Anthony …        3068 YES      MALE   ""                 
    ##  4 http://marvel.wik… "Robert B…        2089 YES      MALE   ""                 
    ##  5 http://marvel.wik… "Thor Odi…        2402 YES      MALE   ""                 
    ##  6 http://marvel.wik… "Thor Odi…        2402 YES      MALE   ""                 
    ##  7 http://marvel.wik… "Steven R…        3458 YES      MALE   ""                 
    ##  8 http://marvel.wik… "Clinton …        1456 YES      MALE   ""                 
    ##  9 http://marvel.wik… "Clinton …        1456 YES      MALE   ""                 
    ## 10 http://marvel.wik… "Pietro M…         769 YES      MALE   ""                 
    ## # ℹ 79 more rows
    ## # ℹ 12 more variables: Full.Reserve.Avengers.Intro <chr>, Year <int>,
    ## #   Years.since.joining <int>, Honorary <chr>, Death1 <chr>, Death2 <chr>,
    ## #   Death3 <chr>, Death4 <chr>, Death5 <chr>, Notes <chr>, Time <dbl>,
    ## #   Return <chr>

Based on these datasets calculate the average number of deaths an
Avenger suffers.

``` r
total_deaths <- sum(deaths$Time)
total_returns <- sum(returns$Time)

num_avengers <- nrow(av)
average_deaths <- (total_deaths - total_returns) / num_avengers

average_deaths
```

    ## [1] 0.6127168

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

The MVP of the Earth-616 Marvel Universe Avengers has to be Jocasta — an
android based on Janet van Dyne and built by Ultron — who has been
destroyed five times and then recovered five times.

mazin- Out of 173 listed Avengers, my analysis found that 69 had died at
least one time after they joined the team.

Luke R- Given the Avengers’ 53 years in operation and overall mortality
rate, fans of the comics can expect one current or former member to die
every seven months or so

### Include the code

``` r
most_deaths_index <- which.max(deaths$Time)
character_most_deaths <- deaths$Name.Alias[most_deaths_index]
most_deaths <- deaths$Time[most_deaths_index]

most_returns_index <- which.max(returns$Time)
character_most_returns <- returns$Name.Alias[most_returns_index]
most_returns <- returns$Time[most_returns_index]

cat("Character with the most deaths:", character_most_deaths, "with", most_deaths, "deaths\n")
```

    ## Character with the most deaths: Jocasta with 5 deaths

``` r
cat("Character with the most returns:", character_most_returns, "with", most_returns, "returns\n")
```

    ## Character with the most returns: Jocasta with 5 returns

``` r
avengers <- av %>%
  mutate(Total_Deaths = rowSums(select(., starts_with("Death")) == "YES"))


died_at_least_once <- sum(avengers$Total_Deaths > 0)


died_at_least_once
```

    ## [1] 69

``` r
death_year <- total_deaths / max(av$Years.since.joining)
death_period <- 1 / (death_year / 12)
death_period #Luke code
```

    ## [1] 6.216216

### Include your answer

Their statement was correct! Jocasta has the most deaths and returns
with 5 each.

mazin- the statement was correct as well. after running it in all the
deaths written with 173 listed avengers, it showed that the number of
deaths more than once is 69.

Luke R- Our results are slightly off their statement. We have a death
occurring every 6.2 months, but that is showing activity for 115 years,
which doesn’t match their statement of 53 years of activity.

Upload your changes to the repository. Discuss and refine answers as a
team.

### Include your answer

There’s a 2-in-3 chance that a member of the Avengers returned from
their first stint in the afterlife, but only a 50 percent chance they
recovered from a second or third death.

``` r
num_died_once <- sum(av$Death1 == "YES" & av$Return1 == "YES") #number of people who died once
num_died_twice <- sum(av$Death2=="YES" & av$Return2 == "YES")
num_died_three <- sum(av$Death3=="YES" & av$Return3 == "YES")



cat("Percentage of a member from the avengers to return from 1st death" , num_died_once / sum(av$Death1 == "YES"))
```

    ## Percentage of a member from the avengers to return from 1st death 0.6666667

``` r
print(" ")
```

    ## [1] " "

``` r
cat("Percentage of a member from the avengers to return from 2nd death" , num_died_twice / sum(av$Death2 == "YES"))
```

    ## Percentage of a member from the avengers to return from 2nd death 0.5

``` r
print(" ")
```

    ## [1] " "

``` r
cat("Percentage of a member from the avengers to return from 3rd death" , num_died_three / sum(av$Death3 == "YES"))
```

    ## Percentage of a member from the avengers to return from 3rd death 0.5
