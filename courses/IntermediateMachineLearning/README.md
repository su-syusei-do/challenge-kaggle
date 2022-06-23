# Intermediate Machine Learning

## 欠損値を見つける

```
missing_val_count_by_col = (X_train.isnull().sum())
# (カラム名, 欠損値の数)
print(missing_val_count_by_col)

# 欠損値 > 0 であるカラム名と欠損値の数を表示
print(missing_val_count_by_col[missing_val_count_by_col > 0])
```

## 欠損値を見つける２

```
cols_with_missing = [col for col in X_train.columns if X_train[col].isnull().any()]
```

## データの種類が少ないカラム(カテゴリ変数)を見つける

```
# nunique()が１０未満で、型がobject
low_cardinality_cols = [col for col in X_train.columns if X_train[col].nunique() < 10 and X_train[col].dtype == 'object']
```

## 数値型のカラムを見つける

```
numerical_cols = [col for col in X_train.columns if X_train[col].dtype in ['int64', 'float64']]
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
