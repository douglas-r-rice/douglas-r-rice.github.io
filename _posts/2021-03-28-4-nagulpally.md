---
layout: post
title: "Dictionary Approaches + Supervised Learning for Media Corpora (Nagulpally)"
author: Anjali Nagulpally
date: 2021-03-28 12:24:54 -0500
categories: nagulpally
---

**Corpus**: CNN news articles ($n \approx 100,000$)

**Goal**: Comparing various low-dimensional representations of texts

**Pre-processing**: Removed capitalization and punctuation; keeping numbers and full words (ie. no stemming) for now

# Dictionary Approaches

### Virtue vs. Vice

Given that I am interested in representing the *content* of the documents in my corpus, I decided to focus on non-sentiment-focused dictionaries. Sentiment dictionaries provide information on *emotion*, which isn't a sufficient proxy for *content* -- eg. knowing a news article has a lot of "excitement" doesn't actually tell me what it's about.  

Instead, I decided to try out the **Moral Foundations Dictionary**, which looks at the proportion of "virtue" and "vice" words for each foundation of morality: care, fairnesss, loyalty, authority, and sanctity. For example, "Kindness" and "compassion" are in the "care-virtue" category of the dictionary, while "suffer" and "cruel" are in the "care-vice" category. There are 210 words per category in the dictionary, on average. These categories seem to be slightly richer signals of content/subject matter, compared to those of sentiment dictionaries.

I first created "virtue" and "vice" scores for each document, by counting up the number of virtue words and vice words, respectively, in each document.

```{r, eval=FALSE}
mfd_df$virtue_score <- apply(mfd_df[virtue_cols], 1, sum)
mfd_df$vice_score <- apply(mfd_df[vice_cols], 1, sum) 
```

This approach was heavily biased by the *length* of texts, as we saw in the class tutorial on dictionary approaches. The documents with the highest virtue or vice scores had many virtue or vice words simply because they were very long texts -- for example, one of the most "vice"-heavy texts was a detailed list of the 48 counts with which Jerry Sandusky, a former football coach and convicted sex offender, was charged. 

Therefore, I normalized the scores by the number of total virtue and vice words in the text:
```{r, eval=FALSE}
mfd_df$virtue_score <- apply(mfd_df[virtue_cols], 1, sum) /  apply(mfd_df[2:11], 1, sum)
mfd_df$vice_score <- apply(mfd_df[vice_cols], 1, sum) / apply(mfd_df[2:11], 1, sum)
```

We can see the distribution of vice and virtue scores below:

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali-Blog4-virtuevice.png?raw=TRUE)

(The plots are mirror images of each other because (based on the way I defined them) the virtue and vice scores sum to 1.)

Interestingly, there are a *lot* of documents that have a virtue score of 1 -- that is, they contain virtue words and *no* vice words. Moreover, the distribution of virtue vs. vice is heavily skewed towards virtue.

#### Drawbacks to ths approach

* This approach has a **"divide-by-zero" issue**: texts with **no** virtue and vice words at all have a zero in the denominator, so the score cannot be computed. There are 511 such texts. This raises the question of what defines a "neutral" text -- is it a text with a virtue or vice score of 0.5? Or is a truly neutral text (in the context of virtue vs. vice) one of the 511 texts with neither any virtue nor vice words at all? I think I would define the former as a *balanced* text, and the latter as a *neutral* text.

* The virtue and vice scores **aren't great summarization tools** -- that is, they don't provide very detailed information about *content*. I can't tell you what an article is about just by looking at the virtue score. 

#### Moral Foundations + Virtue vs. Vice

To address the second drawback, let's preserve all the different categories of the dictionary: care/virtue, care/vice, fairness/virtue, fairness/vice, etc. So there are 10 categories total. We will still normalize the scores by the total number of virtue/vice words in each text, to remove the influence of text length (recall longer texts tend to have more virtue/vice words).

```{r, eval= FALSE}
mfd_df_normed <- mfd_df[,2:11]/rowSums(mfd_df[,2:11])
mfd_df_normed <- cbind(mfd_df$doc_id, mfd_df_normed)
```

