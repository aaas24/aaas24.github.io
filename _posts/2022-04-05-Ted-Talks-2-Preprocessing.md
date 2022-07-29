---
layout: post
title:  "Ted Talks - Preprocessing"
description:  "Second article in series about Ted Talks that focuses on preprocessing data using python."
date:   2022-04-05
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, dataengineering, dummies]
---

TED Talks is a fascinating source of content. Kaggle offers a dataset on TED Talks posted on the website. As a TED Talk fan, I wanted to understand the type of resources available.

This is the **second** article in a series of a larger project that includes several data science techniques, including: scrapping a website, data wrangling, data classification techniques (PCA and clustering) and Machine Learning techniques. To learn more about the code and the project, please visit the [code library](https://github.com/aaas24/code_library/tree/main/ted_talks)



<!--more-->


### INITIAL DATASET

With a more robust dataset obtained in the [previous article](https://aaas24.github.io/coding/2022/04/05/2022-04-05-Ted-Talks-1-web-scraping.html), we will proceed to clean and generate two base DataFrame to be used in the following steps. If you wish to see the entire code, you can visit [this Jupyter notebook.](https://github.com/aaas24/code_library/blob/main/ted_talks/2_preprocessing/ted_talks_preprocessing.ipynb)


* Create Main DataFrame


 With final data joined dataset, we proceed to apply data processing steps, including: 

        - Dropped duplicated or useless columns: 'description2', 'Unnamed: 0', 'link', 'author_y', 'title','url', 'title_1'

        - Converted to datetime date_recorded and date_released columns, extracting YEAR, MONTH, HOUR & MINUTES into numerical columns to be able to feed it into Machine Learning models later

        - Remove lists from keywords column in order to later applied get_dummies() panda function. 
        This step was particularly tricky for me. The original column looks like this: 


```shell
        0      ['TED', ' talks', ' climate change', ' global ...]
        1      ['TED', ' talks', ' education', ' women', ' TE...]
        2      ['TED', ' talks', ' business', ' leadership', ...]
        3      ['TED', ' talks', ' climate change', ' polluti...]
        4      ['TED', ' talks', ' climate change', ' Countdo...]
```

The desired structure looks more like:


```shell
        0       'ted', 'talks', 'climatechange', 'globalissues...
        1       'ted', 'talks', 'education', 'women', 'ted-ed'...
        2       'ted', 'talks', 'business', 'leadership', 'wor...
        3       'ted', 'talks', 'climatechange', 'pollution', ...
        4       'ted', 'talks', 'climatechange', 'countdown', ...
```



In order to achieve this a For loop was used to remove the list, apply lower case and remove the extra spaces in between the ''  that some words had. 

* Create dummy file

 Applied the get_dummies function on the new keywords2 column, which allows to apply a sum() function in a group by in order to obtain a simple count on the number of times a keyword was used across the dataset. It's also important to note that all talks have the keyword 'ted' attached to them, which provides no value overall and therefore was removed during this step. 


``` python
         def create_dummies_file(df):
                #converting keywords into dummy columns
                df2=df.keywords2.str.get_dummies(',')

                #joining with df
                df=pd.concat([df,df2], axis=1)
                
                #removing 'ted' column
                column_to_drop=df.columns[362]
                df2=df.drop(column_to_drop, axis=1)

                #counting dummies and creating file to rename categories
                dummy_columns=pd.Series(np.arange(15,349,1))[1:]
                df_dummies=df.iloc[:,dummy_columns].sum().reset_index()
                df_dummies.columns=['keyword', 'sum']
                df2=df_dummies.copy()
                (df2
                .groupby(['keyword'])
                .agg({'sum':'sum'})
                )
                df2=df2.sort_values(by='sum', ascending=False)
                cwd=os.getcwd()
                df2.to_csv(cwd+'/keywords.csv')
                return df
```


The result of this function is: 
1) A dataFrame 'df' with the main data structure to be later processed. 


||author|	views|	likes|	title|	description_1|	duration_seg|	description_2|	date_recorded_year|	date_recorded_month|	date_released_year|	...	|'water'|	'weather'|	'windenergy'|	'women'|	'womeninbusiness'|	'work'|	'work-lifebalance'|	'writing'|	'youth'|	'ted'|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
0|	Ozawa Bineshi Albert|	404000|	12000|	Climate action needs new frontline leadership|	"We can't rely on those who created climate ch...|	834|	"We can't rely on those who created climate ch...|	2021|	12|	2022|	...|	0|	0|	0|	0|	0|	0|	0|	0|	0|	1|
Dataframe named: df 



2)  A 'keywords.csv' file containing a list of keywords and number of times it appears in the data, example: 


| keyword |sum   |
|---------|------|
|'talks'  | 5440  |
| 'science'  | 1266  |
| technology'  |1200   |
|||


                
* Create Dummy DataFrame

There are 334 keywords, which we will call sub_categories of content. In order to analyze them, we need to consolidate them into bigger buckets. For that reason, I created a one:many relationship between new categories:sub_categories, in order to group the latter into bigger buckets of content that we can understand better. This categorization was done separately and stored in a file called 'keywords_categories.csv'

Utilized thus matrix, I transformed the  'keywords.csv' into a dataframe with the following structure: 



 |       	|sub_category|	num_talks|	category|	        likes|	views|
 |--|--|--|--|--|--|
 |       0	|'3dprinting'|	9|	        technology|	        201574|	6655100|
 |       1|	'activism'|	352|	        values & emotions  |     21752759| 714057797|
 |       2|	'addiction'|	20|	        health	  |              1870500|	60982000|
 |       3|	'africa'|	197|	        global	  |              9097799|	299541000|
 |       4|	'aging'	 |       93|	        society	  |              8152092|	269034199|
 Dataframe named: df_dummies


### NEXT STEP

Please read the next article of this series on [PCA and Clustering](https://aaas24.github.io/coding/2022/04/05/2022-04-05-Ted-Talks-3-PCA-and-Clustering.html)
