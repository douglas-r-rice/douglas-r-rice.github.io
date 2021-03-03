---
layout: post
title: "Scraping text from news articles with r studio (Abassah Manu)"
author: Roselyn Abassah Manu
date: 2021-02-28 12:24:54 -0500
categories: abassahmanu
---


This is a documentation of my project progress as of Week 4. I have my data in R, except for that of the Boston Globe which our amazing UMASS Amherst librarians are helping me acquire due to coyright issues.

```{r}
#####Setting up my vector for all htmls
URLS <- read.csv("Articles html.csv", header=F)$V1  
newspaper <- rep(NA, length(URLS))  
newspaper[which(str_detect(URLS, "washingtonpost")== TRUE)] <- "Washington Post"  
newspaper[which(str_detect(URLS, "latimes")== TRUE)] <- "LA Times"  
newspaper[which(str_detect(URLS, "nytimes")== TRUE)] <- "NY Times"  
newspaper[which(str_detect(URLS, "irishtimes")== TRUE)] <- "Irsih Times"  
```

Further defining my news sources 

```{r}
# newspaper urls
URLS_WP <- URLS[45:93]    
URLS_NYT <- URLS[2:43]  
URLS_LAT <- URLS[1]  
URLS_IRT <- URLS[44]  
```

I kept getting a NULL out put and after playing around a bit I noticed that I had to change to charactor from factor and I used the Washington Post as an example. To clarification, I am working with the New York Times, Washigton Post, Irish Times, LA Times and Boston Globe (if I get access to it via the library)

```{r}
Analysis_Final_WP <- 0  
for (i in 1:length(URLS_WP)) {  
  Articles <- as.character(URLS_WP[i])  
  css_selector <- "#article-body , h1"  
  Analysis <- 0  
  Analysis <- Articles %>%   
    read_html() %>%  
    html_nodes(css = css_selector) %>%  
    html_text()  
  Analysis_Final_WP <- rbind(Analysis_Final_WP, Analysis)  
}
```

I inspected to make sure that CSS selector worked. I noticed that the CSS selector for some news sites were difficult to get but I got them all after practise  

```{r}
Analysis_Final_WP  
```
Then, I did the same for all the news sources  

```{r}
Analysis_Final_NYT <- 0  
for (i in 1:length(URLS_WP)) {  
  Articles <- as.character(URLS_NYT[i])  
  css_selector <- "#link-7e19ebc2 , evys1bko"  
  Analysis <- 0  
  Analysis <- Articles %>%   
    read_html() %>%  
    html_nodes(css = css_selector) %>%  
    html_text()  
  Analysis_Final_NYT <- rbind(Analysis_Final_NYT, Analysis)  
}  

Analysis_Final_NYT  

Analysis_Final_LAT <- 0  
for (i in 1:length(URLS_LAT)) {  
  Articles <- as.character(URLS_LAT[i])  
  css_selector <- ".page-article-body , .headline"  
  Analysis <- 0  
  Analysis <- Articles %>%   
    read_html() %>%  
    html_nodes(css = css_selector) %>%  
    html_text()  
  Analysis_Final_LAT <- rbind(Analysis_Final_LAT, Analysis)  
}  

Analysis_Final_LAT  


Analysis_Final_IRT <- 0  
for (i in 1:length(URLS_IRT)) {  
  Articles <- as.character(URLS_IRT[i])  
  css_selector <- "h1 , .article_body"  
  Analysis <- 0  
  Analysis <- Articles %>%   
    read_html() %>%  
    html_nodes(css = css_selector) %>%  
    html_text()  
  Analysis_Final_IRT <- rbind(Analysis_Final_IRT, Analysis)  
}  

Analysis_Final_IRT  

#####Inspect the scraped data  
Analysis_Final_IRT  
Analysis_Final_LAT 
Analysis_Final_WP  
Analysis_Final_NYT  

#####Save progress
save.image("NewspaperArticles.RData")  

```

From there, it was time to clean the text. Load these libraries 

