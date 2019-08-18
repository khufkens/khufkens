---
layout: post
title:  "Image alignment techniques"
tags:
    - computer_vision
    - cs
    - image_processing
categories:
    - blog
---

I deal with a lot of image processing. This can be within the context of creating a large [composite of aerial photographs](http://cobecore.org/blog/gis/aerial_photographs/aerial-photography-referencing/), [correcting spherical images for lateral shifts](https://khufkens.com/2017/12/25/auto-align-time-series-of-spherical-images/), or [template matching tables](http://cobecore.org/blog/template-matching/) for data digitization and transcription.

However, a lot of these efforts rely in some way or shape in image alignment techniques. As such I thought it would be a nice to list some of the most common techniques in a small python script for further reference.

The python script, [hosted on github](https://github.com/khufkens/align_images), covers three different methods for calculating image registration / alignment transformation information and returning transformed images.

- FFT phase correlation
- Enhanced Correlation Coefficient (ECC) maximization
- Feature based registration

In this list, phase correlation is the simplest method and only calculates the translation (shift) of one image compared to the other. In contrast, the ECC methodology can compensate for both shifts, shifts + rotations (euclidean), shifts + rotation + shear (affine), or [homographic](https://en.wikipedia.org/wiki/Homography_(computer_vision)) (3D) transformations of one image to the next. The feature based methodology uses a homographic representation but uses features within the image rather than a correlation based method on the whole image to search for these values.

Depending on the complexity of the task a matching method should be chosen. In the github repo I've included a worked example on how to align two images taken by separate but closely located cameras (either on the same physical system or different ones). The worked example allows you to play around with some of your own data without too much coding. I hope this helps some with their image alignment problems.
