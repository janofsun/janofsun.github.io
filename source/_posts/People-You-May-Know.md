---
title: People You May Konw
date: 2025-04-23 23:40:37
tags: 
- Machine Learning
- Grokking-the-ML-system-designs
categories: 
- [Tech]
- [System-Design, ML]
---

#### <font color=#377f99>Problem Statement</font>
People You May Know (PYMK) is a list of users with whom you may want to connect based on things you have in common, such as a mutual friend, school, or workplace.
<!--more-->
**Clarifying Requirements**
1. What's the objective/motivation?
    - May I assume the motivation of the PYMK feature is to help users discover potential connections and grow their network?
2. How to define 'connections'?
    - May I assume that people are friends only if each is a friend of the other?
3. What should I consider for forming connections?
    - To recommend potential connections, a huge list of factors must be considered, such as educational background, work experiences, existing connections, historical activities. Should I focus on the most important factors?
4. Define the scale
    - How many connections does an average user have? (like 1000)
    - What's the total number of users on this platform? (like 1 billion)
    - How many of them are daily active users? (like 300 million)
5. Model Dynamics
    - May I assume that the social graph of most users are not dynamic, meaning that their connections don't change significantly over a short period.

**Frame the Problem as an ML task**
- Define the ML Objective
    - Maximize the number of formed connections between users
- Define the input and the output
    - input: a user
    - output: a list of users

**Choosing ML methology**
There are commonly two approaches for PYMK: Pointwise Learning-To-Rank (LTR) and Edge Prediction. 
1. Pointwise LTR
- Why this approach:
    - The pointwise LTR transforms a ranking problem to a supervised learning problem, where the model takes user and a candidate connection pair as inputs, and outputs a score or probability. 
    - In cold-start scenarios where rich interaction data is lacking, the pointwise LTR can quickly model the similarity between user pairs.
- Why not this approach:
    - This ignores some social context, the inputs are distinct users

2. Edge Prediction
- The entire social context can be represented as a graph, where each node represents a user, and an edge between two nodes indicates a formed connection between two users.
- Use the entire graph as input, and predict the probability of an edge existing between user A and other users

**Data Preparation**
We typically use three types of raw data:
- User
- Connections
- Interactions

<p align="center">
<img src="/img/ML_sys_design/PYMK/User.png" width="80%" height="80%">
</p>
One challenge with this 

<p align="center">
<img src="/img/ML_sys_design/PYMK/Connections.png" width="80%" height="80%">
</p>

<p align="center">
<img src="/img/ML_sys_design/PYMK/Interactions.png" width="80%" height="80%">
</p>

**Feature Engineering**


**Model Development**

### <font color=#377f99>Appendix</font>

**Graph-based**
In general, a graph represents relations(edges) between a collection of entities(nodes). Graphs can store structural data, there are three general types of prediction tasks that can be performed on structured data represented by graphs:
- Graph-level
    - like chemical compound
- Edge-level
    - like PYMK on social media
- Node-level
    - like predicting whether a person is a spammer or not, given a social network graph