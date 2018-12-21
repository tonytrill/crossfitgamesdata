CrossFit Games Data
================
Tony Silva
November 14, 2018

### Load Packages

``` r
library("tidyverse")
```

### Load data and blend men and women into one data frame

``` r
# Load both csvs
men <- read_csv('D:/cf_data/athletes_men.csv')
women <- read_csv('D:/cf_data/athletes_women.csv')
# women csv had NULL gender so adjusted
women$gender <- 'F'
# Load both csvs for scores
scores_men <- read_csv('D:/cf_data/scores_men.csv')
scores_women <- read_csv('D:/cf_data/scores_women.csv')

# union all athletes and score files together and remove redundant dfs.
athletes <- union(men, women)
scores <- union(scores_men, scores_women)
rm(men,women, scores_men, scores_women)

# Build Time cap variable for a score. This flags if the athlete beat the time cap.
scores <- mutate(scores, IsBeatTimeCap = ifelse(wod_type == 'time' & type == 'reps', 0, ifelse(wod_type == 'time' & type == 'time', 1, NA)))
athletes$affiliateName[athletes$affiliateId==0] <- 'Unaffiliated'
```

### Data Prep

I want to create feature vectors for each athlete with all the columns I care about.

``` r
cf_score <- select(scores, competitorId, ordinal, cf_score) %>% 
  spread(ordinal, cf_score, fill = 0) %>% 
  rename(Score1='1', Score2='2', Score3='3', Score4='4', Score5='5', Score6='6')
time_cap <- select(scores, competitorId, ordinal, IsBeatTimeCap) %>% 
  spread(ordinal, IsBeatTimeCap, fill=0) %>%
  rename(IsBeatTimeCap1='1', IsBeatTimeCap2='2', IsBeatTimeCap3='3', IsBeatTimeCap4='4', IsBeatTimeCap5='5', IsBeatTimeCap6='6') %>%
  select(competitorId, IsBeatTimeCap2, IsBeatTimeCap4, IsBeatTimeCap5)
scaled <- select(scores, competitorId, ordinal, scaled) %>% 
  spread(ordinal, scaled, fill=0) %>%
  rename(IsScaled1='1', IsScaled2='2', IsScaled3='3', IsScaled4='4', IsScaled5='5', IsScaled6='6')

athletes <- select(athletes, competitorId, affiliateId, affiliateName, age, gender, divisionId, height, weight, regionId, regionName, profession)

athletes <- inner_join(athletes, cf_score, by='competitorId') %>%
  inner_join(time_cap, by='competitorId') %>%
  inner_join(scaled, by='competitorId')
```

``` r
rm(cf_score)
rm(time_cap)
rm(scaled)
```

I want to build feature vectors for each gym in the data set. The goal is to describe each gym by the athletes that fall under it.

``` r
gyms <- athletes %>%
  group_by(affiliateName, affiliateId, regionName, regionId) %>%
  summarise(avgAge=mean(age), maxAge=max(age), minAge=min(age), numAthletes=n())
```