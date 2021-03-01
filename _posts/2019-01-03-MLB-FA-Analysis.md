---
title: "Predicting MLB Free Agent Salaries"
date: 2019-1-3
---

The code and data for this project can be found at this <a href = "https://github.com/CooperLogerfo/MLB-Free-Agents" > repository. </a>
In this writeup I will train a random forest algorithm for the purpose of predicting salaries and use ggplot to plot the algorithm's results.


![Viz1]({{ site.url }}/images/shiny_pic.png)

Above is an image of a <a href = "https://shiny.rstudio.com/" > Shiny App </a> (<a href = "https://github.com/CooperLogerfo/MLB-Free-Agents/tree/master/MLBFA_shiny" > Implementation Here </a>) I produced to visualize data on MLB Free Agents from 2010 - 2017. Follow this link to see the fully interactive Shiny web application: <a href = "https://cooperlogerfo.shinyapps.io/mlbfa_shiny/" > https://cooperlogerfo.shinyapps.io/mlbfa_shiny/. </a>

The rest of this post will be a predictive analysis on the MLB data mentioned above, and shown below. Specifically, the goal will be to predict contract value using the random forest algorithm.

### The data



<div class="table-wrapper" markdown="block">
  
| FA_year | name           | team     | position | contractValue | contractDuration | avg_war | age   | BirthPlace                        | experience | college           |
|---------|----------------|----------|----------|---------------|------------------|---------|-------|-----------------------------------|------------|-------------------|
| 2017    | Eric Hosmer    | Padres   | 1B       | $144,000,000  | 8                | 2.60    | 28.00 | South Miami, FL                   | 6          | None              |
| 2017    | Yu Darvish     | Cubs     | P        | $126,000,000  | 6                | 3.20    | 31.00 | Osaka, Japan                      | 4          | None              |
| 2017    | J.D. Martinez  | Red Sox  | OF       | $110,000,000  | 5                | 3.73    | 30.00 | Miami, FL                         | 6          | Nova Southeastern |
| 2017    | Lorenzo Cain   | Brewers  | OF       | $80,000,000   | 5                | 4.20    | 31.00 | Valdosta, GA                      | 7          | Tallahassee CC FL |
| 2017    | Jake Arrieta   | Phillies | P        | $75,000,000   | 3                | 4.50    | 31.00 | Farmington, MO                    | 7          | TCU               |
| 2017    | Carlos Santana | Phillies | 1B       | $60,000,000   | 3                | 2.77    | 31.00 | Santo Domingo, Dominican Republic | 7          | None              |
| 2017    | Alex Cobb      | Orioles  | P        | $57,000,000   | 4                | 1.10    | 30.00 | Boston, MA                        | 5          | None              |
| 2017    | Wade Davis     | Rockies  | P        | $52,000,000   | 3                | 1.47    | 32.00 | Lake Wales, FL                    | 8          | None              |
| 2017    | Jay Bruce      | Mets     | OF       | $39,000,000   | 3                | 1.03    | 30.00 | Beaumont, TX                      | 9          | None              |

</div>


### Prepping the data

I already completed extensive exploratory data analysis for this data set that can be seen in my <a href ="{{ site.url }}/files/CooperLogerfoThesis.pdf" > thesis (PDF). </a> 

One aspect worth reconsidering before conducting this analysis is our response variable. The primary response will be Average Annual Value (AAV) which is simply a player's total contract value divided by the duration of the contract, in years. For instance, Eric Hosmer's AAV equals $18 million (144,000,000 / 8). 

A cursory look at the distribution of our AAV data via the histogram below shows a significant right skew in the distribution. This is a common occoruance in salary data, and should be corrected with a log transformation before continuing with the analysis.

![Hist1]({{ site.url }}/images/AAV_Hist_i.png)


The histogram below displays the distribution of log(Average Annual Value), showing a much more balanced distribution than the one above.


![Hist2]({{ site.url }}/images/lnAAV_HIST_i.png)


Modifying the "birthPlace" variable. When web scraping the data, the player's birthPlace was identified by a string that contained their hometown and state. I wanted to modify this create two new variables, one identifying their birth country and another identifying whether the player was born in the United States or not.

