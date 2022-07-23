# Trade the Event: Corporate Events Detection for News-Based Event-Driven Tradings

## (Self Comment)
- 需要預先做好事件分類的標注資料集，可以找看看中文事件抽取資料集有標註的來用
- 這篇亮點是在一個新的 Bi-level 金融事件分類模型、自己寫的回測、以事件分類的維度分析漲跌和勝率進而對交易決策做出解釋
- 我們現在真實世界的新聞太多雜訊，也是需要這類型的分類模型；但是他這種是分類和股價變化量分開訓練
- 還缺一個直接從股價變化對新聞給權重的模型，從權重來排序哪種新聞發生更重要
- 股價變化量的計算，時間從1min~1hr 分析影響時效

## Context
- Abrstract
- Intro
- Event-driven trading strategy
- Data
- Experiments

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

### 以更小的顆粒度偵測事件是否存在 (token-level event labels)
優點
- 模型訓練可以透過 token label 指定模型訓練目標

缺點
- 模型會忽略對整篇文章的理解

為了解決這個缺點，本論文提出 **bi-level event detection model**
- `low-level detector` : 透過 token classification 識別句子中的特定事件
- `high-level detector` : 把 `low-level detector` 的預測結果和文章級的上下文特徵整合，一起預測各項事件的存在機率

### 針對股價預測的時效性問題
作者認為若有值得關注的重大新聞發佈，股價是會即時反應的；而過去研究使用隔天股價漲跌當作目標值，無法完整反應新聞的影響程度。

因此本論文採取的做法是「文章一發佈(how fast?)立即做股價預測，並立即根據交易策略進行交易」

## Event-driven trading strategy 

1. `Bi-level Event Detector`
2. `Trading Policy`

### Bi-level Event Detector

