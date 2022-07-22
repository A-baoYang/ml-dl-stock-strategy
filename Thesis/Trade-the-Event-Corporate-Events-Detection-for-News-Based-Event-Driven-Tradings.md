# Trade the Event: Corporate Events Detection for News-Based Event-Driven Tradings

## Context
- Abrstract
- Intro


## Abrstract 

從新聞偵測公司事件 => 預測股價動向 => 根據預測結果進行交易回測

交易策略的基礎：利用公司事件發生當下造成的股價的暫時錯誤定價賺取價差

從新聞偵測公司事件的模型 : bi-level event detection model
- Low-level event detector : 以 token 為單位判斷是否有事件存在
- High-level event detector : 以 article 為單位找出事件

提供自行精心標註的資料集 : EDT 用於公司事件偵測 & 基於新聞的股價預測 benchmark

- 9721 articles **token-level event labels**
- 303893 articles mapping with **min-level stock price** by published timestamp
- strategy outperforms all baselines in **winning rate**, **excess returns**, **avg. return on each transaction**

## Intro
基於新聞的股價預測目的是要自動從巨量新聞中挖掘影響股價的信號。

### 盤點過去基於新聞的股價預測研究
直接使用文本向量，將股價漲跌預測當作一種分類問題
- 問題：缺乏解釋性。作者覺得沒辦法合理解釋交易決策，因為這種做法忽略股價變化的背後原因

使用新聞情緒分數
- 問題：情緒分數的主觀性太強，會受到文章撰寫風格影響、雜訊比例太高 (low signal-to-noise atio)

### 為什麼公司事件是比較適合的特徵
- 衡量客觀事實如何影響投資人對該公司的觀感和評估
- 以此作為訓練樣本還可以提供更有說服力的解釋性預測

### 過去偵測公司事件的研究
- Jacobs et al. (2018) : 先分句後 multi-label sentence classification 
    - 缺點：因為分句忽略了全局的上下文語境

### 更佳做法：以更小的顆粒度偵測事件是否存在
- token-level event labels
- 模型訓練：透過 token label 指定模型

Zero-shot 分類事件 => 