![](https://github.com/douglas-r-rice/douglas-r-rice.github.io/blob/main/_posts/Anjali-Blog4-mfd.png?raw=TRUE)

We can see that "loyalty/virtue", "authority/virtue", "care/virtue", and "care/vice" words tend to be more common than other categories.

Let's look at a text with a high proportion of authority/virtue words. This text has an authority/virtue score of 1:

> (CNN)  -- Lionel Messi will captain Argentina for the first time as they take on Venezuela in a friendly in cricket-mad India Friday. Messi has been handed the responsibility by new Argentina coach Alejandro Sabella after their disappointing Copa America campaign. They went out in the quarterfinals to eventual winners Uruguay, costing then coach Sergio Batista his job. \"He is the captain from now on,\" Sabella told gathered reporters Wednesday ahead of the match in the 120,000 capacity Salt Lake stadium in Kolkata. \"We are looking to build a couple of options, looking for one or two players to build up a combination with him.\" The arrival of World Footballer of the Year Messi in India has been greeted with much excitement and a big crowd gathered at Kolkata airport as he flew in. Barcelona star Messi and his teammates will be looking for a morale-boosting win over their South American rivals in the biggest football match over played in India. They will then go to Bangladesh for a friendly against Nigeria's Super Eagles on September 6 as they prepare for 2014 World Cup qualifiers which begin in October. \"Winning will give us confidence. This is the beginning of a long trip ahead and we hope it finishes with qualification to the World Cup,\" added Sabella. Friday's match will be played on an artificial pitch in a cricket stadium adapted for football. @highlight Lionel Messi to captain Argentina in friendly against Venezuela @highlight Match is taking place at the cricket stadium in Kolkata in India @highlight Argentina went out of the Copa America at the quarterfinal stage"

It's a bit surprising, really -- yes, this text mentions the word "captain" quite a bit, and Lionel Messi *is* the center of the Argentina team, but if I *only* had the various virtue/vice scores I would never had guessed that the associated article was about soccer.

Further, it's not as though all the texts that score high on authority have the same subject matter -- two are about soccer captains, one is about the US Navy, one is about luxury cars, and another about toruism.

So again, we see that this approach has a major drawback -- the moral foundations dictionary doesn't provide sufficient information about **content**.

# Supervised Learning

The other approach we learned about was supervised learning methods, eg. random forest, support vector machine, etc. Each of these models requires a *y variable* -- that is, it needs to predict something. In the tutorials, we used these models to predict the polarity of texts. 

I'm not sure these are appropriate for this corpus/project, where we are interested not in **prediction**, but rather low-dimensional **representation**. Certainly, we want to validate the machine-made representations to some "ground-truth" representation, but I don't think we are interested in predicting some label (eg. polarity).

I have been thinking somewhat about how to validate the embeddings/representations. Each text in the CNN corpus ends with a few "highlights" that summarize the text in a sentence or less. I extracted these from each text in the corpus. Here are a few such highlights, each taken from a separate article:

> @highlight Usain Bolt wins third gold of world championship @highlight Anchors Jamaica to 4x100m relay victory @highlight Eighth gold at the championships for Bolt @highlight Jamaica double up in women's 4x100m relay

> @highlight The employee in agency's Kansas City office is among hundreds of \"virtual\" workers @highlight The employee's travel to and from the mainland U.S. last year cost more than $24,000 @highlight The telecommuting program, like all GSA practices, is under review

> @highlight Labor Day is the unofficial end of summer and the unofficial start to campaign season @highlight As much as $3.4 billion could be spent on advertising for this midterm election @highlight Here are five must-follow races for these midterms

We can see that the highlights provide a reasonable summary of the contents of each text: we can ascertain that the first text recaps Usain Bolt and Team Jamaica's performance at a world championship; the second text reports some extreme spending in a GSA office in Kansas City; and the third is about some upcoming midterm elections. I think this is what I'll want to compare the embeddings to, in some way -- I'll have to think about what kinds of evaluation measures I want to use, as well as what embedding methods I want to explore.