# 参考になる実装

## package

```
import logging
import os
import pickle
import random
import re
from collections import defaultdict
from omegaconf import OmegaConf
```

defaultdict<br>
https://docs.python.org/ja/3.6/library/collections.html#collections.defaultdict

re<br>
https://docs.python.org/ja/3/library/re.html

random<br>
https://docs.python.org/ja/3/library/random.html

pickle<br>
https://docs.python.org/ja/3/library/pickle.html

os<br>
https://docs.python.org/ja/3/library/os.html

logging<br>
https://docs.python.org/ja/3.11/howto/logging.html

## config

OmegaConf<br>
https://omegaconf.readthedocs.io/en/2.3_branch/

yamlファイルの例
~~~
mode: train
cache_dir: /hoge/cache
model_dir: /hoge/model
list:
- 1
- a: '1'
  b: '2'
  3: c
~~~

~~~
config_path = '/config/my-config.yml'
args = OmegaConf.load(config_path)
~~~

'cache_dir'、'model_dir'の指定があればフォルダを作成する

~~~
if ('cache_dir' in args.keys()) and ('model_dir' in args.keys()):
    os.makedirs(args.cache_dir, exist_ok=True)
    os.makedirs(args.model_dir, exist_ok=True)
~~~

configの要素

~~~
if args.mode == 'train':
    something
~~~

## logger

loggerの初期化方法

~~~
def create_logger(dir_path):
    logger = logging.getLogger(__name__)
    logger.handlers = []
    
    stream_handler = logging.StreamHandler()
    file_handler = logging.FileHandler(os.path.join(dir_path, 'experiment.log'))

    formatter = logging.Formatter('[%(asctime)s][%(levelname)s]:%(message)s')
    stream_handler.setFormatter(formatter)
    file_handler.setFormatter(formatter)

    logger.addHandler(stream_handler)
    logger.addHandler(file_handler)
    logger.setLevel(logging.INFO)
    return logger
~~~

## class

train.csv, test.csvを読み込んだdf_train, df_testから各種データセットを作成する。
この様に操作専用クラスを作っておくと散らからない。

~~~
class DataProvider:
    def __init__(self, df_train, df_test, args):
        self.df_train = df_train
        self.df_test = df_test
        self.args = args
        self.something = ...

    def create_dataset(self, X, y=None, batch_size=None, mode='train'):
        data = (X, y) if y is not None else X
        dataset = ...

    def create_train_dataset(self, fold):
        ...

    def create_valid_dataset(self, fold):
        ...

    def create_test_dataset(self):
        ...
~~~

## main

main関数を用意する。

~~~
def main(args):
    logger = create_logger(args.save_dir)
    df_train, df_test = read_data(args)
    dataProvider = DataProvider(df_train, df_test, args)
    
    switch = {
      'train': train_cv,
      'evaluate': evaluate,
      'predict': predict
     }
     
     switch[args.mode](dataProvider, logger, args)

main(args)
~~~

switch文の代用。<br>
switch変数(辞書)の値は関数名。`switch['train']` は関数`train_cv`を指す。<br>
以下のif文より読みやすい。

~~~
if args.mode == 'train':
    train_cv(df_train, df_test, logger, args)
elif args.mode == 'evaluate':
    evaluate(df_train, df_test, logger, args)
elif args.mode == 'predict':
    predict(df_train, df_test, logger, args)
else
    logger.info('invalid mode')
~~~
