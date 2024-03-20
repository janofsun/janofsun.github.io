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
#### <font color=#6caa89>Abstract</font>

<p align="center">
<img src="/img/densenet/deep_densenet.png" width="100%" height="100%">
</p>

<!--more-->
DenseNet is detached from the stereotypical thinking of deepening the number of network layers (ResNet) and widening the network structure (Inception) to improve the performance of the network.

##### <font color=#6caa89>The Limitations</font>
DenseNet and other related networks all share a key characteristic: they create short path from early layers to later layers. There exists some limitations from its pioneering works.
- ResNet combines features through summation (additive identity transformations).
- Stochastic depth improves the training of deep residual networks by dropping layers randomly during training.
The major difference between DenseNet and related works is to use

##### <font color=#6caa89>Advantages</font>

- Improve flow of information and gradients through the network
    - Mitigate the gradient vanishing problem
- Fewer parameters
    - No need to relearn redundant feature-maps
    - DenseNet layer explicitly differentiates between new information and preserved information
    - Each layer is very narrow (e.g., 12 filters per layer)
- Regularizing effect to reduce overfitting

| Network | Advantage | Disadvantage |
| ---- | ---- | ---- |
| Summation (ResNet) | Parameter Efficiency <br> Improved Gradient Flow <br> Identity Shortcut | Feature Mixing <br> Capacity Limitation|
| Concatenation (DenseNet) | Feature Preservation <br> Enhanced Feature Propagation <br> Flexibility | Increased Computational Cost <br> Memory-intensive|

#### <font color=#6caa89>Structure</font>
In a summary, whereas traditional convolutional networks with $L$ layers have $L$ connections, the densenet network has $\frac{L(L+1)}{2}$ direct connections.

<p align="center">
<img src="/img/densenet/densenet_structure.png" width="100%" height="100%">
</p>

#### <font color=#6caa89>Memory-efficient Implementation</font>
The DenseNet enables 

<p align="center">
<img src="/img/densenet/memory-efficient-densenet.png" width="100%" height="100%">
</p>