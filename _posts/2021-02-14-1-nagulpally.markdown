---
layout: post
title: "Nagulpally: Representations of High-Dimensional Datasets"
date: 2021-02-14 12:24:54 -0500
categories: nagulpally, firstpost
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Introduction

My name is Anjali, and I'm a 2nd year PhD student in statistics.

## Research Interests

My research currently centers on clustering models for DNA methylation data, in the context of cancer. A central goal in cancer research is to group cancers by the affected molecular pathways; eg. identifying subtypes within a single type of cancer, or identifying which types of anatomically-different cancers (eg. ovarian, lung, colon) are genetically similar. We use DNA methylation data to do this because of the effects of abnormal methylation on gene expression; hypomethylation (ie. "under"-methylation) of cancer-causing oncogenes and hypermethylation (ie. "over"-methylation) of tumor-suppressing anti-oncogenes are strongly associated with cancer development. 

Our clusters are essentially lower-dimensional representations, ie. *embeddings*, of numerical data. So I'm naturally rather interested in looking at embeddings from the text/NLP side of things. In particular, I'm curious at methods for evaluating or comparing different representations of the same data, especially for non-predictive problems. In the methylation context, we often evaluate clusterings based on "ground-truth" information -- eg. determining whether there is a significant association between clusters of patients and survival outcomes, or to what extent clusterings reflect or improve upon known cancer subgroupings. I've also been exploring another evaluation criterion that doesn't rely on additional information: the *stability* of clusterings relative to "reasonable" perturbations in the data or model. I would be interested in exploring stability measures for text embeddings.

## Research Question

I would like to extend my current research into text data -- namely, develop methods to evaluate and compare various representations of the same texts. One method would be to compare the stability (see previous section) of different representations; I would also like to look into the literature and my chosen corpus to develop additional methods. I am curious as to whether I could find an analogous method to the "ground-truth" type of evaluations we use on methylation data.


## Corpus

I would like to use the **CNN/Daily Mail** dataset, a corpus of approximately 300,000 news stories  from CNN and the Daily Mail. This corpus is often used for 'question and answer' type analyses (ie. Creating a model that can take in text and "answer" questions about their contents); these analyses are fundamentally concerned with summarizing texts. (News articles are, I think, ideal for summarization tasks, given that they are meant to be clear, informative, and concise) Hence, I think this corpus would be good for my research project, which is similarly centered on summarizing texts via embeddings.