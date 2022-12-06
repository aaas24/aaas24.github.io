---
layout: post
title:  "Understanding US Gun Violence with Altair"
description: "Basic functions related to analyzing data using DataFrames"
date:   2022-11-30
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics, visualization]
---

# Project Description

Altair is a python library that enables interactive data visualization. 

Gun Violence in the United States is a topic frequently refer in media news worldwide. It is a sad reality but complex issue that affects my family directly and therefore I sought to explore different data sources that allowed me a better understanding of the issue, while learning a new python library. 

<!--more-->

### TABLE OF CONTENT



## DATASETS

|Dataset|Description|
|[Mother Jones](https://www.motherjones.com/politics/2012/12/mass-shootings-mother-jones-full-data/)|Mother Jones offers a open-source database in order to have an in-depth look at a distinct phenomenon—from the firearms used and mental health factors to the growing copycat problem. |
|[The Associated Press/USA TODAY/Northeastern University Mass Killings database](https://www.usatoday.com/in-depth/graphics/2022/08/18/mass-killings-database-us-events-since-2006/9705311002/)|Overall counting|
|[The Associated Press/USA TODAY/Northeastern University Mass Killings database](https://usafacts.org/data/topics/people-society/population-and-demographics/population-data/population/)|Population by state|



## VISUALIZATIONS
- Please visit [this project website](https://www.alvarez.pub/projects/us-gun-violence) to view the visualizations.


## DATA KEY FINDINGS
- 


## METHODOLOGY

### CREATE THEME IN ALTAIR

Create five-thirty-eight like template in Altair.

```python
def my_theme():
    markColor = '#30a2da';
    axisColor = '#cbcbcb';
    guideLabelColor = '#999';
    guideTitleColor = '#333';
    backgroundColor = '#f0f0f0';
    blackTitle = '#333';
    
    return {
        'config': {
            'arc': {'fill': markColor},
            'area': {'fill': markColor},
            'axis': {
                'domainColor': axisColor,
                'grid': True,
                'gridColor': axisColor,
                'gridOpacity':0.3,
                'gridWidth': 1,
                'labelColor': guideLabelColor,
                'labelFontSize': 10,
                'titleColor': guideTitleColor,
                'tickColor': axisColor,
                'tickSize': 10,
                'titleFontSize': 14,
                'titlePadding': 10,
                'labelPadding': 4,
              },
            'axisBand': {
                'grid': False,
            },
            'background': backgroundColor,

            'group': {
                'fill': backgroundColor,
              },

            'legend': {
                'labelColor': blackTitle,
                'labelFontSize': 11,
                'padding': 1,
                'symbolSize': 30,
                'symbolType': 'square',
                'titleColor': blackTitle,
                'titleFontSize': 14,
                'titlePadding': 10,
              },

            'line': {
                'stroke': markColor,
                'strokeWidth': 2,
              },

            'path': {'stroke': markColor, 'strokeWidth': 0.5},
            'rect': {'fill': markColor},

              'range': {
                'category': [
                  '#30a2da',
                  '#fc4f30',
                  '#e5ae38',
                  '#6d904f',
                  '#8b8b8b',
                  '#b96db8',
                  '#ff9e27',
                  '#56cc60',
                  '#52d2ca',
                  '#52689e',
                  '#545454',
                  '#9fe4f8',
                ],

                'diverging': ['#cc0020', '#e77866', '#f6e7e1', '#d6e8ed', '#91bfd9', '#1d78b5'],
                'heatmap': ['#d6e8ed', '#cee0e5', '#91bfd9', '#549cc6', '#1d78b5'],
              },

              'point': {
                'filled': True,
                'shape': 'circle',
              },

              'shape': {'stroke': markColor},

              'bar': {
                'binSpacing': 2,
                'fill': markColor,
                'stroke': None
              },

              'title': {
                'anchor': 'start',
                'fontSize': 24,
                'fontWeight': 600,
                'offset': 20,
              },
#             
            'mark': {
                'color': 'black',
                'fill': None
            }
        }
    }
# register the custom theme under a chosen name
alt.themes.register('my_theme', my_theme)
# enable the newly registered theme
alt.themes.enable('my_theme')
```