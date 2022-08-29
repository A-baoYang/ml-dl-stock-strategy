# Event Attention Network for Stock Trend Prediction

- Authors: Hongyu Jiang, Chunyang Ye*, Shanyan Lai, Hui Zhou
- Publish Year Month: 2021.10
- Thesis: https://ieeexplore.ieee.org/document/9492207
- Github: 

## Context

- 摘要
- 過去相關文獻及其缺陷
- 論文採用資料集
- 論文任務
- 論文實驗

## 摘要

- 從新聞抽取事件並向量化 => 使用 Attention Mechanism 找出哪個事件才是主要影響價格變化 => 使用 GRU 對事件在股價上的影響建模
- 此法被驗證可在 S&P500 上做出優於 baseline 的績效



技術分析派相信歷史趨勢會重演

但這些方法忽略了產業或社群媒體事件造成的影響，錯失一些從中獲利的機會，且遇到歷史未出現過的特殊事件時也難以即時反應

目前來說財經新聞、社群論壇發文是兩大文本型資料來源
又以財經新聞最受關注因為其專業性和深入解釋某事件帶來產業和宏觀經濟影響

股市對消息十分敏感且即時
過去研究已有人將轉換後的 event embeddings 經過 CNN、RNN 進行股價變化的預測
除了新聞標題、內文外、情緒也被納入為其中一項特徵

但是一天中有無數篇新聞、且新聞中的不同事件對股價變化影響各異；對於不同股票和產業，如果只是將一天中所有新聞concat起來輸入到模型會對成效有負面影響

因此提出 event attention network

新聞抽取事件並向量化 => 使用 Attention Mechanism 找出哪個事件才是主要影響價格變化 => 使用 GRU 對事件在股價上的影響建模

這方法可提升價格變化預測的成效至 68.47%


## 過去相關文獻及其缺陷

### Event Extraction

傳統的三元組抽取 (entity1, rel, entity2) 有著以下缺點：
1. 無法找出真的重要的關係
2. ＿＿——
3. 每種關係只能 2 個 entity 之間

> ### OLLIE
最新的事件抽取框架 OLLIE (Open Language Learning for Information  Extraction) 克服上述問題：
1. 擴展了關係辭彙的文法範圍，可涵蓋更多元的關係表達(?)
2. 將表示法從只能三元組擴增到可以有附加的上下文語境資訊，例如屬性、因果關係
3. 主詞和受詞不再只能一個，可以同時多個

OLLIE 能夠透過屬性關係的設置，獲取更深層的實體關係，語意更完整、精準

### Event Embeddings

