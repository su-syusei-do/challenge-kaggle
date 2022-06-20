# challenge-kaggle

|変数|説明|
|---|---|
| data | 元データ |
| X | 元データ(特徴量) |
| Y | 元データ(目的変数) |
| X_train, Y_train | 訓練データ, Xをtrain_test_split()で分割 |
| X_valid, Y_valid | 検証データ, Yをtrain_test_split()で分割 |
| Y_pred | 予測値 |
| X_test, Y_test | 提出用データ |
```
import pandas as pd

# train.csvデータ全量
data = pd.read_csv('train.csv')

# 目的変数(target)と、それ以外に分ける
X = data.drop(['target'], axis=1)
Y = data.target

# 検証用に分割する場合
from sklearn.model_selection import train_test_split
X_train, X_valid, Y_train, Y_valid = train_test_split(X, Y, train_size=0.8, test_size=0.2, random_state=0)

# 予測モデル
model = ...
model.fit(X_train, Y_train)

# 予測値を得る
Y_pred = model.predict(X_valid)

# 検証データで予測値の精度を確認する
from sklearn.metrics import mean_absolute_error
mae = mean_absolute_error(Y_valid, Y_pred)

# test.csvには目的変数が含まれない
# このデータに対して予測を立てるという想定
X_test = pd.read_csv('test.csv')
Y_test = model.predict(X_test)

# 提出用ファイルの作成
submission = pd.DataFrame({'Id': X_test['Id'], 'target': Y_test})
submission.to_csv('submission.csv', index=False)
```
