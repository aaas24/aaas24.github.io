---
layout: post
title:  "Wikipedia Article"
description:  "Study ways to obtain a Wikipedia article"
date:   2022-05-15
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, dataengineering, re]
---

Wikipedia is one of the most solid references for data nowadays. It is persistently updated and has become a reference in many books. I find myself constantly learning and searching it's data, so I thought it would be interesting to learn and document different methods to obtain information from it. 

This project studies ow to quickly obtain a single article information.

<!--more-->

As a summary, here is my assessment of the libraries used: 

Scale used: 1=Least -> 5= Most

|Concept|Wikipedia|BeautifulSoup|Pandas|
|---------|------|------|------|
|Ease to install|4|3|5|
|Ease to use|5|3|5|
|Flexibility|4|5|3|
|||



Wikipedia does not allow web crawlers for downloading large number of articles. As stated in there [how-to download guide](https://en.wikipedia.org/wiki/Wikipedia:Database_download), Wikipedia servers would not be able to cope with the constant pressure of scrapping the entire site. However, in the case that we do hold one specific url, there are different libraries that can be explored to assist in ths job, like pandas, beautifulsoup and more. I will focus on exploring each of these libraries as the second method explored to understand what each library has to offer. 

To follow along this project's code, please view [location](https://github.com/aaas24/code_library/tree/main/wikipedia).

### DEPENDENCIES


PYTHON LIBRARIES

```python
    import pandas as pd
    import numpy as np
    import os
```



###  USING [WIKIPEDIA LIBRARY](https://pypi.org/project/wikipedia/)

#### **Search FUNCTIONALITY**

Assuming that we have a subject, which is a collection of words or terms we are looking for, this library hasa great search option. As an example, we will use the value:     

``` 
subject='ted talk speakers' 
```

```python
    # To obtain a search on the subject:
    import wikipedia

    search_result = wikipedia.search(subject)
    print('the search result is:')
    print(search_result)
    result=search_result[1]
    print('')
    print('the selected page is:', result)
```

<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/wikipedia/images/wiki_3.png" alt="Search" width="100%">
</p>

Note that in the above code when we select the second value in the search results (In: result=search_result[1]) which is the desired outcome in our case. You need to adapt this to your use case To obtain a URL base on the search result



```python
    url=(result).replace(" ", "_")
    url= 'https://en.wikipedia.org/wiki/'+ url
    print(url)
```


#### **Article Information**

Once you obtain the targeted URL, you can call the attributes title, summary, categories, etc.


```python
    result=wikipedia.page(result)

    #parse attributes library provides

    title=result.title
    summary = result.summary
    categories=result.categories
    content = result.content
    links = result.links
    references = result.references
    html=result.html()

    # print info

    print("Page content:\n", content, "\n")
    print("Page title:", title, "\n")
    print("Categories:", categories, "\n")
    print("Links:", links, "\n")
    print("References:", references, "\n")
    print("Summary:", summary, "\n")
```

For an example of the outputs, this [notebook](https://github.com/aaas24/code_library/blob/main/ted_talks/4_enhance_authors/Authors.ipynb).


###  BEAUTIFUL SOUP

The first step is obtaining the html: 

```python
    from bs4 import BeautifulSoup
    from urllib.request import urlopen


    html = urlopen (url)
    bsObj = BeautifulSoup(html.read (), 'html.parser')
    print(bsObj)
```
Once we obtain the html code of the target website, we can use different methods to extract the parts of the text we wish to obtain.

* Using 'FindAll' Option

```python
    def tag_list(tag):
        """
        This function extracts the list of tags and returns the list. It uses the findAll function of BeautifulSoup
        """
        soup=bsObj
        a=[]
        content=soup.findAll(tag)
        for item in content:
            a.append(item.get_text())
        return a
```
For example, if we wanted to extract a list of authors from this html: 

```python
    authors= (tag_list("td")
            [6:] #remove first 6 values that do not correspond to authors or talks
            )
```
<p align="center">
  <img src="https://github.com/aaas24/code_library/raw/main/wikipedia/images/wiki_4.png" alt="Authors Example" width="80%">
</p>

* Using Regex

```python
    import re
        
    #Parse HTML to find urls of speakers 
    txt = str(bsObj)
    result=[]
    reg='(?<=td>)(.*)(?=</td>)'

    reobj = re.compile(reg)
    for matchobj in reobj.finditer(txt):
        result.append(matchobj[1 ])

    for line in result:
        print (line) 
```
For a more detailed example on regex use, please see this [notebook](https://github.com/aaas24/code_library/blob/main/ted_talks/4_enhance_authors/Authors.ipynb).

###  PANDAS

It's almost obvious but still important to call out how easy panda makes this: 

```python
    html=pd.read_html(url)

    print(type(html))
    print(html)
```


### CONCLUSIONS

The Wikipedia library is by far the most easy and robust to use to connect to the wikipedia API. However, since it pre-processes the output, BeautifulSoup offers more flexibility when targeting specific sections (such as tables). 

BeautifuSoup is very powerful as it allows to target tags along all the html. However, it is not as easy to use because it is not a stand-alone package but rather requires the use 'requests' and some understanding of html & CSS. 

Pandas out of the box read_html is convenient and easy to use. It provides a list of elements that can be cycled through quickly making it both simple and fast. 

Scale: 1=Least -> 5= Most

|Concept|Wikipedia|BeautifulSoup|Pandas|
|---------|------|------|------|
|Ease to install|4|3|5|
|Ease to use|5|3|5|
|Flexibility|4|5|3|


