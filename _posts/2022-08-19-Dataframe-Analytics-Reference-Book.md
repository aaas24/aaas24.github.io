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
    - [Chaining examples](#chaining-examples) <br>
    - [Dictionary Operations](#dictionary-operations) <br>


# Dataframes Operations

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

## Chaining Examples

<table>
<tr>
<td> Operation </td> <td> Example Code </td>
</tr>

<tr>
    <td> Groupby & Aggregations Controlling Column Name by Using 'pd.NamedAgg'.
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

<tr>
    <td> 
        Catching Current State of a DataFrame during Method Chaining 
    </td>
    <td style="font-size: 8px">
        ```python
        def catchstate(df, var_name: str) -> 'pd.DataFrame':
        """
        Helper function that captures intermediate Dataframes mid-chain.
        In the global namespace, make a new variable called var_name and set it to dataframe
        """
        globals()[var_name] = df
        return df
        data=(df2_incidents
            .assign(year=df2_incidents.date.dt.year, month=df2_incidents.date.dt.month)
            .assign(sub_region=df2_incidents.state.map(dict_sub_region))
            .pipe(catchstate, var_name="df2")
            [(df2.sub_region!='Mountain')&(df2.year!=2017)]#filtering outlier of las vegas shooting
            .groupby(by=['year','sub_region'])
            .agg(
                tot_victims=pd.NamedAgg(column="tot_victims", aggfunc="sum"),
                num_incidents=pd.NamedAgg(column="incident_id", aggfunc="count")
            ).reset_index()
        )
        data
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

# Dictionary Operations

</table>

<table>
<tr>
    <td> 
        Removing outer layer of dictionary
    </td>
    <td style="font-size: 8px">

        ```python
        dict_sub_region=(
            data.loc[:,['state','subregion']]
            .set_index('state')
            .set_axis(['sub_region'], axis = 1)#renaming columns
            .to_dict()
        )
        ``` 

        this printed a dictionary like this: 

        ```
        {'sub_region': {'AK': 'Pacific',
        'AL': 'East South Central',
        'AR': 'West South Central',
        'AZ': 'Mountain',
        'CA': 'Pacific',
        'CO': 'Mountain',
        (...)}}
        ```

        To remove 'sub_region', we use: 

        ```python
        from collections import ChainMap
        res = dict(ChainMap(*dict_sub_region.values()))#removes top level of dictionary
        print(res)
        ```
    </td>
</tr>
</table>