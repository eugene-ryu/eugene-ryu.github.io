---
title: "[NLP] Character-Aware Neural Language Models"
excerpt: "Character aware neural language models 논문 리뷰"

categories: 
  - Blog
tags:
  - [machine learning, paper, review, NLP, LSTM, CNN, natural language processing]

toc: true
toc_sticky: true

date: 2019-10-29
last_modified_at: 2021-11-20
---

# Character-Aware Neural Language Models


## Contribution
Suggest a way for improvement word-level language model with character level embeddings<br><br>

### pros and cons of the approach
Used network in the paper was general things at the time, but input was different, character-level embeddings. The results was better if the language had more various morphemes. Yet character-level embeddings has a tradeoff between efficiency and time.<br><br><br>


## Model Architecture
![char-aware-model-architecture](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbAK3bn%2FbtqFfuNXW6B%2Fedf9seGCNhhpQVwKa7Q8rK%2Fimg.png)<br><br><br>



## Summary
### 1. Preprocess
Use Penn Tree Bank dataset for English. (Mikolov., vocabulary size: 10K)<br><>br
use different three embedding matrices<br><br>
1) character-level embedding matrix (this matrix used as an input for network. This is a contribution point of the paper)<br>
2) word-level embedding matrix (for comparision)<br>
3) morpheme matrix (prefix + stem + suffix + word-level embedding matrix)<br><br>


### 2. CharCNN
1) kernel width w = [1,2,3,4,5,6,7] (a large model), w = [1,2,3,4,5,6] (a small model)<br>
2) height: 15 dimension and the number of hidden was [50,100,150,200,200,200,200]<br>
3) Took a max value of each convolution. After stride, apply a tanh, then we got a vector that a collection of max values per a filter. Got a max value of the vector, again.<br>
4) concatenate all the max values. This vector will be an input for Highway Network. <br><br>


### 3. Highway Network
Input a vector **y** that the output of CharCNN.<br><br>
1) Got y and do affine transformation, W_(T) * y + b_(T) (embedding matrix * input + bias)<br>
2) Apply sigmoid function to the result of 1). This is a transforma gate **t**.<br>
3) Do the element-wise product to the result of 2) and ReLU(W_(H) * y + b_(H))<br>
4) Do (1-**t**) This is a carry gate<br>
5) Product carry gate and y<br>
6) Lastly, Compute the result of 3) and 5), then send it to LSTM<br><br><br>


All the W_(T) and W_(H) were square matrices, for computational convenience. (nn.Linear(dim, dim) in PyTorch)<br><br>

- the bias of the Network: -2<br>
- the number of Layers: 2<br><br>


### 4. LSTM
- will be add <br><br><br>



