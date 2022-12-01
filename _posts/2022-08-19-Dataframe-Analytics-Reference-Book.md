---
layout: post
title:  "Dataframe Analytics Reference Book"
description: "Basic functions related to analyzing data using DataFrames"
date:   2022-08-19
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Project Description

The intent of this post is to document basic functions related to analyzing data using DataFrames. 

<!--more-->

### TABLE OF CONTENT

\
    - [Filtering Data Operations](##filtering-data-Operations) <br>
    - [Chaining examples](#chaining-xamples-of-filering-data) <br>


# Dataframes Common Data Operations

## Filtering Data Operations

|Operation|Example Code|
|--|--|
|With AND Condition|df[(df.state=='Texas') & (df.year==1990)]|
|With OR Condition|df[(df.state=='Texas') | (df.year==1990)]|
|With != Column Names Nomenclature|df[df.state=='Texas'] or df[df['state']=='Texas']|
|With a list_of_values|data_filtered=df[df.column_Name.isin(list_of_values)]|
|Using 'query'|df.query('colum_Name>=5 and colum_Name<=10')|

</br>
</br>
</br>

# Chaining examples of filtering data

<table>
<tr>
<td> Operation </td> <td> Example Code </td>
</tr>

<tr>
    <td> Groupby & Aggregations controlling column name by using 'pd.NamedAgg'.
        </br>
        </br>
        See a code example <a href="https://github.com/aaas24/code_library/tree/main/us_mass_shootings">this</a> workbook.
    </td>
    <td style="font-size: 8px">

```python
data=(df2
    [df2.date.dt.year==2017]
    .groupby(df2.state)
    .agg(
        tot_victims=pd.NamedAgg(column="victims", aggfunc="sum"),
        num_incidents=pd.NamedAgg(column="incident_id", aggfunc="count")
    )
    .sort_values(by='tot_victims', ascending=False)
    .reset_index()
)
```
</td>
</tr>

<!--Copy the section below to add another row
<tr>
    <td> 
        Examples 
    </td>
    <td style="font-size: 8px">

```python
data
``` 
</td>
</tr>
-->


</table>