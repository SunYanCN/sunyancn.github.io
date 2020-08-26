---
title: kashgari学习
tags: kashgari
mathjax: false
abbrlink: 10170
date: 2019-10-08 20:05:23
---
## 文本分类
### 训练和保存模型代码
```python
import kashgari
from kashgari.corpus import SMP2018ECDTCorpus
from kashgari.tasks.classification import BiLSTM_Model
from kashgari.embeddings import BERTEmbedding
from kashgari.callbacks import EvalCallBack
from tensorflow.python import keras
from kashgari import utils

# 有GPU的话
kashgari.config.use_cudnn_cell = True

# 数据集
train_x, train_y = SMP2018ECDTCorpus.load_data('train')
valid_x, valid_y = SMP2018ECDTCorpus.load_data('valid')
test_x, test_y = SMP2018ECDTCorpus.load_data('test')

# '<PRE_TRAINED_BERT_MODEL_FOLDER>'：BERT模型路径
bert_embed = BERTEmbedding('/home/new/Toxicity/bert_model/models/chinese_L-12_H-768_A-12',
                           task=kashgari.CLASSIFICATION,
                           sequence_length=100)

model = BiLSTM_Model(bert_embed)
tf_board_callback = keras.callbacks.TensorBoard(log_dir='./logs', update_freq=1000)

eval_callback = EvalCallBack(kash_model=model,
                             valid_x=valid_x,
                             valid_y=valid_y,
                             step=5)

model.fit(train_x,
          train_y,
          valid_x,
          valid_y,
          batch_size=100,
          callbacks=[eval_callback, tf_board_callback])

model.evaluate(test_x, test_y)

# 保存模型到 `saved_classification_model` 目录下
model.save('saved_classification_model')

# 加载保存模型
loaded_model = kashgari.utils.load_model('saved_classification_model')

# 使用模型进行预测
loaded_model.predict(test_x[:10])

# Save model
utils.convert_to_saved_model(model, 
                             model_path='saved_model/blstm', 
                             version=1)

```
<!--more-->
### 模型部署
### 多标签
## 命名实体识别


