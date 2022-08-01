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
