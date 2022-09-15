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
> - $q_{t}$ : 把這 $T$ 日的價格資訊經過 GRU 做加權聚合後的向量

- Encode historical stock price movements to produce price feature ($q_t$)
- use **GRU** to capture sequential dependencies across trading days
- use **temporal attention** to weigh critical days and forms an aggregated feature representation
  - stock trend of each day has a different impact on stock trend prediction

> **Temporal Attention**
- A form of **additive attention**
- Aggregates all hidden representations of the GRU across different time-steps with learned adaptive weights

### (2) Social Media Information Encoder

![](https://i.imgur.com/P1sVcow.png)

> Equation Marks
> - $m$ : Tweets embedding vector
> - $h_{ik}$ : $k_{th}$ Hidden states of day-wise tweets embeddings in day $i$
> - $\gamma_{k}$ : Intra-day level attention of each tweet in the same day
> - $h_{T}$ : Hidden states of aggreated tweets embeddings of day $T$
> - $\beta_{T}$ : Temporal attention weight of day $T$
> - $C_{t}$ : 把這 $T$ 日的推文資訊經過 GRU 做加權聚合後的向量

Tweet Embeddings

Learning Representations for intra-day

Learning Representations across days

### (3) Blending Multimodal Information

> $x_{t} = B(c_{t}, q_{t},) = ReLU(q_{t}^{T} W c_{t} + b)$
> - $q_{t}$ : 把這 $T$ 日的價格資訊經過 GRU 做加權聚合後的向量 ($R^{v}$)
> - $c_{t}$ : 把這 $T$ 日的推文資訊經過 GRU 做加權聚合後的向量 ($R^{u}$)
> - $W$ : 權重矩陣 ($R^{w \times v \times u}$)
> - $b$ : bias ($R^{w}$)
> - $x_{t}$ : 經過 BiLinear transformed 的向量 ($R^{w}$)

### (4) Graph Attention Network

- Input: a set of stock (node) features
  - $h = [x_1, x_2, ..., x_{|S|}]$, $x_i$ 為經過 BiLinear transformed 的向量
  - 