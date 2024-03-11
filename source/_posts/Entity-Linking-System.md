---
title: Entity Linking System
date: 2024-01-12 10:09:31
tags: 
- Machine Learning
- Grokking-the-ML-system-designs
categories: 
- [Tech]
- [Grokking-interviews, ML]
- [System-Design, ML]
---

#### <font color=#FA7F6F>Problem Statement</font>
Given a text and knowledge base, find all the entity mentions in the text(Recognize) and then link them to the corresponding correct entry in the knowledge base(Disambiguate).
<!--more-->
There are two parts to entity linking:
- Named-entity recognition
- Disambiguation

Applications:
- Semantic search
- Content analysis
- Question answering systems/chatbots/virtual assistants, like Alexa, Siri, and Google assistant

#### <font color=#FA7F6F>Metrics</font>
- **Offline metrics**
Offline metrics will be aimed at improving/measuring the performance of the entity linking component.

    - <font color=#BEB8DC>Name Entity Recognition (NER)</font>
        - The tagging scheme: **IOB2** (aka **BIO**)
            - **B** - Beginning of entity
            - **I** - inner token of a multi-token entity
            - **O** - non-entity
        - Performance measurement
            - $Precision = \frac{no. \;of\; correctly \;recognized \;named \;entities}{no. \;of\; total \;recognized \;named \;entities}$
            - $Recall = \frac{no. \;of\; correctly \;recognized \;named \;entities}{no. \;of\; named \;entities \;in \;corpus}$
            - $F1-score = 2*\frac{precision*recall}{precision+recall} $

    - <font color=#BEB8DC>Disambiguation</font>
        - Precision
        - Recall does not apply here as each entity is going to be linked (to either an object or Null).

    - <font color=#BEB8DC>Named-entity linking component</font>
        - F1-score as the end-to-end metric
            - <font color=#82B0D2>*True positive*</font>: an entity has been correctly recognized and linked.
            - <font color=#82B0D2>*True negative*</font>: a non-entity has been correctly recognized as a non-entity or an entity that has no corresponding entity in the knowledge base is not linked.
            - <font color=#82B0D2>*False positive*</font>: a non-entity has been wrongly recognized as an entity or an entity has been wrongly linked.
            - <font color=#82B0D2>*False negative*</font>: an entity is wrongly recognized as a non-entity, or an entity that has a corresponding entity in the knowledge base is not linked.
        - Micro-averaged metrics
            - Aggregate the contributions of all documents to compute the average metrics.
            - Focus on the overall performance, do not care if the performance is better for a certain set.
            - $Precision = \frac{\sum^{n}_{i=1}TP_i}{\sum^{n}_{i=1}TP_i + \sum^{n}_{i=1}FP_i}$
            - $Recall = \frac{\sum^{n}_{i=1}TP_i}{\sum^{n}_{i=1}TP_i + \sum^{n}_{i=1}FN_i}$
            - Micro-averaged F1-score

        - Macro-averaged metrics
            - Compute the metric independently for each document and takes the average (equal weightiage).
            - $Precision = \frac{\sum_{i=1}^n P_{di}}{n}_{where\; n= no. \;of\; documents,\; P_{di}= precision\; over\; document\; i}$
            - $Recall = \frac{\sum_{i=1}^n R_{di}}{n}_{where\; n= no. \;of\; documents,\; R_{di}= recall\; over\; document\; i}$
            - Macro-averaged F1-score 

<figure class="half">
    <img src="/img/ML_sys_design/nel/nel_micro.png" width="345px">
    <img src="/img/ML_sys_design/nel/nel_macro.png" width="345px">
</figure>

- **Online metrics**
Online metrics will be aimed at improving/measuring the performance of the larger system by using a certain entity linking model as its component. (A/B experiments)
    - Search engines: i.e. session success rate
    - Virtual Assistants: i.e. user satisfaction

#### <font color=#FA7F6F>Architecture</font>

<p align="center">
<img src="/img/ML_sys_design/nel/nel_architecture.png" width="100%" height="100%">
</p>

- Model generation path
    - <font color=#BEB8DC>Named Entity Recognition</font>
        - Responsible for building models to recognize entities (i.e. person, organizations)
    - <font color=#BEB8DC>Named Entity Disambiguation</font>
        - Candidate generation
            - Reduce the size of the knowledge base to a smaller subset of candidate entities
        - Linking
    - <font color=#BEB8DC>Metrics</font>
        - NER and NED seperately, Entity linking as a whole.

- Model execution path

#### <font color=#FA7F6F>Modeling</font>

- **NER Modeling**
    - <font color=#BEB8DC>Contextualized text representation</font>
        - [ELMo](https://arxiv.org/pdf/1802.05365.pdf)
            - Raw word vectors: character-level CNN or Word2Vec
            - <font color=#82B0D2>Bi-LSTM</font> with forward/backword pass: "Shallow" bi-directional since the two pass LSTMs are trained independently and concatenated naively, therefore, the left and right contexts can not be used 'simultaneously'.
        - [BERT](https://arxiv.org/abs/1810.04805)
            - The <font color=#82B0D2>transformer</font> layer will take advantage of contexts from both directions, but with problems - "sees itself indirectly".
            - Prediction Objectives: <font color=#82B0D2>Masked</font> language modeling (MLM) and Next sentence prediction (NSP)
    - <font color=#BEB8DC>Contextual embedding as features</font>
        - Utilize contextual embeddings generated by BERT as features (transfer learning) in our NER classifier.
        - The model will not be modified, only the final layer output is used as embedding features.
    - <font color=#BEB8DC>Fine-tuning embeddings</font>
        - Take the pre-trained models and fine-tune them based on our NER dataset to improve the classifier quality.

- **Disambiguation Modeling**
    - <font color=#BEB8DC>Candidate generation</font>
        - Higher <font color=#82B0D2>recall</font> by building an index where terms are mapped to knowledge base entities.
        - Methods:
            - Based on terms' names and their concatenations.
            - Make use of referrals and anchor text.
            - Embedding methods + K Nearest Neighbors for a particular term.

    - <font color=#BEB8DC>Linking</font>
        - Higher <font color=#82B0D2>precision</font>.
        - The <font color=#82B0D2>prior</font>: for a certain entity mention, how many times the candidate entity under consideration is actually being referred to.

<p align="center">
<img src="/img/ML_sys_design/nel/nel_linking.png" width="80%" height="80%">
</p>