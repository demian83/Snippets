### Dataframe row count based on conditions

The fastest method is: 

```python
#           A         B         C         D
# 0 -2.019868  1.227246 -0.489257  0.149053
# 1  0.223285 -0.087784 -0.053048 -0.108584
# 2 -0.140556 -0.299735 -1.765956  0.517803
# 3 -0.589489  0.400487  0.107856  0.194890
# 4  1.309088 -0.596996 -0.623519  0.020400

sum((df['A']>0) & (df['B']>0))
# > 4
```
An alternative but slower way:

```python
len(df[(df['A']>0) & (df['B']>0)])
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

