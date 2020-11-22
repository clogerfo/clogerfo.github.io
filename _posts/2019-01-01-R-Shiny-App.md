---
title: "R Shiny App Implementation"
date: 2019-01-01
excerpt: "Geographic Mapping with an R Shiny App"
---


In this project I try my hand at 2 things - Implementing an R Shiny App and plotting geospatial data. I used a data set I had already built and was familiar with - [MLB FA Data](https://github.com/clogerfo/R_Shiny_App_Choropleth_Map/blob/main/data/MLBFA_data.csv). I built this data set using a web scraping script to pull data from a couple different sites. You can find that script [here]().

All of my code can be found at the [project's repository](https://github.com/clogerfo/R_Shiny_App_Choropleth_Map). 

The R Shiny App I built is an interactive choropleth map of the United States. The data breaks out MLB Free agents between 2010 - 2017 and tracks the value and length of the contracts they signed (along with various other variables). For mapping purposes, each state is shaded according to the total value of contracts signed by players born in that state.

Here's the finished [product](https://cooperlogerfo.shinyapps.io/mlbfa_shiny/).