---
title: Machine Learning Primer
date: 2024-01-18 09:58:03
tags: 
- Machine Learning
categories: 
- [Tech]
- [System-Design, ML]
---

<p align="center">
<img src="/img/ML_sys_design/ML_primer.png" width="100%" height="100%">
</p>

<!--more-->
### <font color=#cb997e>Problem Framing</font>
ML systems can be divided into two broad categories: <u>predictive ML</u> and <u>generative AI</u>. At a high level, ML problem framing consists of two distinct steps:
- Determining whether ML is the right approach for solving a problem.
    - The non-ML solution is the <font color=#ddbea9>benchmark</font> you'll use to determine whether ML is a good use case
- Framing the problem in ML terms, and determining which features have <u>predictive power</u>.

#### <font color=#6b705c>Prediction</font>
- <u>Regression</u> models are unaware of product-defined thresholds. 
    - If your app's behavior changes significantly because of small differences in a regression model's predictions, you should consider implementing a <u>classification</u> model instead.
- Predict the final decision if possible
    - Hiding the app's behavior from the model can cause your app to produce the wrong behavior.
- Understand the problem's constraints
    - Dynamic thresholds: regression
    - Fixed thresholds: classification
- <font color=#ddbea9>Proxy labels</font> substitute for labels that aren't in the dataset. Note: All will have potential problems.

#### <font color=#6b705c>Generation</font>
- Distillation: To create a smaller version of a larger model
- Fine-tuning or parameter-efficient tuning
- Prompt engineering

### <font color=#cb997e>Feature Engineering</font>

#### <font color=#6b705c>One hot encoding</font>
- One hot encoding is used for <font color=#ddbea9>categorical</font> features that have medium cardinality.
- Problems:
    - <u>Expansive computation</u> and <u>high memory consumption</u> are major problems.
    - One hot encoding is not suitable for Natural Language Processing tasks.

#### <font color=#6b705c>Feature hashing</font>


##### <font color=#b8b7a3>Cross feature</font>
Crossed features / conjunction, <u>between two categorical variables</u> of cardinality c1 and c2.
- Crossed feature is usually used with a hashing trick to reduce high dimensions.

##### <font color=#b8b7a3>Embedding</font>
The purpose of embedding is to capture <font color=#ddbea9>semantic</font> meaning of features.
- $d = \sqrt[4]{D}$ where $D$ is the number of categories.
- Embedding features are usually pre-computed and stored in key/value storage to reduce inference latency.

##### <font color=#b8b7a3>Numeric features</font>
- Normalization
- Standardization

### <font color=#cb997e>Training Pipeline</font>
- Data partitioning
    - <font color=#ddbea9>Parquet</font> and <font color=#ddbea9>ORC</font> files are usually get partitioned by time for efficiency to speed up the query time.
- Handle imbalance class distribution
    - Use class weights in loss function
    - Use naive resampling
    - Use <font color=#ddbea9>synthetic resampling</font>
        - Synthetic Minority Oversampling Technique (SMOTE)
            - Randomly picking a point from the minority class
            - Computing the k-nearest neighbors for that point
            - The <font color=#ddbea9>synthetic points</font> are added between the chosen point and its neighbors.
- Choose the right loss function
- Retraining requirements

### <font color=#cb997e>Inference</font>
Inference is the purpose of using a trained machine learning model to make a prediction.
- Imbalanced Workload
    - Upstream process <i class="fa-light fa-arrow-right"></i> Aggregator Service <i class="fa-light fa-arrow-right"></i> Worker pool to pick workers; <font color=#ddbea9>Aggregator Service</font> can pick workers through following ways:
        - Work load
        - Round Robin
        - Request parameter
    - Serving logics and multiple models for business-driven system.
- Non-stationary problem
    - Update or retrain models to achive sustained performance. One common algo is [Bayesian Logistic Regression](https://quinonero.net/Publications/AdPredictorICML2010-final.pdf).
- Exploration vs. exploitation
    - One common technique is Thompson Sampling where at a time, t, we need to decide which action to take based on the reward.

### <font color=#cb997e>Metrics</font>
- Offline metrics
    - Metrics like MAE, or R2 to measure the goodness of of  fline fit.
- Online metrics
    - Expose the model to a specific percentage of real traffic.
    - Allocate traffic to different models in production.
    - <font color=#ddbea9>A/B testing</font> (examples)
        - [Amazon SageMaker](https://aws.amazon.com/blogs/machine-learning/a-b-testing-ml-models-in-production-using-amazon-sagemaker/)
        - [Linkedin A/B testing](https://engineering.linkedin.com/blog/2015/11/top-five-lessons-from-running-a-b-tests-on-the-world-s-largest-p)

<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.0.13/css/all.css">