---
title: WebScraping4Palestine
author: Shea Turner-Matthews
output: github_document
---
```{r load-packages, echo = FALSE}
library(tidyverse)
library(ggplot2)
library(broom)
library(readxl)
library(tidytext)
library(dplyr)
library(tm)
library(SnowballC)
library(lubridate)
library(plotly)
library(ggpmisc)
library(htmlwidgets)
```
```{r load-data, echo = FALSE}
Palestine_news_articles <- read_xlsx("data/Palestine_news_articles.xlsx")
Israel_news_articles <- read_xlsx("data/israel_news_articles.xlsx")
Gaza_news_articles <- read_xlsx("data/gaza_news_articles.xlsx")
additional_news_data <- read_csv("data/news_data.csv")
million_news <- read_csv("data/abcnews-date-text 2 (1).csv")
```

## Summary

Media plays a pivotal role in shaping public opinion and understanding of complex geopolitical conflicts. However, western news can at times portray a biased perspective on different international issues, particularly regarding the situation Palestine-Israel (Jackson 2024).

In this project, I aimed to examine potential biases in news coverage of the Palestine-Israel conflict through computational text analysis. To do this I analyzed word frequency patterns and trends in news article headings and descriptions obtained through web scraping of Google search results, supplemented by two additional datasets: the A Million News Headlines dataset containing a million news headlines from ABC News and the Israel-Palestine Conflict News Dataset containing headlines and descriptions from Al Jazeera.

The goal was to determine whether significant differences existed between the relative word usage among different news journals and whether their were any interesting trends in word use over time. Despite challenges such as limited historical data availability and the US-centric nature of my web scraped dataset, the analysis did generate interesting insights into potential biases in news reporting. This was particularly evident when examining the percentage use of the word "palestine" and "israel" in accordance with the different searched keywords and different news outlets.

The following plot showing the variation in the percentage of selected words used in articles released from Al Jazeera and The Times of Israel showcases this bias. Al Jazeera uses words such as Palestine, ceasefire, and genocide, far more frequently than the Times of Israel. This is likely reflective of the lack of desire to portray the current event in Palestine as being as horrific as they are, so as to preserve the western image of Israel acting out of self defense and being a democratic and just government. 

```{r important plot,  echo = FALSE}
All_tokens %>%
  mutate(word = case_when(word %in% c("palestinian", "palestinians") ~"palestine",
                          word %in% c("israeli", "israelis") ~"israel",
                          word %in% c("terrorism", "terrorist", "terrorists") ~"terror",
                          word == "west" ~ "west bank", 
                          .default = word)) %>% 
  filter(!is.na(journal)) %>% 
  group_by(word, journal) %>%
  summarize(word_count = n(), journal, Description, keyword, in_both, Title, word, title_or_description) %>%
  ungroup(word) %>% 
  group_by(journal) %>%
  mutate(total_words = n()) %>%
  mutate(word_percentage = word_count/total_words) %>%
  mutate(weighted_percentage = word_percentage * log(total_words)) %>%
  #arrange(desc(weighted_percentage)) %>% 
  filter(journal %in% c("The Times of Israel", "Al Jazeera")) %>% 
  filter(total_words > 5000) %>% 
  #filter(word_count > 500) %>% 
  filter(word %in% c("peace", "terror", "west bank", "palestine", "israel", "gaza", "crisis", "idf", "aggression", "bombing", "genocide", "ceasefire", "occupied", "military")) %>% 
  ggplot(aes(x = word_percentage, y = word, fill = journal, colour = journal)) +
  geom_col(position = "fill") +
  labs(title = "Al Jazeera/Times of Israel Word Frequency Compairison")+
  xlab("Relative Word Percentages") +
  ylab("")
```

Word trends over time was also an interesting way of observing the how different events impact relative word percentages across different years. This was especially evident in the percentage use of the word "peace" which had a large spike in 2013 aligning with the Peace talk that between the palestine and israel governments that were initiated in that year. 

In conclusion, it was possible to show preliminary evidence that suggests bias in different New Journals. Furthermore, it was also possible to examine trends in percetage word use over . this project highlights the importance of critically evaluating media coverage, particularly concerning complex geopolitical issues like the Palestine-Israel conflict. While more data and further analysis are needed for a comprehensive understanding, these initial findings offer valuable insights into how biases may manifest in news reporting. Moving forward, I plan to expand this project by gathering more data to achieve a more statistically significant portrayal of the results.


## Presentation

My presentation can be found using the following URL: https://docs.google.com/presentation/d/1wFxg9FdoWaEOXZ2M5OufKsmUOfTJ5tBuysAuRT2lfec/edit?usp=sharing 

## Data 

Web scraped data: Scraped from google search using Rstudio and ImportFromWeb webscraper on March 15th 2024
Phrases searched: "Palestine news", "Israel news", "Gaza news"

Kulkarni, R. (2022, June 11). A million news headlines. Kaggle. https://www.kaggle.com/datasets/therohk/million-headlines 
This contains data of news headlines published over a period of nineteen years. Sourced from the reputable Australian news source ABC (Australian Broadcasting Corporation).

Silsüpür, E. (2023, December 23). Israel-Palestine Conflict News Dataset. Kaggle. https://www.kaggle.com/datasets/emirslspr/israel-hamas-conflict-news-dataset 
This dataset comprises headlines and corresponding descriptions related to the Israel-Palestine conflict sourced from Al Jazeera, spanning the years 2021 to 2023. 

## References

Adam Johnson, O. A. (2024, January 10). Coverage of gaza war in the New York Times and other major newspapers heavily favored Israel, analysis shows. The Intercept. https://theintercept.com/2024/01/09/newspapers-israel-palestine-bias-new-york-times/ 

Jackson, H. M. (2023). the New York times distorts the Palestinian Struggle: A case study of anti-Palestinian bias in US News coverage of the first and second Palestinian intifadas. Media, War &amp; Conflict, 17(1), 116–135. https://doi.org/10.1177/17506352231178148 

Kulkarni, R. (2022, June 11). A million news headlines. Kaggle. https://www.kaggle.com/datasets/therohk/million-headlines 

Shlaim, A. (2016, December 24). Barack Obama, John Kerry, and the Palestine Saga. Al Jazeera. https://www.aljazeera.com/opinions/2016/12/24/barack-obama-john-kerry-and-the-palestine-saga#:~:text=No%20American%20president%20ever%20came,to%20bring%20about%20Palestinian%20statehood. 

Silsüpür, E. (2023, December 23). Israel-Palestine Conflict News Dataset. Kaggle. https://www.kaggle.com/datasets/emirslspr/israel-hamas-conflict-news-dataset
