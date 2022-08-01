# Model

## GBDT

```
import xgboost as xgb
from sklearn.metrics import log_loss

dtrain = xgb.DMatrix(x_train, label=y_train)
dvalid = xgb.DMatrix(x_valid, label=y_valid)
dtest  = xgb.DMatrix(x_test)

# binary:logistic = 二値分類
# reg:squarederror = 回帰
params = {'objective': 'binary:logistic', 'silent': 1, 'random_state': 71}
num_round = 50

# 学習
watchlist = [(dtrain, 'train'), (dvalid, 'eval')]
model = xgb.train(params, dtrain, num_round, evals=watchlist)

# スコアの確認
y_pred = model.predict(dvalid)
score  = log_loss(y_valid, y_pred)
print(f'logloss: {score:.4f}')

# 予測値
y_test = model.predict(dtest)
```
