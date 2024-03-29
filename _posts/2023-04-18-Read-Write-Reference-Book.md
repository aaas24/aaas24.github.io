---
layout: post
title:  "Reading/Writing Reference Book"
description: "Common operations with files in Python"
date:   2022-08-19
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Project Description

The intent of this post is to document basic functions related to reading & writing files in python. 

<!--more-->

## TABLE OF CONTENT


- [Read & Write Quick Examples](##summary) <br>
- [Zip Examples](##zip) <br>
- [Programatically Examples](##var) <br>
- [Kaggle Examples](##kaggle) <br>
 


<br>
<br>
<br>

## <a name="summary"> QUICK EXAMPLES </a>

|Format| Read Examples| Write Examples|
|---|---|---|
|Excel  | *) `pd.read_excel('data.xlsx', index_col=0)` </br> *) `pd.read_csv('data.csv', index_col=0, parse_dates=['IND_DAY']`  | *) `df.to_excel('data.xlsx')` </br> *) `df.to_excel('data.xlsx', sheet_name='COUNTRIES',startrow=2, startcol=4)`|
|CSV| *) `pd.read_csv('data.csv', index_col=0)`| *) `df.to_csv('data.csv')`</br> *) `df.to_csv('formatted-data.csv', date_format='%B %d, %Y')`</br>  *) `df.to_csv(sep=';', header=False)`|
|JSON| *) `df = pd.read_json('data-index.json', orient='index',convert_dates=['IND_DAY'])`| *) `df = pd.DataFrame(data=data).T df.to_json('data-columns.json')` </br>  *) `df.to_json('data-records.json', orient='records')`|
|HTML| *) `pd.read_html('data.html', index_col=0, parse_dates=['IND_DAY'])`| *) `df.to_html('data.html')`|
|ZIP| Printing Contents of a Zip File </br> `import zipfile` </br> `with zipfile.ZipFile('/Users/nikpi/ThatExcelSiteGSC/Archive.zip', 'r') as zip:` </br> `zip.printdir()`</br> </br> How to Extract a single file from a Zipfile using </br> ZipFile.extract(member, path=None, pwd=None) </br></br> `with zipfile.ZipFile('/Users/datagy/Archive.zip', 'r') as zip: zip.extract('file1.txt', '/Users/datagy/')` | How to Zip All Files in a Directory </br> `import os` </br> `import zipfile`</br></br> `directory='/Users/datagy/files'` </br> `files = os.listdir(directory)` </br></br> `with zipfile.ZipFile('zipfile.zip', 'w') as zip:` </br> `for file in files:` </br> `file_path=os.path.join(directory, file)` </br> `zip.write(file_path)`|
|Kaggle| *) From competition: `api.competition_download_cli(<NAME_OF_COMPETITION>, path=<PATH_WHERE_TO_WRITE>)` </br>  *) From Dataset: `kaggle.api.dataset_download_files(<DATASET_NAME>, path=<PATH_WHERE_TO_WRITE>, unzip=True)` ||
||||


## <a name="zip"> ZIP </a>


### Example of opening files from Zip

```python
import urllib.request as req
import zipfile
import io

url = 'https://github.com/ecodan/school-shooting-data/archive/refs/heads/master.zip'
fin = req.urlopen(url)

with zipfile.ZipFile(io.BytesIO(fin.read())) as z:
    print(z.namelist())
    raw_data_2 = pd.read_csv(z.open('school-shooting-data-master/cps_01_formatted.csv'))
    raw_data_1 = pd.read_csv(z.open('school-shooting-data-master/pah_wikp_combo.csv'))

print(raw_data_2.head())
print('----------------------------')
raw_data_1
```
||||
|--|--|--|
||||

## <a name="var"> WRITING PROGRAMMATICALLY </a>

### Example of reading dafataframes

``` python
def read_csv_into_dataframes(folder):
    '''
    functions converts .csv files into dataframes and assigns file name as the 'name' attribute for each dataframe that can later be retrieved using df.attrs['name'].

    :param folder: a path to .cvs files
    :returns: message with names of dataframes loaded
    '''
    for file in os.listdir(folder):
        vars()[file[:-4]]=pd.read_csv(os.path.join(folder,file))
        vars()[file[:-4]].attrs['name']=str(file[:-4]) #assigns name of file as name of df to be retrieved later
        print('the following dataframes were read: /n', vars()[file[:-4]].attrs['name'])
```


### Example of writing dafataframes

``` python
data_location=os.path.join(datasets,'dtd', 'pre_processed_data')

dataframes=['miseducation_data', 'book_club']
for d in dataframes:
    vars()[d].to_csv(os.path.join(data_location,d+'.csv'), date_format='%B %d, %Y')
```
## <a name="kaggle"> KAGGLE </a>

required functions: 

```python
import json
import kaggle

#definitions
kaggle_type='dataset' #options['dataset', 'competition']
kaggle_name = 'cooperunion/cardataset' ## Make sure yu accepted rules of competition first
kaggle_dataset=os.environ.get('KAGGLE_LOC') #path to directory for kaggle

#retrieve location & credentials.
kaggle_json=os.path.join(kaggle_dataset,'kaggle.json')

try:
    with open(kaggle_json) as json_data:
        data = json.load(json_data)
        kaggle_user=data['username']
        kaggle_key=data['key']
        print('Succesffully retrieved kaggle API keys')
except:
    print('Error opening kaggle json file in ', kaggle_json)

#connect to api and download files
wpath=os.path.join(kaggle_dataset, kaggle_name)

if not os.path.exists(wpath):
    try:
        if kaggle_type=='competition':
            kaggle.api.competition_download_cli(kaggle_name, path=wpath, unzip=True)
        elif kaggle_type=='dataset':
            kaggle.api.dataset_download_files(kaggle_name, path=wpath, unzip=True)
        else:
            print('kaggle_type not recognized')
    except:
        print('Error: unable to write. Make sure yu accepted rules of competition first at https://www.kaggle.com/<comp>/rules')
else:
    print('Error: directory exists')

```

# Sources

[1] https://realpython.com/pandas-read-write-files/  </br> 
[2] https://datagy.io/python-zip-unzip-files/