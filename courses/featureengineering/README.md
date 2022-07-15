# Feature Engineering

## DataFrame.factorize

```
import pandas as pd

df = pd.read_csv("./test.csv")

X = df.copy()
Y = X.pop("Price")

# カテゴリ変数をラベルエンコーディングする
for colname in X.select_dtypes("object"):
  X[colname], _ = X[colname].factorize()

discrete_features = X.dtypes == int
```

## mutual information

相互情報量。2つの確率変数の相互依存の尺度。

```
from sklearn.feature_selection import mutual_info_regression

def make_mi_scores(X, y, discrete_features):
    mi_scores = mutual_info_regression(X, y, discrete_features=discrete_features)
    mi_scores = pd.Series(mi_scores, name="MI Scores", index=X.columns)
    mi_scores = mi_scores.sort_values(ascending=False)
    return mi_scores

mi_scores = make_mi_scores(X, y, discrete_features)
mi_scores[::3] # 3つおきに確認する
```

## 描画

```
def plot_mi_scores(scores):
    scores = scores.sort_values(ascending=True)
    width = np.arange(len(scores))
    ticks = list(scores.index)
    plt.barh(width, scores)
    plt.yticks(width, ticks)
    plt.title("Mutual Information Scores")


plt.figure(dpi=100, figsize=(8, 5))
plot_mi_scores(mi_scores)
```
