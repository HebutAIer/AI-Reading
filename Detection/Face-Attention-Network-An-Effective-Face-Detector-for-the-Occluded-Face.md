# Face Attention Network: An Effective Face Detector for the Occluded Face
## Contribution
1. Anchor-level attention is proposed.
2. Achieve state of art
## 1. Face Attention Network
1. Base Framework
    1. Shallow layers usually have high spatial resolution, which is good for spatial localization of small objects, but low semantic information, which is not good for visual classification
    2. RetinaNet is attached with two subnets: one for classfiying and the other for regressing, All convolution layers in this subnet share parameters across all pyramid levels.
2. Attention Network
    1. Three design principles about Attention Network
        1. Addressing different scales of the faces in different feature layers.
        2. Highlighting the features from the face region and diminish the regions without face.
        3. Generating more occluded faces for training.
    2. Anchor Assign Strategy
        1. Five detector layers each associated with a specific scale anchor.
        2. The aspect ratio for our anchor is set as 1 and 1.5.
        3. More than 80% faces have an object scale from 16 to 406 pixel.
        4. Set anchor scale step to $2^{1/3}$, which ensure every ground-truth boxes have anchor with IOU > 0.6
        5. pos threshold is 0.5 and neg is 0.4.
    3. Attention Function
        1. The attention supervision information is obtained by filling the ground-truth box.
        2. Attention supervised information is associated to the ground-truth which the anchors at the current layer match. The hierarchical attention maps could decrease the correlation among them. 
        3. The attention maps are first feed to an exp operation and then dot with feature maps. It is able to keep more context information, and meanwhile highlight the detection information.
    4. Data Augmentation
        1. Randomly crop square patches from original images, whose range between [0.3, 1] of the short edge from the original images. and keep the overlapped part of the ground-truth box if its center is in the dampled patch.
        2. Random flip.
        3. Color jitter.
    5. Loss function
        1. Classification loss is focal loss.
        2. Regression loss is smooth L1 loss.
        3. Attention loss is pixel-wise sigmoid cross entropy.