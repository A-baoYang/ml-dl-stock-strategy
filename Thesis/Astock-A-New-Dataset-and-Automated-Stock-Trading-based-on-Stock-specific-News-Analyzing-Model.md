# Astock: A New Dataset and Automated Stock Trading based on Stock-specific News Analyzing Model

- Authors: Jinan Zou, Haiyao Cao, Lingqiao Liu, Yuhao Lin, Ehsan Abbasnejad, Javen Qinfeng Shi
- Publish Year Month: 2022.06
- Thesis: https://arxiv.org/pdf/2206.06606v1.pdf
- Github: https://github.com/JinanZou/Astock

## Context

- 摘要
- 過去相關文獻及其缺陷
- 論文採用資料集
- 論文任務
- 論文實驗

## 摘要

1. 建立了一個平台 利用 NLP 加值 股票自動交易演算法
    1. 以個股為出發點的新聞
    2. 納入不同股市因子
    3. 使用更加財務導向的績效評估指標

2. Self-supervised SRLP method 提升對 stock movement prediction
使用 NLP 中的 SRL 語意角色標注，加上 self-supervised 衍生出 SRLP (Semantic Role Labeling Pooling) 對每篇文章聚合生成新的向量表示，最後模型使用 SRLP + 技術指標

3. (2) 也對 Out-of-distribution (OOD) generalization (泛化) 有所幫助

