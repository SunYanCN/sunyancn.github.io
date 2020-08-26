---
title: cdqa使用方法
tags: cdqa
mathjax: false
abbrlink: 37942
date: 2019-10-09 16:47:35
---
文档：[Closed Domain Question Answering](https://github.com/cdqa-suite/cdQA)
## 使用CSV文件数据
```python
import os
import pandas as pd
from ast import literal_eval

from cdqa.utils.filters import filter_paragraphs
from cdqa.pipeline import QAPipeline

#Download pre-trained reader model and example dataset
from cdqa.utils.download import download_model, download_bnpp_data
download_bnpp_data(dir='./data/bnpp_newsroom_v1.1/')
download_model(model='bert-squad_1.1', dir='./models')
#Visualize the dataset
df = pd.read_csv('./data/bnpp_newsroom_v1.1/bnpp_newsroom-v1.1.csv', converters={'paragraphs': literal_eval})
df = filter_paragraphs(df)
print(df.head())
#Instantiate the cdQA pipeline from a pre-trained CPU reader
cdqa_pipeline = QAPipeline(reader='./models/bert_qa_vCPU-sklearn.joblib')
cdqa_pipeline.fit_retriever(df=df)
#Execute a query
query = 'Since when does the Excellence Program of BNP Paribas exist?'
prediction = cdqa_pipeline.predict(query)
#Explore predictions
print('query: {}'.format(query))
print('answer: {}'.format(prediction[0]))
print('title: {}'.format(prediction[1]))
print('paragraph: {}'.format(prediction[2]))
```
<!--more-->
## PDF文档数据
```python
import os
import pandas as pd
from ast import literal_eval

from cdqa.utils.converters import pdf_converter
from cdqa.utils.filters import filter_paragraphs
from cdqa.pipeline import QAPipeline
from cdqa.utils.download import download_model

# Download model
download_model(model='bert-squad_1.1', dir='./models')
# Download pdf files from BNP Paribas public news
def download_pdf():
    import os
    import wget
    directory = './data/pdf/'
    models_url = [
      'https://invest.bnpparibas.com/documents/1q19-pr-12648',
      'https://invest.bnpparibas.com/documents/4q18-pr-18000',
      'https://invest.bnpparibas.com/documents/4q17-pr'
    ]

    print('\nDownloading PDF files...')

    if not os.path.exists(directory):
        os.makedirs(directory)
    for url in models_url:
        wget.download(url=url, out=directory)

download_pdf()
df = pdf_converter(directory_path='./data/pdf/')
print(df.head())

cdqa_pipeline = QAPipeline(reader='./models/bert_qa_vCPU-sklearn.joblib', max_df=1.0)
# Send model to GPU
cdqa_pipeline.cuda()
# Fit Retriever to documents
cdqa_pipeline.fit_retriever(df)
#Execute a query
query = 'How many contracts did BNP Paribas Cardif sell in 2019?'
prediction = cdqa_pipeline.predict(query)
#Explore predictions
print('query: {}'.format(query))
print('answer: {}'.format(prediction[0]))
print('title: {}'.format(prediction[1]))
print('paragraph: {}'.format(prediction[2]))
```
## 训练模型
```python
import os
import torch
import joblib
from cdqa.reader import BertProcessor, BertQA
from cdqa.utils.download import download_squad
download_squad(dir='./data')
train_processor = BertProcessor(do_lower_case=True, is_training=True, n_jobs=-1)
train_examples, train_features = train_processor.fit_transform(X='./data/SQuAD_1.1/train-v1.1.json')
#Train the model
reader = BertQA(train_batch_size=12,
                learning_rate=3e-5,
                num_train_epochs=2,
                do_lower_case=True,
                output_dir='models')

reader.fit(X=(train_examples, train_features))
#Send model to CPU
reader.model.to('cpu')
reader.device = torch.device('cpu')
#Save CPU model locally
joblib.dump(reader, os.path.join(reader.output_dir, 'bert_qa_vCPU.joblib'))
```