## Character-level Convolutional Neural Network
|notation|explanation|
|:-----------|:--------------------------------------------------|
|t: timestep|[someone, has, a, dream, ...]|
|C: vocabulary of characters|the size of character embeddings: 15|
|d: the dimensionality of character embeddings|d=15<br>If word=<k','n'.'o','w'>, then the dimension of character-level embedding vector is 15 by 4|
|Q: R^(dx｜C｜), matrix character embeddings|｜C｜ is a cardinality of all characters. Q is a character embedding matrix for all words|
|C^(k): a matrix of character embedding for word k|if word=<'k','n','o','w'> then 15 by 65. 65 was a max_word_length in the codes. START,END,EOS were applied as well.|   
|H: filter, H is a subset of R^(d x w)|w is a width of convolution filter, w = [1,2,3,4,5,6,7] (a large model) <br>Thus, d x w means [15 by 1],[15 by 2],[15 by 3],[15 by 4],[15 by 5],[15 by 6],[15 by 7]. All these H kernel matrix will be product to all words.| 
|f^(k) is a subset of R^(l-1): a feature map||
|h (small h)|the hidden size of kernels, 650 (a large model)|<br><br><br>
    
    
## Creation of a feature map f
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdGvEhS%2FbtqDTk0qulm%2F2FXz45xdwJMfWe0EoCy7Qk%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FQxhq2%2FbtqDTG904s1%2FXdX1fmlysMvsqTMNgfC6m1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FQxhq2%2FbtqDTG904s1%2FXdX1fmlysMvsqTMNgfC6m1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxvebW%2FbtqDSjOsR4Z%2FFxXMEkjEs6gG3Y4XbYgHek%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F5P1dC%2FbtqDQ7Vv2V8%2FYLdNZIYz6nUWQUEcA79S3K%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FL0g02%2FbtqDQtktQS8%2FN1i39wzx4Kjj7AOy60W560%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdD3IG2%2FbtqDRvV9sTb%2F6EvfWNUgguWaezmpB7A9X0%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fl3fCh%2FbtqDSAJcTxv%2FM7utMGRyv3Eykv019UPrS1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrR4FH%2FbtqDQsTj0Tz%2FfN0Mc8irZ80xXJHeLhrNu0%2Fimg.png)<br><br>
In the paper, describe the product of kenel matrix H as a character n-gram. <br><br><br>

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FoocoV%2FbtqDShXoyaP%2FIEnKnkqTWNYjcBzDAgQjE1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwXN89%2FbtqFyfKrqgS%2FCtYHwshUJ389XTDaKzBUm1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbfQy2s%2FbtqDQtdDu7F%2FIuXb7u0Y4O8qkk6kHX73Ek%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmXUAr%2FbtqDQsy3xU8%2FEwJsug9symEbBuHuq8R98K%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbDyD5o%2FbtqFvfFacaR%2F2fpl9FtMXqzrKezKC1Azf0%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd2ZQJd%2FbtqDTGWuesR%2FaLZhsAPXd9BCLk8gBItnZ1%2Fimg.png)<br><br>

The result of these process is y^(k) (a vector). y^(k) is a matrix representation of specific word k and y is a vector, too.<br><br><br>


## Highway Network
We could simple replace x^(k) with y^(k) at each t in the RNN-LM.<br><br>
y: output from CharCNN<br>
By construction, the dimensions of y and z have to match, and hence W_(T) and W_(H) are square matrices.<br><br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fubbw8%2FbtqFdLXGnoy%2FOU3jDB25rvn0ig2wYsDyE1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fs0GKi%2FbtqFe3iJUEn%2FrsyGKl8oRYukH97V4cpLR1%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb62XIN%2FbtqFvuIN75q%2FPza11OKTvaphByvWdKWGSK%2Fimg.png)<br><br><br>


## Recurrent Neural Network Language Model
V: fixed size of vocabulary of words<br>
P: output embedding matrix<br>
p^(j): output word embedding<br>
q: bias term (=b)<br>
g: composition function<br><br><br>

Our model simply replaces the input embeddings X with the output from a character-level convolutional neural network. <br><br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FRukxf%2FbtqFe3C5uvx%2F5ptFYada7Tu7N0afN8a8G0%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbLLucR%2FbtqFyEweyUH%2FHf3BHnkU4WEDbL2Y5Yk0E0%2Fimg.png)<br><br><br>

## Baselines
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSl91q%2FbtqFe2KYXe0%2FAe3pzuoQW1HXZRYxuBo15k%2Fimg.png)<br><br><br>

## Optimization
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbQKRQt%2FbtqFvHVuW1v%2FFYjQ6bdVPuFYekOforT7M0%2Fimg.png)<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbiT4qb%2FbtqFvvOx84R%2Flt2Ev6ZPGA2hCHohJGfwf0%2Fimg.png)<br><br>

Dropout<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcvP1lU%2FbtqFyEbYSQW%2FIVRJN4gIr5hfMQYgW78my0%2Fimg.png)<br><br>

Hierarchical Softmax<br>
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRiKaz%2FbtqFvIfNO3x%2FlT3OFwRZnupcNK06IGQU3K%2Fimg.png)<br><br>

The first term of the equations is the probability of picking cluster r.<br>
The second term of the equations is the probability of picking word j (with respect to given cluster r)<br><br>
We found that hierarchical softmax was not necessary for models train on DATA-S.<br><br>

References<br>
1) http://web.stanford.edu/class/cs224n/<br>
2) https://www.quantumdl.com/entry/3%EC%A3%BC%EC%B0%A81-CharacterAware-Neural-Language-Models<br>