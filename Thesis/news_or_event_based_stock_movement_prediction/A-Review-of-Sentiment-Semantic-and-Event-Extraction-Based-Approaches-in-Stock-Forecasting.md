# A Review of Sentiment, Semantic and Event-Extraction-Based Approaches in Stock Forecasting


## Adaptive Markets Hypothesis (AMH)

- 股價反應投資人對公司未來價值的期望
- 根據有效市場假說，理性投資人會對市場上的消息進行反應；因此出現套利行為，讓價格變得在高估和低估間遊走 -> 使得價格遵守隨機漫步假說，這個假說認為股市是不可預測的
- 但是，現實生活中的有效市場假說存在嗎？在一些文獻中做到了超額報酬，駁斥了有效市場假說，因此大家詢問這些超額報酬的來源，而出現了「適應性市場假說」
- 金融學家 Andrew Lo 結合進化論、有效市場理論、行為財務理論、心理經濟學結合起來，提出了適應性市場假說(Adaptive Markets Hypothesis，AMH)，它認為有效只是壹種完美的均衡狀態，在大多數的時間市場並不是處於完全有效的狀態，而是處在從無效趨向有效的動態交替過程中
  - (1) 市場中的個體基於自身利益做決策
  - (2) 市場中的個體會犯錯
  - (3) 市場個體會學習和適應
  - (4) 競爭導致個體適應和更新
  - (5) 自然選擇塑造市場生態，進化決定著市場動態
- 資訊不對稱是破壞完美市場條件的主因，也是前述提到超額回報可能的由來
- 金融時間序列資料有高度的變動性和噪聲以至於在預測上有難度
- 研究指出不同市場、公司、國家間的時間序列有複雜的關聯性


## Search methods
- primary search terms are “text”, “news”, “sentiment”, “event”, “stock”, “forecast”, “predict”, “neural network”, “machine learning”, “financial”, etc., along with the Boolean operators (‘AND’, ‘OR’, ‘NOT’, etc.)
- 針對近五年的研究、Google Scholar 前 15 頁、high citation score, Web of Science ranking
- 再從這些文獻中的參考文獻回溯重要的研究，建立正確的脈絡


## Semantic-based modelds

- NewsCATS: Bag-of-words (BOW) + KNN & SVM [21]
- BOW + TFIDF [22]
  - BOW is difficult to distinguish the semantical relation between words 無法利用到字詞間的語意關係
  - => n-gram takes a contiguous sequence of n items of words from a text sequence which might better capture the syntactic(句法) information between the words
- Noun phrase + Entities + TFIDF [23]
- TFIDF + SVM [24]
- N-gram : 2-gram > 1-gram & 3-gram [25] on ad hoc announcement data
  - N-gram is based on lexical co-occurrence statistics (詞彙共現性)
  - But also has curse of dimensionality issue, sparser the data distribution and weaken the model’s robustness
- N-gram + word2vec on tweets + RF
- BOW + word2vec on tweets => word2vec > BOW [29]
- W2V on Reuters news + 7 技術指標 predict S&P500 index [30]
- W2V + BiGRU predict S&P500 index [31]
- Skipgram + CNN predict 5天後股價
- character level embedding 


## HAN (Hybrid Attention Network)

應對不穩定的新聞品質，提出三個處理原則
1. Sequential Context Dependency where the news is interdependent
2. Diverse Influence where the news impact varies across each other
3. Effective and efficient learning

Hybrid Attention network (HAN)
1. news level attention to effectively capture important news information
2. temporal attention to better capture the time-varying effect

應對 noise of irrelevant text 
> HCAN (hierarchical complementary attention network) [37]
> - Title + content (concat)
1. word-level attention to capture the significance words in news title and content
2. sentence-level attention to capture the significance of each sentence in content
> NBA (Numerical-based Attention)
- improve the interconnection between the market and news data to align the news embedding with the stock vector[38]

Stock2Vec: adapt the Word2Vec algorithm in sector embedding[39]
- stocks with similar properties are likely to appear in a same neighborhood in the vector space
- modelling of the intrinsic relationship among stocks could improve the predictive performance

Stock2Vec + GRU + Sentiment

## Event-extraction-based