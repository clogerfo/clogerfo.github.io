---
title: "Using Classification to Re-think the Strikezone"
date: 2020-10-15
excerpt: "Predicting strikes"
---

# H1 Heading

## H2 Heading

**Re-thinking** the strike zone use data from MLB games. 


You can use any dataset of strikes and balls to replicate this analysis. So long as the data has pitch coordinates (px, pz) and outcomes (strike or ball). Statcast data can be found [here](https://baseballsavant.mlb.com/statcast_search). The data I used and all of my code can be found at the [project's repository](https://github.com/clogerfo/predicting_strikes). The code for cleaning/preparing the data is included in the R script. Credit to Jim Albert's [(Exploring Baseball Data with R)](https://baseballwithr.wordpress.com/author/bayesball/) [code](https://gist.github.com/bayesball) for building the strike zone visualization.

# Background
A strike zone in baseball is the area over home plate that a pitch must pass through in order to be called a strike. By the rule book, the horizontal dimension is 17 inches - the width of home plate - and the vertical dimension is from the batter's knees the the midway point between their shoulders and waist. Ideally, everything that is in that zone will be called a strike 100% of the time, and everything outside of that zone will be called a ball 100% of the time. However, because balls and strikes depend on human judgement, the ideal strike zone is not realistic. 

In reality, the strike zone is a map of probabilities. That is, a pitch that touches a part of the strike zone has a certain probability of being called a strike by the umpire. Essentially, this is a classification problem. A pitch is either a strike or a ball depending on its x,y coordinates over home plate.

# Prediction via Classification
Based on the above framework, we expect a pitch right down the middle of the plate to be called a strike nearly 100% of the time. Use this visualization from MLB.com as a reference.

![](http://mlb.mlb.com/assets/images/0/5/6/308414056/raw.jpg)

As the pitches get further from the center (in any direction), the probability of being called a strike falls.

So, you can imagine every possible location in our sample space will have an associated likelihood of being called a strike or ball. We determine this likelihood, or probability, based on historical data detailing the x,y coordinates and outcomes (strike or ball) of thousands of pitches.

Here's how we can do that.
All code at [repo](https://github.com/clogerfo/predicting_strikes).


I began by filtering down the data so that we had only pitches that were called balls or strikes by an umpire (removed any pitches where a batter made contact).

Then, this data was used to fit the following GAM model: is_strike ~ s(px, pz). This model will produce, based off our data, the likelihood of a pitch being called a strike given its x and y coordinates. 

Now we can plug in any x,y coordinate into that model and it will return a strike probability.

Instead of going one pitch at a time and testing the endless possible locations, we opt to build a data frame of x and y coordinates and feed that data to the model. This was above me, but thankfully Jim Albert [figured this out](https://gist.github.com/bayesball).


Because I split the data between left and right handed batters to assess how their strike zones differed, I trained 2 GAMs and built 2 plots: 

<figure class="half">
	<a href="https://imgur.com/zQ1tCes"><img src="https://i.imgur.com/zQ1tCes.png" title="source: imgur.com" /></a>
	<a href="https://imgur.com/LekHnf6"><img src="https://i.imgur.com/LekHnf6.png" title="source: imgur.com" /></a>
	<figcaption>LHB and RHB Strike Zones.</figcaption>
</figure>

If umpires were perfect, everything inside the strike zone would have a 100% probability of being called a strike and everything outside would have a 0% probability of being called a strike.

As we can see, the strike zone is more oval-shaped than rectangular. The further from the center a pitch is the less likely it is to be called a strike. And, we see that strikes are actually called differently depending on the batter being a lefty or righty.





