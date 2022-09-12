# Deep Attentive Learning for Stock Movement Prediction From Social Media Text and Company Correlations

- DOI: https://www.aclweb.org/anthology/2020.emnlp-main.676/
- EMNLP 2020
- Github: https://github.com/midas-research/man-sf-emnlp

## Contribution

**MAN-SF** : Multipronged Attention Network for Stock Forecasting that jointly learns from historical prices, social media, and inter-stock relations
- captures relevant signals across diverse data through **hierarchical attention** to train a **GAT** (Graph Attention Network)

## Problem Formulation
- Objective: Learn temporally relevant information jointly from tweet & historical price signals by leveraging stock relations

### Problem statement

## Framework

![](https://i.imgur.com/uweVZYI.png)

### (1) Price Encoder

- Encode historical stock price movements to produce price feature ($q_t$)
- use **GRU** to capture sequential dependencies across trading days
- use **temporal attention** to weigh critical days and forms an aggregated feature representation
  - stock trend of each day has a different impact on stock trend prediction




