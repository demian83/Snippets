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
# >4
```
An alternative but slower way:

```python
len(df[(df['A']>0) & (df['B']>0)])
```


