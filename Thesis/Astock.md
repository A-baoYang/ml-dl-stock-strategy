# Astock: A New Dataset and Automated Stock Trading based on Stock-specific News Analyzing Model

2022.06

## Abstract

1. 建立了一個平台 利用 NLP 加值 股票自動交易演算法
    1. 以個股為出發點的新聞
    2. 納入不同股市因子
    3. 使用更加財務導向的績效評估指標

2. Self-supervised SRLP method 提升對 stock movement prediction
使用 NLP 中的 SRL 語意角色標注，加上 self-supervised 衍生出 SRLP (Semantic Role Labeling Pooling) 對每篇文章聚合生成新的向量表示，最後模型使用 SRLP + 技術指標

3. (2) 也對 Out-of-distribution (OOD) generalization (泛化) 有所幫助

- OOD 樣本: Out-of-distribution 相對於訓練、驗證、測試集的樣本，稱之為異常樣本。[有關 OOD Detection](https://zhuanlan.zhihu.com/p/102870562)

#### **結果**
於 CSI300 index & XIN9 獲得超出年回報率的 baseline 、在 maximum drawdown 也是

## 過去相關文獻及其缺陷

過去使用 NLP 技術預測股價的論文多使用情緒分數或文本直接預測幾日後漲跌幅或股價，這些研究不能用在真實交易有兩個主要問題：

1. 有的論文忽略了財務指標（financial factors），這在真實交易上是很重要的
2. 這些論文只用一般機器學習模型指標在評估，而沒有使用實際的交易收益評估

## 本文資料集
![](https://i.imgur.com/i7yVrk0.png)

中國 A 新聞 + 股價指標 + 股價

(ID : In-distribution)
(OOD : Out-of-distribution)
**新聞部分**
- 內容：包含個股公告 + 相關新聞，共 40963 篇
- ID 時間軸：2018/07-2020/12
    - train: 80%
    - val: 10%
    - test: 10%
- OOD 時間軸：2021/01-2021/11
    - use for OOD generalization testing

**股價部分**
- ID 時間軸：2018/07-2020/12
- OOD 時間軸：2021/01-2021/11
- 訓練目標：
- 交易動作
    - Long
    - Preserve
    - Short

**股市因子 Stock Factor**
- Dividend yield 
- Total share
- Circulated share
- Free Float share
- Market Capitalization
- Price-earning ratio
- PE for Trailing Twelve Months
- Price/book value ratio
- Price-to-sales Ratio
- Price to Sales ratio
- Circulate Market Capitalization
- Open price
- High price
- Low price
- Close price 
- Previous close price
- Price change
- Percentage of change
- Volume
- Amount
- Turn over rate
- Turn over rate for circulated Market Capitalization
- Volume ratio

## SRL & Self-Supervised Learning 

在使用 SRL 以前
- 過去論文大多以整篇關鍵詞或句子進行股價預測，因為包含太多雜訊，很容易 overfitting 且無法辨識出重要的文本特徵

使用 SRL 以後
- 可透過擷取語意再進行預測

搭配 self-supervised 
- 此法可在無人工情形下產生 labeled data 並學到共通的語言特徵

## 論文任務
### Text-based stock movement classification
(srl triplet based)

> ### stock return rate of each news piece
- 以 stock return rate 衡量每篇個股新聞的影響、標註為 $r$
- 最後將所有新聞用 $r$ 排行、以級距分成 3 類，用意是為了找到最強的股市變化信號
- 根據專家建議和作者做的一些實驗，最後將設定參數 a,b,c,d 為

![](https://i.imgur.com/gkonRS1.png)

- $a=20; b=40; c=60; d=20$

> ### 分類訓練設置
- 自 ID 隨機選取 80% 為訓練集；另外 20% 均分到驗證與測試集

### simulated trading
(on github page)

## 論文方法論
其中有兩個重要部件：
### SRLP

> ### 生成 SRL Embeddings
1. 對文本進行 SRL 後輸出 {A0, V, A1} 的向量三元組

  ![](https://i.imgur.com/DlkNSYt.png)

2. 將 SRL 三元組結果輸入到 PTMs 獲取句子向量
3. 針對全域新聞的向量 Pooling 到 A0, A1, V 身上

![](https://i.imgur.com/tLQzFeD.png)

4. 最後將每

![](https://i.imgur.com/6QYCXEX.png)

### SRLP based Self-supervised