```{r}
library(tidytext)  
library(dplyr)  
library(tidyverse)  

 
#####Remove punctuation from the text with the code below
gsub(pattern = "\\W", replace = " ", Analysis_Final_IRT)

#####Update Analysis_Final_IRT to include the change above
Analysis_Final_IRT <- gsub(pattern = "\\W", replace = " ", Analysis_Final_IRT)

#####Change text to all lowercase because I do not want to treat the same words differently base on case sensitivity
tolower(Analysis_Final_IRT)

#####Update Analysis_Final_IRT to include the change above
Analysis_Final_IRT <- tolower(Analysis_Final_IRT)

#####Install this package to remove stop words. I tactually masks from quanteda

install.packages("tm")  
library(tm)  

#####Remove stop words with this code below
removeWords(Analysis_Final_IRT, stopwords())

Analysis_Final_IRT <- removeWords(Analysis_Final_IRT, stopwords())

#####Save the changes above
Analysis_Final_IRT <- removeWords(Analysis_Final_IRT, stopwords())

#####Remove single letters from the text
gsub(pattern = "\\b[A-z]\\b{1}", replace = " ", Analysis_Final_IRT)  

Analysis_Final_IRT <- gsub(pattern = "\\b[A-z]\\b{1}", replace = " ", Analysis_Final_IRT)  

#####Clean up the white spaces
stripWhitespace(Analysis_Final_IRT)  

Analysis_Final_IRT <- stripWhitespace(Analysis_Final_IRT)  

#####Part of output with cleaning so far
red hair silver earrings stand mile frog damien jay connell almost conspicuous clowns jugglers rock musicians gathered mostar first post war arts festival held former yugoslavia asked comes frog peers glasses says ballyfuckinfermot someone puts soundtrack trainspotting lust life frog favourite song hurtling along bumpy roads mostar wonder two streetwise young dubliners ended aid workers based around area saw much worst fighting bosnian conflict city located 150 km south sarajevo now totally divided 1992 city came attack powerful serb forces moslems croats fought together defending home town friendships forged backdrop sniper fire rocket launchers serbs withdrew city 1993 bonds dissipated almost overnight bitter bloody war lasting 10 months broke former allies thousands killed city incredible architecture including stari famous 16th century bridge spanned neretva river destroyed latter pummelled watery grave croats politically motiveless act vandalism malignant twist tale two cities frog 24 speaks local language fluently policeman stops vehicle metkovic border croatia bosnia herzegovina jay 25 first encounter former yugoslavia came three half years ago accompanied blind mother pilgrimage nearby medjugorje offered short trial volunteers charity called nobody children says six weeks working orphaned homeless children camps surrounding mostar didn want go home googletag cmd push function googletag display div gpt ad incontent 1 home says frog part time musician meant ballyfermot blues future dole life street corners call came return mostar never really option moira kelly founded nobody children currently working transport seriously ill bosnian children irish hospitals said asked back irreplaceable friends thought mad still says jay receive allowance 150 per month rely family supporters back home keep jeans trainers past three years revealed whole new world brothers opened says frog irish mostar en masse summer irish aid worker jadzia kaminska returned city dublin based charity cradle made children mostar priority since war began cites experience father arrived ireland child refugee poland second world war catalyst work set primary school mostar established violin project rekindle long
```



Note that I did this process for all my news sources. The New York Times(NYT) text data was the difficult to scrape because the way their archive is set up makes it difficult to get the whole body text as a chunck with the CSS selector. It was a bit challenging cleaning it out but I am almost done with that data. The other challenge I had was the NULL output I kept getting initially. I later realised that I had to change from factor to character. Now, I am going to try running some analysis with the NLP event data codes we worked on this week. I figured out that scraping, cleaning and creating the final Corpus takes patience and time but the front loading work of cleaning I guess will pay off at the analysis stage. After I am done cleaning with the NYT data, I will put all my data into one Corpus and the fun will begin. I will have some graphs to show in my next post!






