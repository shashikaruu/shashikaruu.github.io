---
title: "Simulating an alternate history"
date: 2018-01-28
tags: [machine learning, history, development]
header:
  image: ""
excerpt: "Machine Learning, History, Development"
---

Us development economists love to ask the question: "But what is your counterfactual?" or to put simply "What are we comparing this treatment/experience to?" The question becomes harder to answer when you look investigating a historical experience.

Let's take a colonial treatment as an example.

Frequently, when we want to assess the long-term effects of being colonised, we find a comparison area that was almost colonised as a counterfactual group. This technique, pioneered by Dell (2010), famously uses a geographic regression discontinuity design framework. To put it simply, you compare regions that were colonised, with regions that were *almost* colonised - where borders between the two were arbitrarily designed.

As an example in historical India, one can compare villages in a former British colonised region, with villages in a nearby Native Ruled State that was almost colonised, whose borders were determined arbitrarily by rivers.

But what if, within the colonised region, the coloniser enacted a variety of programs - some which are potentially good and some potentially bad for long-run development? What then is the counterfactual comparison group?

This is the question I face in assessing the long-run effects of colonial land tenures in India. In my study area, the region south of the red border was colonised by the British. Here, the British established four different types of land tenures (depicted in the map below). Some were potentially bad for long-run development (for example, extractive feudal landlord tenures, i.e. *villages in pink*). However, some may be potentially good for long-run development (for example strong property rights cultivator villages, *villages in blue*). If we want to assess the long-run effects of these different land tenures, we need to compare them with villages who were almost treated by these different colonial land tenures ... so what do we do?

![svg]({{ site.url }}/images/act_institutions_v2.jpg)

In turns out, machine learning can help us with this. We can employ its approach to simulate colonial British land tenures in the nearby region that was not colonised - and then compare each treated land tenure villages with its matching comparison group in the not colonised region.

I do so by exploiting the assignment mechanism of these land systems. In my study area, these different tenures were assigned by the British Imperial Forest Department, and were based on the quality of forests.
If we map out the land suitability for forest (below) and visually compare it with the actual land tenures assigned, you can see the relationship between both.

![svg]({{ site.url }}/images/1880_forest.jpg)
