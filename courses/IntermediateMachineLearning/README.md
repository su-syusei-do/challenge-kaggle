# Intermediate Machine Learning

## 欠損値を見つける

```
missing_val_count_by_col = (X_train.isnull().sum())
# (カラム名, 欠損値の数)
print(missing_val_count_by_col)

# 欠損値 > 0 であるカラム名と欠損値の数を表示
print(missing_val_count_by_col[missing_val_count_by_col > 0])
```

## 欠損値を含むカラムの削除

```
cols = ['col1', 'col2', 'col3']

# axis=1で列方向を指定
X_train.drop(cols, axis=1)
```
