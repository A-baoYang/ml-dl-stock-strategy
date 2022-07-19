# 語義角色標註用於台股預測

https://hackmd.io/@GFIdYvfqTVq3NkpW4eRBig/S1IvXP9sc

## Context

- 不同資料集版本和label計算方式的模型成效
- 根據預測結果的回測成效
- 資料準備流程自動化
- 解決過的技術問題整理

---

## SRLP 模型成效
> based on 不同資料集版本和label計算方式

- 預測錯誤當天有數則新聞，本來個股是上漲但受到大盤下跌新聞影響而預測錯誤 => 改為新聞標題若無直接提及公司或相關產業、概念股的訊息才使用其他新聞，否則當天該個股要濾掉無提及該公司名稱的新聞標題
  - stock id name 比對(直接用goodinfo上的名稱)
  - 文本產業／概念股歸類(直接用goodinfo上的名稱)
- 發現預測錯誤的 labels 其 pct_chg_next 的絕對值較小且相對集中
  - labels 的切分用固定級距 先不要用比例 因為會受到不同時間區段的影響
  - 看是否能抓到比較大的價格波動為主

## 回測成效
> based on SRLP 模型預測結果

## 資料準備流程自動化

## 解決過的技術問題整理


> ###  #Prob1 : 語義角色標註（以下簡稱 SRL）的結果要轉換成 token mask
> `[已解決]` [已將結果留言到原作 issue](https://github.com/JinanZou/Astock/issues/2)


原本使用 LTP 產生的 SRL 結果

![](https://i.imgur.com/dmtWsiL.png)
![](https://i.imgur.com/GNbpxpg.png)

上面的編號是來自分詞陣列

![](https://i.imgur.com/HYp69h9.png)

這篇論文不是直接將句子轉成 token embeddings 的方式，而是要在句子中做 mask ，透過 self-supervised learning 對 mask 掉的動詞學習語意，以增加效果

![](https://i.imgur.com/iaIAz30.png)

> mxn 矩陣，其中 
> - m: 最大文本長度
> - n: 事件數

之前卡住的小問題：**BERT Tokenizer 產生的 token 和 LTP 的分詞表，當遇到非中文字元時會造成長度落差**

![](https://i.imgur.com/uxu7lf3.png)

> tokenizer 輸出的結果會將英文、數字合併或拆解

有測試過：

- 用 BERT Tokenizer tokenize 後的分詞結果接進去 LTP srl model，但缺點是同一組動詞被拆字到不同三元組，有些動詞會缺字，缺失掉 LTP 已經做到的分詞的效果

![](https://i.imgur.com/5aTUqNX.png)

> ### 解決方式

- 後來發現不用那麽複雜，因為 LTP 的分詞會自動將空格排除，可以直接拿 LTP 分詞結果組成最後要輸入到模型的文本。
- 而英文詞彙在句子中的 tokenize 會隨著搭配不同上下文改變的問題，透過進行『兩次 LTP 分詞』來解決；第一次分詞會將空格移除，等於把英文詞彙 token 變化的變因拿掉，以第一次分詞後的結果作為模型訓練要用的文本。第二次分詞的隱藏層向量則為 LTP 語義角色模型推論的輸入資料。
- 由於事件動詞、主詞、受詞的位置是來自於分詞表，就把分詞表用 BERT Tokenizer 轉成 token，用位置累加的方式就可以得到正確的 tokenizer。

代碼請參考我的 repo : https://github.com/A-baoYang/Astock-on-TWSE/blob/twse/transform.py

<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2FA-baoYang%2FAstock-on-TWSE%2Fblob%2Ftwse%2Ftransform.py&style=github&showBorder=on&showLineNumbers=on&showFileMeta=on&showCopy=on"></script>

![](https://i.imgur.com/PGXpUNY.png)


> ###  #Prob2 : Model & Data Parellal 
> `[已解決]` 

論文參數
- batch_size : 16
- max_length : 300

我們採用
- batch_size : 16
- max_length : 512

一顆 GPU 會 `RuntimeError: CUDA out of memory.`

![](https://i.imgur.com/SSfneq6.png)

> ### PyTorch 中不同的分散和平行運算

- [Single-Machine Model Parallel](https://pytorch.org/tutorials/intermediate/model_parallel_tutorial.html) | [example1](https://github.com/chi0tzp/pytorch-dataparallel-example/blob/master/main.py) | [DataParallel function explanation](https://pytorch.org/tutorials/beginner/former_torchies/parallelism_tutorial.html)
- [Multi-Machine Model Parallel - DistributedDataParallel (DDP)](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html)


使用 Single-Machine Model Parallel，調用 PyTorch function `nn.DataParallel` : 

```python=
model = SentimentClassifier(n_classes=3)
if torch.cuda.device_count() > 1:
    print("Using", torch.cuda.device_count(), "GPUs")
    model = nn.DataParallel(model.cuda(), device_ids=[0, 1, 2, 3])
model = model.to(device)

```

但出現錯誤 `AssertionError: Gather function not implemented for CPU tensors`

查詢一些論壇大概了解可能是 model 和 tensor data 沒有都移到 GPU 上的緣故

最後將模型隱藏層和 forward 中運算、傳輸的 tensors 都加上 `.to(device)`，才終於順利跑起

![](https://i.imgur.com/MgcabmJ.png)


