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
- [Parsing Addresses](##Parsing-Addresses)<br>
 
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
    """
    function that replaces values in columns using dictionary provided below.

    :param query: List of DataFrame column names.
    :returns: List of columns names formatted .
    """
    col_dict={
        'mailing_or_mail_box_address':'mailing_address'
        'what_is_your_mailing_address?':'mailing_address'
        'please_select_your_current_school':'current_school'
    }
    return [x if x not in col_dict else col_dict[x] for x in my_list]

def clean_column_names(df, pprint='False'):
    """
    function to call on after DataFrame is created to format column names in lowercase 
    without spaces before querying.
    
    :param query: DataFrame
    :returns: DataFrame with columns formatted  
    """
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

## Parsing Addresses in US

This function uses the usaddress library. This is the main fucntion, it takes in a list of addreses and a mapping. This is because I rename the default variables to maintain a lower case format.

For the example, I used the data available in this [Kaggle_link](https://storage.googleapis.com/kagglesdsdata/datasets/1957378/3228086/list_of_real_usa_addresses.txt?X-Goog-Algorithm=GOOG4-RSA-SHA256&X-Goog-Credential=gcp-kaggle-com%40kaggle-161607.iam.gserviceaccount.com%2F20230818%2Fauto%2Fstorage%2Fgoog4_request&X-Goog-Date=20230818T191305Z&X-Goog-Expires=259200&X-Goog-SignedHeaders=host&X-Goog-Signature=3fa3282142c31207de4bcbc1f7fb962cefcd79567a3faa760676de47f5851ed225133957d3c3a8568d5003bd46488678ea625a8c0395913b60dcdea8263c8125abeeebba1a36c0ab57cccc4c19975159cc34a1681e2afbcee6c431f22db1b10f3d26e580135377ff2ddf2ff6c2ccd9cefe0f6eb8e5743c98997ef52f1c576aea0f11a6f94515590268a747980205ef6e84399b6647d89df6bdd46517edbadcf4ad9747ea9a539a246bdb121b0c27a3ecd0389b1938aa6d86f0da2b61fe149485360c878ebd612fb1e707de8de0be9d8bc547ed958e881c18a170ce77b35b65078eba99d685a42a76c86da045966720aa03fb7861a55ae5b071c70d1b151e07dc).

```python
def complete_addresses(addresses, mapping):
    """
    function to call on after DataFrame is created to format column names in lowercase 
    without spaces before querying.
    
    :param query: DataFrame
    :returns: DataFrame with columns formatted  
    """
    import usaddress
    import pandas as pd
    
    df_result=pd.DataFrame(columns=list(mapping.values()))
    for i, addr in enumerate(addresses,start=0):
        dic_address={'address': addr, 'country':'United States' }
        info= usaddress.tag(addr, tag_mapping=mapping)
        #extract nested dictionary 
        info=(str(info[0])
            .split('[')
              [1][:-2]
              .replace("', ","' : ")
              .replace("('","")
              .replace("')","")
              .replace("'","")
              .replace("'","")
              .split(',')
             )
     
        for x in info:
            key=x.split(':')[0].rstrip().lstrip()
            value=x.split(':')[1].rstrip().lstrip()
            dic_address.update({key:value})

        dct = {k:[v] for k,v in dic_address.items()}
        df_result=pd.concat([pd.DataFrame(dct), df_result])
    
    return (df_result)
 

#rename the out-of-the-box variables
mapping={
'Recipient': 'recipient',
'AddressNumber': 'number',
'AddressNumberPrefix': 'num_prefix',
'AddressNumberSuffix': 'num_sufix',
'StreetName': 'street',
'StreetNamePreDirectional': 'street_pre_directional',
'StreetNamePreModifier': 'street_pre_modifier',
'StreetNamePreType': 'street_pre_type',
'StreetNamePostDirectional': 'street_pos_directional',
'StreetNamePostModifier': 'street_pos_modifier',
'StreetNamePostType': 'street_pos_type',
'CornerOf': 'corner',
'IntersectionSeparator': 'intersection',
'LandmarkName': 'landmark',
'USPSBoxGroupID': 'usps_groupbox_id',
'USPSBoxGroupType': 'usps_groupbox_type',
'USPSBoxID': 'usps_bx_id',
'USPSBoxType': 'usps_box_type',
'BuildingName': 'building_name',
'OccupancyType': 'occupancy_type',
'OccupancyIdentifier': 'occupancy_id',
'SubaddressIdentifier': 'sub_address_id',
'SubaddressType': 'sub_address_type',
'PlaceName': 'city',
'StateName': 'state',
'ZipCode': 'zip_code', 
'CountryName':'country'
}

import pandas as pd
df=pd.read_csv(Kaggle_link)
addresses=(df
           .assign(addr=df.iloc[:,0]+df.iloc[:,1])
           .iloc[:,2]
          )
addresses=addresses[:10]

df=complete_addresses(addresses, mapping)
print(df.head(3)
```
The result: 

```
                                        address        country number  \
0            121 Worcester Rd Framingham MA 1701  United States    121   
0  374 William S Canning Blvd Fall River MA 2721  United States    374   
0     42 Fairhaven Commons Way Fairhaven MA 2719  United States     42   

              street street_pos_type                     city state zip_code  \
0          Worcester              Rd               Framingham    MA     1701   
0            William             NaN  Canning Blvd Fall River    MA     2721   
0  Fairhaven Commons             Way                Fairhaven    MA     2719   

  street_pos_directional recipient  ... landmark usps_groupbox_id  \
0                    NaN       NaN  ...      NaN              NaN   
0                      S       NaN  ...      NaN              NaN   
0                    NaN       NaN  ...      NaN              NaN   

  usps_groupbox_type usps_bx_id usps_box_type building_name occupancy_type  \
0                NaN        NaN           NaN           NaN            NaN   
0                NaN        NaN           NaN           NaN            NaN   
0                NaN        NaN           NaN           NaN            NaN   

  occupancy_id sub_address_id sub_address_type  
0          NaN            NaN              NaN  
0          NaN            NaN              NaN  
0          NaN            NaN              NaN  

[3 rows x 28 columns]
```