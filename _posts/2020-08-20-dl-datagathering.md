---
layout: post
title:  "Quantrack Python crash course."
date:   2020-04-26 18:05:55 +0300
image:  beginner.jpeg
tags:   [Deep learning,DarKnet, Scrapping]
---


## Context 

This is the third article of our series : Deep Learning and Cybersecurity and we will talk about one of the most crucial part at the core of almost any machine learning project : Data Gathering. Let's say you work for an investigation unit and you are flooded by dozens, centuries, if not thousands of lists of links of hidden services, each of them being a potential place where to look for in order to find interesting pieces of work. 

Unfortunately, DarkNet is not only about escaping censorship and free-speech assembly. Massive weapons sellers and pedopornographic websites are among the types of service you can find on hidden services and cybercriminality specialists are trying to tackle this issue. As of now, specialized unit can only mitigate a tiny portion of illegal activities related to pedopornography or weapon selling for example. What if it would be possible to detect hidden services related to illegal domain automatically ? Without having to visit them manually, we can imagine the following approach resumed in the following steps :


1. Gather a list of labeled hidden services to train our algorithm, this part is actually very easy as there as thousands of darknet archives, in our next article we will use [Gwern](https://www.gwern.net/DNM-archives) archives 

2. Train our algorithm using NLP classifiers to predict if a hidden service is related to illegal activity

3. Automatically scrap hiddden services and deploy our algorithm to verify if it is related to some illegal activity 

In our next article, we will develop more about the first part. Now let's imagine we already have our trained algorithm and want to test it on hidden services, we need an efficient scraping engine that takes hidden services links as an input and gives us their text contents as an output. We will later use these output to evaluate our prediction algorithm and make sure these website are not related to any illegal activities. If our classifer is powerful enough, we will be ableto classify and filter hidden services, thus gaining a lot of time in our investigation. 

## Scrap hidden services using Python, Stem package,  Tor, and Privoxy

We will use Python, Stem Python package, Tor, and Privoxy. 
Stem is a Python controller library that allows applications to interact with Tor and Privoxy is a non-caching web proxy. 
Make sure you have Python installed as this article does not cover this part. 

### Installation 

Before you run the scrapper make sure the following things are done properly:

* Run tor service
`sudo service tor start`
or `brew services tor start` for MacOS users


* Set a password for tor
`tor --hash-password "my_password" ` and do not forget to include it in your python scripts

* Modify value in `scrapper.py`

* Go to /etc/tor/torrc and uncomment - _**ControlPort 9051**_ , you may consider accessing torrc config file using `sudo nano torrc` to be able to save it

### Privoxy setup

* Install privoxy
`sudo apt-get install privoxy`
or `brew install privoxy` for MacOS users


* Change your privoxy config to get access to Tor Network  
`cd /` to go to root directory then
`cd /etc/privoxy`  

* Open your config file  
`nano config`

* Uncomment following line  
`forward-socks5 / localhost:9050`

* Restart prixoxy to load changes
`sudo /etc/init.d/privoxy restart` or
`brew services restart privoxy`for MacOS users

### Deployment

* Clone the git directeory, create your virtualenv and install requirements by running the following commands :

```
git clone https://github.com/jct94/qt_blog_onion-scraper.git
pip install virtualenv    
virtualenv yourenv   
source yourenv/bin/activate    
pip install -r requirements.txt    
```

Now you are ready to start scraping, you can add hidden services in `onions.txt` file

* Run the scrapper using python3

```
python3 scrapper.py
```

You should get the following screen :

![](screen1.png)

Choose option number 2, and press enter, your scrapping engine is now running and you should have the following output :

![](screen2.png)

Note that for the purpose of the article, ´onions.txt´ file only contains one link.

Our scraping engine saves the html response in the `/output` folder. In our next article, we will train a NLP algorithm on DarkNet archives and predict if our scraped hidden services are related to illegal or criminal activity.
