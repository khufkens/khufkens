---
layout: post
title:  "Data science environment gripes"
tags:
    - computer_vision
    - data science
    - cs
    - image_processing
categories:
    - blog
---

I've been working on data science and deep learning problems for a while in both R and python. Yet, the python environment is prone to package conflicts, and the larger your install base the higher the likelihood of something being in conflict with, or not compatible, with something else.

So, in order to get done what I set out to do I've recently been forced to reconsider my workflow and move away from using the global environment, in favour of python virtual environments.

The process is nicely explained in the [TensorFlow installation instructions](https://www.tensorflow.org/install/pip). You can basically take it from there and install keras in the same environment as well as all dependent packages. After setting up a virtual environment installing packages is as easy as:

```bash
# Keras
pip install keras-gpu

# Tensorflow
pip install tensorflow-gpu

# opencv
pip install opencv-contrib-python

# etc
```


My gripe with this is that it diverges from what I'm used to in the R environment. There, the highly curated package system generally causes few, if not any, conflicts. Although the curation by CRAN is extensive, and development of official packages rather "rough" it does pay when considering the issues which pop up in python.

I'll probably use the R implementation of Keras moving forward, but for some problems which require a dedicated workflow virtual environments seem the only way to go. So for now, virtual environments (and potentially docker setups) will be part of the workflow.