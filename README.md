# Portfolio of Projects

## [Data modeling for Wishi with dbt and Looker](https://github.com/danbratton/wishi-dbt/blob/main/wishi/WALKTHROUGH.md)
I built and maitain a wishlist application called [Wishi](http://www.getwishi.com). On Wishi users create an accounts, create wishlists, add items to wishilsts, and share wishlists with others. Along this user journey, there are several events that I want to track so that I can measure important metrics like Monthly Active Users, Activation Rate, Retention Rate, and more.

I designed a data model to transform raw event data into analytics-ready data. Raw event data is generated on the **Django** backend and stored in a **Postgres** database. From there, **dbt** is used to build a fact table for events and several dimension tables. I visualized the data with **Looker Studio** in [this public dashboard](https://lookerstudio.google.com/reporting/b5c35fcb-9d47-48e8-af6f-e75dd61164da).

The **dbt** codebase I use is [here](https://github.com/danbratton/wishi-dbt/tree/main/wishi). 

This [walkthrough](https://github.com/danbratton/wishi-dbt/blob/main/wishi/WALKTHROUGH.md) explains the data modeling decisions I made. 

## [Wishi - The simple, sharable wishlist](https://www.getwishi.com)

### Motivation
After running my family's annual secret santa, I was annoyed that Amazon no longer allowed non-Amazon items on wish lists. This was how we'd shared lists as a family for years!

I tested a few other alternatives, but many were too complicated or difficult to use for our simple need: adding items to a list and sharing it.

Here's what ended up happening. Members of my family used a hodpodge of options from using a Google Docs to from texting their list to the group chat 🤦‍♂️. It was a mess. So much so that in the confusion I ended up not getting a gift at all while someone else got 2 gifts!

I set out to solve this myself. Thus, [Wishi](https://www.getwishi.com) was born. 

### Tech Stack
I decided to build Wishi using the Django framework because I am most familiar with Python, which Django uses. I also figured if it's good enough for Instagram it should be good enough for me.

I also decided to use Postgres for my database. I've used it before, it pairs well with Django, and will perform good enough for my needs.

I used bootstrap to build the front end components.

## [To Dodge or Not to Dodge?: Predicting League of Legends Match Outcomes](https://github.com/danbratton/portfolio/blob/master/To%20Dodge%20or%20Not%20to%20Dodge.ipynb)
The purpose of this study is to develop a model to predict the likelihood of a team winning a League of Legends match only using information available prior to the start of a match. The model would provide a recommendation to players to "dodge" a League of Legends match and avoid the consequences of defeat when the probability of victory is low enough such that the nominal LP loss from dodging the match is more beneficial to the player than attempting to win the match. This recommendation tool would increase player enjoyment of the game while working to optimize a player's LP gain per unit of time played.

## [NYU Course Selection Tool](https://github.com/danbratton/Course-Evaluations/blob/master/NYU%20Stern%20Course%20Evaluation%20-%20Data%20Analysis.ipynb)
Oftentimes we need to make decisions using data from a wide variety of sources.  When done manually, this process can be error prone, time consuming, and frustrating.  This was exactly the case when I was reviewing which classes to take at NYU Stern while pursuing my MBA.  I had a list of available classes for the upcoming semester, but the course evaluations (reviews by students who took the class previously) were in a completely different place.  I combined the data by scraping both pages with Selenium, cleaned and combined the data with Pandas, and then published the resulting dataframe via a [Shiny web app](https://danbratton.shinyapps.io/XCourses/).
