# Feature Pyramid Networks for Object Detection
## Abstract
1. Recent deep learning object detectors have avoided pyramid representations, in part because they are compute and memory intensive.
2. In this paper, the inherent multi-scale pyramidal hierarchy of deep convolutional networks is exploited to construct feature pyramids with marginal extra cost.
3. A top-down architecture with lateral connections is developed for building high-level semantic feature maps at all scales.
## Introduction
1. Feature pyramids and image pyramids are scale-invariant in the sense that an object's scale change is offset by shifting its level in the pyramid.
2. Featuring each level of an image pyramid has obvious limitations:
    1. Inference time increases considerably.
    2. Training deep networks end-to-end on an image pyramid is infeasible in terms of memory.
3. A deep ConvNet computes a feature hierarchy layer by layer, and with sub-sampling layers the feature hierarchy has an inherent multiscale, pyramidal shape, but introduces large semantic gaps caused by different depths.
    1. high-resolution maps have low-level features that harm their representational capacity for object recognition.
4. SSD misses the opportunity to reuse the higher-resolution maps of the feature hierarchy, but such maps are important for detecting small object.
5. The goal of this paper is to naturally leverage the pyramidal shape of a ConvNet's feature hierarchy while creating a feature pyramid that has strong semantics at all scales. To achieve this goal, low-resolution, semantically strong features are combined with high-resolution, semantically weak features wia a top-down pathway and lateral connections.
6. The FPN can be trained end-to-end with all scales and is used consistently at train/test time, which would be memory-infeasible using image pyramids.
## Feature Pyramid Networks
1. The method takes a single-scale image of an arbitrary size as input, and outputs proportionally sized feature maps at multiple levels, in a fully convolutional fashion. The FPN involves three components as introduced in the following.
#### Bottom-up pathway
1. The bottom-up pathway is the feed-forward computation of the backbone ConvNet.
    1. The layers, producing output maps of the same size, are called a stage.
    2. One pyramid level is defined in each stage.
    3. The output of the last layer of each stage as the reference set of feature maps.
    4. The first stage is not include into the pyramid due to its large memory footprint.
#### Top-down pathway and lateral connections
1. Higher pyramid levels are upsampled and merged with feature maps of the same spatial size from the bottom-up pathway.
2. A $3 \times 3$ convolution on each merged map to generate the final feature map.
3. All levels of the pyramid use shared classifiers as in the traditional featurized image pyramid, so the feature dimension in all the feature maps is fixed to d=256.
## Applications
1. FPN for RPN
    1. Attach a head of the same design ($3\times3$ conv and two sibling $1\times1$ conv) to each level on FPN.
    2. Anchors of a single scale is assigned to each level.
2. FPN for Fast R-CNN
    1. ROIs of different scales is assign to the pyramid levels.
    2. An ROI of width w and heigh h is assigned to level $P_k$ of feature pyramid by $k=\lfloor k_0+log_2{(\sqrt{wh}/244)}\rfloor$, which means if the ROI's scale becomes smaller, it should be mapped into a finer-resolution level.
    3. The predictor heads is attached to all ROIs of all levels, and the heads all share parameters, regardless of their levels.
