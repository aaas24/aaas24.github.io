---
layout: post
title:  "Useful Cleaning Data Functions"
description: "Useful functions in data cleaning" 
date:   2023-06-11
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Project Description

The intent of this post is to document common used functions when cleaning data with python. 

<!--more-->

## TABLE OF CONTENT


- [Add Missing Packages to Kernel](##Add-Missing-Packages-to-Kernel) <br>
- [Pretty print a list](##Pretty-Print-Lists) <br>
- [Save State of Dataframe](##Save-State-of_Dataframe)<br>
- [Clean Column Names](##Clean-Column-Names)<br>
 
<br>
<br>
<br>

## Add Missing Packages to Kernel

```python
def add_package(package_name):
    '''
    function that takes name of python library, installs it in appropriate environment.

    :param query: string variable of the package name.
    :returns: output of installation.
    '''
    import sys
    !{sys.executable} -m pip install package_name
```
## Pretty Print Lists

```python
def pprint(lst): 
       '''
    function that a lists, sorts it and prints elements in separate rows to ease comprehension.

    :param query: list.
    :returns: elements of list per row.
    '''
    lst=sorted(lst)
    print(*lst, sep='\n')
```

## Save State of Dataframe

```python
def catchstate(df, var_name: str) -> 'pd.DataFrame':
    """
    Helper function that captures intermediate Dataframes mid-chain.
    In the global namespace, make a new variable called var_name and set it to dataframe. 
    This function is useful when chaining, through the use of 'pipe', in order to filter after applying changes to dataframe. Ex: [Gun Violence](https://projects.aaas24.io/portfolio/viz/us-gun-violence.html)
        data=(df
                .assign(year=df.date.dt.year, 
                        month=df.date.dt.month
                        )
                .pipe(catchstate, var_name="df2")
                #filtering outlier using the new variable 'year' assigned
                [(df2.sub_region!='Mountain')&(df2.year!=2017)]
                .groupby(by=['year','sub_region'])
                .agg(
                    tot_victims=pd.NamedAgg(column="tot_victims", aggfunc="sum"),
                    num_incidents=pd.NamedAgg(column="incident_id", aggfunc="count")
                ).reset_index()
            )
        print(data)


    :param query: DataFrame
    :returns: DataFrame stored in variable 
    """
    globals()[var_name] = df
    return df
```

## Clean Column Names

```python
def replace_column_names(my_list):
    '''
    function that replaces values in columns using dictionary provided below.

    :param query: List of DataFrame column names.
    :returns: List of columns names formatted .
    '''
    col_dict={
        'mailing_or_mail_box_address':'mailing_address'
        'what_is_your_mailing_address?':'mailing_address'
        'please_select_your_current_school':'current_school'
    }
    return [x if x not in col_dict else col_dict[x] for x in my_list]

def clean_column_names(df, pprint='False'):
    '''
    function to call on after DataFrame is created to format column names in lowercase 
    without spaces before querying.
    
    :param query: DataFrame
    :returns: DataFrame with columns formatted  
    '''
    import pandas as pd 

    columns=df.columns
    new_names=[]
    char_dict={' ':'_',
               " ":'_',
               ':':'', 
               '/n':''
              }
    for c in columns:
        new_names.append(c
            .lower()
            .translate(char_dict)
            .rstrip()
            .lstrip()
            .replace(' ','_')
          )
    names_transformed=replace_column_names(new_names)
    df.columns=names_transformed
    if pprint=='True':
        pprint(new_names)
    return df
    ```