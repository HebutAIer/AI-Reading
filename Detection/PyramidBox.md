# PyramidBox: A Context-assisted Single Shot Face Detector
## Contributions
1. PyramidAnchors, introduce supervised information on learning contextual features for small, blurred and partially occluded faces.
2. Low-level Feature Pyramid Networks (LFPN) to merge contextual features and facial features better. 
3. Context-sensitive prediction module, consisting of a mixed network structure and max-in-out layer to learn accurate location and classification from the merged features.
4. Data-anchor-sampling strategy to change the distribution of training samples to put emphasis on smaller faces.
5. Achieve superior performance over state-of-the-art on the common face detection benchmarks FDDB and WIDER FACE.
## PyramidBox
1. Scale-equitable Backbone Layers
    >1. The base convolution layers and extra convolutional layers is same as S3FD.
2. Low-level Feature Pyramid Layers
    >1. Build the low-level Feature Pyramid Network starting a top-down structure from a middle layer (Conv3_3-conv_fc7).
3. Pyramid Detection Layers 
    >1. Use L2 normalization to rescale the norm of LFPN layers.
4. Predict Layers
    >1. $w_l=h_l=640/2^{2+l}$
    >2. $c_l=20$
        1. 4 ($cp_l+cn_l$): classification of face: 
        $$ cp_l= 
    \begin{cases} 
    1,  & l=0\\ 
    3, & otherwise
    \end{cases} $$
        2. 4: localize of face
        3. 2: classification of head
        4. 4: localize of head
        5. 2: classification of body
        6. 4: localize of body
    >3. Context-sensitive Predict Modle: replace the convolution layers of context module in SSH by the residual prediction module of DSSD
    >4. PyramidAnchors: generate a series of anchors corresponding to large regions related to a face that contain more contextual information, such as head, shoulder and body.
        1. $label_k(anchor_{i,j})=
        \begin{cases}
        1, if\ iou\left(anchor_{ij}\cdot s_i/s_{pa}^{k},region_{target}> threshold\right)\\
        0, otherwise
        \end{cases}$ 
        2. $anchor_{ij}$: the j-th anchor at the i-th feature layer with stride size $s_i$
        3. $s_{pa}$ is the stride of pyramid anchors, default value is 2
        4. $anchor_{ij}\cdot s_i$: corresponding region in the original image of $anchor_{ij}$
        5. $anchor_{ij}\cdot s_i/s_{pa}^{k}$: corresponding down-sampled region by stride $s_{pa}^{k}$
        6. default value is 0.35
        7. $label_0, label_1, label_2$ are labels of face, head and body
        8. In prediction process, only face branch is used.
5. PyramidBox loss layers
6. Train dataset
    >1. WIDER FACE Training set
    >2. augmentation: 
        1. color distort
        2. random crop
        3. horizontal flip
7. Data-anchor-sampling
    >1. Randomly select a face of size $s_{face}$ in a sample, let $i_{anchor}=argmin_iabs(s_anchor_i-s_{face})$.
    >2. A random index $i_{target}$ is choosed in $\{0,1,...,max(5, i_{anchor}+1)\}$
    >3. The face is resized to $s_{target}=random(s_{i_{target}}/2, s_{i_{target}}*2)$
    >4. The image resize scale is $s^*=s_{target}/s_{face}$
    >5. Resize the original image with the scale $s^{*}$
