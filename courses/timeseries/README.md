# Time Series

```
import pandas as pd

# 日付をindexとして扱う
df = pd.read_csv(
    "../input/ts-course-data/book_sales.csv",
    index_col='Date',
    parse_dates=['Date'],
).drop('Paperback', axis=1)
```

## Time Step

timeダミー
時系列に+1した値

```
df['Time'] = np.arange(len(df.index))
```

|Date|Hardcover|Time|
|--|--|--|
|2000-04-01|139|0|
|2000-04-02|128|1|
|2000-04-03|172|2|
|2000-04-04|139|3|
|2000-04-05|191|4|

## Lag

観測値を数ステップシフトした特徴量。
ラグ1は、1つシフトした特徴量。

```
df['Lag_1'] = df['Hardcover'].shift(1)
df = df.reindex(columns=['Hardcover', 'Lag_1'])
```

|Date|Hardcover|Lag_1|
|--|--|--|
|2000-04-01|139|NaN|
|2000-04-02|128|139|
|2000-04-03|172|128|
|2000-04-04|139|172|
|2000-04-05|191|139|
