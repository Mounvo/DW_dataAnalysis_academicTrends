# Task4 论文种类分类

## 论文说明

+ 学习主题：论文分类（数据建模任务），利用已有数据建模，对新论文进行类别分类；
+ 学习内容：使用论文标题完成类别分类；
+ 学习成果：学会文本分类的基本方法、`TF-IDF`等；



## 数据处理步骤

在原始arxiv论文中论文都有对应的类别，而论文类别是作者填写的。在本次任务中我们可以借助论文的标题和摘要完成：

- 对论文标题和摘要进行处理；
- 对论文类别进行处理；
- 构建文本分类模型；



## 文本分类思路

- 思路1：TF-IDF+机器学习分类器

直接使用TF-IDF对文本提取特征，使用分类器进行分类，分类器的选择上可以使用SVM、LR、XGboost等

- 思路2：FastText

FastText是入门款的词向量，利用Facebook提供的FastText工具，可以快速构建分类器

- 思路3：WordVec+深度学习分类器

WordVec是进阶款的词向量，并通过构建深度学习分类完成分类。深度学习分类的网络结构可以选择TextCNN、TextRnn或者BiLSTM。

- 思路4：Bert词向量

Bert是高配款的词向量，具有强大的建模学习能力。



## 具体代码实现以及讲解

为了方便大家入门文本分类，我们选择思路1和思路2给大家讲解。首先完成字段读取：

``` python
# 导入所需的package
import seaborn as sns #用于画图
from bs4 import BeautifulSoup #用于爬取arxiv的数据
import re #用于正则表达式，匹配字符串的模式
import requests #用于网络连接，发送网络请求，使用域名获取对应信息
import json #读取数据，我们的数据为json格式的
import pandas as pd #数据处理，数据分析
import matplotlib.pyplot as plt #画图工具
```

``` python
def readArxivFile(path, columns=['id', 'submitter', 'authors', 'title', 'comments', 'journal-ref', 'doi',
       'report-no', 'categories', 'license', 'abstract', 'versions',
       'update_date', 'authors_parsed'], count=None):
    '''
    定义读取文件的函数
        path: 文件路径
        columns: 需要选择的列
        count: 读取行数
    '''
    
    data  = []
    with open(path, 'r') as f: 
        for idx, line in enumerate(f): 
            if idx == count:
                break
                
            d = json.loads(line)
            d = {col : d[col] for col in columns}
            data.append(d)

    data = pd.DataFrame(data)
    return data

data = readArxivFile('arxiv-metadata-oai-snapshot.json', 
                     ['id', 'title', 'categories', 'abstract'],
                    200000)

```

为了方便数据的处理，我们可以讲标题和摘要拼接一起完成分类。

``` python
data['text'] = data['title'] + data['abstract']

data['text'] = data['text'].apply(lambda x: x.replace('\n',' '))
data['text'] = data['text'].apply(lambda x: x.lower())
data = data.drop(['abstract', 'title'], axis=1)
```

由于原始论文有可能有多个类别，所以也需要处理：

``` python
# 多个类别，包含子分类
data['categories'] = data['categories'].apply(lambda x : x.split(' '))

# 单个类别，不包含子分类
data['categories_big'] = data['categories'].apply(lambda x : [xx.split('.')[0] for xx in x])
```

然后将类别进行编码，这里类别是多个，所以需要多编码：

``` python
from sklearn.preprocessing import MultiLabelBinarizer
mlb = MultiLabelBinarizer()
data_label = mlb.fit_transform(data['categories_big'].iloc[:])
```

### 思路1

思路1使用TFIDF提取特征，限制最多4000个单词：

``` python
from sklearn.feature_extraction.text import TfidfVectorizer
vectorizer = TfidfVectorizer(max_features=4000)
data_tfidf = vectorizer.fit_transform(data['text'].iloc[:])
```

由于这里是多标签分类，可以使用sklearn的多标签分类进行封装：

``` python
# 划分训练集和验证集
from sklearn.model_selection import train_test_split
x_train, x_test, y_train, y_test = train_test_split(data_tfidf, data_label,
                                                 test_size = 0.2,random_state = 1)

# 构建多标签分类模型
from sklearn.multioutput import MultiOutputClassifier
from sklearn.naive_bayes import MultinomialNB
clf = MultiOutputClassifier(MultinomialNB()).fit(x_train, y_train)
```

