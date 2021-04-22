---
layout: post
title: "LDA Model and Stability (Nagulpally)"
author: Anjali Nagulpally
date: 2021-04-18 12:24:54 -0500
categories: nagulpally
---


This week, I worked on fitting an LDA model to my corpus (CNN news articles). A topic model soft clusters texts into "topics" that are defined by a group of salient terms. By looking at the top terms in a cluster, we can assign the topics (and the texts belonging to them) labels that reflect their semantic content. In this way, the topics provide a low-dimensional representation of texts.

### Fitting the model

##### Attempt #1

**Summary**

* **Tokenization**: Remove capitalization and punctuation
* **Vocabulary pruning**: Terms must occur a minimum of 10 times in the corpus and at maximum in 20% of the texts

**Motivation**

To begin with, I made the same pre-processing/pruning decisions as we made in the topic modeling tutorial from class. This led to sensible results on the movie review corpus ($n \approx 3000$), so I wanted to try it out on my corpus to see what would happen

**Results**

This set of vocabulary pruning parameters shrunk the "raw" vocabulary from 282,413 to ~75,000 terms. That seemed like way too many to be wieldy, from a computational standpoint. So I decided to stop here and adjust my pruning parameters for my next attempt.

##### Attempt #2

**Summary**

* **Tokenization**: Remove capitalization and punctuation
* **Vocabulary pruning**: Terms must occur a minimum of 10 times in the corpus, in at least 1% of texts, and at maximum in 20% of the texts

**Results**

Just adding one more parameter (minimum document frequency = 1%) shrunk the raw vocabulary all the way down to 4,605 words! Note that for the CNN corpus, 1% of texts is just over 900 texts. I was a little worried that a vocabulary of 4,605 words was too small for a corpus of this size, so I stopped here to relax the minimum document frequency requirement for my next attempt.

For my next attempt, I also wanted to play with the *maximum* document frequency parameter, too -- my thinking was that 20% might be too stringent, as CNN might report on the same things quite often (eg. US politics), and I didn't want to lose semantically important words. I gut-checked this by taking a quick look at the most frequent words in the vocabulary, and found that there didn't seem to be any stop words (the top 5 most frequent words included 'obama', 'united', and 'states').

##### Attempt #3

**Summary**

* **Tokenization**: Remove capitalization and punctuation
* **Vocabulary pruning**: Terms must occur a minimum of 10 times in the corpus, in at least 1% of texts, and at maximum in 30% of the texts

**Results**
For this attempt, I relaxed the minimum document frequency to 0.05% and the maximum document frequency to 30%. This increased the vocabulary to 7,648 terms.

From here, I fit an LDA model with 10 topics and with both priors set to 0.1. I visualized the topics and their top terms to see if the topics in fact made a sensible partition of the corpus.

I was impressed to find that all topics but 2 had clear, distinct "themes" from a semantic standpoint.

Here is what the top 5 words were for each topic:

* Topic 10: film, movie, actor, album, comedy

* Topic 9: children, students, school, student, cancer

* Topic 8: users, google, technology, apple, facebook

* Topic 7: cup, players, league, football, match 

* Topic 6: china, chinese, billion, hong, economic

* Topic 5: flight, water, plane, passengers, storm

* Topic 4: obama, republican, senate, republicans, election

* Topic 3: police, court, attorney, case, charges

* Topic 2: al, military, forces, syria, iraq

* Topic 1: me, think, i'm, really, know

Clearly, all but topics 1 and 9 have a clear theme (and arguably, we could label 9 as 'education and health'). The main issue is with Topic 1, the largest topic, which consists of mainly stop-words. So for my next attempt, I need to remember to remove stop-words as part of my pre-processing steps.

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_topic1_stopwords.PNG?raw=TRUE)


I also see that some of the top words in each topic are different forms of the same word (eg. 'republican' and 'republicans'). Stemming words during pre-processing would avoid this, but I don't think that it's necessarily a problem that *needs* to be fixed -- and stemming could cause more issues by conflating semantically different words.

##### Attempt #4

**Summary** 

* **Tokenization**: Remove capitalization, punctuation, and stopwords
* **Vocabulary pruning**: Terms must occur a minimum of 10 times in the corpus, in at least 1% of texts, and at maximum in 30% of the texts

**Results**

Removing stopwords (instead of simply relying on the maximum document frequency parameter) led to an improvement in topic clarity. Specifically, now *all* the topics have a relatively clear theme, as we can see by the top words:

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_topwords.PNG?raw=TRUE)

We could easily "name" the first 8 topics: "legal", "health and education", "travel, transportation, and weather", "military and Middle East", "technology", and "entertainment". The only ambiguity is between the last 2 topics -- the delineation between them is a little more fuzzy.

## Evaluating the model: stability

#### Introduction 
One way to evaluate a representation learning model is to examine its **stability** -- that is, how the model results change in response to reasonable perturbations to the data or model architecture. The idea is that a "good" model will not shift too much in response to small, reasonable changes (eg. changes in hyperparameter values, additional data, feature engineering decisions); stability is a key component of reliable and reproducible results. The use of stability as a model selection and evaluation criterion is becoming more prominent in the literature, and is part of Bin Yu's "Veridical Data Science Framework".

For a fixed number of perturbations $n$, we can evaluate the stability of a clustering model using a **co-occurrence matrix**:

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_co-occurrence.png?raw=TRUE)

(Note that by definition, it is a symmetric matrix, so we need only consider the upper triangular portion, highlighted in green)

The more stable a model is, the closer to $n$ the off-diagonal elements will be.

We can further use the co-occurrence matrix to **compare** the stability of various representation models (eg. LDA, STM, etc.).

#### Idea

When measuring stability, the idea is that the perturbations you're measuring should reflect decisions made during the modeling process: eg. pre-processing parameters, choice of prior parameters on the LDA model, number of topics in the model. 

My plan is to measure the stability of the LDA model with respect to the following changes:

* Pre-processing parameters: vary the maximum document frequency between $\{20\%, 25\% , 30\%\}$ and the minimum document frequency between $\{0.5\%, 1\%, 5\%\}$

* Model parameters: vary the number of topics between $\{10, 20, 25, 30\}$, and vary the prior parameters between $\{0.1, 0.2, 0.3, 0.4\}$.

TBD: [Wallach et al. (2009)](https://dirichlet.net/pdf/wallach09rethinking.pdf) argues for the use of "asymmetric" and "symmetric" priors for the document-topic and topic-word priors. I'm not sure whether the text2vec LDA function allows for this in $R$. I'll have to look into this further.