---
title: "Simulating an alternate history - with machine learning"
date: 2018-01-28
tags: [machine learning, history, development]
header:
  image: ""
excerpt: "Machine Learning, History, Development"
---

## What is your counterfactual?

Us development economists love to ask the question: "But what is your counterfactual?" or to put simply "What are we comparing this treatment/experience to?" The question becomes harder to answer when you're investigating a historical experience.

Let's take a colonial treatment as an example.

Frequently, when we want to assess the long-term effects of being colonised, we find a comparison area that was almost colonised as a counterfactual group. This technique, pioneered by Dell (2010), famously uses a geographic regression discontinuity design framework. To put it simply, you compare regions that were colonised, with regions that were *almost* colonised - where borders between the two were arbitrarily designed.

## Land tenures in colonial tenures

As an example in historical India, one can compare villages in a former British colonised region, with villages in a nearby Native Ruled State that was almost colonised, whose borders were determined arbitrarily by rivers.

But what if, within the colonised region, the coloniser enacted a variety of programs - some which are potentially good and some potentially bad for long-run development? What then is the counterfactual comparison group?

This is the question I face in assessing the long-run effects of colonial land tenures in India. In my study area, the region south of the red border was colonised by the British. Here, the British established four different types of land tenures (depicted in the map below). Some were potentially bad for long-run development (for example, extractive feudal landlord tenures, *in blue*). However, some may be potentially good for long-run development (for example strong property rights cultivator villages, *in pink*).

![svg]({{ site.url }}/images/act_institutions_v2.jpg)

If we want to assess their long-run effects, we must compare them with villages who were almost treated by these different colonial land tenures ... so what do we do?

## Help us out, machine learning!

In turns out, machine learning can help us out - a lot. We borrow from its techniques to simulate an alternate history. In my case, we can simulate the land tenures that would have been established in the non-colonised area, had the British actually colonised the region. We then use these as the comparison counterfactual groups to the actual British land tenures.

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

ml_british<-as.data.frame(cbind(british$forest_suitability,british%village_ID,
as.factor(british$land_tenure))

ml_not_british<-as.data.frame(cbind(not_british$forest_suitability,not_british%village_ID,
as.factor(not_british$land_tenure))

#MAKE A TRAINING AND TEST SAMPLE
set.seed(333)
samp <- sample(nrow(british), 0.80 * nrow(british))
train <- british[samp, ]
test <- british[-samp, ]

library("randomForest")
model <- randomForest(land tenure ~ . - village_ID, data=train, na.action=na.roughfix, ntree=25000)

#TEST SAMPLE Outputting

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

If we map it out, this is what the actual and counterfactual land tenures look like in the study region. We can not compare the long-run effects of each British land tenure system, by comparing villages assigned the actual tenures with villages with its counterfactual villages in the non-colonised region.

![svg]({{ site.url }}/images/cf_institutions.jpg)

## A novel approach ... in economics

The approach I've used is straightforward and already well-established in the computer science literature ... yet (as with many things) it has to take a mainstream hold in economics. I hope this example will motivate you to venture out into other possibilities outside economics - particularly to borrow from techniques in machine learning and use to to answer questions that we otherwise couldn't, with our exiting tools in economics!
