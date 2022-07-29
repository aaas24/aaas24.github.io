---
layout: post
title:  "Ted Talks - Web Scrapping"
description:  "First article in series about Ted Talks"
date:   2022-04-05
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, dataengineering, web scrapping]
---

TED Talks is a fascinating source of content. Kaggle offers a dataset on TED Talks posted on the website. As a TED Talk fan, I wanted to understand the type of resources available.

This is the **first** article in a series of a larger project that includes several data science techniques, including: scrapping a website, data wrangling, data classification techniques (PCA and clustering) and Machine Learning techniques. Here are the links to all the articles in this series: 

* [1 - Web Scrapping](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-1-web-scraping.html)
* [2 - Preprocessing](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-2-Preprocessing.html)
* [3 - PCA and Clustering](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-3-PCA-and-Clustering.html)
* [4 - Predicting Performing Models](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-4-ML-Predicting-Performing-Videos.html)
* [5 - Recommendation Engine](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-5-ML-Recommendation-Engine.html)



To learn more about the code and the project, please visit the [code library](https://github.com/aaas24/code_library/tree/main/ted_talks)



<!--more-->


### INITIAL DATASET

Kaggle offers a dataset on TED Talks posted on the website. The data has been uploaded to a Dataframe.Looking at a brief set of the data: 

 <p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted-talk-1.png" alt="Data Exploration" width="80%">
</p>

From this general view, we can immediately ask some general questions, like:
- Which are the most viewed videos?
- Who are the most viewed authors?
- Is there a substantial difference between liked and viewed?. If so, which are the top videos on each?

However, some other interesting questions can be:

- Which content categories are most viewed?
- Which content categories are made available the most?
- Could the duration of the video affect the likeability of the videos?
- When was the video published? (Assuming that older videos can have more likes than newer)
- What type of event was this part of? Was it a Women, or a specific location?

To answer some of these questions, the current data set is insufficient. However, this information may be available on each of the video's website. This poses the opportunity to extract the data using a targeted web scrapping technique. 

### WEB SCRAPPING

The goal of this section is to obtain a more robust dataset than the one provided by Kaggle by scrapping the TED Talk's site. To view the entirety of the code used for this section, please visit [this link](https://github.com/aaas24/code_library/blob/main/ted_talks/3_code/code_1_scrape_data.py). 

It is important to note that throughout the process of understanding the HTML and partial tweaking of the code, I stored locally the initial HTML and/or utilize samples of 1-10 links at a time with flags in the code oto avoid disrupting the site's servers while developing the code. I believe this is the code any scrapper should approach a target. 

The first step is to understand what kind of HTML we would get back and identify where we could obtain the information to answer the questions above and generate new valuable insights. 

**Fetch an example**

Using one of the links provided in the Kaggle dataset obtained in Step 2, we scrape the HTML using the following code:

```python
        from urllib.request import urlopen
        from bs4 import BeautifulSoup

        html = urlopen ( "https://www.ted.com/talks/ozawa_bineshi_albert_climate_action_needs_new_frontline_leadership" ) 
        soup = BeautifulSoup ( html . read (), 'html.parser')
```

**Parse the desired content**

Our goal is to extract the names of the authors and their quotes, to later store them in a dictionary we can later query when we want to. 

Now we need to better analyzing the HTML tree using the prettify property. Using the property prettify (print(bsObj.prettify()), we can see [the HTML.](https://github.com/aaas24/aaas24.github.io/blob/master/assets/post_files/2022-03-21-Scraping-&-Analyzing-TED-Talks/example_html.xml)

In the HTML we can find the information we need is stored in a tag called "meta". 


```python
        meta=soup.find_all("meta")
```

When we study the tag we see that in the elements inside this tag we find the information we need in the following indices: 


|Position in the 'meta' tag|Data|
|---------|------|
|1|url|
|27|title style 1|
|28|title style 1|
|29|description style 1|
|30|description style 2|
|33|duration in seg|
|34|keywords|
|35|release date|
|37|author|


Through some data transformation in python we are able to clean the data an obtain a new dataframe we called `df_scrapped`. To see the data cleaning process, you can look at the [code](). 

Because we included the initial `link` column from `df_kaggle`, we can join both data frames using panda merge. 

```python
        df=df_scrapped.merge(df_kaggle, left_on='link', right_on='link')
```

The final joined data was stored in a file [here.]('https://github.com/aaas24/code_library/raw/main/ted_talks/1_raw_data/final_raw_data.csv')



### NEXT STEP

Please read the next article of this series on preprocessing the data, [here](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-2-Preprocessing.html)
