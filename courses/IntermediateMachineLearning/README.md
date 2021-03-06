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

# axis=1で列を指定
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

### ordinal encoder

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

### one hot encoder

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

### pandas

```
# pandasでカテゴリ変数をダミー変数に変換する
# データ型dtypeが'object'か'category'である列がダミー変数に変換される。数値やbool型の列は変換されない
X_train = pd.get_dummies(X_train)
X_valid = pd.get_dummies(X_valid)
X_test = pd.get_dummies(X_test)
X_train, X_valid = X_train.align(X_valid, join='left', axis=1, fill_value=0)
X_train, X_test = X_train.align(X_test, join='left', axis=1, fill_value=0)
```

上記ではX_train, X_valid, X_testが同じダミー変数に変換されることを前提としている。
もし、X_valid, X_testにしか存在しないカテゴリがあると、X_train.align(.., join='left', ..)の際に切り捨てられる。

```
train = pd.DataFrame([1, 2, 3], columns=['A'])
test = pd.DataFrame([7,8], columns=['A'])
train = pd.get_dummies(train, columns=['A'])
test = pd.get_dummies(test, columns=['A'])

train, test = train.align(test, join='left', axis=1, fill_value=0)
print(train)
print(test)

# testにはtrainにないカテゴリ変数(7, 8)が存在するが、alignによって失われる

   A_1  A_2  A_3
0    1    0    0
1    0    1    0
2    0    0    1
   A_1  A_2  A_3
0    0    0    0
1    0    0    0
```

# Pipelineを使う

Pipelineは複数の処理をまとめて記述できコードが簡潔になる

```
from sklean.compose import ColumnTransformer
from sklean.pipeline import Pipeline
from sklean.impute import SimpleImputer
from sklean.preprocessing import OneHotEncoder

# 数値型の前処理は欠損値の補完
num_transformer = SimpleImputer(strategy='median')

# カテゴリ型に対する前処理は、欠損値の補完とOneHotエンコーディング
cat_transformer = Pipeline(steps=[
  ('imputer', SimpleImputer(strategy='most_frequent')),
  ('onehot', OneHotEncoder(handle_unknown='ignore'))
])

# 数値型、カテゴリ型のカラムに対する前処理を定義する
preprocessor = ColumnTransformer(
  transformers=[
    ('num', num_transformer, num_cols),
    ('cat', cat_transformer, cat_cols)
  ]
)
```

```
from sklearn.ensumble import RandomForestRegressor

model = RandomForestRegressor(n_estimators=100, random_state=0)

# 前処理のあと、モデルを実行する
myPipeline = Pipeline(steps=[
  ('preprocessor', preprocessor),
  ('model', model)
])

myPipeline.fit(X_train, Y_train)
Y_pred = myPipeline.predict(X_valid)

score = mean_absolute_error(Y_valid, Y_pred)
```
