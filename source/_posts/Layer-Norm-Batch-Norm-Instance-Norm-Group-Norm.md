---
title: Layer Norm | Batch Norm | Instance Norm | Group Norm
date: 2023-11-14 09:41:33
tags: Deep Learning
categories: Tech
---
<p align="center">
<img src="/img/norm/norm.png" width="100%" height="100%">
</p>

#### LB | BN | IN | GN in NLP
- **Batch Norm**: Normalization is performed for each batch, treating the vector of the same position in each sentence as a group.
- **Layer Norm**: Normalization is conducted across a single sentence or sentence-wise.
- **Instance Norm**: Each word's vector is treated as a group for normalization.
- **Group Norm**: The vectors of every few words in each sentence are treated as a group for normalization.
<!--more-->

<p align="center">
<img src="/img/norm/bn&ln.png" width="100%" height="100%">
</p>

##### Scenarios for Batch Norm and Layer Norm:
In terms of operations, BN targets all data within the same batch (resulting in as many means and variances as there are dimensions), while LN is aimed at a single sample (resulting in as many means and variances as there are in the batch size.).

- In ML/CV tasks, data often consists of each column representing a feature, and the processed data typically has interpretability, with different units and properties across columns, hence BN is more commonly used in machine learning tasks.
- In NLP, since data dimensions are generally [batch_size, seq_len, dim_size] (Like in Transformer, every sentence is with different length), and we ultimately aim to normalize the word vectors in a sentence, LN is more frequently used.

#### LB | BN | IN | GN in CV
Considering the feature map dimensions where H and W (height and width) are treated as one dimension, C (number of channels) as another dimension, and N (batch size) as a separate dimension:
- **Batch Norm**: Normalization is applied to the same layer of the feature map of each sample within a batch.
- **Layer Norm**: The entire feature map of each individual sample is normalized independently.
- **Instance Norm**:  Each layer of the feature map of each sample is normalized separately.
- **Group Norm**: A set of several layers of the feature map of each sample is normalized together.

##### Scenarios for Instance Norm and Group Norm:
- **Instance Norm**: it is particularly suited for style transfer tasks and image generations. Instance normalization helps maintain the individuality and unique style of each image.
- **Group Norm**: It does not depend on batch size, thus performs well in small batches or even single-sample (batch size=1) scenarios.  In tasks that require refined feature representation (i.e image segmentation and object detection), Group Norm can provide more stable training processes and better performance.

#### References ⭐
- [归一化方法 BN、LN、IN、GN、SN](https://blog.csdn.net/weixin_39910711/article/details/124403863)
- [Normalization之BN, LN, IN, GN](https://blog.csdn.net/weixin_43845922/article/details/129799681)