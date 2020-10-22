In this post I will use R's <a href = "https://github.com/dkahle/ggmap" > ggmap </a> and <a href = "https://github.com/thomasp85/gganimate" > gganimate </a> packages to analyze Twitter reactions in respect to Hurricane Michael. Specifically, I looked at tweets from Panama City, Pensicola, Tallahassee, Montgomery, Mobile, Dotham, Colombia, and Albany over the period October 3rd - October 15th.

The complete code and data sets can be found on my github page at this <a href = "https://github.com/CooperLogerfo/HurricaneMichael_NLP" > repository </a>.

### Getting the data

I used a python script that implemented <a href = "http://www.tweepy.org/" > Tweepy </a> in order to access the necessary Tweets.
Here is the basic code that is used to connect to Twitter's API via Tweepy. When searching for Tweets, Twitter's API permits the use of a query and geocode. This was important for me as I was interested in tweets originiating from specific cities in Southeast USA.

The query I made was "Hurricane Michael" and the geocode was the longitude and latitude of each city, with a 5mile radius. This means my script returned all the tweets made on a given day from each location that included the words "Hurricane Michael." This isn't perfect of course, as it will miss tweets that included the words "Michael storm" or simply "hurricane," for example. 

```python
def connect_to_twitter():
    auth = OAuthHandler(creds.CONSUMER_KEY, creds.CONSUMER_SECRET)
    auth.set_access_token(creds.ACCESS_TOKEN, creds.ACCESS_TOKEN_SECRET)
    api = API(auth)
    return api
    
api = connect_to_twitter()
searched_tweets = [status for status in tweepy.Cursor(api.search, q=query, geocode = geo, 
wait_on_rate_limit = True, show_user = True).items(max_tweets)]
```

I used my script to formulate a unique json file for "hurricane michael" tweets made each day from each city. That way, I could analyze the rise and fall of the quantity of tweets as Hurricane Michael was nearing landfall and subsequently lessening in strength. 

### Organizing the data
The primary goal I had was to plot the number of tweets, and their sentiment, made from each city on each day, hopefully seeing how the quantity increased and sentiment worsened as the storm was gaining strength. So, the first step was writing functions that would count the number of tweets from each city made on each day and assess their sentiment (simply positive or negative). They looked like this.

```{r}
# Returns vector of sizes (number of tweets) for each dataframe in city list
get_size <- function(city_list){
  size <- rep(0, length(city_list) )
  for( i in 1:length(city_list) ){
    if(is.null(dim(city_list[[i]])[1])){
      size[i] <- 0
    }
    else{
      size[i] <- dim(city_list[[i]])[1]
    }
  }
  return(size)
}

# Returns vector of sentiment for each dataframe in city list
get_sent <- function(city_list){
  sent_vector <- rep(0, length(city_list) )
  for( i in 1:length(city_list) ){
    if( length(city_list[[i]]) < 1 ){
      #skip
    }
    else{
      sent_vector[i] <- sum(get_sentiment(city_list[[i]]$Tweets))/dim(city_list[[i]])[1]
    }
  }
  return(sent_vector)
}
```
The json files for tweets from each day were stored in a seperate list for each city. So, I had 8 lists of length 12. That is, each list had 12 seperate dataframes that housed the tweets made from that day. The "get_size" and "get_sent" functions took one of these lists and returned a vector each of 12 numbers identifying the number of tweets made and the sentimenet of the tweets. 

In order to use this data with ggmap and gganimate, I had to find a a creative way to combine the data sets - 8 cities over 12 days produced 96 unique json files of tweets. It was actually very easy. I first built a dataframe for each city that stored the city's coordinates (necessary to plot with ggmap), the number of tweets made on each day, and the associated sentiment. 
Then, combine the 8 of these with a row-bind call. 

```{R}
# Function to build the dataframe ggplot and gganimate will work with
build_df <- function(){
  Albany <- cbind( x = rep(loc_Albany[1], duration), y = rep(loc_Albany[2], duration),
                   size_Albany, as.factor(dates), sent_Albany)
  Col <- cbind( x = rep(loc_Col[1], duration), y =rep(loc_Col[2], duration), 
                size_Col, as.factor(dates), sent_Col)
  Dot <- cbind( x = rep(loc_Dot[1], duration), y =rep(loc_Dot[2], duration),
                size_Dot, as.factor(dates), sent_Dot)
  MB <- cbind( x = rep(loc_MB[1], duration), y =rep(loc_MB[2], duration), 
               size_MB, as.factor(dates), sent_MB)
  Mont <- cbind( x = rep(loc_M[1], duration),y = rep(loc_M[2], duration), 
                 size_Mont, as.factor(dates), sent_Mont)
  PC <- cbind(  x =rep(loc_PC[1], duration),y = rep(loc_PC[2], duration), 
                size_PC, as.factor(dates), sent_PC)
  Pen <- cbind( x = rep(loc_Pen[1], duration),y = rep(loc_Pen[2], duration), 
                size_Pen, as.factor(dates), sent_Pen)
  Tal <- cbind( x = rep(loc_Tal[1], duration), y =rep(loc_Tal[2], duration), 
                size_Tal, as.factor(dates), sent_Tal)
  return(as.data.frame(rbind(Albany, Col, Dot, MB, Mont, PC, Pen, Tal)))
}
```
The trick was to include "dates" for each city's individual dataframe. This was important because it enables frame-by-frame annimation through gganimate.

### Animating the data

To start the plotting and animation process you need to make a ggmap call to get a map of Southeast US. 
```{r}
michael_map <- get_map(location = c(-90, 25.7617, -76.2, 33.8), source = "stamen", maptype = "terrain-background")
```
Then I plotted an animation displaying the *quantity* of tweets.

```{r}
size_plot <- ggmap(michael_map) + 
  geom_point(aes(x = x_c, y = y_c, size = sizes), alpha = 0.5, data = full_df) +
  geom_text(aes(x = x_c, y = y_c, label=city_labels), hjust=-0.2, vjust=0, data = full_df) +
  transition_states(full_df$date, transition_length = 1, state_length = 12) +
  labs(title = 'date: 10-{closest_state}-2018', size = "Num Tweets")
```

![](https://i.imgur.com/4VGqnmZ.gif)

Then an animation displaying the *sentiment* of tweets.
```{r}
sent_plot <- ggmap(michael_map) + 
  geom_point(aes(x = x_c, y = y_c, size = 5, colour = avg_sent ), alpha = 0.5, data = full_df) +
  geom_text(aes(x = x_c, y = y_c, label=city_labels), hjust=-0.2, vjust=0, data = full_df) +
  transition_states(full_df$date, transition_length = 1, state_length = 12) +
  labs(title = 'date: 10-{closest_state}-2018') +
  scale_colour_gradient(low = "violet", high = "violetred4")
```
![](https://i.imgur.com/mCc7CP9.gif)

Then an animation displaying the both the quantity and sentiment of tweets. This produced a less desirable graphic, but it can be done using this code. 

```{r}
full_plot <- ggmap(michael_map) + 
  geom_point(aes(x = x_c, y = y_c, size = sizes, colour = avg_sent ), alpha = 0.5, data = full_df) +
  geom_text(aes(x = x_c, y = y_c, label=city_labels), hjust=-0.2, vjust=0, data = full_df) +
  transition_states(full_df$date, transition_length = 1, state_length = 12) +
  labs(title = 'date: 10-{closest_state}-2018', size = "Num Tweets") +
  scale_colour_gradient(low = "violet", high = "violetred4")
```
![](https://i.imgur.com/aCK1wDJ.gif)


