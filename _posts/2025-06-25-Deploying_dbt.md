---
layout: post
title:  "Deploying dbt"
description:  "Step by step installation of local dbt connected to a read-only dbt repository"
date:   2025-06-25
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-python-2021-04-05.jpg
category: Coding
tags: [python, analytics , dataengineering, dbt]
---


Lots of companies are moving their scalable analytics into dbt deployments. DBT is scalable and easy to understand as it primarily uses SQL and yaml files. 


<!--more-->
To follow along this project, I've made a copy of the code in this [location](https://github.com/aaas24/aaas24.github.io/tree/master/assets/post_files)

### 


### STEP 1 INSTALLATION

1. Create conda environment
2. Download python package

```bash
conda install python=3.11.6
conda install dbt-core==1.8.0
conda install dbt-snowflake==1.8.2
```
3. Confirm version and compatibility using

`dbt --version`


### COMMON DBT COMMANDS

|Explanation|Example|
|---|---|
running a single table/model with a specific profile without predecesors or descendents|`dbt run --select MODEL_NAME --profile snowflake --target test > ../log.txt`|
|compile query|`dbt compile --select  MODEL --profile snowflake --target test > ../log.txt`|
|find|`find models -name 'MODEL_NAME.sql'` |