---
title: "Using Classification to Re-think the Strikezone"
date: 2020-10-15
excerpt: "Predicting strikes"
---

# H1 Heading

## H2 Heading

**Re-thinking** the strike zone use data from MLB games. 


You can use any dataset of strikes and balls to replicate this analysis. So long as the data has pitch coordinates (px, pz) and outcomes (strike or ball). Statcast data can be found [here](https://baseballsavant.mlb.com/statcast_search). The data I used and all of my code can be found at the [project's repository](https://github.com/clogerfo/predicting_strikes). The code for cleaning/preparing the data is included in the R script. 

# Background
A strike zone in baseball is the area over home plate that a pitch must pass through in order to be called a strike. By the rule book, the horizontal dimension is 17 inches - the width of home plate - and the vertical dimension is from the batter's knees the the midway point between their shoulders and waist. Ideally, everything that is in that zone will be called a strike 100% of the time, and everything outside of that zone will be called a ball 100% of the time. However, because balls and strikes depend on human judgement, the ideal strike zone is not realistic. 

In reality, the strike zone is a map of probabilities. That is, a pitch that touches a part of the strike zone has a certain probability of being called a strike by the umpire. Essentially, this is a classification problem. A pitch is either a strike or a ball depending on its x,y coordinates over home plate.

# Prediction via Classification
Based on the above framework, we expect a pitch right down the middle of the plate to be called a strike nearly 100% of the time. Use this visualization from MLB.com as a reference.

![](http://mlb.mlb.com/assets/images/0/5/6/308414056/raw.jpg)

As the pitches get further from the center (in any direction), the probability of being called a strike falls.

So, you can imagine every possible location in our sample space will have an associated likelihood of being called a strike or ball. We determine this likelihood, or probability, based on historical data detailing the x,y coordinates and outcomes (strike or ball) of thousands of pitches.













