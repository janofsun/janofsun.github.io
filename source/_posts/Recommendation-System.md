---
title: Recommendation System
date: 2023-12-24 23:40:37
tags: 
- Machine Learning
- Grokking-the-ML-system-designs
categories: 
- [Tech]
- [Grokking-interviews, ML]
- [System-Design, ML]
---

#### <font color=#377f99>Problem Statement</font>
Display media recommendations for a specific user. Type of user feedback includes explicit and implicit feedbacks. The implicit feedback allows collecting a large amount of training data.
<!--more-->
#### <font color=#377f99>Metrics</font>
- **Online metrics**
    - Engagement rate
    - Videos watched
    - Session watch time (better than the above intuitively)
- **Offline metrics**
    - <font color=#f6a69c>mAP</font> (Mean Average Precision) @ N (length of the recommendation list)
        - $P = \frac{num\_of\_relevant\_recommendations}{total\_num\_of\_recommendations}$
        - $P @k = $proportion of all examples above that rank which are from the positive class. Precision represented at each cut off k.
        - $AP@N = \frac{1}{m}\sum_{k=1}^{N}P(k)*rel(k)$ where the $P(k)$ only contributes to $AP$ (average precision) if the recommendation at position $k$ is relevant.
    - <font color=#f6a69c>mAR</font> (Mean Average Recall) @ N
        - mAR @ N at a high-level, showes how well the recommender recalls all the items with positive feedback.
        - $r = \frac{num\_of\_relevant\_recommendations}{num\_of\_all\_possible\_relevant\_items}$
    - <font color=#f6a69c>F1 Score</font>
        - $F1 score = 2 * \frac{mAR*mAP}{mAP+mAR}$
    - For optimizing ratings
        - Optimize the recommendation system for getting the ratings (explicit feedback).
        - $RMSE = \sqrt{\frac{1}{N}\sum_{i=1}^N(\hat{y_i} - y_i)^2}$

#### <font color=#377f99>Architecture</font>
There are two stages, candidate generation, and ranking. The reason for two stages is to make the system **scale**.

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_2stages.png" width="80%" height="80%">
</p>

##### <font color=#377f99>Candidate Generation</font>
This component focuses on higher recall.

- Collaborative filtering
    - Nearest Neighborhood
        - Collaborate with similar users to generate candidate media for active user.
        - Caveat:
            - This process will be computationally expensive.
            - This sparsity of this matrix poses the cold start problem (New users ans movies).
    - Matrix factorization
        - Latent vectors (User profile matrix - $n*M$, Media profile matrix - $M*m$) can be thought of as features of a movie or a user.
    - It does not require domain knowledge to create profiles, thus, this filtering suffers from the cold start problem.

- Content-based filtering
    - Make recommendations based on the characteristics or attributes of the media the users have already interacted with.
    - Represent the media as vectors containing the TF of all the attributes; Normalize the TF by the length of the vectors; Multiply normed TF and IDF element-wise to make TF-IDF vectors.
        - Similarity with historical interactions
        - Similarity between media and user profiles
            - The user profiles can be built from historical interactions with media.
    - It requires some initial input from the user regarding their preferences.

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_TF_IDF.png" width="100%" height="100%">
</p>

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_TF_IDF_profile.png" width="100%" height="100%">
</p>

- Embedding-based similarity
    - The NNs allow us to use all the sparse and dense features.
    - Set up a two-tower model with one tower feeding in media only sparse and dense features and the other tower feeding in user-only sparse and dense features.
    - Loss function: $min(abs(dot(u, m) - label))$
    - The NN technique suffers from the cold start problem.

##### Training Data Generation
- Generating training samples
    - The data will be split as positive, negative, ot falls into the uncertainty bucket (watch time falls between 20%-80%).
- Balancing samples by randomly downsampling negative examples.
- Weighting training examples based on watch time or other features.
        - One caveat of utilizing weights is that the model might only recommend content with a longer watch time.
- Train test split: models should be built with the intent to forecast the future.

##### Ranker
This component focuses on higher precision.
- Logistic regression or random forest
    - Reasons
        - Training data is limited.
        - You have limited training and model evaluation capacity.
        - Model explainability is required.
        - An initial baseline is required.

- Deep NN with sparse and dense features
    - Reasons
        - Hundreds of millions of training examples and capacity should be available.
        - Model interpretability is not required.
    - Important sparse features
        - Videos that the user has previously watched
        - The user's search terms
        - The sparse features are list-wise (Averaged pooling before being fed to network). 

- Re-ranking: bringing diversity or past watches to the recommendation.

#### <font color=#377f99>Feature Engineering</font>

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_FE.png" width="100%" height="100%">
</p>

- User-based features
- Context-based features
- Media-based features
- Media-user cross features

#### <font color=#377f99>Example: Video Recommendation</font>
- **Problem statement**:
    - Build a video recommendation system for YouTube users.

- **Metrics**:
    - Offline：
    - Online:

- **Requirements**

| Type | Desired requirements |
| ---- | ---- |
| Training | High throughput with the ability to retrain many times per day |
| Inference | Latency from 100ms to 200ms<br>Balances between exploration vs. exploitation |

- **Model**

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_example_FE.png" width="100%" height="100%">
</p>

- **Calculation&&Estimation**
    - Assumptions:
        - Video views are $150$ billion per month
        - $10%$ of videos are recommended, $15$ billion
        - 
    - Data Size:
        - $500 $
    - Bandwidth: 
        - Generate recommendation requests for $10$ million users per second
        - Each request will generate ranks for $1k-10k$ videos.
    - Scale: $1.3$ billion users

- **High-level design**

<p align="center">
<img src="/img/ML_sys_design/recommender/RS_high_level_design_scale.png" width="100%" height="100%">
</p>