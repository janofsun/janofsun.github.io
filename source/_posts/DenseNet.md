---
title: Intro to DenseNet
date: 2024-01-30 17:03:27
tags: 
- Deep Learning
- Paper
categories: 
- Tech
- Intro
---
#### <font color=#8f98c9>Abstract</font>

<p align="center">
<img src="/img/densenet/deep_densenet.png" width="100%" height="100%">
</p>

<!--more-->
DenseNet is detached from the stereotypical thinking of deepening the number of network layers (ResNet) and widening the network structure (Inception) to improve the performance of the network.

##### <font color=#8f98c9>The Limitations</font>
DenseNet and other related networks all share a key characteristic: they create short path from early layers to later layers. There exists some limitations from its pioneering works.
- <font color=#5a5fa3>ResNet</font> combines features through summation (additive identity transformations).
- <font color=#5a5fa3>Stochastic depth</font> improves the training of deep residual networks by dropping layers randomly during training.
    - This makes the state of ResNets similar to (unrolled) RNN.

The major difference between DenseNet and related works is to use concatenating features-maps.

##### <font color=#8f98c9>Advantages</font>

- Improve flow of information and gradients through the network
    - Mitigate the <font color=#e7cbe0>gradient vanishing</font> problem
- <font color=#e7cbe0>Fewer parameters</font>
    - No need to relearn redundant feature-maps
    - DenseNet layer explicitly differentiates between new information and preserved information
    - Each layer is very narrow (e.g., <font color=#e0cbe0>12</font> filters per layer)
- <font color=#e7cbe0>Regularizing</font> effect to reduce overfitting

| Network | Advantage | Disadvantage |
| ---- | ---- | ---- |
| Summation <br> (ResNet) | Parameter Efficiency <br> Improved Gradient Flow <br> Identity Shortcut | Feature Mixing <br> Capacity Limitation|
| Concatenation (DenseNet) | Feature Preservation <br> Enhanced Feature Propagation <br> Flexibility | Increased Computational Cost <br> Memory-intensive|

#### <font color=#8f98c9>Structure</font>
In a summary, whereas traditional convolutional networks with $L$ layers have $L$ connections, the densenet network has $\frac{L(L+1)}{2}$ direct connections.
Our basic **dense connectivity** can be represented as:
$$ x_l = H_l([x_0, x_1, ...., x_{l-1}])$$

<p align="center">
<img src="/img/densenet/densenet_structure.png" width="100%" height="100%">
</p>

- **<font color=#e7cbe0>Dense Block</font>**: To facilitate down-sampling within the architecture and to address concatenation issues arising from changes in the sizes of feature-maps between two blocks.
- **<font color=#e7cbe0>Composite function</font>**: $H_l(.)$ consists of three consecutive operations: batch normalization, ReLU, and a $3*3$ convolution.
    - The conv layer shown above corresponds the sequence BN-ReLU-Conv.
- **<font color=#e7cbe0>Transition Layer</font>**: it does convolution and pooling.

- **<font color=#e7cbe0>Bottleneck layers</font>**: a $1*1$ convolution can be introduced as *bottleneck layer* before each $3*3$ convolution to reduce the number of input feature-maps and to improve computational efficiency.
    - **DenseNet-B**: BN-ReLU-Conv($1*1$)-BN-ReLU-Conv($3*3$)

- Parameters:
    - There is a general trend that DenseNets perform better as $L$ and $k$ increase without compression or bottleneck layers.
    - **<font color=#e7cbe0>Growth Rate</font>**: The growth rate $k$ regulates how much new information each layer contributes to the global state.
    - **<font color=#e7cbe0>Compression</font>**: $0<\theta<1$ is referred to as the compression factor. It is set to 0.5 to improve model compactness which is to reduce the number of feature-maps at transition layer.
        - **DenseNet-BC**: both the bottleneck and transition layers with $\theta<1$ are used.

The following heatmaps shows the average absolute weight to serve as a surrogate for the dependency of a convolution layer on its preceeding layer.

<p align="center">
<img src="/img/densenet/feature_reuse_heatmap.png" width="100%" height="70%">
</p>

- The top row of each heatmap indicates that the transition layer outputs many redundant features.
- The right-most column shows that the weights of the transition layer also spread their weight across all layers within the preceding dense block.
    - The DenseNet-BC helps to alleviate this problem.
- The column shown on the very right suggests that there may be some more high-level features produced late in the network.

#### <font color=#8f98c9>Memory-efficient Implementation</font>

<p align="center">
<img src="/img/densenet/memory-efficient-densenet.png" width="100%" height="100%">
</p>

 #### References ⭐
 - [Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993)