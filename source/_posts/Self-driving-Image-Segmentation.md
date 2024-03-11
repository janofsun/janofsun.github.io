---
title: Self-driving Image Segmentation
date: 2024-01-17 09:20:17
tags: 
- Machine Learning
- Grokking-the-ML-system-designs
categories: 
- [Tech]
- [Grokking-interviews, ML]
- [System-Design, ML]
---

#### <font color=#90bee0>Problem Statement</font>
Design a self-driving car system focusing on its perception component (semantic image segmentation in particular)
<!--more-->
Subtasks
- Object detection
- Semantic segmentation
    - Semantic segmentation can be viewed as a pixel-wise classification of an image.
- Instance segmentation
    - It combines object detection and segmentation to classify the <u>pixels of each instance</u> of an object.
- Scene understanding
- Movement plan

#### <font color=#90bee0>Metrics</font>
- **Component level metric**
    - <font color=#e1df92>Goal</font>: Higher pixel-wise accuracy for objects belonging to each class.
    - <font color=#e1df92>IoU</font> (Intersection over Union) 
        - This will be used as the offline metrix.
        - $IoU = \frac{|P_{pred}\cap P_{gt}|}{|P_{pred}\cup P_{gt}|}$
            - <font color=#fc8c5a>“area of overlap”</font>: means the number of pixels that belong to the particular class in both the prediction and ground-truth
            - <font color=#fc8c5a>“area of union”</font> refers to the number of pixels that belong to the particular class in the prediction and in ground-truth, but not in both (the overlap is subtracted).
        - The <font color=#fc8c5a>mean IoU</font> is calculated by taking the avaerage of the IoU for each class

- **End-to-end metric**
    - Manual intervention
    - Simulation errors

#### <font color=#90bee0>Architecture</font>
- **Overall architecture** for self-driving vehicle
    - The object detection CNN detects and localizes all the obstacles and entities
        - <font color=#fc8c5a>Drivable region detection CNN</font>: Action predictor RNN is information that allows it to extract a drivable path for the vehicle.
        - <font color=#fc8c5a>Semantic image segmentation</font> (from raw pixel-wise boundaries)

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_high_level.png" width="100%" height="100%">
</p>

- **System architecture for semantic image segmentation**
    - The real-time driving images are captured and manually given pixel-wise labels.

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_system_sis.png" width="100%" height="100%">
</p>

#### <font color=#90bee0>Training Data Generation</font>
- Human-labeled data
- Open Source dataset
- Training data enhancement through [GANs](https://janofsun.github.io/2023/12/01/Intro-to-GAN/)
    - Generating new training images
    - Ensuring generated images have different conditions (e.g. weather and lighting conditions)
        - Image-to-image translation (cGANs)

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_cgans.png" width="100%" height="100%">
</p>

- Targeted data gathering

#### <font color=#90bee0>Modeling</font>
- **SOTA segmentation models**
- <font color=#e1df92>FCN</font>
    - Segmentation is a dense prediction task of pixel-wise classification.
    - Major characteristics
        - <font color=#fc8c5a>Dynamic input size</font>: the fully connected layers are replaced by convolutional layers at the end of the regular convolution and pooling process.
        - <font color=#fc8c5a>Skip connection</font>: the initial layers capturing good edges are connected with the coarse pixel-wise segmentations.

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_skip_connections.png" width="100%" height="100%">
</p>

- <font color=#e1df92>U-Net</font>
    - It is built upon FCN and commonly used for semantic segmentation-based vision applications.
    - <font color=#fc8c5a>Downsampling</font> increases info about what objects are present, decreases info about where objects are present.
    - <font color=#fc8c5a>Upsampling</font> creates high-resolution segmented output by making use of skip connections.

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_unet.png" width="100%" height="100%">
</p>

- <font color=#e1df92>Mask R-CNN</font>
    - It is used for <font color=#fc8c5a>instance segmentation</font>.
    - Faster R-CNN for object detection and localization and FCN for pixel-wise instance segmentation of objects.
        - Backbone of a CNN followed by a <font color=#fc8c5a>Feature Pyramid Network (FPN)</font> which extracts feature maps at different scale.
        - The feature maps are fed to the <font color=#fc8c5a>Region Proposal Network (RPN)</font>.
        - These proposals are fed to the <font color=#fc8c5a>RoI Align layer</font> that extracts the corresponding ROIs (regions of interest) from the feature maps to align them with the input image properly.
        - The ROI pooled outputs are fixed-size feature maps that are fed to parallel heads of the Mask R-CNN.
    - Mask R-CNN has <font color=#fc8c5a>three parallel heads</font> to perform Classification, Localization, and Segmentation.

<p align="center">
<img src="/img/ML_sys_design/sdis/sdis_maskrcnn.png" width="100%" height="100%">
</p>

- **Transfer learning**
    - <font color=#e1df92>Retraining topmost layer</font>
        - Update the final pixel-wise prediction layer in the pre-trained FCN
        - This approach makes the most sense when
            - The data is limited.
            - You believe that the current learned layers capture the information that you need for making a prediction.
    - <font color=#e1df92>Retraining top few layers</font>
        - Update the upsampling layers and the final pixel-wise layer.
        - This approach makes the most sense when
            - Have a medium-sized dataset
            - Shallow layers generally don’t need training because they are capturing the basic image features, e.g., edges
    - <font color=#e1df92>Retraining entire model</font>
        - Laborious and time-consuming
        - The dataset has completely different characteristics from the pre-trained network