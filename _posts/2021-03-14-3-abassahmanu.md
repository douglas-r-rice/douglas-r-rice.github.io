---
layout: post
title: "Representing text from news articles with r studio (Abassah Manu)"
author: Roselyn Abassah Manu
date: 2021-03-14 12:24:54 -0500
categories: abassahmanu
---


### Preprocessing and Representing Text

This week's post is an update on my project, which involves using text to measure emotions. As highlighted in my February 14th post, I hypothesize that news articles with a high empathetic narrative will lead to some form of intervention by the international community. But how do we identify the narratives in news articles? The main goal is to find the narratives in the newspaper articles I am using. I ask, can we identify narratives in text data to help us identify emotions? The study employs framing theory. I make use of 4 corpora for my research. I extracted and preprocessed news article text about the Bosnia and Somalia conflicts from the Washington Post, New York Times, LA Times, and Irish Times archives - the corpus comprises 94 hand-coded articles. These news articles were coded for thematic vs. episodic frames and given a score of emotionality by human coders, ranging from highly emotional to not emotional. As part of the project's preprocessing stage, I used gsub() to remove capitalization, stopwords, punctuations, single letters, and white spaces. I also noticed that removing punctuations would cause the problem of words like U.S. and U.N losing their worth, so I had to replace those with the complete spelling; hence I used the gsub() function to remove and replace. I created a term-document matrix and ran a word frequency analysis to ascertain the top 7 words. What I found was that the words "serb", "bosnia", "united_nations", "sarajevo", "war", "government" and "forces" were the top 7 words. It was interesting to see that Somalia was not in the top 7 words. With this outcome, I created graphical representations using wordcloud2 and ggplot in R Studio. Even though I have had difficulty combing my corpora, it was interesting to see a similar trend across, especially my Washington Post and New York Times corpora, especially with the word frequencies. My goal is to be able to figure out the correct code to combine my corpora. The problem is that the data structure is different for every news source per their CSS selectors. I hope to combine all corpora and work with some feature co-occurrence matrix and also incorporate dictionary methods and build my way up to Topic Modeling, themes, and mapping out themes. I am intrigued to know more about what is going on in the text through topic modeling and embedded themes embedded. In my next blog post, I will share more of the discoveries within my corpus and my graphical representations as I work my way up from dictionary methods to topic modeling. 
