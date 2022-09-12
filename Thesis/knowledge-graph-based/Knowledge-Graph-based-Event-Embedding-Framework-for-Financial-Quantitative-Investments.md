# Knowledge Graph based Event Embedding Framework for Financial Quantitative Investments

- DOI: https://doi.org/10.1145/3397271.3401427
- SIGIR ’20, July 25–30, 2020, Virtual Event, China
  - SIGIR: ACM International Conference on Research and Development in Information Retrieval (Special Interest Group on Information Retrieval)
  - **ranked/rated A according to the CORE 2018 ranking**
    - CORE Conference Ranking: a measure to assess the major conference in the computing field.
- 產學跨校合作論文: Seek Data Inc., Shanghai, China, Shanghai Jiao Tong University, Zhejiang Gongshang University, University of Technology Sydney

## Abstract

- event representative learning 對事件導向的量化投資很有幫助
- 但是金融市場的事件也會被 lead-lag effect 影響，是源自於 internal relations
- 因此提出一個框架 **KGEEF** 把 知識圖譜 embed 到 event representative learning 中，並且直接用量化投資當作下游任務 fine-tune

## Intro

> " Microsoft buys LinkedIn "

- 純文本的缺點是沒有對收購方和被收購方的股價變化學習

> (A="Microsoft", P="buys", O="LinkedIn")

- 這種結構化的表達方式增加了 event vector 的稀疏程度，使得預測能力受限
- 即使用 event embeddings 可以將動詞意思相近的三元組靠攏，但也會因為維度過高的緣故，使得兩個完全不相關的三元組反而有很相似的 embeddings

> 在 event representation learning 過程中加入 external KG

- 利用外部知識圖譜(OpenKG, ex: Wikidata)，讓模型知道哪些實體的距離才應該要比較接近
- 但是，個股的股價不會僅受到自己發生的事件影響，也會受到其他相關公司發生的事件影響，而且彼此影響的速度和程度都不同，這稱作 **lead-lag relationship** ，起因於不同事件在不同實體上傳播的速度差，例如 "Microsoft buys LinkedIn" 不只會影響這兩間公司，也會影響其上游和競爭者如 "Salesforce" 和 "Meetup" 等。這很挑戰性因為這是需要領域專家＆標註訓練資料才能做到的

> Incorporate the knowledge of lead-lag relationship into a novel knowledge graph-based event embedding framework (KGEEF) for quantitative investment

## Solution: KGEEF

![](https://i.imgur.com/3KsBDoq.png)

1. multi-source input layer : event tuples + FinKG + relation tuples
2. event representative learning layer : Learns embeddings of the 3 input type (Event/KG/Relation)
3. multi-source attention layer : fed (b) for a high-order representations
4. fully connected layer : combined Event loss, Graph loss & Relation loss
