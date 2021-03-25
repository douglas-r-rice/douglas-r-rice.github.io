---
layout: post
title: "Pre-processing the CNN Corpus (Nagulpally)"
author: Anjali Nagulpally
date: 2021-03-14 12:24:54 -0500
categories: nagulpally
---


As a reminder, I'm using the **CNN dataset**, which consists of almost 100,000 news stories from CNN.com. There is a pretty wide variety in article length in this corpus; they range between 1 and 173 sentences in length. On average, articles are about 33 sentences or ~800 words long.

## Pre-processing

#### Tokenization

I first tried out simple tokenization -- that is, not removing any punctuation, capitalization, or numbers.

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_BlogPost3_simpletok.PNG?raw=TRUE)

This histogram shows that most articles have somewhere between 250 and 1250 tokens, with a few outlier articles containing greater than 2000. 

However, taking a closer look at the tokens themselves shows that there are some "meaningless" tokens -- for example, parentheses, colons, periods, and common stopwords like "the". It's also immediately apparent that there are some repeats due to capitalization; for example "The" and "the" are considered distinct tokens. That is probably an issue, but I'm hesitant at the moment to remove all capitalization from the corpus; we'll see if removing stopwords takes care of "the" and "The" later.

I think it's fair to say that punctuation marks like colons, commas, periods, and parentheses don't provide much semantic value, so let's remove them as tokens. There's also an option to split  hyphenated words (so "15-year-old" becomes "15" "year" "old"); I'm not going to do that for now, since that seems to remove a fair bit of meaning.

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_BlogPost3_nopunc.PNG?raw=TRUE)

After removing punctuation, we see that:

* The general shape of the distribution doesn't change too much. Most articles still have between 250 and 1250 tokens.

* The outliers have gone away -- there are no longer any articles with >2000 tokens.


Still, I'm not completely satisfied after looking at the list of tokens for each article. I was hesitant before, but it seems clear now that capitalization is definitely an issue, because the first word in every sentence of every article is capitalized. That creates a *lot* of duplicates. I'll remove capitalization for now, but I'll do sensitivity analysis with regards to this decision when I get to the analysis stage of my project, to see how much it affects my results. Interestingly *tokens_tolower()* has an option to retain the capitalization of acronyms (by not converting words that are in all-caps). That may come in handy later.

I'm not going to remove numbers for now, because I don't want to blindly remove too many tokens. I'll see if it will become necessary to do so later on.


**Note**: It took a couple of tries to make the histogram of token counts. My first attempt at getting the token counts after each step looked like this:

```{r, eval=FALSE}
hist(docvars(cnn_tokens)$Tokens)
hist(docvars(cnn_tokens_nopunctuation)$Tokens)
```

This doesn't accurately count the number of tokens -- specifically, these counts always stay the same (I believe they reflect the token counts from the unprocessed corpus). So tokenization doesn't change these counts.

Instead, what worked was using the function *ntoken()*:

```{r, eval=FALSE}
hist(ntoken(cnn_tokens))
hist(ntoken(cnn_tokens_nopunctuation))
```

#### Stop words

**Sidenote**: I added the string "cnn" to the stopword dictionary; this string precedes the vast majority of texts in my corpus (most articles begin with the slug *(CNN)*), so it doesn't provide much  meaning.

Removing stop words changes the distribution of tokens quite a bit.

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_BlogPost3_nopuncstop.PNG?raw=TRUE)


Now most articles have between 100 and 600 tokens. With stopwords removed, looking at the list of tokens for each article, I'm beginning to get a rough idea of what each article is about.

For example:

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali_BlogPost3_tokenlist.PNG?raw=TRUE)

I can take an educated guess that Article 1 is (at least in part) about lawmakers weighing the use of military force during the Obama administration; Article 2 is about Usain Bolt winning his third gold medal at the World Championships, which took place on a Sunday in Moscow; and Article 3 is about a Kansas City, MO  organization involved in an investigation regarding lavish spending.

This is promising -- the goal of my project is to evaluate different approaches to summarizing/embedding texts. I can see that the texts are already being reduced into more concise forms that still preserve meaning. There's certainly still a long way to go -- most texts still are several hundred tokens long -- but we're already getting somewhere.
