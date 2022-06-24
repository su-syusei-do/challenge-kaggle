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

## カテゴリ変数の処理

```
from sklearn.preprocessing import OrdinalEncoder

s = (X_train.dtypes == 'object')
# sは、全カラムの比較結果(True/False)を含むSeries
# print(s[s])はTrueのカラムのみ出力
# print(s[s].index)はTrueのカラム名を出力
# Seriesからリストに変換 list(s[s].index)
object_cols = list(s[s].index)

ordinal_encoder = OrdinalEncoder()
ordinal_encoder.fit(X_train[object_cols])
label_X_train[object_cols] = ordinal_encoder.transform(X_train[object_cols])
```

```
from sklearn.preprocessing import OneHotEncoder

# カテゴリ変数カラム
object_cols = [col for col in X_train.columns if X_train[col].dtype == 'object']

# 多重度が10未満のカテゴリ変数カラムを特定
cat_cols = [col for col in X_train.columns if X_train[col].dtype == 'object' and X_train[col].nunique() < 10]

onehot_encoder = OneHotEncoder(handle_unknown='ignore', sparse=False)
onehot_encoder.fit(cat_X_train)

# 多重度10未満のカラムのみOneHotEncodingする。OneHotEncodingするとカラム名は新しく作られる。
# ndarrayからDataFrameを作成
OH_cols_train = pd.DataFrame(onehot_encoder.transform(X_train[cat_cols]))

# indexをコピー
OH_cols_train.index = X_train.index

# カテゴリ変数カラムを除く
num_X_train = X_train.drop(object_cols, axis=1)

# 数値カラムと、OneHotEncodingで生成したカラムを結合する
OH_X_train = pd.concat([num_X_train, OH_cols_train], axis=1)
```

多重度が大きい変数をOneHotEncodingすると、データセットのサイズが膨大になってしまう。<br>
この様なカラムはdrop()するか、OrdinalEncoder()を適用する。
