---
layout: post
title:  "VSCode for python snowflake github"
description: "Efficiently analyzing data by using VSCode with snowflake, python and github" 
date:   2024-08-16
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Project Description

Switching between applications is one of the pitfuls of efficientcy. Remaining in the same UI but benefitting from the efficientcy of different tools is the dream environment for analysts. 

This blog post will guide you on how to create an environment that combines the efficientcy of SQL databases (Snowflake), python analytical and visualization libraries and github's code sourcing. 

During this setup we will be using 1Password to connect retrieve sensitive information. If you do not have a password manager you may skip these sections

<!--more-->

## TABLE OF CONTENT


- [Install Applications](##app) <br>
- [Setting up VSCode](##vscode) <br>
- [Activating python in VSCode](##python)<br>
- [Setting up 1Password](##passwd)<br>
- [Putting everything together](##notebook)<br>
- [Source Code](##git)<br>
 
<br>
<br>

## <a name="app"> Install Applications</a>



| Application  | Link  |Comment   | 
|---|---|---|
|  VSCode | [website](https://code.visualstudio.com)  |  Free |  
|  Conda | [website](https://anaconda.org/anaconda/conda)  |  Free |  
|  1Password|  [website](https://1password.com) |  Paid application |  
<br>

## <a name="app">  Setting up VSCode

1) Download the following Extensions:

* [1Password for VScode](https://marketplace.visualstudio.com/items?itemName=1Password.op-vscode): Extension Id= 1Password.op-vscode
* [Jupyter]()https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter : Extension ID: ms-toolsai.jupyter
* [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Snowflake (optional)](https://marketplace.visualstudio.com/items?itemName=snowflake.snowflake-vsc)

<br>

## <a name="vscode"> Setting up Conda </a>

After you download Conda: 
- created new environment called `analytics: conda create -n analytics python=3.6`
- activated environment `conda activate analytics`
- install python for jupyter library: `conda install ipykernel`
- install and activate kernel

```
conda install -c conda-forge ipykernel
python -m ipykernel install --user --name=myenv
```
<br>

## <a name="python">  Activating python in VSCode </a>

Now that we have set up the applications, it's time to bring everything together in VSCode. 

* Open a new file and save it with the extendsion ".ipynb"
* In the properties palette (Cmd+ Shit+P in Mac) or by tipying ">" in the search box in VSCode UI we activate the option: "Nobebook: Select Notebook Kernel" and you should see the kernel we created "analytics"

<p align="center">
  <img src="https://github.com/aaas24/aaas24.github.io/raw/main/assets/post_files/2024-08-16-VSCode-for-python-snowflake-github/3.png" width="600" title="">
</p>

<br>

## <a name="passwd"> Setting up 1Password </a>

1) Follow the steps on the [documentation to configure 1Password connection](https://developer.1password.com/docs/vscode/) to: 
- Install 1Password in your laptop
- [Install the latest version of 1PasswordCLI](https://developer.1password.com/docs/cli/get-started/#install)
- [Configure the integration on 1Pasword app](https://developer.1password.com/docs/cli/get-started/#step-2-turn-on-the-1password-desktop-app-integration)

2) Create a record in 1Password with your Snowflake connexion variables. 

<p align="center">
  <img src="https://github.com/aaas24/aaas24.github.io/raw/main/assets/post_files/2024-08-16-VSCode-for-python-snowflake-github/1.png" width="600" title="">
</p>

<br>

## <a name="notebook">  Putting everything together </a>

In the notebook: 

### Cell 1: Initializing notebook
____
___

```python 
## Initializing notebook
### Importing modules

#### data
import pandas as pd
import numpy as np

#### snowflake connector/query
import snowflake
import snowflake.connector
from snowflake.connector.pandas_tools import write_pandas
import snowflake.snowpark as snowpark
from snowflake.snowpark import Session

#### ini values
d={}

```
### Cell 2: Getting Snowflake Credentials from 1Password
____
___

```python 
## Retrieving credentials
def from_1password(item):
    '''
    this function converts onePassword output into dictionary. 
    :param item=  a Slist obtained through 1PasswordCLI command.Example: item=!op item get item-name
    :output= dictionary with fields found in item SList
    '''
    items=[]
    credentials={}
    for item,line in enumerate(item):
        try:
            key=line.split(':')[0].strip().lstrip()
            value=line.split(':')[1].strip()
        except:
            pass
        credentials.update({key:value})
    return credentials

credentials=!op item get my_snowflake_credentials
snow_credentials=from_1password(credentials)
```



### Cell 3: Connecting to Snowflake
____
___

Choose one of the following options:

You will need your default values, regardless of if you using a password manager such as 1password or storing them in a file. 

1) If your snowflake instance uses authentication(recommended), add this code in your cell: 

```python
def start_snowflake_connection(credentials):
    """
    this function stablishes a snowflake connection ir order to later executes a query with SQL commands inside jupyter. It assumes snowflake credentials are stored in the same folder
    in dictionary type variable.

    This module requires snowflake connector to be installed. 
    Please view Snowflake documentation for details on downloading the right package: 
    https://docs.snowflake.com/en/user-guide/python-connector-install.html. 
    Ex: 
    !{sys.executable} -m pip install pip install -r https://raw.githubusercontent.com/snowflakedb/snowflake-connector-python/v2.7.11/tested_requirements/requirements_38.reqs 
    
    :param snow_credentials: dictionary with credentials
    :returns: DataFrame styled query result
    """
    import snowflake
    import snowflake.connector

    # fire up an instance of a snowflake connection. this will open a browser window for the authenticator
    ctx= snowflake.connector.connect (
        account  = credentials['account_name'],
        role     = credentials['default_role'],
        user     = credentials['username'],
        database = credentials['default_database'],
        schema   = credentials['default_schema'],
        warehouse= credentials['default_warehouse'],
        authenticator='externalbrowser'
    )
    cs = ctx.cursor()
    clear_output(wait=True)
    return cs

```
2) If you don't use a password manager, here is [an example of how to connect using a file.](https://github.com/aaas24/code_library/tree/main/jupyter/jupyter-and-snowflake) The example will allow you to have the credentials into a python variable.

Once you have read the credentials, you can also use this code:

```python
def query_snowflake_passwd(query, credentials):
    '''
    :param query: SQL commands to execute
    :param credentials: dictionary with account,  user, password and default database, role, schema and warehouse
    :returns: DataFrame styled query result
    '''
    import snowflake
    import snowflake.connector

  # fire up an instance of a snowflake connection 

    ctx= snowflake.connector.connect (
        account  = credentials['account_name'],
        role     = credentials['default_role'],
        user     = credentials['username'],
        password = credentials['password'],
        database = credentials['default_database'],
        schema   = credentials['default_schema'],
        warehouse= credentials['default_warehouse'],
    )
  
    try:
        cs = ctx.cursor()
        return return cs

    except Exception as e:

        raise e
```

### Cell 4: Querying Snowflake
____
___

```python 
#build your query
d.update({'table_name': 'DATABASE.SCHEMA.TABLE_NAME'})
q = '''\
SELECT
*
FROM
    {table_name}
LIMIT 100
;\
'''.format(**d)

#query snowflake
cs.execute(q)
df=pd.DataFrame(cs.fetchall(), columns=pd.DataFrame(cs.description)['name'])

#display result
with pd.option_context('display.max_columns', 500, 'display.min_rows',100):
    display(df.head())
```
<br>

## <a name="git"> Source Code: Github </a>

VSCode has native connection to Github so as long as you have connected your location to a repository you can select the "Source Control" tab on the left panel and commit the changes at the end of your session.

<p align="center">
  <img src="https://github.com/aaas24/aaas24.github.io/raw/main/assets/post_files/2024-08-16-VSCode-for-python-snowflake-github/2.png" width="600" title="">
</p>

## RELATED: 

* [Getting Started with Jupyter](http://web.aaas24.io/coding/2022/03/18/Python-Libraries-Reference-Book.html)