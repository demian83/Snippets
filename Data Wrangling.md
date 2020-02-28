
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
### Maximum by category
If `df` has an index with no duplicate values, then you can use `idxmax` to return the index of the maximum row for each group. Then use `df.loc` to select the entire row:

```py
#	name     type      votes     
#	bob       dog        10
#	pete      cat         8
#	fluffy    dog         5
#	max       cat         9

df.loc[df.groupby('type').votes.agg('idxmax')]
 
#	  name type  votes
#	3  max  cat      9
#	0  bob  dog     10

```
If df.index has duplicate values, i.e. is not a unique index, then make the index unique first:

```py
df = df.reset_index()
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
