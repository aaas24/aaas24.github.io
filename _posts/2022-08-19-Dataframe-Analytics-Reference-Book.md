---
layout: post
title:  "Dataframe Analytics Reference Book"
description: "Common operations with DataFrames"
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
    - [Data Exploration](##data-exploration)<br>
    - [Operations with rows and columns](#operations-with-rows-and-columns)<br>
    - [Filtering Data Operations](##filtering-data-operations) <br>
    - [Chaining examples](#chaining-examples) <br>
    - [Dictionary Operations](#dictionary-operations) <br>

<br><br>

# Dataframes Operations

## Data Exploration
<br>


|Command |Syntax  | 
| --- | --- |
|Describe|`df.describe`|
|Number of Rows and Columns|`df.shape`, where:<br>  `df.shape[0]`= rows & `df.shape[1]`= columns|
|Names in Columns|`df.columns.values`|
|Column data types|`df.dtypes`|
|Min|`df['COLUMN_NAME'].min()`|
|Max|`df['COLUMN_NAME'].max()`|
|Number of unique values|`len(df['COLUMN_NAME'].unique())`|
|Sorted list of unique values|`sorted(df['COLUMN_NAME'].unique())`|
|Identify type of value in a column|`type(df_reason_mod['COLUMN_NAME'][0])`|
|Count per value in column|`df.['COLUMN_NAME'].value_counts(dropna=False)`|
|Traspose|`df.head().T`|
|View column with missing value in rows|`df.loc[df.COLUMN_NAME.isna(), ['COLUMN_NAME_1', 'COLUMN_NAME_2', 'COLUMN_NAME_3']]`|
|||

<br>

##  Operations with rows and columns
<br>

|Command |Syntax  | 
| --- | --- |
|Dropping column|`df=df.drop(['COLUMN_NAME'], axis=1)`|
|Adding column|`df['NEW_COLUMN']=df.sum(axis=1)`|
|Sum values per row|`df['NEW_COLUMN']=df.sum(axis=1)` |
|Sum values per column|`df['COLUMN_NAME'].sum(axis=0)` |
|Cut dataframe from Columns A to D|`new_df=df.loc[:,A:D]`|
|Concatenate multiple df|`df= pd.concat([df1,df2,df3,df4,...,dfn], axis=1)`|
|Renaiming columns| 1) get initial values with `column_names=df.columns.value`<br> 2) modify the name value in variable <br> 3) `df.columns=column_names`|
|Reordering columns| 1) get initial values with `colum_names_reordered=df.columns.value`<br> 2) modify order of values in variable <br> 3) `df=df[colum_names_reordered]`|
|Convert values in column to timestamp format. Ex, a column `DATE` with str such as 07/15/2018|`pd.to_datetime(df_reason_mod['Date'], format='%d/%m/%Y')` <br> The convension of the  `format` parameter, [see the 'strftime' documentation](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)|
|Reclassify the values in a column|In a column with 4 distinct values, where we wish to combine 2,3 & 4 as B, and re-label 1 as A: `df['COLUMN_NAME']=df['COLUMN_NAME'].map({1:A,2:B,3:B,4:B})`|
|Slice data with iloc|`df.iloc[START_ROW:END_ROW,START_COLUMN:END_COLUMN]` <br>  Ex: `df.iloc[:,:-1]`, which is all rows, all columns but the last one|
|Where| `df['COLUMN_NAME']= np.where(daf['COLUMN_NAMEs']>CONDITION,TRUE_VALUE,FALSE_VALUE)`|
|Filter data|`df[df['COLUMN_NAME']=='VALUE']`|
|Temporarily display more rows|`with pd.option_context('display.max_columns', 22, 'display.min_rows',10): display(df)`|
|||

<br>

## Examples of groupby

Use the parameter 'as_index=False' in the groupby to have teh output show in the same level as the input df

``` python
# initialize data of lists.
data = {'department': ['shoes', 'clothing', 'shoes', 'furniture']
        , 'product_type':['shoes', 'shirt', 'shoes', 'furniture']
        ,'order_amount': [20, 21, 19, 180]
        ,'taxes_paid':[1.5,2,1.2,10]
        }

# Create DataFrame
df = pd.DataFrame(data)
display(df)

df_result=(df.groupby(['department', 'product_type'], as_index=False)
           .agg({
               'order_amount':'sum'
               ,'taxes_paid':'sum'
           })
)

# Print the output.
display(df_result)
```

## Useful codes snippets

* Find Missing data

For a dataFrame called 'df', this shows the 'percentage' of missing values per column, where there is in fact missing values detected by the '.isna()' function.

