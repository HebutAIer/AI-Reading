# Seeing Small Faces from Robust Anchor's Perspective
## Contribution
1. Provide an in-depth analysis of the anchor matching mechanism under different conditions with the newly proposed Expected Max Overlap score to theoretically characterize anchors' ability of achieving high face IOU scores.
2. Propose several effective techniques of new anchor design for higher IOU scores especially for tiny faces:
    1. Reduce the anchor stride with various network architecture designs.
    2. Add anchors shifted away from the canonical center so that the anchor distribution becomes denser.
    3. Stochastivally shift the faces in order to increase the chance of getting higher IOU overlaps.
    4. Match low-overlapped faces with multiple anchors.
3. Achieve state-of-the-art performance on Wider Face, AFW, PASCAL Faces and FDDB with competitive runtime speed.
## 1. Introduction
1. Anchor-based detectors quantize the continuous space of all possible face bounding boxes on the image plane into discrete space of a set of pre-defined anchor boxes that serve as references.
2. It's not surprising to find that average IOUs across face scales are positively correlated with the recall rates.
3. Anchor boxes with low IOU overlaps with small faces are harder to be adjusted to the ground-truth, reulting in low recall of small faces.
## 2. Expected Max Overlapping Scores
$$ EMO= \int_{0}^{\frac{s_A}{2}}\int_{0}^{\frac{s_A}{2}}\left(\frac{2}{s_A}\right)^2\frac{(l-x')(l-y')}{2l^2-(l-x')(l-y')}dx'dy'$$
1. l: the size of face and anchor.
2. $s_A=\frac{W}{w}=\frac{H}{h}$: anchor stride. the smaller $s_A$ the higher EMO score achieves.
3. $\frac{s_A}{2} \times \frac{s_A}{2}$: centered on the center of the anchor, is the region where the IOU between the anchor and face is max.
4. (x',y'): the distence between the center of anchor and face.
## 3. Strategies of New Anchor Design
1. Increase the average IOU by:
    1. Reducing anchor stride
        1. Stride Reduction with Enlarged Feature Maps.
            1. Bilinear upsamplnig upscales the feature map by a factor of 2.
            2. Upscaled feature map augmented with the features from shallower large feature map by skip connection.
            3. Dilated convolutions
        2. Redefined the anchor locations such that the anchor stride can be further reduced.
            1. These shifted anchors share the same feature representation with the anchors in the centers.
    2. Reducing the distance between the face center and the anchor center
        1. Training samples are randomly shifted in each iteration during training. The offset $dx,dy \in \{0, 1, ..., s_A/2-1\}$, which is the integrating region of EMO 
    3. Hard Face Compensation
        1. Label anchors as positive if their overlapping scores with faces are higher than a threshold T.
        2. Faces whose highest overlapping scores are below T are the hard faces.
        3. The top N anchors with highest overlap with them are labeled as positive.