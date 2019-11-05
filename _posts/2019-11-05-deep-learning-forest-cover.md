---
layout: post
title:  "Deep learning forest cover"
teaser: "annotating maps the fast and easy way"
tags:
    - computer_vision
    - data science
    - cs
    - image_processing
categories:
    - blog
---

Within my COBECORE project I promised to digitize and map past forest cover. This task has been finished, but a thorough analysis of this data would add a lot of value in terms of the disturbance history of the forests around Yangambi.

However, the area covers roughly a 35x40km outline at ~1m resolution is hard to manually segment this forest cover and disturbed areas (plantations, build areas, agriculture). In a previous version of the orthomosaic I've done this excercise.  However it can be argued that this analysis is biased, if not due to the size of the brush used to infill the classes.

In order to side-step this issue, and increase reproducibility, I decided to tackle it using deep learning. I choose to go down this path in part because the problem at hand resembles standard supervised texture segmentation, a common computer vision issue, which has become trivial with deep learning.

My approach went through a number of iterations, not in the least because I wanted to limit my workload. This workload, in particular generating a deep learning training set, although not as large as a full segmentation of the map, can still be signficant. This is due do to the fact that deep learning is a supervised methodology which requires tons of training data to learn what forested or disturbed areas look like.

In my first approach I outlined a number of areas which were homogenous with respect to a particular class (forested, disturbed / non-forest). Within this region I randomly sampled small images (513x513 pixels). By default I knew what was covered and I could generate ground truth labels for training a deep learning network with only two masks.

## Large scale representation issues

When using this initial dataset accuracy stalled at roughly 60% Intersect over Union (IoU, a metric of segmentation accuracy). When inspecting the data it was obvious that for homogenous tiles the deep learning network performed well. However, it performed poorly for those instances when two classes would mix in a given "scene". The model made an implicit assumption based upon the size and overall content of the small images in training, rather than evaluating every pixel (and surrounding texture) in an image.

This effect can be seen in the below image where the edges between forest and disturbed (lighter bright pink) patches are surrounded by a large area in which there is uncertainty (purple tints). This uncertainty also takes on the shape of the square (moving) window used during evaluation of the scene.


![](http://cobecore.org/images/documentation/segmentation_1.png)

Acknowledging this issue I decided to add image augmentation on top of the binary labelled training data. I created mash ups of the binary labelled datasets, combining forested and disturbed images using a random gaussian field mask (see below). These artificial scenes provide the algorithm transition states between the forest and disturbed patches without having to manually segment those, a time intensive task.

![](http://cobecore.org/images/documentation/train.png)

## Synthetic landscapes to sidestep manual segmentation

Using these artificial scenes the algorithm IoU metrics jumped to 92%, very good for a segmentation task. Evaluating the same scene as above shows this improvement with mapping finer detail in the disturbances (pink) and fewer broad areas of uncertainty (purple). In short, transitions between forested and disturbed areas are better detected, resulting in sharper edges.

![](http://cobecore.org/images/documentation/segmentation_2.png)

When evaluating the whole map (below) performance is indeed in line with the validation results. The majority of the surface area is correctly classified. However, exceptions to this rule exist. In particular stitch lines between different images used to create the orthomosaic are incorrectly labelled as a disturbance. Arguably, this indeed represents a sort of disturbance, but not one related to the true structure of the forest. More data augmentation is needed to address this issue. In particular I combined two forest tiles to combine different texture of forest, an issue which due to acquisition date differences causes issues along stitch lines.

After this final correction, classification accuracy increases to 96% IoU accuracy, while early stopping the training process (some gains are still possible by increasing the training duration still). I expect that the final classification accuracy should reach ~98% IoU, an extremly high value. 

![](http://cobecore.org/images/documentation/yangambi.png)