- OOD 樣本: Out-of-distribution 相對於訓練、驗證、測試集的樣本，稱之為異常樣本。[有關 OOD Detection](https://zhuanlan.zhihu.com/p/102870562)

> ### **結果**
於 CSI300 index & XIN9 獲得超出年回報率的 baseline 、在 maximum drawdown 也是

## 過去相關文獻及其缺陷

過去使用 NLP 技術預測股價的論文多使用情緒分數或文本直接預測幾日後漲跌幅或股價，這些研究不能用在真實交易有兩個主要問題：

1. 有的論文忽略了財務指標（financial factors），這在真實交易上是很重要的
2. 這些論文只用一般機器學習模型指標在評估，而沒有使用實際的交易收益評估

## 論文採用資料集
![](https://i.imgur.com/i7yVrk0.png)

> 中國 A 新聞 + 股價指標 + 股價


**新聞部分**
- 內容：包含個股公告 + 相關新聞，共 40963 篇
- ID 時間軸：2018/07-2020/12
    - (ID : In-distribution)
    - train: 80%
    - val: 10%
    - test: 10%
- OOD 時間軸：2021/01-2021/11
    - (OOD : Out-of-distribution)
    - use for OOD generalization testing

**股價部分**
- ID 時間軸：2018/07-2020/12
- OOD 時間軸：2021/01-2021/11
- 訓練目標：
- 交易動作
    - Long
    - Preserve
    - Short

**論文原始使用的24種股市因子 Stock Factor**

| Factor Name                                                                          | tushare columns   | yfinance columns                  | finlab columns |
| ------------------------------------------------------------------------------------ | ----------------- | --------------------------------- | -------------- |
| Open price                                                                           | `open`            | `Open`                            | `Open`         |
| High price                                                                           | `high`            | `High`                            | `High`         |
| Low price                                                                            | `low`             | `Low`                             | `Low`          |
| Close price                                                                          | `close_x`         | `Close`                           | `Close`        |
| Previous close price                                                                 | `pre_close`       | custom                            | custom         |
| Price change 涨跌额<br>= 今日 close - 昨日 close                                     | `change`          | custom                            | custom         |
| Percentage of change 涨跌额<br>= (今日 close - 昨日 close) / 昨日 close              | `pct_chg`         | custom                            | custom         |
| Volume 成交量 （手）                                                                 | `vol`             | `Volume`                          | `成交筆數`     |
| Amount 成交額 （仟元）                                                               | `amount`          | ~                                 | `成交金額`     |
| Dividend yield 股息收益率 （%）                                                      | `dv_ratio`        | `dividendYield`                   | `殖利率(%)`    |
| Dividend yield for Trailing Twelve Months                                            | `dv_ttm`          | `trailingAnnualDividendYield`     | cutsom         |
| Total share 总股本 （万股）                                                          | `total_share`     | `sharesOutstanding`               | `股本`         |
| Circulated share 流通股本 （万股）                                                   | `float_share`     | ~                                 |                |
| Free Float share 自由流通股本 （万）                                                 | `free_share`      | `floatShares`                     |                |
| Price-earning ratio 市盈率（总市值/净利润， 亏损的PE为空）                           | `pe`              | `forwardPE`                       | `本益比`       |
| PE for Trailing Twelve Months                                                        | `pe_ttm`          | `trailingPE`                      | cutsom         |
| Price/book value ratio 市净率（总市值/净资产）                                       | `pb`              | `priceToBook`                     | `股價淨值比`   |
| Price-to-sales Ratio 市销率                                                          | `ps`              | ~                                 | ~              |
| Price to Sales ratio for Trailing Twelve Months                                      | `ps_ttm`          | `priceToSalesTrailing12Months`    | ~              |
| Market Capitalization 总市值 （万元）                                                | `total_mv`        | `marketCap`                       | ~              |
| Circulate Market Capitalization 流通市值（万元）                                     | `circ_mv`         | ~                                 | ~              |
| Turn over rate 週轉率（%）                                                           | `turnover_rate`   | `annualHoldingsTurnover`          | `存貨週轉率`   |
| Turn over rate for circulated Market Capitalization 週轉率（自由流通股）             | `turnover_rate_f` | ~                                 | ~              |
| Volume ratio 量比<br>> 開市後每分鐘的平均成交量與過去5個交易日每分鐘平均成交量的比例 | `volume_ratio`    | X (1m-data is limited in 30 days) | ~              |

> 來自 tushare.pro 數據
- [A股日線行情](https://tushare.pro/document/2?doc_id=27)
- [每日指標](https://tushare.pro/document/2?doc_id=32)

> 如果使用 `yfinance` 也可以取得 `Ticker.info()`
- [Statistics](https://finance.yahoo.com/quote/000001.SZ/key-statistics?p=000001.SZ)

## 論文任務
> ### Text-based stock movement classification
(srl triplet based)

> #### **stock return rate of each news piece**
- 以 stock return rate 衡量每篇個股新聞的影響、標註為 $r$
- 最後將所有新聞用 $r$ 排行、以級距分成 3 類，用意是為了找到最強的股市變化信號
- 根據專家建議和作者做的一些實驗，最後將設定參數 a,b,c,d 為

![](https://i.imgur.com/gkonRS1.png)

- $a=20; b=40; c=60; d=20$

- *(猜測)* $r$ 的計算方式應該是 $r = \frac{close_{next} - close}{close}$ ，在看到答案的情況下計算，在新聞發布當天買進、隔天的回報率

> ### 分類訓練設置
- 自 ID 隨機選取 80% 為訓練集；另外 20% 均分到驗證與測試集

> ### simulated trading
(on github page)

## 論文方法論
其中有兩個重要部件： SRLP & Self-Supervised Learning based on SRLP

> ### 為什麼採用 SRL & Self-Supervised Learning 

在使用 SRL 以前
- 過去論文大多以整篇關鍵詞或句子進行股價預測，因為包含太多雜訊，很容易 overfitting 且無法辨識出重要的文本特徵

使用 SRL 以後
- 可透過擷取語意再進行預測

搭配 self-supervised
- 此法可在無人工情形下產生 labeled data 並學到共通的語言特徵

> ### SRLP

#### **生成 SRL Embeddings**
1. 對文本進行 SRL 後輸出 {A0, V, A1} 的向量三元組

  ![](https://i.imgur.com/DlkNSYt.png)

2. 將 SRL 三元組結果輸入到 PTMs 獲取句子向量
3. 針對全域新聞的向量 Pooling 到 A0, A1, V 身上

![](https://i.imgur.com/tLQzFeD.png)

4. 最後將每個 A0, A1, V 及 本論文挑選的 24 個股價因子 concat 起來作為輸入資料 X，對股價變化量級距標籤 (top 20%, 20-40%, bottom 20%) 進行分類預測

![](https://i.imgur.com/6QYCXEX.png)

> ### Self-supervised Learning based on SRLP 

![](https://i.imgur.com/P7mWPQO.png)

有別於一般常見的監督式學習訓練股價變化分類模型，本論文使用 **self-supervised learning** 做為輔助任務來訓練這個分類模型。股市預測相關的題目，足夠泛化的模型是很重要的，因為我們在訓練集所使用的時間段和真正測試的往往是不同的時間段，價格數據分佈一定也會有所不同。而根據研究所示 (Mohseni et al., 2020; Hendrycks et al., 2019)，在監督式學習任務中融合 self-supervised learning 能夠有更好的泛化表現。

本論文的 self-supervised learning 任務是『隨機挑選一個三元組中的一個語義角色(動詞,主詞,受詞) embedding 遮罩起來，讓神經網絡預測』，以這樣克漏字類型的任務幫助神經網絡提升對金融文本的理解。

#### **語義角色隨機遮罩作法**

> 1. 隨機挑選一個三元組中的一個語義角色 embedding 換成 all-zero vector : 
> ![](https://i.imgur.com/mzBNw5E.png)
> 2. 將 $E'$ 輸入 transformer 模型，返回 **query vector sequence** $q$ 
>    - $q = Transformer(E')[:, t]$ 
>    - 其中 $[:, t]$ 表示有遮罩語義角色的那個三元組 embedding
> 3. 其他沒有被遮罩起來的語義角色(看實驗情境選擇的是哪一種)也會輸入到一個 encoder 轉換為 candidate key vectors $K$
>    - $K = [f_{V}(e^{1}_{V}), ... , f_{V}(e^{t}_{V}), ... , f_{V}(e^{N}_{V})]$
>    - $f_{V}$ 是特定用來 encode 動詞的 encoder
> 4. 接著第二步的 query vector $q$ 會去比對第三步產生的 candidate key vectors $K$，訓練的目標就是讓 $K$ 中的第 $t$ 個向量有最高的機率值，代表神經網絡找得到被遮罩的動詞是哪一個
>    - 這個比對過程會透過 matrix multiplication 和 Softmax 運算得知 : $P_{SSL} = Softmax(qK)$
> 5. 神經網絡訓練時，使用 loss function 來對目標損失進行最小化，因此我們的 loss function 為 : 
>    - $L = \alpha L_{CLS} + (1-\alpha)L_{SSL}$
>    - 其中 $L_{CLS}$ 是 text-based stock movement classification 的 cross-entropy loss
>    - $L_{SSL}$ 則是 self-supervised learning prediction $P_{SSL}$ 的 cross-entropy loss
>    - $\alpha$ 是一個 trade-off parameter

## 論文實驗

> ### Performance for Text-based Stock Movement Classification

- Metrics for Text-based stock movement classification: **Accuracy, F1 Score, Recall, Precision**

**[In-distribution results]**
![](https://i.imgur.com/qLXgdsy.png)

**[Out-of-distribution results]**
![](https://i.imgur.com/uGs1i7I.png)

> ### Performance for simulated trading

- Metrics for simulated trading: **Annualized Rate of Return, Maximum Drawdown, Sharpe Ration**

**[Out-of-distribution results]**
![](https://i.imgur.com/ppkabnq.png)

**[Out-of-distribution performance time-series]**

Return Rate & Drawdown Rate
![](https://i.imgur.com/BVR9Jz8.png)