![](https://i.imgur.com/XnXxwbp.png)

> ### Domain Adaptation
目的
- 讓模型學習金融背景知識

原因
- 同樣的事件可能有不同講法

作法
- 訓練 Masked-Language-Model (MLM) 預測被遮罩的 tokens (multi-class classification over entire vocabulary)
- input : a financial encyclopedia (百科全書) & financial news
  - 每個 input sentence 中 15% 的 token 被遮罩
- Loss : `categorical_crossentropy` 

> ### Bi-level Event Detector
- 每篇文章以 `[CLS]` 連接
- 以 $h_{cls}$ (`[CLS]`'s last hidden state) 來表示整篇文章
- $h_{i}$ 則是表示 token $i$ 

> ### Bi-level Event Detector > Low-level detector
- a sequence labeling problem
- Label set : 包含 $k$ 個事先標注好的事件和特殊 label $O$ (代表無事件)
  - $L = \{e_{1}, e_{2}, ..., e_{k}, O\}$ 
- 將新聞文本表示為一連串 tokens $x$
  - $x = (x_{1}, x_{2}, ..., x_{n})$
- 新聞文本的 label 為 $y$
  - $y= (y_{1}, y_{2}, ..., y_{n})$
- 相同的事件可能在同一篇新聞中被多次提到，且同一篇新聞可能包含多事件
- 假設有一 $x$ 的子序列（序列中其中一段） $x'$ 代表了事件 $i$ ，則 label 序列 $y$ 中的第 t ~ t+s 項 $\{y_{j}\}_{j=t}^{t+s}$ 會存入 “$e_{i}$”
  - $x' = (x_{t}, x_{t+1}, ..., x_{t+s})$
- 其餘無事件的字都標為 $O$

> ### Bi-level Event Detector > High-level detector
- 基於 `low-level detector` 的預測標注，結合整篇文章的語意理解，預測各個事件的存在機率；如果存在機率大於設定好的 threshold 代表事件 $i$ 存在。
- 如果機率超過門檻表事件存在，則立即計算與事件相關的股票的買賣點

### Trading Strategy

1. 從新聞中識別交易信號
2. 識別該篇新聞影響到的股票
3. 取得新聞發佈時間附近的股價
4. 執行交易策略

> ### Ticker Recognizer
前提
- 論文簡化的設定，只識別新聞對哪個股票的影響最高

作法
- 透過字面比對
- 對標題提及的股票加權最高

> ### Trading Policy
前提
- 為了最小化其他因子的影響，論文的交易標的僅限股票（沒有選擇權等）

作法
- 根據偵測到的「事件種類」關聯到 $long$ 或 $short$ 兩種交易信號
- $long$ : 會使股價上漲的事件類型
- $short$ : 會使股價下跌的事件類型

Policies
- 都在事件被偵測的當下最近可交易時間進行交易
- 以兩種 policy 衡量從事件發生後的交易最高能獲得多少利潤
- `Trade-At-End (k)` : 從事件發生就一直持有 k 日後(/賣出)直到下市賣出(/持有)
- `Trade-At-Best (k)` : 從事件發生一直持有 k 日後(/賣出)到最佳價格時賣出(/持有)


## Data

### EDT Dataset
可用的訓練目標
1. corporate event detection 公司事件偵測
2. news-based trading benchmark 基於新聞的交易策略標竿
3. financial domain adaptation 讓模型熟悉金融文本知識

> ### (1) corporate event detection 公司事件偵測

根據對金融領域的了解篩選 11 種公司事件

有納入這11類事件的特性
- 對股價較有直接影響的
- 非週期性（**因為如果是有週期性的，投資人可以提前算到時間，價格波動可能比事件更早發生**）

11 種事件
1. Guidance Increase (GI) 公司預估業績指引報告
   - 公司對自己季、年營收的公開預估提升或升級
2. Acquisition (A) 收購
   - 一間公司購買其他公司的所有或部分資產或股份
3. New Contract (NC) 簽訂新合約
4. Stock Split (SS) 股票分割
   - 現有股票價格稀釋、股數增加
5. Reverse Stock Split (RSS) 反向股票分割
   - 將公司已發行的股份數量縮小、每股價格上升、股東持有比例不變
6. Positive Clinical Trial & FDA Approval (CT)
   - 人體臨床實驗有正向結果
   - 獲得 FDA 批准可在美國市場合法上市販售
7. Stock Repurchase (SR) 股票回購
   - 上市公司將其發行在外的普通股買回，可用來振興股價、改變資本結構、防惡意收購
8. Dividend (RD) 發放股息
9.  Dividend Cut (DC) 股息縮減
10. Dividend Increase (DI) 股息增加
11. Special Dividend (SD) 發放特殊股息

![](https://i.imgur.com/i7LerR3.png)

- 9721 篇英文新聞，其中 2266 篇有至少一項事件，其餘 7455 篇沒有包含任何事件
- 新聞來源於 PRNewswire, Businesswire, GlobeNewswire 的關鍵詞搜尋結果，關鍵詞是根據每種事件的範例人為決定的
- 新聞經字符清洗後，將標題、副標題、內文 concat 起來當做 input
- 事件標注由兩位標注人執行，以 low-level token based ，將最代表事件的句子標出
- 雖幾篩選 80% 有包含事件的新聞和 80% 無事件新聞組成 training set; 其餘則為 validation set

> ### (2) News-based stock prediction strategy benchmark
- 303893 篇英文新聞來自
  - PRNewswire : 2020/05 - 2021/04
  - Businesswire : 2020/08/16 - 2021/05/06
  - 已排除 (1) 的 training set 新聞
  - 並確保每種事件都至少有數篇新聞包含
- 每篇新聞都有 minute-level timestamp, 受影響的公司, 公司的股價變化量 (price labels)
    - （開市期間）下一分鐘的 open, close 
    - 接下來 1/2/3 天的 highest, lowest, close
    - 若該篇新聞沒有受影響的公司或該公司缺乏歷史股價，則 price labels 為空值
    - 共 106619 篇新聞有非空值 price labels

> ### (3) Domain Adaptation
- 包含財經新聞和財經專有名詞百科全書 (unstructured domain knowledge)
  - 財經專有名詞 來自 `Investopedia` : 6260 explanatory documents 每篇介紹一個財經專有名詞及其在財經市場扮演的角色
  - 財經新聞 使用 (1) 的 training set


## Experiments