``` python
    ((df
    .isna()
    .mean()
    *100)
    .pipe(lambda ser:ser[ser>0])
    )
```

* Change format of columns

<br>

## Filtering Data Operations

|Operation|Example Code|
|--|--|
|With AND Condition  |` df[(df.state=='Texas') & (df.year==1990)] `|
|With OR Condition   | `df[(df.state=='Texas') \| (df.year==1990)]`|
|With != Column Names Nomenclature   |`df[df.state=='Texas'] or df[df['state']=='Texas']`|
|With a list_of_values   |`data_filtered=df[df.column_Name.isin(list_of_values)]`|
|Using 'query'   |`df.query('colum_Name>=5 and colum_Name<=10')`|
|||

<br>

## Date Formatting Data Operations

|Operation|Example Code|
|--|--|
|Converting timestamp into datetime   |`data['dt'] = data['ts'].map(lambda x: datetime.strptime(x, '%Y-%m-%dT%H:%M:%SZ').strftime("%Y-%m-%d"))`|
|Function to convert columns|`for column in columns_to_date:` </break> `df[column] = pd.to_datetime(df[column], format='%B %d, %Y', errors='coerce')`|
|||

<br>

## Chaining Examples
<table>
    <tr>
        <td> Operation </td> <td> Example Code </td>
    </tr>
    <tr><td></td><td></td></tr>
    <tr>
        <td> Groupby & Aggregations Controlling Column Name by Using 'pd.NamedAgg'
        </td>
        <td>
            <pre> See a code example <a href="https://github.com/aaas24/code_library/tree/main/us_mass_shootings">this</a> workbook.
            <pre>
            <code>
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
            </code>
            </pre>
        </td>
    </tr>
    <tr>
        <td> 
            Renaming Columns With .set_axis()
        </td>
        <td>
            <pre>
            <code>
        dict_sub_region=(
        data.loc[:,['state','subRegion']]
            .set_index('state')
            .set_axis(['sub_region'], axis = 1) #renaming columns
            .to_dict()
        )
            </code>
            </pre>
        </td>
    </tr>
    <tr>
        <td> 
            Catching Current State of a DataFrame during Method Chaining 
        </td>
        <td>
            <pre>
            <code>
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
            </code>
            </pre>
        </td>
    </tr>
    <tr>
        <td>
            Adding time values based on date related column 'time_col'. Ex: time_col='date_of_birth'
        </td>
        <td>
            <pre>
            <code>
            df=df.assign(
                month=lambda df_: df_[time_col].dt.month,
                qtr=lambda df_: df_[time_col].dt.quarter,
                year=lambda df_: df_[time_col].dt.year,
                day=lambda df_: df_[time_col].dt.day_name()
            ).rename(columns={'month': ('').join([time_col, '_month']),
                                'qtr': ('').join([time_col, '_qtr']),
                                'year': ('').join([time_col, '_year']),
                                'day': ('').join([time_col, '_day'])
                        }
                    )
            df.head(5)
            </code>
            </pre>
        </td>
    </tr>
    <tr>
        <td>
            Cleaning with regex & formatting datetime columns
        </td>
        <td>
            <pre>
            <code>
            #cleaning df
            df2=df.copy()
            d1=(df2.pop('country')
                .str.replace('\n','')
                .str.replace(r"(\||\d)",'',regex=True)
                .str.replace('[','')
                .str.replace(']','')
            date_time_cols=['end_date','start_date']
            df[date_time_cols] = df[date_time_cols].apply(pd.to_datetime)
            df=pd.concat([df2, d1], axis=1)
            </code>
            </pre>
        </td>
    </tr>
</table>


## Dictionary Operations

<table>
    <tr>
        <td> Operation </td> <td> Example Code </td>
    </tr>
    <tr><td></td><td></td></tr>
    <tr>
        <td> 
            Removing outer layer of dictionary
        </td>
    <tr>
        <td>
            <pre>
            <code>
            dict_sub_region=(
                data.loc[:,['state','subregion']]
                .set_index('state')
                .set_axis(['sub_region'], axis = 1)#renaming columns
                .to_dict()
            )
            </code>
            this printed a dictionary like this: 
            <code>
            {'sub_region': {'AK': 'Pacific',
            'AL': 'East South Central',
            'AR': 'West South Central',
            'AZ': 'Mountain',
            'CA': 'Pacific',
            'CO': 'Mountain',
            (...)}}
            </code>
            To remove 'sub_region', we use: 
            <code>
            from collections import ChainMap
            res = dict(ChainMap(*dict_sub_region.values()))#removes top level of dictionary
            print(res)
            </code>
            </pre>
        </td>
    </tr>
</table>