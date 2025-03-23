---
layout: post
title:  "Can AI help us find lost ancient woodland?"
author: EvieB
categories: [Blog]
tags: [Geospatial, AI, Machine Learning, Geography, Computer Vision]
image: assets/images/post-imgs/2024-09-07-Woodland-AI-1/woodland-cover.jpg
description: "Using Computer Vision to identify areas of ancient woodland."
featured: true
hidden: true
---

*Image Source: [Welsh Government](https://www.gov.wales/27-woodlands-join-national-forest-for-wales)*

## Using computer vision models on historic maps to identify areas of “lost” woodland in Wales.

Historic maps can provide valuable insights into how our landscape has changed over time. However, manually labelling this data is labour intensive and requires expert domain knowledge. The Data Science Unit has been investigating how computer vision models could be used to label large areas of historic maps quickly and accurately. In this blog, we present findings from the first pilot phase of the work, where we apply an image segmentation model to predict areas of woodland on OS historic landmark maps. 

### Background

There are numerous areas of Wales where woodland clearance has taken place, especially since the 19th century, these are areas of “lost historic woodland”. Areas of ‘lost historic woodland’ may be better suited for replanting and woodland creation schemes due to their remnant seed store and ecosystem. 

We have been working with CADW, the Welsh Government’s historic environment service, to create a national consistent dataset of areas of “lost historic woodland”. This will help support the delivery of key government targets for woodland creation, such as the National Forest for Wales, by increasing the confidence and certainty of woodland proposals in different regions.

### Method

Image segmentation models are a type of computer vision method which predict the location and identity of different objects in an image, based on a small sample of prelabelled images.
We used historic OS Survey Maps [Epoch 1] – 25 inch to create our training data. We split Wales into a grid of 200m x 200m tiles and selected a representative sample of 1,096 images to manually label (approximately 0.2% of all tiles in Wales). Figure 1 shows an area of the Cardiganshire historic county raster image split into three 200mx200m grids.

**Figure 1: Part of the base maps split in to 200x200m areas**
<img class="featured-image img-fluid" width="80%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood1.png" alt="A base map.">



The tiles were selected based on location (such as the historic county) and features (such as whether it’s located in a rural or urban area).   This ensured that our model could be trained on a range of tiles containing different landscapes including woodland, farmland, and built-up areas.

Our data contained a mix of large areas of woodland outlined by a clear boundary, sparsely spread smaller groups of tree symbols and lines of individual trees. Figure 2 shows the range of symbols used to represent different types of woodland on this map. 


**Figure 2: Some examples of symbols used to represent various types of woodland**
<img class="featured-image img-fluid" width="80%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood2.png" alt="3 examples showing different symbols used to denote different types of trees on the base maps.">
 
We labelled each sample tile using the map key as reference. We used open-source geospatial software (QGIS) to draw outlines around areas of the map containing symbols to capture different types of woodland in a polygon shape. Each polygon shape was given a label representing the type of woodland such as Fir, Orchards, and Osiers.

The first phase of work focused on training a single label model as a proof of concept. We used all types of manually labelled woodland areas, however changed their labels from the specific woodland type to the label “wood”. Plans to train a multi-label model to distinguish between different types of woodland is described in the final section of this blog.  

The sample of labelled data was split into a training (80%) and validation (20%) set. We trained a deep learning convolutional neural network (Mask R-CNN) on the training set with the default resnet50_fpn architecture. The model was trained to recognise features that corresponded to areas of woodland, such as the pattern, location, and intensity of the pixels. We used components available in the Azure Machine Learning Workspace to record and monitor the performance of the model when predicting the areas of woodland in the image compared to our manual labels. 

### Results

For each image, the model returns a series of bounding boxes to represent where an area of woodland was believed to exist, a set coordinates which depicts the exact location of the prediction and a probability score between 0-1, which is the likelihood the predicted area contains the label (“wood”). Predicted areas with a probability score less than the threshold 0.3 are removed. 

Figure 3 is an example of predicted areas of woodland on a single 200mx200m tile in Flintshire. It shows the model has successfully identified different areas woodland in the image, including bushes and mixed wood.

**Figure 3: Model predictions in one 200x200m area correctly predicting woodland**
<img class="featured-image img-fluid" width="60%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood3.png" alt="An example of predicted areas of woodland">
 
The primary metric used on the validation set (208 images) to optimise model performance was Average Precision (AP). This metric summarises the precision and recall scores for each validation tile as a single value.
Each prediction is labelled as “True Positive”, “False Positive” or “False Negative” depending on whether the label is correct, and the predicted area intersects the manually labelled area by a ratio of 0.5 or more (intersection over union (IOU)). 

A precision recall curve is then plotted by ranking each detection by its probability score and calculating the precision and recall values of the accumulated true positive or false positive detections across the whole validation dataset. The AP is the area under curve (AUC) of this precision recall graph. The best AP returned from the trained was 0.46 after 6 training epochs.

The model can predict multiple shapes for a single area of woodland, due to the different types of trees, as shown in Figure 4 of an area of Caernarfon. 

**Figure 4: Overlapping predictions for different classes of trees**

<img class="featured-image img-fluid" width="60%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood4.png" alt="An example of model predictions that overlap">

In this example, four predicted shapes cover a single area of woodland. When calculating the AP metric, only one prediction (with the highest intersection over union score) would be classed as True Positive, and the three other predictions would be classed as False Positive. 
 
To mitigate against this issue, we can apply post-processing steps to the model predictions. These steps involve combining overlapping and closely touching areas of woodland into single objects and removing objects that do not match the definition of “woodland”. The definition of woodland we have used is specified by Natural Resources Wales; where an area of woodland must have a minimum area of 0.5ha and minimum width of 20m.  

Table 1 shows the precision and recall scores of the new post-processed predictions, where a prediction is classified as “correct” or “incorrect” at different IOU thresholds. By combining multiple overlapping predictions from the model, we reduce the number of predictions counted as “false detections” and return the outputs in a more useable format. 

**Table 1: Precision and recall scores**

|IOU threshold|0.2|0.4|0.6|0.8|
|-----:|-----:|-----:|-----:|-----:|
|Precision|&nbsp; 0.97|&nbsp;    0.90|&nbsp;    0.90|&nbsp;    0.78|
|Recall|&nbsp; 0.85|&nbsp;    0.76|&nbsp;    0.76|&nbsp;    0.59|

<br>
However, these results show the model is less successful at predicting the exact shape of the woodland, as shown by the drop in precision and recall at higher IOU thresholds. This is understandable, as there was a level of uncertainty when manually labelling woodland in the images, particularly in areas where there was no clear boundary to guide the outlines that were manually drawn.   

### Application across Wales

We then applied the same model and post processing steps to predict areas of woodland on unseen data which we mapped to the original image to inspect the predictions. Our first version of the model has shown promising results, as shown in Figure 5. In this example, from an area in Llanerchymedd, Anglesey, model predictions represented in green. 

**Figure 5: An example of a model prediction for woodlands**
<img class="featured-image img-fluid" width="60%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood5.png" alt="An example of accurate predictions, woolands areas shown with accurate model prediction of wooded areas in green">
 
There are cases where the model mislabels other areas of the map as woodland, such as roads (that share many of the same characteristics as hedgerows), quarries, cliffs, and shorelines (Figure 6). Text on the map, which was often captured in the training data to name a forest or wood, has also been mislabelled as being an area of woodland by the model. We are working to mitigate the risk of misprediction by training with the model with additional data labelled as “shoreline”, “cliffs” and “contour lines”, and then removing these predictions from the final “woodland” dataset.   
 
 **Figure 6: an example of a mis-prediction by the model that has identified an area of cliffs as potential woodlands**
<img class="featured-image img-fluid" width="60%" height="auto"  src="{{ site.basurl }}/assets/images/post-imgs/2024-09-07-Woodland-AI-1/wood6.png" alt="Model mispredictions: cliffs identified as woodlands">

Due to the issues such as those described above, this method should not be used to calculate the total area coverage of ancient woodland in Wales, due to the likelihood of it being an over-estimate. However, this process would be suitable to assess the likelihood of an area containing woodland, based on the presence of any predictions, the size of the area predicted and confidence score.

### What’s next?

We are currently focusing on refining the model predictions by adding more training data of commonly mis-predicted areas, like coastlines, and tuning the parameters of the model. We will also compare the model predictions with other data sources, like the Ancient Woodland Inventory, to identify areas where woodland may have existed in the past, but no longer exists now, and therefore have been “lost” and could be suitable for replantation. Our next phase of work will then cover training a multi-labelled model and assessing the capability at identifying different types of woodland in the dataset. We look forward to sharing the next stage of work in a future blog!



    


  


