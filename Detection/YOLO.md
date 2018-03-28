# YOLO
## 1. You Only Look Once: Unified, Real-Time Object Detection
### Abstract
1. We frame object detection as a regression problem to spatially separated bounding boxes and associated class probabilities.
2. A single neural network predicts bounding boxes and class probabilities directly from full images in one evaluation.
### Introduction
1. YOLO has several benefits over traditional methods:
    >1. YOLO is extremely fast. 
    >2. YOLO reasons globally about the image when making predictions. Unlike sliding window and region proposal-based techniques. YOLO sees the entire image during training an test time so it implicitly encodes contextual information about classes as well as their appearance.
    >3. YOLO learnings generalizable representations of objects. It's less likely to break down when applied to new domains or unexpected inputs
### Unified Detection
1. Divides the input image into an $S\times S$ grid 
    >1. If the center of an object falls into a grid cell, that grid cell is responsible for detection that object
2. Each grid cell predicts B bounding boxes and confidence scores for those boxes.
    >1. (x,y): the center of the box relative to the bounds of the grid cell?????
    >2. (w,h): predicted relative to the whole image
    >3. confidence: IOU between the predicted box and any ground truth box
3. Each grid cell also predicts C conditional class probabilities. 
    >1. In this paper only predict one set of class probabilities per grid cell, regardless of the number of boxes B
#### Network Design
1. The network has 24 convolutional layers followed by 2 fully connected layers.
2. The final output of our network is the $7 \times 7 \times 30$ tensor of predictions
#### Training
1. The network is trained for approximatly a week and achieve a single crop top-5 accuracy of 88% on the ImageNet 2012 validation set.
2. Four convolutional layers and two fully connected layers with randomly initialized weights is added on top of the pretrained model.
3. Increase the input resolution of the network from $244 \times 244$ to $448 \times 448$.
4. A linear activation function is used for the final layer and all other layers use the following leaky rectified linear activation.
5.  Sum-squared error is used in the output of the model
    >1. Because Sum-squared error is easy to optimize, however it does not perfectly align with our goal of maximizing average precision.
    >2. It weights localization error equally with classification error which may not be ideal.
    >3. In every image many grid cells do not contain any object. This pushes the confidence scores of those cells towards zero. To remedy this, writer increase the loss from bounding box coordinate predictions and decrease the loss from confidence predictions for boxes that don't contain objects. $\lambda_{coord} = 5, \lambda_{noobj}=0.5$
    >4. Sum-squared error also equally weights errors in large boxes and small boxes. To partially address this we predict the square root of the bounding box width and height instead of the width and height directly
6. A predictor which has the highest current IOU with the ground truth, is selected to optimize。**This leads to specialization between the bounding box predictors**
7. The loss function only penalizes classification error if an object is present in that grid cell. It also only penalizes bounding box coordinate error if that predictor is responsible for the ground truth box.
8. Parameter:
    >1. epoch: 153
    >2. batch: 64
    >3. momentum: 0.9
    >4. decay: 0.0005
    >5. lr: 1: $10^{-3}$, 75: $10^{-2}$, 30: $10^{-3}$, 30: $10^{-4}$
    >6. dropout: 0.5
    >7. data augmentation: 
        >>1. random scaling and translations of up to 20%
        >>2. random adjust the exposure and saturation of the image by up to a factor of 1.5 in the HSV color space.
#### Limitations of YOLO
1. Imposes strong spatial constraints on bounding box predictions since each grid cell only predicts two boxes and can only have one class.
2. Struggles to generalize to objects in new or unusual aspet ratios or configurations
3. Using relatively coarse features for predicting bounding boxes since the architecture has multiple downsampling layers from the input image.
4. The loss function treats error the same in small bounding boxes versus large bounding boxes. A small error in a large box is generally benign but a samll error in a small box has a much greater effect on IOU