## Implement Detail by tf
1. image_features = self._resnet_base_fn(image_input)
2. image_features = self._filter_features(image_features)
3. image_features.append(conv(image_features[-1], (3,3)))
4. image_features.append(conv(image_features[-1], (3,3))
5. feature_maps = feature_map_generators.fpn_top_down_feature_maps(image_features, depth)
    1. top_down = conv(image_features[i+1], (1,1), depth)
    2. top_down = nearest_neighbor_upsampling(top_down, 2)
    3. residual = convl(image_features[i], (1,2), depth)
    4. top_down = 0.5 * top_down + 0.5 * residual 
    5. pyramid_layer = slim.conv2d(top_down, depth, (3, 3))

#Focal Loss for Dense Object Detection
## Abstract
1. The accuracy of one-stage detectors is lower than two stage detectors, because the extreme foreground-background class imbalance encountered during training of one-stage detectors, 
2. Class imbalance is addressed by reshaping the standard cross entropy loss such that it down-weights the loss assigned to well-classified esamples.
## Introduction
1. While hard example mining also be applied to one-stage detector, they are inefficient as the training procedure is still dominated by easily classified background examples.
2. This paper propose a new loss function where the scaling factor decays to zero as confidence in the correct class increases.
3. Generally speaking, the one-stage detectors evaluate $10^4-10^5$ candidate locations per image but only a few locations contain objects. This imbalance causes two problems:
    1. training is inefficient as most locations are easy negatives that contribute no useful learning signal.
    2. the easy negatives can overwhelm training and lead to degenerate models.
4. A new loss, called focal loss:
    1. can efficiently train on all examples without sampling.
    2. without easy negatives overwhelming the loss and computed gradients.
## Focal Loss 
1. Cross entropy:
    $$ CE(p,y)= 
    \begin{cases} 
    -\log(p),  & if y=1\\ 
    -\log(1-p), & otherwise
    \end{cases} $$
2. The probability of the groundtrue label predicted by classifier$p_t$:
    $$ p_t= 
    \begin{cases} 
    p,  & if y=1\\ 
    1-p, & otherwise
    \end{cases} $$
3. Rewrite $CE(p,y)=CE(p_t)=-\log(p_t)$
#### Balanced Cross Entropy
1. A common method for addressing class imbalance is to introduce a weighting factor $\alpha\in [0,1]$ for class 1 and $1-\alpha$ for class -1, which balance the importance of positive/negative examples.
2. In practice $\alpha$ may be set by inverse class frequency or treated as a hyperparameter to set by cross validation.
3. $\alpha$ is defined analogously to how to defined $p_t$
#### Focal Loss Definition
1. Focal loss is defined as $FL(p_t)=-\alpha_t(1-p_t)^{\gamma}\log(p_t)$.
2. When an example is misclassified and $p_t$ is small, $(1-p_t)^{\gamma}$ is near 1 and the loss is unaffected.
3. The focusing parameter $\gamma$ smoothly adjusts the rate at which easy examples are downweighted.
4. $(1-p_t)^{\gamma}$ reduces the loss contribution from easy examples and extends the range in which an example receives low loss.
5. The implementation of the loss layer combines the sigmoid operation for computing p with the loss computation, resulting in greater numerical stability.
#### Class Imbalance and Model Initialization
1. Introduce the concept of a prior for the value of p estimated by the model for the rare class at the start of training, which can improve training stability for both the cross entropy and focal loss in the case of heavy class imbalance.
2. For example, set the prior so that the model's estimated p for examples of the rare class is low.
## RetinalNet Detector
#### Implementation Details
1. FPN Backbone
2. Anchor: as each anchor is assigned to at most one object box, we set the corresponding entry in its length K label vector to 1 and all other entries to 0.
3. Classification/BoxRegression Subnet:   
    1. Parameters of this subnet are shared across all pyramid levels. 
    2. The two subnet sharing a common structure, use separate parameters.
    3. The subnet contains 4 conv layers.
#### Inference and Training
1. To improve speed, only at most 1k top-scoring predictions with thresholding above 0.05, are decoded per FPN level
2. $\alpha$ should be decreased slightly as $\gamma$ is increased, for $\gamma=2,\alpha=0.25$ works best.
3. The prior is set to 0.01. This initialization prevents the large number of background anchors from generating a large, destabilizing loss value in the first iteration of training.

## Implement Detail by tf
    if self.param.focalType.lower() == "crossentropy":
        faceLabelLoss = tf.nn.sigmoid_cross_entropy_with_logits(labels=faceLabelGT, logits=faceLabelResult)
        prediction_probabilities = tf.sigmoid(faceLabelResult)
        p_t = ((faceLabelGT * prediction_probabilities) + ((1 - faceLabelGT) * (1 - prediction_probabilities)))
        modulating_factor = tf.pow(1.0 - p_t, gamma)
        alpha_weight_factor = (faceLabelGT * alpha + (1 - faceLabelGT) * (1 - alpha))
        faceLabelLoss = tf.reduce_mean(faceLabelLoss, axis=-1, name=self.name + "_FaceLabel")
    elif self.param.focalType.lower() == "softmax":
        faceLabelLoss = tf.nn.softmax_cross_entropy_with_logits_v2(labels=faceLabelGT, logits=faceLabelResult)
        prediction_probabilities = tf.nn.softmax(faceLabelResult)
        p_t = tf.reduce_max(faceLabelGT * prediction_probabilities, axis=-1)
        modulating_factor = tf.pow(1.0 - p_t, gamma)
        alpha_weight_factor = tf.reduce_sum(faceLabelGT * tf.convert_to_tensor(alpha), axis=-1)
        faceLabelLoss = tf.multiply(modulating_factor, alpha_weight_factor * faceLabelLoss, name=self.name + "_FaceLabel")