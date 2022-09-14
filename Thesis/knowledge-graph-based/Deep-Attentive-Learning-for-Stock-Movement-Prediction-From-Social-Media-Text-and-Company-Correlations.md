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

![](https://i.imgur.com/kndHeeT.png)

> Equation Marks
> - $T$ : 訓練資料的時間窗格（參考前 $T$ 日來預測第 $d$ 日股價）
> - $P_{d-T}$ : 第 $d-T$ 日的 price vector ($R^{d_{p}}$, $d_{p}$ 是向量維度)
> - $h_{T}$ : GRU 的 hidden states vector ($R^{d_{p} \times T}$)
> - $\beta_{T}$ : temporal attention 也就是該日的權重
> - $q_{t}$ : 把這 $T$ 日的價格資訊經過 GRU 做加權聚合後的向量 $R_{v}$，$q_{t} = ζ (\hat{h}_p )$

- Encode historical stock price movements to produce price feature ($q_t$)
- use **GRU** to capture sequential dependencies across trading days
- use **temporal attention** to weigh critical days and forms an aggregated feature representation
  - stock trend of each day has a different impact on stock trend prediction

> **Temporal Attention**
- A form of **additive attention**
- Aggregates all hidden representations of the GRU across different time-steps with learned adaptive weights

### (2) Social Media Information Encoder

Tweet Embeddings

Learning Representations for intra-day

Learning Representations across days

