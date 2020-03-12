### Dataframe row count based on conditions

The fastest method is: 

```python
#           A         B         C         D
# 0 -2.019868  1.227246 -0.489257  0.149053
# 1  0.223285 -0.087784 -0.053048 -0.108584
# 2 -0.140556 -0.299735 -1.765956  0.517803
# 3 -0.589489  0.400487  0.107856  0.194890
# 4  1.309088 -0.596996 -0.623519  0.020400

sum((df['A'] > 0) & (df['B'] > 0))
# > 4
```
An alternative but slower way:

```python
len(df[(df['A'] > 0) & (df['B'] > 0)])
```


### Groupby and Sum

```python
df.groupby(['Fruit','Name'])['Number'].sum().reset_index()

# Fruit   Date      Name  Number
# Apples  10/6/2016 Bob    7
# Apples  10/6/2016 Bob    8
# Apples  10/6/2016 Mike   9
# Apples  10/7/2016 Steve 10
# Apples  10/7/2016 Bob    1
# Oranges 10/7/2016 Bob    2
# Oranges 10/6/2016 Tom   15
# Oranges 10/6/2016 Mike  57
# Oranges 10/6/2016 Bob   65
# Oranges 10/7/2016 Tony   1
# Grapes  10/7/2016 Bob    1
# Grapes  10/7/2016 Tom   87
# Grapes  10/7/2016 Bob   22
# Grapes  10/7/2016 Bob   12
# Grapes  10/7/2016 Tony  15

# > Without reset index:

#                Number
# Fruit   Name         
# Apples  Bob        16
#         Mike        9
#         Steve      10
# Grapes  Bob        35
#         Tom        87
#         Tony       15
# Oranges Bob        67
#         Mike       57
#         Tom        15
#         Tony        1

# > With reset index:

# Fruit   Name       Number
# Apples  Bob        16
# Apples  Mike        9
# Apples  Steve      10
# Grapes  Bob        35
# Grapes  Tom        87
# Grapes  Tony       15
# Oranges Bob        67
# Oranges Mike       57
# Oranges Tom        15
# Oranges Tony        1

```
But the above comes out as a Pandas series instead of Dataframe. W can use double square brackets around 'Number' to get a Dataframe
`df.groupby(['Fruit','Name'])[['Number']].sum()`

A variation is to use the `.agg()` function; provides the ability to (1) persist type DataFrame, (2) apply averages, counts, summations, etc. and (3) enables groupby on multiple columns while maintaining legibility:

```python
df.groupby(['att1', 'att2']).agg({'att1': "count", 'att3': "sum",'att4': 'mean'})

# > With our specific example:

df.groupby(['Name', 'Fruit']).agg({'Number': "sum"})
```
##NEW

### Pandas Groupby and Sum Only One Column

```python
#	       A      B      C
#	1     foo    12    California
#	2     foo    22    California
#	3     bar    8     Rhode Island
#	4     bar    32    Rhode Island
#	5     baz    15    Ohio
#	6     baz    26    Ohio

# Desired result:

#	      A       B      C
#	1    foo     34    California
#	2    bar     40    Rhode Island
#	3    baz     41    Ohio
```

One way is to include C in the groupby (the groupby function can accept a list).

```python
df.groupby(['A','C'])['B'].sum()
```

One other thing to note, if you need to work with df after the aggregation you can also use the as_index=False option to return a dataframe object. 

```python
df.groupby(['A','C'], as_index=False)['B'].sum()
```

### Pandas: How to sum columns based on conditional of other column values?

```python
df = pd.DataFrame(np.random.randn(5,3), columns=list('ABC'))

#	          A         B         C
#	0  0.197334  0.707852 -0.443475
#	1 -1.063765 -0.914877  1.585882
#	2  0.899477  1.064308  1.426789
#	3 -0.556486 -0.150080 -0.149494
#	4 -0.035858  0.777523 -0.453747


df['total'] = df.loc[df['A'] > 0,['A','B']].sum(axis=1)
df['total'].fillna(0, inplace=True)
df


#	          A         B         C     total
#	0  0.197334  0.707852 -0.443475  0.905186
#	1 -1.063765 -0.914877  1.585882  0.000000
#	2  0.899477  1.064308  1.426789  1.963785
#	3 -0.556486 -0.150080 -0.149494  0.000000
```


Another approach is to call `where` on the `sum` result, this takes a value param to return when the condition isn't met:

```python
df[['A','B']].sum(axis=1).where(df['A'] > 0, 0)
```
