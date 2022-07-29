---
layout: post
title:  "Ted Talks - ML- Predicting Performing Videos"
description:  "Fourth article in series about Ted Talks focuses on predicting performing videos."
date:   2022-04-05
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, ml, logistic regression, simple decision tree, random forest, XG Boost]
---

TED Talks is a fascinating source of content. Kaggle offers a dataset on TED Talks posted on the website. As a TED Talk fan, I wanted to understand the type of resources available.

This is the **fourth** article in a series of a larger project that includes several data science techniques, including: scrapping a website, data wrangling, data classification techniques (PCA and clustering) and Machine Learning techniques. Here are the links to all the articles in this series: 

* [1 - Web Scrapping](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-1-web-scraping.html)
* [2 - Preprocessing](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-2-Preprocessing.html)
* [3 - PCA and Clustering](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-3-PCA-and-Clustering.html)
* [4 - Predicting Performing Models](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-4-ML-Predicting-Performing-Videos.html)
* [5 - Recommendation Engine](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-5-ML-Recommendation-Engine.html)



To learn more about the code and the project, please visit the [code library](https://github.com/aaas24/code_library/tree/main/ted_talks)



<!--more-->


### Predicting model for if a video will perform well

The goal is to predict a "good performance" for a given video, when we are defining 'good performance' as at least 75% percentile. 

We run 4 different models:

<div align="center">

|Model|Y_Prediction|
|--|--|
|Logistic Regression|0.61|
|Simple Tree|0.61|
|Random Forest|0.67|
|X-Boost|0.67|

</div>

Based on the Best Performing Models "XG-Boost", the key features are: 


- Date related variables, where we can see that for well performing videos: 

* there were more videos performing better with 5 min duration. Underperformed videos tended to last longer
* Well performing videos tended to be released during spring


- Keywords related to: 

* Personal Growth: personality, goals, motivation, collaboration, communication, humanity, self, performance, creativity
* Work: business, work-life balance , productivity, 
* Global issues: culture, politics, climate change, planets, gender, virus
* Other topics: music, sports, philosophy, art, health


#### Models

##### Preparing the data
To be able to utilize the models below, the data must be clean of NaNs, numerical, balanced, standardized and split into training, test and validation. In this section we show the steps in code we utilized to achieve the state used for the models. 

Note: For readability purposes we show the output of Jupyter notebook staring with ">"



``` python
        #verifying no NAN in data feeding model
        df[df.likes.isnull()==True]
        >  0 rows × 363 columns 

        #define target

        #we define TARGET a well performing video if it is above 75% percentile. So the model should predict if a video will#perform above 75% percentile
        threshold= np.percentile(df.likes, 75)

        #create target column
        df['target']=[1 if x>threshold else 0 for x in df.likes]

        #drop multicolinearity columns
        data=df.drop(['likes', 'views'], axis=1)

        #drop text columns
        data=data.drop(['author', 'title', 'description_1', 'description_2', 'keywords2'], axis=1)

        #Balance data
        data.target.value_counts()
        > 0    4098
        1    1342
        Name: target, dtype: int64

        positive_labels = data[data.target==1]
        num_positive_labels = positive_labels.shape[0]
        num_positive_labels
        >1342

        negative_labels = data[data.target==0].sample(num_positive_labels)
        negative_labels.shape
        >(1342, 357)

        balanced_data =  positive_labels.append(negative_labels)
        balanced_data.target.value_counts()
        >1    1342
        0    1342
        Name: target, dtype: int64

        ## Splitting data into test splits
        y = balanced_data.pop('target')
        X = balanced_data

        X_train, X_valid, y_train, y_valid = train_test_split(X, y, test_size = 0.3)
        X_valid, X_test, y_valid, y_test = train_test_split(X_valid, y_valid, test_size = 0.33)
```



##### Logistic Regression


``` python
        # fit a model
        clf = LogisticRegression(penalty='l2').fit(X_train, y_train)
        # predict probabilities
        pred_reg = clf.predict_proba(X_test)[:, 1]
        
```


##### Decision Tree


``` python
        from sklearn.tree import DecisionTreeClassifier
        dt_model = DecisionTreeClassifier(max_depth=10)
        dt_model = dt_model.fit(X_train,y_train)
        pred_dt = dt_model.predict_proba(X_valid)[:, 1]
```


##### Random Forest


``` python
        from sklearn.ensemble import RandomForestClassifier
        rf_model = RandomForestClassifier()
        rf_model = rf_model.fit(X_train, y_train)
        pred_rf = rf_model.predict_proba(X_valid)[:, 1]
        
```


##### XGBoost


``` python
        #code to fix error taken from: https://stackoverflow.com/questions/43579180/feature-names-must-be-unique-xgboost
        X_train = X_train.loc[:,~X_train.columns.duplicated()]
        X_valid = X_valid.loc[:,~X_valid.columns.duplicated()]

        from xgboost import XGBClassifier
        xgb_model = XGBClassifier()
        xgb_model = xgb_model.fit(X_train, y_train)
        pred_xgb = xgb_model.predict_proba(X_valid)[:, 1]
        
```


##### Comparing Models



``` python
        def create_roc_plot(name, predictions):
        if name == 'Logistic':
                auc = roc_auc_score(y_test, predictions).round(2)
                fpr, tpr, _ = roc_curve(y_test, predictions)
        else: 
                auc = roc_auc_score(y_valid, predictions).round(2)
                fpr, tpr, _ = roc_curve(y_valid, predictions)

        plt.figure(figsize=(5, 4))
        plt.plot([0, 1], [0, 1], linestyle='--')  # plot horizontal line 
        plt.plot(fpr, tpr, label='{} AUC = {}'.format(name, auc)) # plot the roc curve for the model
        plt.xlabel('FPR')
        plt.ylabel('TPR')
        plt.legend(loc='lower right')  # show the legend
        plt.show() # show the plot
        
        return None
        create_roc_plot('Logistic', pred_reg)
        create_roc_plot('Simple Decision Tree', pred_dt)
        create_roc_plot('Random Forest', pred_rf)
        create_roc_plot('XGBoost', pred_xgb)
```

<div align="center">

|Logistic Regression|Simple Decision Tree|
|---------|------|
|<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_model_1.png" alt="Linear Regression" width="100%"> | <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_model_2.png" alt="Simple Tree" width="100%"> |

|Random Forest|XGBoost|
|---------|------|
|<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_model_3.png" alt="Random Forest" width="100%"> | <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_model_4.png" alt="XGBoost" width="100%"> |
</div>

Given that Random Forest & XGBoost tends to outperform the other models, let's see the Feature importance on these two models: 



``` python
        #XGBoost - All Features to observe the curve
        from xgboost import plot_importance
        plot_importance(xgb_model)
        plt.show()   
```




``` python
        #XGBoost - Top Features
        df_graph=(pd.Series(xgb_model.feature_importances_, index=X.columns.values)
        .sort_values(ascending=False)
        .iloc[:40]
        .sort_values(ascending=True)
        .plot.barh()
        )

        #improving labels
        ax.set_xlabel('Importance')
        ax.set_ylabel('Features ')


        #styling grid, leyend and title
        plt.title('Feature Importance', ha='center', fontsize='x-large')
        ax.set_facecolor("white")
        plt.grid(axis='y', color='black', alpha=.2)
```


|Total Features|Top Features|
|---------|------|
|<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc1.png" alt="" width="100%"> | <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc2.png" alt="" width="100%"> |



``` python
        #Random Forest  - All Features to observe the curve
        from sklearn.inspection import permutation_importance
        rf_model.feature_importances_
        plt.barh(X.columns.values, rf_model.feature_importances_)     
```


``` python
        #Random Forest  - Top Features
        (pd.Series(rf_model.feature_importances_, index=X.columns.values)
        .sort_values(ascending=False)
        .iloc[:10]
        .plot.barh()
        )
        
```


|Total Features|Top Features|
|---------|------|
|<img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc3.png" alt="" width="100%"> | <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc4.png" alt="" width="100%"> |



Based on these features, we can identify some keywords and variables. 

1- Regarding Variables:

        Understanding date_recorded_year &  date_released_year



``` python
        df2=pd.concat([X,y], axis=1)
        df_graph=df2.loc[:,['date_released_year', 'target']]
        dfgraph_y=df_graph[df_graph.target==1]
        dfgraph_n=df_graph[df_graph.target==0]



        #improving graph
        fig, ax = plt.subplots(nrows=1, ncols=1, figsize=(13,5), sharex=True)

        # #plotting first histogram
        ax=(dfgraph_y.groupby(['date_released_year'])
        .agg({'target':['sum']})
        .plot( kind = 'bar',alpha=0.6, ax=ax,) 
        )
        # #plotting second histogram
        ax=(dfgraph_n.groupby(['date_released_year'])
        .agg({'target':['count']})
        .plot( kind = 'bar',alpha=0.5, ax=ax, color='#76725e') 
        )

        # # #improving labels
        ax.set_xlabel('')
        ax.set_ylabel('Count Videos ')

        #styling grid, leyend and title
        plt.title('Released Year by Performing and Not Performing Videos', ha='center', fontsize='xx-large')
        plt.legend(["Performing", "Not Performing"], loc='upper center',ncol=2, bbox_to_anchor=(0.5, 1.1), borderaxespad=2.6, facecolor="white")
        ax.set_facecolor("white")
        plt.grid(axis='y', color='black', alpha=.2)
        
```

<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc5.png" alt="Data Exploration" width="600">
</p>

        Understanding date_released_month



``` python
        df2=pd.concat([X,y], axis=1)
        df_graph=df2.loc[:,['date_released_month', 'target']]
        dfgraph_y=df_graph[df_graph.target==1]
        dfgraph_n=df_graph[df_graph.target==0]



        #improving graph
        fig, ax = plt.subplots(nrows=1, ncols=1, figsize=(13,5), sharex=True)

        # #plotting first histogram
        ax=(dfgraph_y.groupby(['date_released_month'])
        .agg({'target':['sum']})
        .plot( kind = 'bar',alpha=0.6, ax=ax,) 
        )
        # #plotting second histogram
        ax=(dfgraph_n.groupby(['date_released_month'])
        .agg({'target':['count']})
        .plot( kind = 'bar',alpha=0.5, ax=ax, color='#76725e') 
        )

        # # #improving labels

        ax.set_xticks(ticks=range(0,12,1))  
        ax.set_xticklabels(['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct', 'Nov', 'Dec'])
        ax.set_xlabel('')
        ax.set_ylabel('Count Videos ')

        #styling grid, leyend and title
        plt.title('Released Month by Performing and Not Performing Videos', ha='center', fontsize='xx-large')
        plt.legend(["Performing", "Not Performing"], loc='upper center',ncol=2, bbox_to_anchor=(0.5, 1.1), borderaxespad=2.6, facecolor="white")
        ax.set_facecolor("white")
        plt.grid(axis='y', color='black', alpha=.2)
```

<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/ted_talks/images/ted_talks_models_fc6.png" alt="Data Exploration" width="70%">
</p>


### NEXT STEP

Please read the next article of this series on [ML - Recommendation Engine](https://aaas24.github.io/coding/2022/04/05/Ted-Talks-5-ML-Recommendation-Engine.html)
