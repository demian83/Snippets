
## Summarize Data


### Basic descriptive statistics for each column (or GroupBy)

```py
df.describe()
```

### # of distinct values in a column

```py
df['w'].nunique()
```

### # of rows in DataFrame

```py
len(df)
```

### Count number of rows with each unique value of variable

```py
df['w'].value_counts()
```

###

```py

```

### List unique values

```py
# List unique values in the df['name'] column
df.name.unique()
```


## Reshape Data

### Gather columns into rows

```py
pd.melt(df)
```

### Spread rows into columns

```py
df.pivot(columns='var', values='val')
```

### Append rows of DataFrames

```py
pd.concat([df1,df2])
```

### Append columns of DataFrames

```py
pd.concat([df1,df2], axis=1)
```



	
```py

```

[CheatSheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf)
