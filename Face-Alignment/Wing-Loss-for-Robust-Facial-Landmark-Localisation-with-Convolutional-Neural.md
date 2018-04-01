# Wing Loss for Robust Facial Landmark Localisation with Convolutional Neural Networks
## Contributions
1. Compare and analyse different loss functions including L2, L1 and smooth L1.
For the training of a CNN-based localisation model, more attention shoule be paid to small and medium range errors. 
2. A new loss amplifies the impact of errors from the interval by switching from L1 loss to a modified logarithm function.
3. Deal with the data imbalance problem by duplicating the minority training samples and perturbing them.
4. A two-stage framework for robust facial landmark localisation is proposed.
## 1. Introduction
1. One crucial aspect of deep learning is to define a loss function leading to better-learnt representation from underlying data.
2. L2 loss function is sensitive to outliers.
## 2. Related work
1. Network Architecture:
    >1. Regression based
    >2. FCN based
2. Dealing with Pose Variations:
    >1. Multi-view models
    >2. 3D face models
    >3. Multi-task learning
    >4. Treat the pose challenge as a training data imbalance
3. Cascaded Networks
    >1. Shape- and landmark-related features
    >2. Extract CNN features from local patches around facial landmarks
    >3. Coarse to fine
## 3. CNN-based facial landmark localisation
1. CNN-6: 
    >1. Input: $ 64 \times 64 \times 3 $
    >2. Output: 2L
    >3. Cov/MaxPool/Relu $ \times 5$  
    >4. Fc $ \times 2$
### 4. Wing loss
1. Analysis of different loss functions
    >1. $ L1\left(x\right)=|x| $
    >2. $ smooth_{L1}(n)= 
    \begin{cases} 
    \frac{1}{2}x^2,  & |x|<1\\ 
    |x|-\frac{1}{2}, & otherwise
    \end{cases} $
    >3. $ L2\left(x\right)=\frac{1}{2}x^2$
    >4. $wing\left(x\right)=
    \begin{cases}
    w ln\left(1+|x|/\epsilon\right), & |x|<w\\
    |x|-C, & otherwise
    \end{cases}
    $
    >>1. non-negative w sets the range of the nonlinear part to (-w, w).
    >>2. $\epsilon$ limits the curvature of the nonlinear region, and should not be set very small, to prevent gradient exploding.
    >>3. $C=w-w ln\left(1+w/\epsilon\right)$
    >>4. $w=10, \epsilon=2$ in the original paper
2. All loss functions perform well for large errors, so training of a neural network should pay more attention to the samples with small or medium range errors.
### 5. Pose-based data balancing
1. Resampling on the training set, to make the distribution is uniform on the PCA subspace
