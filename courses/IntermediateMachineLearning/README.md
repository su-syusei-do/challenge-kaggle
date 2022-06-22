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

## 欠損値の補完

```
from sklearn.impute import SimpleImputer

# strategy='mean' 平均値で補完する
imp_mean = SimpleImputer(missing_values=np.nan, strategy='mean')
imp_mean.fit(X_train)
imputed_X_train = pd.DataFrame(imp_mean.transform(X_train))
imputed_X_valid = pd.DataFrame(imp_mean.transform(X_valid))

imputed_X_train.columns = X_train.columns
imputed_X_valid.columns = X_valid.columns
```
