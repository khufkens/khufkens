---
layout: post
title:  "Finding empty cells in tables"
tags:
    - data_recovery
    - digitization
    - citizen_science
    - meta-data
categories:
    - blog
---

I previously outlined how dealing with +70K scans in the COBECORE project presents an inssue when it comes to processing and extracting data. Due to template matching a large part of these issues have been automated away. Yet, even when the data can be extracted one hurdles remains, empty cells in table.

Yet the digitized tables are sparse. This means that the bulk of the data in the tables consists of empty table cells, while the remaining part is true valuable data. Since transcription will rely on a pair of human eyes evaluating every single cell of data it is obviously a waste of time to review what are empty cells. A solution has to be found to quickly and accurately screen these empty cells, and remove them from the final data set to limit the workload (not wasting time of volunteers).

## Tensorflow transfer learning

During a [previous project](https://github.com/khufkens/TF_transfer_learning) transfer learning, based on the [Tensorflow framework](https://www.tensorflow.org/), provided a fast solution for a simple classification task. Transfer learning is a way of rapidly training a complex image processing model by leveraging the efforts of previous researchers. Previous research groups have created models which are tuned using a large selection (many millions) of labelled images. This model therefore includes a fairly good representation of what you might encounter, and want to label, in the real world. In transfer learning I use this existing model and tune it further to a specific use case. This is often far faster than creating your own model, which also requires vast amounts of data. 

I use a transfer learning approach to classify cells of the digitized tables as either *empty* or *complete* (Fig 1.). To do this the cells of 3 tables with varying handwriting or typed numbers were extracted using the template matching approach, as previously described. This left me with training data of ~1400 cell values (split evenly among the two types). This data was used to retrain the model for our use case.

<center>
{% include _image.html url="/images/documentation/cell_values.jpg" description="Fig. 1 - An empty and complete table cell value. &copy; State Archive (COBECORE)" %}</center>

After retraining the model had an accuracy of **~98%**. For the task at hand this is sufficient, as additional screening based upon column wide statistics will be made. A visualization of the classification results of one particular table are given below (Fig 2.). Here, light blue pixels represent those of the template used, red/pink pixels represent those of the matched table, blue pixels show agreement between the template and the matched table and, finally, **white crosses indicate empty cells** as predicted by our Tensorflow model.

In the below table you see only few misclassified cells. In particular you can find one false positive, claiming to be empty when it is not, and six false negatives, where empty cells are not flagged. With over 400 values in the table and an accuracy of ~98% having an error rate of seven values is roughtly what you might expect.

{% include _image.html url="/images/documentation/alignment_preview.jpg" description="Fig. 2 - Cells classified as empty marked with a white X. &copy; State Archive (COBECORE)" %}
