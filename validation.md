# Validation

## Train Test Split

```
from sklearn.model_selection import train_test_split
```

## Cross-Validation

```
from sklearn.model_selection import cross_val_score

scores = -1 * cross_val_score(my_pipeline, X, y, cv=5, scoring='neg_mean_absolute_error')
```
