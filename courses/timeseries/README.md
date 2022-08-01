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

|Date|Hardcover|Time|
|--|--|--|
|2000-04-01|139|0|
|2000-04-02|128|1|
|2000-04-03|172|2|
|2000-04-04|139|3|
|2000-04-05|191|4|
