---
layout: post
title:  "Ted Talks - ML- Recommendation Engine"
description:  "Fifth article in series about Ted Talks focuses on building a recommendation engine."
date:   2022-04-05
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, ml, recommendation engine]
---

TED Talks is a fascinating source of content. Kaggle offers a dataset on TED Talks posted on the website. As a TED Talk fan, I wanted to understand the type of resources available.

This is the **last** article in a series of a larger project that includes several data science techniques, including: scrapping a website, data wrangling, data classification techniques (PCA and clustering) and Machine Learning techniques. Here are the links to all the articles in this series: 

* [1 - Web Scrapping](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-1-web-scraping.html)
* [2 - Preprocessing](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-2-Preprocessing.html)
* [3 - PCA and Clustering](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-3-PCA-and-Clustering.html)
* [4 - Predicting Performing Models](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-4-ML-Predicting-Performing-Videos.html)
* [5 - Recommendation Engine](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-5-ML-Recommendation-Engine.html)



To learn more about the code and the project, please visit the [code library](https://github.com/aaas24/code_library/tree/main/ted_talks)



<!--more-->


### Building a recommendation engine

##### Build Model

``` python
        #define variables to use in model
        review=df.description_1
        title=df.title

        #Define a TF-IDF Vectorizer Object. Remove all english stop words such as 'the', 'a'
        tfidf = TfidfVectorizer(stop_words='english')#max_features=5000

        #Construct the required TF-IDF matrix by fitting and transforming the data
        tfidf_matrix = tfidf.fit_transform(review)

        #Output the shape of tfidf_matrix
        tfidf_matrix.shape

        #create matrix
        cosine_sim = cosine_similarity(tfidf_matrix, tfidf_matrix)

        #extract indices
        indices = (pd.Series(df.index, index=title)
        .reset_index()
        .drop_duplicates(subset=['title'], keep='first')
        ).set_index('title')
                
        indices.columns=['index']
        indices=indices.squeeze()


        def get_recommendations(title, cosine_sim=cosine_sim):
                '''
                Function that returns ten indices of top talks based on model created above and a talk title passed
                '''
                # Get the index of the talk that matches the title
                idx = indices[title]

                #     Get the pairwsie similarity scores of all talks with that movie
                sim_scores = list(enumerate(cosine_sim[idx]))
                
                #     Sort the talk based on the similarity scores
                sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)

                #     Remove duplicates scores 
                sim_scores=pd.Series(v[0] for v in sim_scores).drop_duplicates()

                # Get the talk indices
                recommendations=(
                        df.title.iloc[sim_scores]
                        .drop_duplicates()
                        [1:11]
                        .reset_index()
                ).drop('index', axis=1)
                
                # Return the top 10 most similar values
                return recommendations
```


##### Test Models



``` python
        #selet talk
        talk_liked='Can machines read your emotions?'


        # change display option to be able to see ful title name
        pd.set_option('display.max_colwidth', None)
        get_recommendations(talk_liked)        
```


<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_rc1.png" alt="" width="40%">
</p>



``` python
        #compare results of recommendation engine
        df_graph=df.query('title.str.contains("machines", "emotions")', engine='python')
        df_graph[['author', 'title', 'likes']].drop_duplicates(subset='title').sort_values(by=['likes'], ascending=False)
        
```


<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_rc2.png" alt="" width="40%">
</p>


#### FINDINGS

The current model's outputs does not perform well against a simple df.query using keywords from the title. The model currently used is based on TF-IDF (term frequency and Inverse Document frequency) applied to the talk description. The model could be improved by adding other variables available like: keywords, likes & author.