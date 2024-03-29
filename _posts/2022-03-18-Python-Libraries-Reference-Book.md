---
layout: post
title:  "Python Libraries Reference Book"
description: "Basic functions related to installing, configuring and using some common Python tools & Libraries"
date:   2022-03-18
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Project Description

The intent of this post is to document basic functions related to installing, configuring and using a jupyter notebook. The content focuses on performing basic preprocessing commands to explore and analyze the data using python libraries such as pandas, pymysql and others. 

This file contains the notes related to the [Udemy Course - Python, SQL and Tableau Integration](https://www.udemy.com/course/python-sql-tableau-integrating-python-sql-and-tableau). The installation was done in a Proxmox VM running linux. 

### TABLE OF CONTENT

\
    - [Anaconda](#anaconda) <br>
    - [Jupyter Notebook](#jupyter-notebook) <br>
    - [Pandas](#pandas) <br>
    - [Numpy](#numpy) <br>
    - [PyMySQL](#pymysql) <br>


<!-- no toc -->



# Anaconda 

## Installing Conda

- Downloaded Anaconda 64-Bit (x86) Installer (581 MB) from anaconda.com using wget command
- Following the Anaconda [Documentation](https://docs.anaconda.com/anaconda/install/linux/), we use 'bash ~/Downloads/Anaconda3-2020.02-Linux-x86_64.sh', agree to the terms and set the file path
- Restarted bash with `source ~/.bashrc`

## Using Conda

- Created new environment py3-analytics `conda create -n py3-analytics python=3.6`
- Activated environment `conda activate py3-analytics`
- Installed packages described in environment.yml `conda install --name py3-analytics jupyter`
- Launched Jupyter but forced the ip to reflect the network assigned ip and no browser options. Example: `jupyter notebook --ip=123.123.123.123 --no-browser`
- In order to use Conda functions from a bash script, add the following lines to your .bashrc (Source: [Conda Issue 7980](https://github.com/conda/conda/issues/7980))

```shell
    export -f conda
    export -f __conda_activate
    export -f __conda_reactivate
    export -f __conda_hashr
```
## [Conda cheat sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)


|Command |Syntax  | 
--- | --- |
|Activate Environments|`conda activate py3-analytics`|
|Update Environments|`conda env update --prefix ./env --file environment.yml  --prune`|
|Viewing environments Available|`conda env list`|
|Installing packages with conda|`conda install –name py3-analytics opencv`|
|Create a environment yml file|`conda env export --from-history -f environment.yml`|
|update environment based on yml file|`conda env update --file environment.yml`|
|||

# Jupyter Notebook

## Running Jupyter Notebook in the background

As Described in [this Website](https://towardsdatascience.com/run-jupyter-notebook-as-a-background-service-on-ubuntu-c5d6298ed1e), we need to use: 

```shell
    nohup jupyter-notebook --ip=123.123.123.123 --no-browser --port=8888 &
```

To check that the process is running and obtain the PID: 

```shell
    lsof nohup.out`
```
or

```shell
    ps au | grep jupyter

```

To kill the process, use:
```shell
    kill -9 <PID>
```
or in case there are several open
```shell
    killall --exact "jupyter-notebook"
```
To see all running notebooks addresses: 
```shell
    jupyter notebook list
```
## Jupyter keyboard cheat sheet

|Command |Syntax  | 
--- | --- |
|Delete Cell|D+D|
|Create one line Above|A|
|Create one line Below|B|
|Markdown cell|M|
|Convert Markdown to code cell|Y|
|||

## Adding virtual environment to your kernel
Change the NAME_OF_ENV in the following code with the name you want to use as reference:

```shell
conda activate NAME_OF_ENV
conda install ipykernel # only in case new environment doesn't have library
python3 -m ipykernel install --user --name=NAME_OF_ENV 
```

# Pandas

## Data Preprocessing Tips in Panda

These are code samples of part of the [Udemy Course - Python, SQL and Tableau Integration](https://www.udemy.com/course/python-sql-tableau-integrating-python-sql-and-tableau) that I thought interesting highlighting and they mainly relate to the use of Python's panda and jupyter notebook library. 

### Displaying data

Using the max rows and columns option in panda you can configure a scroll window within the output cell to be able to scroll a partial or complete set of the data. Example: 

```python
    import pandas as pd
    pd.options.display.max_rows= None
    pd.options.display.max_columns= None
    raw_csv_data= pd.read_csv('FILE/LOCATION')
    df=raw_csv_data.copy()
    display(df)
```


# Numpy

*  Statistical Operations

    |Command |Syntax  | 
    --- | --- |
    |Median|`df.median()`|
    |Mean|`df.mean()`|
    |||


# PyMySQL


Th following code snippets are taken from a Jupyter notebook and consider establising a direct connection between jupyter and mysql server. 

1)  Initialize the database in MySQL

```sql
        DROP DATABASE IF EXISTS database_outputs;
        CREATE DATABASE IF NOT EXISTS database_outputs;
        USE database_outputs();

        DROP TABLE IF EXISTS table_outputs;
        CREATE TABLE table_outputs
        (
        age INT NOT NULL,
        month_value INT NOT NULL,
        probability FLOAT NOT NULL,
        prediction BIT NOT NULL
        );
```

2)  Establish a connection from Jupyter to MySQL

```python
        database='DATABASE_NAME'
        user= 'USER_NAME'
        PASSWORD = 'PASSWORD'

        import pymysql
        conn=pymysql.connect(database=database, user=user, password=password)
        cursor=conn.cursor()
```
3)  Convert a dataFrame into a SQL query: 

    This is an example code that can be easily reutilized. It turns the data stored into a dataframe 'df' and frames it as a query to input the values in a table called `table_outputs`. Please note that this table must exist in your sql server and make sure that the printed query contains the correct SQL structure with rows separated by ',' and a ';' at the end of the statement. Please note that there may be limits of ~20k rows per query sent to MySQL. Such limitations must be tested in order to ensure that all the data is transferred successfully. 

```python
        insert_query='INSERT INTO table_outputs VALUES '

        for i in range(df.shape[0]):
            insert_query += '('
            
            for j in range (df.shape[1]):
                insert_query += str(df[df.columns.values[j]][i])+', '
                                        
            insert_query=insert_query[:-2]+'),'

        insert_query = insert_query[:-1]+';'
        print(insert_query)
```
4)  Send data from Jupyter to MySQL

```python
        cursor.execute(insert_query)
        conn.commit()
        conn.close()
```
---