# Analyze

## 欠損値の確認

データファイルの読込
```
import pandas as pd

test_df = pd.read_csv("../test.csv", na_values="?")
```

info(), describe()で全体を確認する
```
test_df.info()
test_df.describe()
```

空白値が多い項目を視覚的に表示する
```
test_df.isnull().sum().divide(len(test_df)).multiply(100)\
  .reset_index(name="null_percent").query("null_percent > 0.0")\
  .sort_values("null_percent")\
  .set_index("index").plot.barh()
```

相関係数の確認
```
numerical_df = test_df.select_types("number")
numerical_df.corr()["SalePrice"].sort_values().plot.barh(figsize=(10,15))
```

カテゴリ変数の場合、boxプロットで分布を確認する
```
height=11
width=4
fig, axes = plt.subplots(nrows=height, ncols=width)

categorical_df = test_df[test_df.columns.difference(numerical_df.columns)]
for i, column in enumerate(categorical_df.columns):
  row = i // width
  col = i % width
  sale_price_for_each_item = pd.get_dummies(categorical_df[column]).multiply(test_df["SalePrice"], axis=0)
  sale_price_for_each_item.plot.box(ax=axes[row,col], figsize=(15, 30), title=column, ledgend=False, rot=90)
```
