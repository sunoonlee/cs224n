## Lecture 2: Word Vectors

### lecture plans

* word meanings
* word2vec introduction
* word2vec objective function gradients
* optimization refresher 

### 词义表示与词向量

如何表示词的含义？一种方法是借助 WordNet 这种人工录入的词分类，但有较大局限性。传统上常把词看做 atomic symbols，表示成向量就是超高维的 one-hot 向量，缺点是无法表达词与词的联系，而且非常稀疏。

目前流行的方法则是用一个低维的稠密词向量，维度一般是 25~1000。获得词向量的方式是利用词的上下文，也就是说，词的上下文决定了词的含义。

具体实现方法有两类：

一是对 cooccurrence 矩阵 X 进行 SVD 分解: `X = USV'`，然后可以取 U 的前 k 列作为词向量。这种方法的缺点是：
* 计算复杂度是三次方
* 不容易添加新词或新语料
* 学习的方式跟其他深度学习模型不同? (different learning regime than other DL models)

二是更为常用的基于迭代的方法（iteration based methods)。Word2vec 便是典型代表。

### word2vec

cs224n 对 word2vec 的介绍对新手并不友好，但课程建议阅读的材料里，有一篇非常棒：[Word2Vec Tutorial - The Skip-Gram Model · Chris McCormick](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/)。

word2vec 的基本架构并不复杂，不过是一个单隐层神经网络，隐层不加激活函数，输出层用 softmax 激活。复杂之处可能在于具体的训练过程，特别是出于工程考虑采用的一些技巧。

word2vec 采用的基本模型用两种，一是上面文章里介绍的 skip-gram，二是 continuous bag-of-words(CBOW)。区别是前者由中心词预测 context，后者则相反。前者应用更广。

word2vec 的具体训练方法就是更高级的话题了，涉及很多工程上的考虑。常用的两种高效的方法，一是 negative sampling, 二是 hierarchical softmax (这个真没看懂)。Chris McCormick 还写过一篇 [Word2Vec Tutorial Part 2 - Negative Sampling](http://mccormickml.com/2017/01/11/word2vec-tutorial-part-2-negative-sampling/)，介绍了 word2vec 作者提出的改进 skip-gram model 的一些方法，包括：
* 把 word pairs、phrases 看做整体处理
* 必要时忽略 the 这种高频词
* negative sampling, 这是文章的重点

Negative sampling 是指，通过在训练时的近似处理，大大减小每条 training example 更新 weights 矩阵的计算量(注意这里的 sampling 跟输入的样本不是一个概念)。具体来说，本来每个 training example 会让 weights 矩阵中所有词对应的行或列都更新一遍，但实际上这条 example 只对应一个 true word，对其余所有的 negative words 对应的权重影响很小，因此可以只从中选最高频的几个(5-20 个)。
