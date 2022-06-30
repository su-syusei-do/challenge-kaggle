# Validation

## sklearn.model_selection

https://scikit-learn.org/stable/modules/classes.html#module-sklearn.model_selection

## Train Test Split

```
from sklearn.model_selection import train_test_split

X_train, X_valid, Y_train, Y_valid = train_test_split(X, Y, train_size=0.8, test_size=0.2, random_state=0)
```

## Cross-Validation

```
from sklearn.model_selection import cross_val_score

scores = -1 * cross_val_score(my_pipeline, X, y, cv=5, scoring='neg_mean_absolute_error')
print("Average MAE score:", scores.mean())
```
