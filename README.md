# 以資料科學進行股票買賣策略整理

進行股票買賣策略需要的先備知識：

- 盤點會影響的股市因子
- 金融時間序列工程
- 適用於時間序列的神經網絡
- 績效指標
- 策略類型
- 論文整理

---

## 盤點會影響的股市因子

## 金融時間序列工程

## 適用於時間序列的神經網絡

## 績效指標

## 策略類型
- [形態學 - “尋找股價轉折為買賣點”](./Strategies/形態學.md)


## 論文整理

### News/Event Based Stock Movement Prediction
#### 2022
- [J. Zou, H. Cao, L. Liu, Y. Lin, E. Abbasnejad, J. Q. Shi, "Astock: A New Dataset and Automated Stock Trading based on Stock-specific News Analyzing Model," 2022](https://arxiv.org/pdf/2206.06606v1.pdf) | [source code](https://github.com/JinanZou/Astock)
  - 使用語意角色標注的抽取結果作為 input data 對股價進行預測
  - 此方法在 CSI300 & XIN9 的 annualized rate of return & maximum drawdown 都優於 baseline
  - [論文筆記](./Thesis/news_or_event_based_stock_movement_prediction/Astock-A-New-Dataset-and-Automated-Stock-Trading-based-on-Stock-specific-News-Analyzing-Model.md)
  - [衍生實作](https://github.com/A-baoYang/Astock-on-TWSE/tree/twse)
- [Cheng, W.K.; Bea, K.T.; Leow, S.M.H.; Chan, J.Y.-L.; Hong, Z.-W.; Chen, Y.-L., "A Review of Sentiment, Semantic and Event-Extraction-Based Approaches in Stock Forecasting," 2022](https://doi.org/10.3390/math10142437)
  - 整理以往 text-based 金融預測方法及優缺點、適用情境，包含 semantic-based, sentiment-based, event-extraction-based, hybrid approaches
  - 提出未來 text-based 金融預測方法的研究方向
  - [論文筆記]()
#### 2021
- [Z. Zhou, L. Ma, H. Liu, "Trade the Event: Corporate Events Detection for News-BasedEvent-Driven Trading," 2021](https://doi.org/10.48550/arXiv.2105.12825) | [source code](https://github.com/Zhihan1996/TradeTheEvent)
  - 使用 bi-level event detector 判斷新聞是否存在事件以及分類是什麼類型的事件 (11種預定義事件)
  - 以不同事件類別的維度分析其報酬率、勝率差別
  - 此方法在 winning rate, excess returns over market, avg. return 都優於 baseline
  - [論文筆記](./Thesis/news_or_event_based_stock_movement_prediction/Trade-the-Event-Corporate-Events-Detection-for-News-Based-Event-Driven-Tradings.md)
- [H. Jiang, C. Ye, S. Lai and H. Zhou, "Event Attention Network for Stock Trend Prediction," 2021 International Conference on Service Science (ICSS), 2021](https://ieeexplore.ieee.org/abstract/document/9492207)
  - 從新聞抽取事件並向量化 => 使用 Attention Mechanism 找出哪個事件才是主要影響價格變化 => 使用 GRU 對事件在股價上的影響建模
  - 此方法在 S&P500 能做出優於 baseline 的成效
  - [論文筆記](./Thesis/news_or_event_based_stock_movement_prediction/Event-Attention-Network-for-Stock-Trend-Prediction.md)
- [A. Hogenboom A. Brojba-Micu, F. Frasincar, "The impact of word sense disambiguation on stock price prediction," 2021](https://www.sciencedirect.com/science/article/pii/S0957417421009738)
  - 事件內詞彙的歧異消解，將事件抽取數量收斂 30%，產生更少不良的交易信號
  - excess returns 提升 70% 

#### 2019
- [Y. Wang, Q. Li, Z. Huang, and J. Li., "EAN: Event Attention Network for Stock Price Trend Prediction based on Sentimental Embedding," 2019](https://doi.org/10.1145/3292522.3326014)

#### 2018
- [Z. Hu, W. Liu, J. Bian, X. Liu, and T. Y. Liu, "Listening to Chaotic Whispers: A Deep Learning Framework for News-oriented Stock Trend Prediction," 2018](https://dl.acm.org/doi/abs/10.1145/3159652.3159690)
  - 目標是要克服市場新聞資訊的高雜訊
  - 模仿人類面對雜亂新聞訊息時的學習過程，提出三原則：sequential content dependency, diverse influence, effective & efficient learning
  - 提出 Hybrid Attention Networks (HAN) 基於近期新聞預測股價趨勢
  - 導入 self-paced learning machanism 提高模型學習效率
- [Manuel R. Vargas; Carlos E. M. dos Anjos; Gustavo L. G. Bichara; Alexandre G. Evsukoff, "Deep Leaming for Stock Market Prediction Using Technical Indicators and Financial News Articles," 2018](https://ieeexplore.ieee.org/abstract/document/8489208)
  - 實驗發現在捕捉語意上 CNN 比 RNN 表更好；RNN 則更適用於捕捉上下文語境及複雜時間序列
  - 提出混合財經文本和技術指標特徵預測股變化的模型 : IS-RCNN；及另一個僅使用技術指標的模型 : I-RNN
  - 交易策略統一在預測隔天上漲時今日買進、隔天賣出，反之則今日賣出、隔日買入
  - 實驗發現財經文本是模型表現穩定的重要角色，而比較多個不同組合的技術指標幾乎沒有幫助模型成效增長

## Repositories
### News/Event Based Stock Movement Prediction
[[vedic-partap/Event-Driven-Stock-Prediction-using-Deep-Learning](https://github.com/vedic-partap/Event-Driven-Stock-Prediction-using-Deep-Learning)]
- A novel neural tensor network for learning event embeddings & CNN to model the **combined influence of long-term events and short-term events** on stock price movements

[[dmis-lab/HATS](https://github.com/dmis-lab/hats)]
- Source code of thesis "HATS: A Hierarchical Graph Attention Network for Stock Movement Prediction" 
- 2 tasks: node classification & graph classification


[[lixiaojieff/HGTAN](https://github.com/lixiaojieff/HGTAN)]
- Source code of thesis "Temporal-Relational Hypergraph Tri-Attention Networks for Stock Trend Prediction"
- HGTAN: Hypergraph tri-attention network (intra-hyperedge, inter-hyperedge, inter-hypergraph attention modules)
  - determines the importance of nodes, hyperedge, hypergraphs, to find potential synergies between stock movements
  - have a more risk-adjusted return

https://github.com/jwwthu/DL4Stock
https://github.com/SakastLord/deep-stock
https://github.com/sangyx/deep-finance

## Datasets

[[Tushare](https://tushare.pro/document/2?doc_id=32)]
- 提供 Python API 串接中國滬深股票、指數、公募基金、期貨、現貨、齊全、債券、外匯、港股、公司公告及新聞等數據

