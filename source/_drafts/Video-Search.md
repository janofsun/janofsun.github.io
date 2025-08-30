---
title: YouTube Video Search
date: 2025-07-26 18:11:03
tags: 
- Machine Learning
categories: 
- [Tech]
- [System-Design, ML]
---

#### <font color=#377f99>Problem Statement</font>
Design a video search system that can efficiently handle billions of videos.
<!--more-->

**Clarifying Requirements**
1. Input format?
    - Text queries only
2. Assumption of video relevancy
    - The relevancy is determined by its visual content and the textual data associated with the video (e.g. title, description, tags ..)
3. Assumption of training dataset
    - [video, text quert]
4. Personalization?
   - Not essentially necessary. This is a searching system focusing on video relevancy

**Frame the Problem as an ML task**
- Define the ML Objective
    - Rank videos based on their relevance to the text query.
- Define the input and the output
    - input: text-only query
    - output: a list of videos sorted by their relevancy

**Choosing ML methology**
- Visual search: using video encoder to encode videos/frames into embeddings
- Text search: text encoder

**Data Preparation**
Let's assume the dataset in the following structure:

<p align="center">
<img src="/img/ML_sys_design/video-search/dataset.png" width="80%" height="80%">
</p>

- Feature Engineering for text data
  - Text Normalization
      - Lowercasing, Punctuation removal, Trim whitespaces, Strp Accents ..
  - Tokenization
    - Statistical methods
    - ML-model based methods

- Feature Engineering for video data
    - video -> Decode frames -> Sample frames -> Resizing -> Scaling, normalizing, and correcting color mode -> frames.npy

**Model Selction**

<font color=#f6a69c>Text encoder</font>
- Bag-of-Words (BoW)
  - This converts a sentence into a fixed-length vector. It models sentenceword occurances by creating a matrix.
  - Limitations:
    - Does not consider the order of words in a sentence
    - Does not capture semantic and contextual meaning of the sentence
    - The representation vector is sparse

- Term Frequency Inverse Document Frequency (TF-IDF)
  - This is a numerical statistic intended to reflect how important a word is to a document in a collection or corpus. It creates the same sentence-word matrix as in BoW, but it normalizes the matrix based on the frequency of words.
  - Limitations:
    - A normalization step is needed to recompute term frequency when a new sentence is added
    - All the same limitations as the above BoW

- ML-based models
  - Embedding layer
    - Assume as a lookup table, which is good at converting sparse features into a fixed-size embedding
  - Word2vec
  - Transformer-based

<font color=#f6a69c>Video encoder</font>
- Video-level models
  - It is computationally expensive to convert the whole video using 3D convolutions/Transformer
- Frame-level models
  - Steps:
    - Process a video and sample frames
    - Run the model on the sampled frames to create frame embeddings
    - Aggregate frame embeddings to generate the video embedding

**Model Training**

Using a contrastive learning approach:
<p align="center">
<img src="/img/ML_sys_design/video-search/model-training.png" width="80%" height="80%">
</p>

**Evaluation**
- Offline
  - Precision@k mAP
  - Recall@k
  - Mean Reciprocal Rank (MRR)
- Online
  - Click-through rate (CTR)
  - Video completion time
  - Total watch time of search results

**Serving**

<p align="center">
<img src="/img/ML_sys_design/video-search/serving.png" width="80%" height="80%">
</p>