```{r}
state_list = c("AL", "AK", "AZ", "AR", "CA", "CO", "CT", "DE", "DC",  "FL", "GA", "HI", "ID", "IL", 
               "IN", "IA", "KS", "KY", "LA", "ME", "MD", "MA", "MI", "MN", "MS", "MO",
               "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "OH", "OK", "OR", "PA", 
               "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VA", "WA", "WV", "WI", "WY", "Puerto Rico")

canada = c("ON", "BC")

for (i in 1:length(d$name)){
  for(j in 1:52){
    if(grepl(state_list[j], birth[i]))
      birthCountry[i] <- "US"
  }
  if (grepl("DR", birth[i])){
    birthCountry[i] <- "DR"
  }
  else if (grepl("Dominican Republic", birth[i])){
    birthCountry[i] <- "DR"
  }
  else if (grepl("Venezuela", birth[i])){
    birthCountry[i] <- "Venezuela"
  }
  else if (grepl("Puerto Rico", birth[i])){
    birthCountry[i] <- "Puerto Rico"
  }
  else if (grepl("Mexico", birth[i])){
    birthCountry[i] <- "Mexico"
  }
  else if (grepl("Cuba", birth[i])){
    birthCountry[i] <- "Cuba"
  }
  else if (grepl("Japan", birth[i])){
    birthCountry[i] <- "Japan"
  }
  else if (grepl("US", birthCountry[i])){
    #skip
  }
  else if (grepl("CA", birthCountry[i])){
    #skip
  }
  else{
    #print(birth[i])
    birthCountry[i] <- "Other"
  }
}

d$birthCountry <- as.factor(birthCountry)

#this checks whehter the BirthPlace string contains a US state. If it does, it returns a 1 and if not a 0.
d$US <- as.logical(str_detect(d$BirthPlace, paste(state_list, collapse='|')))
```

There are some final modifications to make. Creating a binary variable for whether or not the player attended college (1 = attended, 0 = did not attend), creating a binary variable for contract duration (1 = 1 year, 0 = multiyear), and modifying type of race and target.year variables. 

```{r}
d$race <- as.factor(d$race)
d$target.year <- as.factor(d$target.year)

d$binaryCol <- as.factor(ifelse(d$college == "None", 0, 1))
d$contractDuration <- as.factor(ifelse(d$contractDuration == 1, 1, 0))
```

This data set has some extreneous variables, for instance "age" provides the age at the time the data was pulled from ESPN/Fangraphs. "contractAge," meanwhile, is the age the player was at the point of signing his contract. "experience" vs "contractExperience" should be interpreted the same but with the player's years of experience instead of age. The following variables can be removed :

```{r}
d <- d %>% dplyr::select(-c(age, experience, college, BirthPlace,
                            contractValue, name))
```
### The analysis

Required packages:
```{r}
install.packages('randomForest', 'tidyverse', 'stringr')
```

The first step is to split the data into training and test subsets such that the model can be appropriately evaluated. To do so, I will randomly sample data points from our data set, building a training set that contains 80% of the original observations and a test set that contains 20%.  
```{r}
set.seed(101)
split <- sample(2, nrow(d), replace = TRUE, prob = c(0.8, 0.2))
train1 <- d[split == 1, ]
test1 <- d[split == 2,]
```

Then I trained and predicted the data using the randomForest package.

```{r}
set.seed(202)
rf1 <- randomForest(avg.value ~ ., data = train1)
p1 <- predict(rf1, test1)
test_mse1 <- mean((p1 - test1[, "avg.value"])^2)
```

The "test_mse1" variable represents the algorithm's mean squared error, which is the metric for evaluating how well it predicts the data. It is the average of the differences between the models predictions and the actual observations in the test data subset. 

Setting mtry = 4 ultimately produced the smallest MSE, equal to 0.467.

```{r}
set.seed(303)
rf2 <- randomForest(avg.value ~., mtry = 4, data = train1)
p2 <- predict(rf2, test1)
test_mse2 <- mean((p2 - test1[, "avg.value"])^2)
```


