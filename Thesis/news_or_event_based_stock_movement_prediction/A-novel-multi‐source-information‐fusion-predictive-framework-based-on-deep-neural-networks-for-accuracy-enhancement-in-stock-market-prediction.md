# A novel multi‐source information‐fusion predictive framework based on deep neural networks for accuracy enhancement in stock market prediction

- Author: Isaac Kofi Nti, Adebayo Felix Adekoya and Benjamin Asubam Weyori
- Source: https://journalofbigdata.springeropen.com/articles/10.1186/s40537-020-00400-y
- Year: 2021

## Context
- Goal
- Related Works
- Solutions

## Contribution 
- Since integrating these factors from different data sources as one dataset for market analysis is seen as challenging because they come in a different format (numerical or text)
- propose a novel multi-source information-fusion stock price prediction framework based on a hybrid deep neural network architecture of CNN & LSTM named **IKN-ConvLSTM** to integrate 6 heterogeneous data sources
- A random search algorithm as a feature selector to optimise our initial training parameters
- A stacked LSTM network is fine-tuned by using the tuned parameter (features) from the base-model to enhance prediction accuracy
- Performance:  stock data (2017/01/03 - 2020/01/31) from the Ghana Stock Exchange (GSE) => Fusion data sources > individual data sources
    - Accuracy: 98.31%, 
    - specificity (0.9975), 
    - sensitivity (0.8939%) 
    - F-score (0.9672)


## Introduction
- 由於市場資料的多層次和互聯性，混合他們將提供更佳的預測效果；**Multi-source Data-Fusion (MDF)** 成為一個近期熱門研究領域

## Why CNN + LSTM?
- CNN: convolution & pooling => extract the appropriate internal structure from time series data to create in-depth input features
- CNN + LSTM is state-of-the-art regarding noise tolerance & accuracy for time-series classification

## Solution
1. **IKN-ConvLSTM**: CNN + Stacked LSTM
2. fuses 6 heterogeneous indicators:
   - users’ sentiments (tweets), 
   - Web news, 
   - forum discussion
   - Google trends
   - historical macroeconomic variables
   - past stock data
3. Solve data sparsity problem by exploring the association among these information sources using NN
4. Effectiveness evaluation on Ghana stock market

## Related Works
- 66% of these studies utilised historical stock price (Quantitative), 23% qualitative (textual) dataset to predict the future stock prices with various models

### Studies Based on Quantitative Dataset
- [20] the fusion of multiple stock price indicators can improve prediction accuracy
- [22] identified the relation within a given stock dataset
- [23] proposed a multi-indicator feature-selection for CNN-driven stock index prediction based on technical indicators, get a higher performance than baseline in trading simulations. And shows DNN > ARIMA.
- [24] ensemble techniques offer higher prediction accuracy. (Enhanced SVM ensemble with genetic algorithm predictive model)

---

- [25] using technical indicators only cannot exclusively capture the precariousness of price movements.
- [26] behavioural finance shows that the emotions of investors can affect their investment decision making

---

### Studies based on qualitative dataset
- [31] Support Vector Machine (SVM) and Particle Swarm Optimisation (PSO) based on sentiments analysis proved a positive association between stock volume and public sentiment.
- [37] predict SSE 50 Index with public sentiment and achieved an accuracy of 89.93% using SVM
- [40] tweets sentiments in 4 countries proved to have a high association with stock price movement
- [9, 41-43] web search queries could effectively predict stock price volatility (but since user's location is not sure so this result cannot be generalized)
- [41-43] Google Tredn Index can effectively communi cate the future volatility of the stock price

### Studies based on both qualitative and quantitative datasets
- in 122 studies, 
  - 89.38% uses a single data source
  - 8.2% use 2 data sources
  - 2.46% use 3 data sources

![](https://i.imgur.com/5bZuRGb.png)


## Goal
enhance the prediction accuracy, using both quantitative and qualitative stock-related information as input features

## Data 
- Duration: 2017/01/03 - 2020/01/31 (744 trading days)
- 6 sources  concat as a vector
  - users’ sentiments (tweets), (`SM`) (use Tweepy)
    - polarity: range [-1.0, 1.0]
    - subjectivity: range [0.0, 1.0]
    - diffusion 擴散程度
  - Web news, (`W`) (extract sentiment from title only)
    - ghanaweb.com,
    - myjoy-online.com 
    - graphic.com.gh 
  - forum discussion (`FD`)
  - Google trends index (`GTI`)
  - historical macroeconomic variables (`MD`): 44 economic indicators of Ghana
  - past stock data (`HSD`)
- Label: next-day stock return ratio (1 if R > 0 else 0)

![](https://i.imgur.com/FRhWjSd.png)

### Quantitative dataset
- Miss value preprocessed
- Normalized: The dataset was normalised in the range of [−1, 1]

  ![](https://i.imgur.com/jgpA232.png)
  ![](https://i.imgur.com/GqcYltU.png)

### Qualitative (textual) dataset
- forum discussion & tweets & news titles => sentiments
- news => NLTK preprocess (tokenised, segmented, normalised, free noises)

  ![](https://i.imgur.com/vjISxJW.png)

## Framework

![](https://i.imgur.com/2bujcfq.png)

![](https://i.imgur.com/oiDSYD7.png)

### Feature engineering with CNN
- Goal: higher accuracy & reduce computational time
- [59] random search feature selection with a CNN (1 Convolutional Layer + 2 Dense Layers + MaxPooling)
- CNN: CL + MaxPooling + ReLU 
  - MaxPooling: pooling over any feature map bearing a close similarity to the practice of feature selection in finding investments patterns
  - ReLU: easy implementation and vantage of nimbler convergence

\+ Flatten
<br>
\+ ReLU
<br>
\+ Sigmoid


![](https://i.imgur.com/oo8l6uL.png)

- performance when only CNN 

  ![](https://i.imgur.com/jejEptx.png)

### Stacked LSTM
- L1 layer size > L2 layer size (LSTM layer 1: 40 blocks, LSTM layer 2: 20 blocks)
- L1 layer : recognising general features 
- L2 layer : aimed at specific features

### Training Arguments
- Adam : combines strength of ADAgrad and RMSprop
- grid search for hyperparameters tuning,

  ![](https://i.imgur.com/8p5wCQ1.png)


## Performance of IKN-ConvLSTM

- Comparing with 3 baselines

  ![](https://i.imgur.com/1qLFizm.png)

- IKN-ConvLSTM
  
  ![](https://i.imgur.com/yYIZ1Zz.png)

- Comparing Quantitative(Structured) v.s. Qualitative(Unstructured) accuracy

  ![](https://i.imgur.com/ME8QJbj.png)
 
  ![](https://i.imgur.com/foHTN4K.png)

