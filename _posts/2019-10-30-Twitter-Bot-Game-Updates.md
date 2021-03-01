---
title: "Twitter Bot for MLB Game Updates"
date: 2019-10-30
excerpt: "Twitter Bot that tweets out customer game updates. Uses: Tweepy, Selenium, Beautiful Soup."
---


Using ESPN and Twitter to Create Custom MLB Updates
The ESPN and MLB iPhone applications do a good job at providing general game updates for live baseball games. For instance, the MLB At-Bat app has a setting that allows push-notifications for updates in the game's score. Their customization is limited, however, so I wrote a script that pulls live game data off the ESPN MLB Scoreboard and tweets updates for specific players during a game. Then I follow the Twitter account linked to the script and turn on push-notifications so that I can get the updates straight to my phone.



I began by connecting to the ESPN scoreboard and the Twitter account for which I have "Standard API" access - which you can get too .
The full code is available on my github page . Here are the basics for connecting to Twitter and ESPN's MLB scoreboard
import tweepy 
from tweepy import API
from tweepy import Cursor
from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream

from selenium import webdriver
from bs4 import BeautifulSoup as soup

def connect_to_twitter():
    auth = OAuthHandler(creds.CONSUMER_KEY, creds.CONSUMER_SECRET)
    auth.set_access_token(creds.ACCESS_TOKEN, creds.ACCESS_TOKEN_SECRET)
    api = API(auth)
    return api
    
def connect_to_scoreboard(url):
	driver = webdriver.Chrome()
	driver.get(url)
	return driver

url = "http://www.espn.com/mlb/scoreboard"
twitter_account = connect_to_twitter()
web_driver = connect_to_scoreboard(url)
For web scraping ESPN, I used Selenium and BeautifulSoup . For connecting to twitter's API, I used Tweepy .
I found my web driver (Google Chrome) to be loading the webpage too slowly after live updates to the site. This resulted in the incorrect html being scraped which obviously lead to various errors. To work around this, while also reducing the number of requests made to the server hosting ESPN's site, I made use of time.sleep() to pause the script between html requests.
The structure of my script is as follows, where "status" is a string whose value is scraped from the ESPN scoreboard and varries between these variants :
"Final" = Game is completed.
"End" = Game is inbetween innings.
"Top" = Game is in the top half of an inning.
"Bottom" = Gam eis in the bottom half of an inning.
status = None
while(status.find("Final") < 0):
   innerHTML = driver.execute_script("return document.body.innerHTML")
   page_soup = soup(innerHTML, "html.parser")
   
   #parse through data
   	#update "status" string
   
   time.sleep(60)
The script begins when run on your device with command line arguments formatted as follows:
Team name, num batters, num pitchers, list of batters, list of pitchers
command line examples:
python script.py Brewers 1 1 Braun Knebel
python script.py Mets 3 2 Conforto McNeil Rosario deGrom Lugo
python script.py Yankees 1 2 Judge Stanton Severino
The reason the user must identify which players are position players and which are pitchers is because the scoreboards and boxscores on ESPN's site are unique for each group of player. This code could be re-written such that the user simply inputs player names, however, this would require more tedious web scraping and result in a slower script.
After scraping the data, the script will use the provided Twitter account to tweet 3 unique updates:
Whether the team identified by the user is pitching or batting. This was useful to me as a fan of the NY Mets who have great starting pitching and bad offense, so I was more likely to want to watch the game while the Mets were pitching.
(Work in Progress) Whether a player identified by the user is currently pitching or batting. The MLB season ended before I was able to complete this and so it will be shelved until spring training.

Final stat lines for the desire players.
Lets look at the first example. The user is requesting in-game updates for position player (Ryan) Braun and pitcher (Corey) Knebel who play for the Brewers.
Player ABs runs hits rbi walks
Braun 4 0 1 1 0
Player Innings Hits Rs ERs BBs Ks PC-st
Knebel 1.0 0 0 0 0 1 10-7
