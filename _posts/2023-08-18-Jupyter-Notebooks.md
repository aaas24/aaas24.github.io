---
layout: post
title:  "Jupyter Notebooks"
description: "Useful Notes on using Jupiter Notebooks" 
date:   2023-06-11
banner_preview: blog-350-250-python-b&w-2021-04-05.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Coding
tags: [ python, analytics]
---

# Description

The article intents to summarize useful applications and basic functions of Jupyter Notebooks.

<!--more-->

## TABLE OF CONTENT


- [Running Jupyter Notebook](##Running-Jupyter-Notebook) <br>
- [Navigation with keyboard](##Navigation-with-keyboard) <br>
- [Slide presentation](##Slide-presentation)<br>
- [Blogging](##Blogging)<br>

 
<br>
<br>
<br>

# Jupyter Notebook

## Running Jupyter Notebook
As Described in [this Website](https://towardsdatascience.com/run-jupyter-notebook-as-a-background-service-on-ubuntu-c5d6298ed1e)

|Command |Syntax  | 
| --- | --- |
|Start Jupyter| ```nohup jupyter-notebook --ip=123.123.123.123 --no-browser --port=8888 & ```  |
|Verify if notebook is running| ``` lsof nohup.out ``` |
|Close Jupyter | ```  killall --exact "jupyter-notebook" ``` |
|See running notebooks| ``` jupyter notebook list ```|


## Navigation with keyboard

|Command |Syntax  | 
--- | --- |
|Delete Cell|D+D|
|Create one line Above|A|
|Create one line Below|B|
|Markdown cell|M|
|Convert Markdown to code cell|Y|
|||

## Slide presentation

You can use your Jupyter Notebook as slide by using [Rise](https://rise.readthedocs.io). Just downloaded and restart your jupyter notebook. Once enabled, the RISE Jupyter extension displays a new button (“Enter/Exit Live Reveal Slideshow”) in the toolbar, (can be also activated with Alt-r by default).

![Image](https://res.cloudinary.com/edlitera/image/upload/c_fill,f_auto/v1670536357/blog/alwkzfx5kvrfqleta3gv "new button - Enter/Exit Live Reveal Slideshow")

To stablish which cells go on the slides, you must 

* Shortcuts:

    - Alt-r | Option-r, “Enter/Exit Live Reveal Slideshow”
    - SpaceBar to go forward
    - Shift-SpaceBar to go backwards
    - Shift-i®, Toggle slide
    - Shift-b, Toggle subslide
    - Shift-g, Toggle fragment
    

## Blogging

Using [Quarto](https://quarto.org/docs/get-started/hello/jupyter.html) to create a direct render of your notebook
- Installing dependencies in MacOS

```
python3 -m pip install jupyter jupyterlab
python3 -m pip install matplotlib plotly
```
Now open file: `python3 -m jupyter lab hello.ipynb`