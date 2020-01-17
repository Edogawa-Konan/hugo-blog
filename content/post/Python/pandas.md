---
title: "Pandas"
date: 2020-01-17T14:59:41+08:00
lastmod: 2020-01-17T14:59:41+08:00
draft: false
tags: ["Python"]
categories: ["数据处理"]
---

# pandas

## dataframe isnull vs isna

These two DataFrame methods do exactly the same thing!

In short:

- To detect NaN values numpy uses `np.isnan()`.
- To detect NaN values pandas uses either `.isna() or .isnull()`.
  The NaN values are inherited from the fact that pandas is built on top of numpy, while the two functions' names originate from R's DataFrames, whose structure and functionality pandas tried to mimic.

[QA](https://datascience.stackexchange.com/questions/37878/difference-between-isna-and-isnull-in-pandas)

## 基本操作

```python
'''The frac keyword argument specifies the fraction of rows to return in the random sample, so frac=1 means return all rows (in random order).'''
df.sample(frac=1)
# Note: If you wish to shuffle your dataframe in-place and reset the index, you could do e.g.
df = df.sample(frac=1).reset_index(drop=True)
'-------------------------------------------------------------------'
'''dataframe choose rows'''
#To select rows whose column value equals a scalar, some_value, use ==:
df.loc[df['column_name'] == some_value]
#To select rows whose column value is in an iterable, some_values, use isin:

df.loc[df['column_name'].isin(some_values)]
#Combine multiple conditions with &:

df.loc[(df['column_name'] == some_value) & df['other_column'].isin(some_values)]
#To select rows whose column value does not equal some_value, use !=:

df.loc[df['column_name'] != some_value]
#isin returns a boolean Series, so to select rows whose value is not in some_values, negate the boolean Series using ~:
df.loc[~df['column_name'].isin(some_values)]
'-------------------------------------------------------------------'
```

## 统计na值

```python
In [1]: s = pd.Series([1,2,3, np.nan, np.nan])

In [4]: s.isna().sum()   # or s.isnull().sum() for older pandas versions
Out[4]: 2

In [5]: df = pd.DataFrame({'a':[1,2,np.nan], 'b':[np.nan,1,np.nan]})

In [6]: df.isna().sum()
Out[6]:
a    1
b    2
dtype: int64
```

## [check if a column exists in Pandas](https://stackoverflow.com/questions/24870306/how-to-check-if-a-column-exists-in-pandas)

This will work:

```python
if 'A' in df:
```

But for clarity, I'd probably write it as:

```python
if 'A' in df.columns:
```

如果一次检验多个的话，可以用如下方法：

```python
if set(['A','C']).issubset(df.columns):
   df['sum'] = df['A'] + df['C']    
```

## [determine whether a column is numeric or not ](https://stackoverflow.com/questions/19900202/how-to-determine-whether-a-column-variable-is-numeric-or-not-in-pandas-numpy)

```python
import pandas as pd
from pandas.api.types import is_string_dtype
from pandas.api.types import is_numeric_dtype

df = pd.DataFrame({'A': ['a', 'b', 'c'], 'B': [1.0, 2.0, 3.0]})

is_string_dtype(df['A'])
>>>> True

is_numeric_dtype(df['B'])
>>>> True
```

## 水平方向合并两个dateframe，去除重复列

```python
DF1 = pd.DataFrame(columns=['col1', 'col2', 'col3'])
DF2 = pd.DataFrame(columns=['col2', 'col4', 'col5'])


DF2 = DF2[DF2.columns.difference(DF1.columns)]
print (DF2)
Empty DataFrame
Columns: [col4, col5]
Index: []

print (pd.concat([DF1, DF2], axis = 1))
Empty DataFrame
Columns: [col1, col2, col3, col4, col5]
Index: []
```

[stackoverflow](https://stackoverflow.com/questions/44545921/in-pandas-how-to-concatenate-horizontally-and-then-remove-the-redundant-columns)

## copy vs view

Here's the rules, subsequent override:

- All operations generate a copy
- If `inplace=True` is provided, it will modify in-place; only some operations support this
- An indexer that sets, e.g. `.loc/.iloc/.iat/.at` will set inplace.
- An indexer that gets on a single-dtyped object is almost always a view (depending on the memory layout it may not be that's why this is not reliable). This is mainly for efficiency. (the example from above is for `.query`; this will **always** return a copy as its evaluated by `numexpr`)
- An indexer that gets on a multiple-dtyped object is always a copy.

[stackoverflow](https://stackoverflow.com/questions/23296282/what-rules-does-pandas-use-to-generate-a-view-vs-a-copy)

## group

`dataframe`对象调用`groupby`方法后，得到的是`GroupBy`对象，此时可以调用聚集函数，如果只相对某一列执行聚集函数，可以使用`[]`。

```python
import pandas as pd

df = pd.DataFrame({'Animal': ['Falcon', 'Falcon',
                              'Parrot', 'Parrot'],
                   'Max Speed': [380., 370., 24., 26.]})

df.groupby(['Animal'])['Max Speed'].mean()
#Animal
#Falcon    375.0
#Parrot     25.0
#Name: Max Speed, dtype: float64
df.groupby(['Animal'], as_index=False)['Max Speed'].mean()
#Animal  Max Speed
#0  Falcon      375.0
#1  Parrot       25.0
```