```
             precision    recall  f1-score   support

           0       0.95      0.85      0.89      7925
           1       0.85      0.79      0.82      7339
           2       0.77      0.72      0.74      2944
           3       0.00      0.00      0.00         4
           4       0.72      0.48      0.58      2123
           5       0.51      0.66      0.58       987
           6       0.86      0.38      0.52       544
           7       0.71      0.69      0.70      3649
           8       0.76      0.61      0.68      3388
           9       0.85      0.88      0.87     10745
          10       0.46      0.13      0.20      1757
          11       0.79      0.04      0.07       729
          12       0.45      0.35      0.39       507
          13       0.54      0.36      0.43      1083
          14       0.69      0.14      0.24      3441
          15       0.84      0.20      0.33       655
          16       0.93      0.16      0.27       268
          17       0.87      0.43      0.58      2484
          18       0.82      0.38      0.52       692

   micro avg       0.81      0.65      0.72     51264
   macro avg       0.70      0.43      0.50     51264
weighted avg       0.80      0.65      0.69     51264
 samples avg       0.72      0.72      0.70     51264
```

### 思路2

思路2使用深度学习模型，单词进行词嵌入然后训练。将数据集处理进行编码，并进行截断：

``` python
from sklearn.model_selection import train_test_split
x_train, x_test, y_train, y_test = train_test_split(data['text'].iloc[:100000], 
                                                    data_label[:100000],
                                                 test_size = 0.95,random_state = 1)
```

``` python
# parameter
max_features= 500
max_len= 150
embed_size=100
batch_size = 128
epochs = 5

from keras.preprocessing.text import Tokenizer
from keras.preprocessing import sequence

tokens = Tokenizer(num_words = max_features)
tokens.fit_on_texts(list(data['text'].iloc[:100000]))

y_train = data_label[:100000]
x_sub_train = tokens.texts_to_sequences(data['text'].iloc[:100000])
x_sub_train = sequence.pad_sequences(x_sub_train, maxlen=max_len)
```

定义模型并完成训练：

``` python
# LSTM model
# Keras Layers:
from keras.layers import Dense,Input,LSTM,Bidirectional,Activation,Conv1D,GRU
from keras.layers import Dropout,Embedding,GlobalMaxPooling1D, MaxPooling1D, Add, Flatten
from keras.layers import GlobalAveragePooling1D, GlobalMaxPooling1D, concatenate, SpatialDropout1D# Keras Callback Functions:
from keras.callbacks import Callback
from keras.callbacks import EarlyStopping,ModelCheckpoint
from keras import initializers, regularizers, constraints, optimizers, layers, callbacks
from keras.models import Model
from keras.optimizers import Adam

sequence_input = Input(shape=(max_len, ))
x = Embedding(max_features, embed_size, trainable=True)(sequence_input)
x = SpatialDropout1D(0.2)(x)
x = Bidirectional(GRU(128, return_sequences=True,dropout=0.1,recurrent_dropout=0.1))(x)
x = Conv1D(64, kernel_size = 3, padding = "valid", kernel_initializer = "glorot_uniform")(x)
avg_pool = GlobalAveragePooling1D()(x)
max_pool = GlobalMaxPooling1D()(x)
x = concatenate([avg_pool, max_pool]) 
preds = Dense(19, activation="sigmoid")(x)

model = Model(sequence_input, preds)
model.compile(loss='binary_crossentropy',optimizer=Adam(lr=1e-3),metrics=['accuracy'])
model.fit(x_sub_train, y_train, 
          batch_size=batch_size, 
          validation_split=0.2,
          epochs=epochs)
```

```
Epoch 1/5
625/625 [==============================] - 103s 161ms/step - loss: 0.2149 - accuracy: 0.4019 - val_loss: 0.1167 - val_accuracy: 0.6583
Epoch 2/5
625/625 [==============================] - 102s 163ms/step - loss: 0.1141 - accuracy: 0.6699 - val_loss: 0.1058 - val_accuracy: 0.6883
Epoch 3/5
625/625 [==============================] - 103s 165ms/step - loss: 0.1059 - accuracy: 0.6923 - val_loss: 0.0998 - val_accuracy: 0.7059
Epoch 4/5
625/625 [==============================] - 103s 165ms/step - loss: 0.1000 - accuracy: 0.7019 - val_loss: 0.0962 - val_accuracy: 0.7171
Epoch 5/5
625/625 [==============================] - 105s 168ms/step - loss: 0.0961 - accuracy: 0.7143 - val_loss: 0.0950 - val_accuracy: 0.7214
```

