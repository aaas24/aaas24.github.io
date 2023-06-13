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


- [Read & Write Examples](##Read-write-examples) <br>
 


<br>
<br>
<br>

## Read write examples

|Format|Read Examples|Write Examples|
|--|--|--|
|Excel  | *) `pd.read_excel('data.xlsx', index_col=0)` </br> *) `pd.read_csv('data.csv', index_col=0, parse_dates=['IND_DAY']`  | *) `df.to_excel('data.xlsx')` </br> *) `df.to_excel('data.xlsx', sheet_name='COUNTRIES',startrow=2, startcol=4)`|
|CSV| *) `pd.read_csv('data.csv', index_col=0)`| *) `df.to_csv('data.csv')`</br> *) `df.to_csv('formatted-data.csv', date_format='%B %d, %Y')`</br>  *) `df.to_csv(sep=';', header=False)`|
|JSON| *) `df = pd.read_json('data-index.json', orient='index',convert_dates=['IND_DAY'])`| *) `df = pd.DataFrame(data=data).T df.to_json('data-columns.json')`</br>  *) `df.to_json('data-records.json', orient='records')`|
|HTML| *) `pd.read_html('data.html', index_col=0, parse_dates=['IND_DAY'])`| *) `df.to_html('data.html')`|
|ZIP| Printing Contents of a Zip File </br>`import zipfile` </br> `with zipfile.ZipFile('/Users/nikpi/ThatExcelSiteGSC/Archive.zip', 'r') as zip:` </br> `zip.printdir()`</br> </br> How to Extract a single file from a Zipfile using </br>ZipFile.extract(member, path=None, pwd=None) </br></br> `with zipfile.ZipFile('/Users/datagy/Archive.zip', 'r') as zip: zip.extract('file1.txt', '/Users/datagy/')` | How to Zip All Files in a Directory </br>`import os` </br> `import zipfile`</br></br>`directory='/Users/datagy/files'` </br>`files = os.listdir(directory)`</br></br>`with zipfile.ZipFile('zipfile.zip', 'w') as zip:`</br>`for file in files:`</br>`file_path=os.path.join(directory, file)`</br>`zip.write(file_path)`|
||||

## ZIP

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

## WRITING PROGRAMMATICALLY

### Example of writing dafataframes

``` python
data_location=os.path.join(datasets,'dtd', 'pre_processed_data')

dataframes=['miseducation_data', 'book_club']
for d in dataframes:
    vars()[d].to_csv(os.path.join(data_location,d+'.csv'), date_format='%B %d, %Y')
```

# Sources

[1] https://realpython.com/pandas-read-write-files/  </br> 
[2] https://datagy.io/python-zip-unzip-files/