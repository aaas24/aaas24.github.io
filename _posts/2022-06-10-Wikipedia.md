---
layout: post
title:  "Wikipedia Dumps"
description:  "Study ways to obtain all of data from Wikipedia"
date:   2022-05-15
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, dataengineering, re]
---

Wikipedia is one of the most solid references for data nowadays. It is persistently updated and has become a reference in many books. I find myself constantly learning and searching it's data, so I thought it would be interesting to learn and document different methods to obtain information from it. 

This project studies downloading the entire wikipedia data and how to quickly obtain a single article information.

<!--more-->

As a summary, here is my assessment of the libraries used: 

1=Least -> 5= Most

**For all data dump:**
|Concept|Wiki Dump Parser|Wiki Dump Reader
|---------|------|------|
|Ease to install|4|4|
|Ease to use|4|4|
|Output Value|3|5|
|||

</br>
Wikipedia does not allow web crawlers for downloading large number of articles. As stated in there [how-to download guide](https://en.wikipedia.org/wiki/Wikipedia:Database_download), Wikipedia servers would not be able to cope with the constant pressure of scrapping the entire site. However, they have made available copies of the site that you can download in different formats, the easiest would be the latest copy of the state of all the pages. This will be the method explored. 

To follow along this project's code, please view [location](https://github.com/aaas24/code_library/tree/main/wikipedia)

### METHODOLOGY

**Disclaimer**: I tried running all commands from the Jupyter notebook in an effort to test its capabilities, expand my knowledge on libraries, increase traces of code changes and reduce switching between tools. This may have resulted in other inefficiencies that can be solved by directly running scripts. In order to run shell commands I utilized this function: 

```python
    import subprocess

    def runcmd(cmd, verbose = False, *args, **kwargs):

        process = subprocess.Popen(
            cmd,
            stdout = subprocess.PIPE, 
            stderr = subprocess.PIPE,
            text = True, 
            shell = True
        )
        std_out, std_err = process.communicate()
        if verbose:
            print(std_out.strip(), std_err)
        pass
```
```
    runcmd("echo 'Hello World'", verbose = True)
```

**Example:**

### DEPENDENCIES

<span style="font-size:11px"> 

PYTHON LIBRARIES

```python
    import pandas as pd
    import numpy as np
    import os
```
</span>

SHELL PACKAGES: 

- wget
- bzip2


###  STEP 1 DOWNLOADING AND DECOMPRESSING DUMP 


```python
    # deletes previous file
    try:
        os.remove(filename)
    except OSError:
        pass

    # download latest wiki dump 
    runcmd("wget https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2", verbose = True)

    # decompress file using #wikiextractor library
    runcmd("bzip2 -d /Users/alialvarez/Desktop/STUDIES/github/code_library/wikipedia/enwiki-latest-pages-articles.xml.bz2", verbose = True) 
```

###  STEP 2 PROCESS DUMP

* OBTAIN METADATA

Evaluated the [Wiki Dump Parser Library](https://github.com/Grasia/wiki-scripts/tree/master/wiki_dump_parser). 

```python
    try:
        os.remove(filename[:-8]+'.csv')
    except OSError:
        pass


    import wiki_dump_parser as parser
    parser.xml_to_csv('enwiki-latest-pages-articles.xml')
```
The resulting file shown below has several failed parsed rows while processing the text columns [page_title & contributor_name] that make it hard to utilize directly. However, with some data wrangling, one would think it could be possible to obtain a curated list of the page_id's and titles. However, it would be an interesting exercise to understand if one of the dump files contains this data pre-arranged, namely the "enwiki-****-all-titles.gz"

 <p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/wikipedia/images/wiki_1.png" alt="Metadata Example" width="600">
</p>

* OBTAIN MARKUP

Evaluated the [Wiki Dump Reader Library](https://github.com/CyberZHG/wiki-dump-reader)). 

```python
    from wiki_dump_reader import Cleaner, iterate

    wiki={}
    cleaner = Cleaner()

    for title, text in iterate('enwiki-latest-pages-articles.xml'):
        orig_text=text
        text = cleaner.clean_text(text)
        cleaned_text, links = cleaner.build_links(text)

        #add files to dictionary
        wiki.update({title:[cleaned_text]})

    #create DataFrame and export it as CSV
    df_wiki=pd.DataFrame.from_dict(wiki, orient='index')
    df_wiki.columns=['cleaned']
    df_wiki.to_csv(os.path.join(os.getcwd(), 'wiki_dump_example.csv'))
```

The code above results in a clean file with many redirects rows, like this:  

 <p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/wikipedia/images/wiki_2.png" alt="Metadata Example" width="600">
</p>

One potential improvement is to delete this REDIRECTS and, when failing to find a term, utilize the "enwiki-****-redirects.gz" file provided as part of the dumps to find a different title page

</br>

### CONCLUSIONS

The Wiki Dump Parser Library is very easy to utilize but the output requires further transformations as it does not correctly parses the text fields. If you require the ID or titles alone, it might be available on one of the file dumps released by wikipedia. However, for basic stats like bits or contributor name, it seems like a good start. 

The Wiki Dump Reader provides a great markup result for a fast understanding of each article and could be fed into a ML model with ease. 

Scale: 1=Least -> 5= Most

|Concept|Wiki Dump Parser|Wiki Dump Reader
|---------|------|------|
|Ease to install|4|4|
|Ease to use|4|4|
|Output Value|3|5|
|||


### IMPROVEMENTS

- Compare output "enwiki-****-all-titles.gz" with Wiki Dump Parser Library. 

- Delete this REDIRECTS and, when failing to find a term, utilize the "enwiki-****-redirects.gz" file provided as part of the dumps to find a different title page. 

