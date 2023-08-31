```
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.compose import make_column_transformer
from sklearn.model_selection import GroupShuffleSplit

# データ読み込み
spotify = pd.read_csv('../input/dl-course-data/spotify.csv')

# 欠損データを削除、ターゲット変数は track_popularity
X = spotify.copy().dropna()
y = X.pop('track_popularity')

def group_split(X, y, group, train_size=0.75):
    splitter = GroupShuffleSplit(train_size=train_size)
    train, test = next(splitter.split(X, y, groups=group))
    return (X.iloc[train], X.iloc[test], y.iloc[train], y.iloc[test])

artists = X['track_artist']
X_train, X_valid, y_train, y_valid = group_split(X, y, artists)

# 量的変数
features_num = ['danceability', 'energy', 'key', 'loudness', 'mode',
                'speechiness', 'acousticness', 'instrumentalness',
                'liveness', 'valence', 'tempo', 'duration_ms']
# カテゴリ変数
features_cat = ['playlist_genre']

# 標準化、One-Hotエンコーディング
preprocessor = make_column_transformer(
    (StandardScaler(), features_num),
    (OneHotEncoder(), features_cat),
)

X_train = preprocessor.fit_transform(X_train)
X_valid = preprocessor.transform(X_valid)
y_train = y_train / 100 # popularity is on a scale 0-100, so this rescales to 0-1.
y_valid = y_valid / 100
```
