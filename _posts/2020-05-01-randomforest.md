---
title: "Simulating an alternate history"
date: 2018-01-28
tags: [machine learning, history, development]
header:
excerpt: "Machine Learning, History, Development"
---

## What is your counterfactual?

Us development economists love to ask the question: "But what is your counterfactual?" or to put simply "What are we comparing this treatment/experience to?" The question becomes harder to answer when you're investigating a historical experience.

Let's take a colonial treatment as an example.

Frequently, when we want to assess the long-term effects of being colonised, we find a comparison area that was almost colonised as a counterfactual group. This technique, pioneered by [Dell (2010)](https://scholar.harvard.edu/files/dell/files/ecta8121_0.pdf), uses a geographic regression discontinuity design framework. To put it simply: you compare regions that were colonised, with regions that were *almost* colonised - where borders between the two were arbitrarily designed.

As an example in historical India, one can compare villages in a former British colonised region, with villages in a nearby Native Ruled State that was almost colonised, whose borders were determined arbitrarily by rivers.

## Land tenures in colonial India

But what if, within the colonised region, a variety of programs were enacted - some which are potentially good and some potentially bad for long-run development?

How do you evaluate these programs? And what are their respective counterfactuals?

This is the question I face in assessing the long-run effects of colonial land tenures in India. In my study area (depicted below), the region south of the red border was colonised by the British. Here, the British established four different types of land tenures. Some were potentially bad for long-run development (for example, extractive **feudal landlord** tenures *in blue*). However, some may be potentially good for long-run development (for example strong **property rights-based** cultivator villages, *in pink*).

![svg]({{ site.url }}/images/act_institutions_v2.jpg)

If we want to assess their long-run effects, we have to compare villages who received these different treatments with their appropriate counterfactuals ... but how do we do this?

## Help us out, machine learning!

In turns out, machine learning can help us out, a lot. We can borrow from its predictive techniques to simulate history, and estimate an alternate historical scenario as a counterfactual.

In my case, we can simulate the land tenures that would have been established in the non-colonised area, had the British actually colonised the region. These can then be used as the appropriate comparison groups to assess the different colonial programs.

I can do this by exploiting the assignment program of these land systems. In this study area, different tenures were assigned by the British Imperial Forest Department, and were based on the quality of forests.
If we map out the land suitability for forest (below) and visually compare it with the actual land tenures assigned, you can see the relationship between both.

![svg]({{ site.url }}/images/1880_forest.jpg)

What I do then is I use a regression tree-approach, to model and best-predict the actual land tenures by forest suitability. Below is a simplified version of the approach, I use in R, to do this:

```r
village_dataset <- read.csv("/village_dataset.csv")

#SPLIT SAMPLE BTW. BRITISH COLONISED
  #AND NON-COLONISED VILLAGES

british<-village[which(village$british==1),]
not_british<-village[which(village$british==0),]

ml_british<-as.data.frame(cbind(british$forest_suitability,british$village_ID,
as.factor(british$land_tenure))

ml_not_british<-as.data.frame(cbind(not_british$forest_suitability,not_british$village_ID,
as.factor(not_british$land_tenure)))

#MAKE A TRAINING AND TEST SAMPLE
set.seed(333)
samp <- sample(nrow(british), 0.80 * nrow(british))
train <- british[samp, ]
test <- british[-samp, ]

library("randomForest")
model <- randomForest(land tenure ~ . - village_ID, data=train, na.action=na.roughfix, ntree=25000)

#TEST SAMPLE OUTPUT

prediction <- predict(model, newdata = test)
table(prediction, test$institution)
```

Now that we have a model, we run it in the non-colonised region, to estimate the counterfactual land tenures by each village.

```r

#APPLY MODEL TO NON-COLONISED SAMPLE
predictions <- predict(model, newdata = ml_not_british)
counterfactual<as.data.frame(cbind(ml_not_british$village_ID,
  as.character(predictions)))

#ATTACH LAND TENURE COUNTERFACTUALS BACK TO VILLAGE DATASET

for(i in 1:nrow(counterfactual)){
  village$land_tenure[village$village_ID == counterfactual[i,1]]=as.character(counterfactual[i,2])
}

```

If we map it out, this is what the actual (in the south) and simulated counterfactual (in the north) land tenures look like in the study region. We can now assess the long-run effects of each British land tenure system, by comparing villages assigned the actual tenures with villages with its counterfactual villages in the non-colonised region.

![svg]({{ site.url }}/images/cf_institutions.jpg)

## It's not science fiction - it's data science

The techniques I've used is straightforward and already well-established in the computer science literature - yet, this type of application is new.

As with many things in economics, the use of machine learning has yet to take a mainstream hold in the field. I show here that the use of these techniques (in this case, leveraging off the predictive superiority of regression trees) allows us to probe into questions that we otherwise couldn't, given our existing economics toolset.

I hope this example motivates you to venture out into other possibilities beyond economics. In my case, it's allowed to finally answer a long-standing question in the development and historical literature: what exactly is the legacy (both the good and bad) of British land tenure systems in India today?
