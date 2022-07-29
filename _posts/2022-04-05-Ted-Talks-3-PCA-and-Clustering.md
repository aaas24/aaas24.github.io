---
layout: post
title:  "Ted Talks - PCA and Clustering"
description:  "Third article in series about Ted Talk focusing on Principal Component Analysis and clustering."
date:   2022-04-05
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, ml, clustering, pca]
---

TED Talks is a fascinating source of content. Kaggle offers a dataset on TED Talks posted on the website. As a TED Talk fan, I wanted to understand the type of resources available.

This is the **third** article in a series of a larger project that includes several data science techniques, including: scrapping a website, data wrangling, data classification techniques (PCA and clustering) and Machine Learning techniques. Here are the links to all the articles in this series: 

* [1 - Web Scrapping](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-1-web-scraping.html)
* [2 - Preprocessing](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-2-Preprocessing.html)
* [3 - PCA and Clustering](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-3-PCA-and-Clustering.html)
* [4 - Predicting Performing Models](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-4-ML-Predicting-Performing-Videos.html)
* [5 - Recommendation Engine](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-5-ML-Recommendation-Engine.html)



To learn more about the code and the project, please visit the [code library](https://github.com/aaas24/code_library/tree/main/ted_talks)



<!--more-->


### Principal Component Analysis (PCA)

With the two dataframes created on [previous article](https://aaas24.github.io/coding/2022/04/05/2022-04-05-Ted-Talks-2-Preprocessing.html), we proceed to apply data science techniques to understand the dataset and explore key variables.


Even through the dataset is intuitively simple to understand, I wanted to practice doing a PCA analysis, which is normally reserved for models with many variables. To do this I followed these steps: 

- Prepared the dataFrame to use by dropping all categorical columns & dummy columns from `df` and standardized the data using the sklearn.preprocessing library called 'StandardScaler'
- Used the decomposition.PA from the Sklearn library to create dataframe with components columns: 


``` python
        pca = decomposition.PCA()
        pca_X = pd.DataFrame(pca.fit_transform(X_std), columns=[f'PC{i+1}' for i in range(len(X.columns))]) 
```


- From `pca.explained_variance_ratio_`, we can see that the first 2 components contain almost 50% of the data"



``` python
        print(pca.explained_variance_ratio_)

        array([2.61155893e-01, 2.09808747e-01, 1.27262369e-01, 1.19521228e-01,
        1.01198399e-01, 9.06161518e-02, 8.05875806e-02, 9.81195975e-03,
        3.76713129e-05])
```


- Visualizing the columns in the first two principal components



``` python
        (pd.DataFrame(pca.components_, columns=X.columns)
        .iloc[:2]
        .plot.bar()
        .legend(bbox_to_anchor=(1,1)))
```


<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_pca_1.png" alt="PC1 & PC2" width="600">
</p>

### CLUSTERING 

#### 1) Determining the number of clusters to use

By utilizing three separate methods of visualization, we arrived at the conclusion that 4 clusters would be the best solution for this data. 

The first method is a linear representation, nicknamed 'the elbow', as you try to search for a break on the lines. This method suggests two potential cuts, one on1 or another at three but does not provide a clear answer. 

The second method is the Silouhettes, where the shapes of the clusters do show a reasonable grouping where samples are in 4 clusters. In this case we se that the 'bellies' of the clusters are not as pronounced as when using 2 & 3 clusters, without a significant lost in the average score. 

The third method using a dendrogram is even more clear: there is a significant increase in clusters after the 4 column. This perhaps is the clearest confirmation of our assumption 


<div align="center">

|Elbow Method|Silouettes Method|Dendrogram Method|
|---------|------|-----|
|<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_clustering_1.png" alt="PC1 & PC2" width="100%"> | <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_clustering_2.png" alt="PC1 & PC2" width="100%"> |<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_clustering_3.png" alt="PC1 & PC2" width="100%">|
|||
</div>

### 2) Assigning the clusters


``` python
        k9 = cluster.KMeans(n_clusters=4, random_state=42)
        k9.fit(X_std)
        labels = k9.predict(X_std)
        X.assign(label=labels)
```


### 3) Exploring clusters

- Determine how many values are on each cluster



``` python
        print(pd.Series(labels).value_counts().sort_index())

        0    1628
        1    2063
        2    1684
        3      65
        dtype: int64
```


- Describe a label in a cluster



``` python
        (X.assign(label=labels)
        .query('label == 0')
        .describe()
        )
```


- Visualize the means of each variable per cluster


``` python
        (X.assign(label=labels)
        .groupby('label')
        .mean()
        .T
        .style.background_gradient(cmap='RdBu', axis=1)
        )  
```



<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_clustering_4.png" alt="Data Exploration" width="600">
</p>


### NEXT STEP

Please read the next article of this series on [ML - Predicting Performing Videos](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-4-ML-Predicting-Performing-Videos.html)
