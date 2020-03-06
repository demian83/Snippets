#Z-Score Several Columns

Select the z-score of every number of every row, according to the average and standard deviation of its column:
```sql
--id    col_d     col_e     col_f
--1    -0.4343    1.0203    ...
--2     0.1434   -0.8729
--3    -0.8234   -1.2323
--4     1.889     1.5343
```
In general you can use window function, but Impala does not allow windowing (analytical function with `STDDEV()`:
```sql
select
    t.id,
    (t.col_a - avg(t.col_a) over()) / stdev(t.col_a) over() as col_d,
    (t.col_b - avg(t.col_b) over()) / stdev(t.col_b) over() as col_e
from my_table as t
```
or cross join with precalculated  `avg` and `stdev`:
```sql
select
    t.id,
    (t.col_a - tt.col_a_avg) / tt.col_a_stdev as col_d,
    (t.col_b - tt.col_b_avg) / tt.col_b_stdev as col_e
from my_table as t
    cross join (
        select 
            avg(tt.col_a) as col_a_avg,
            avg(tt.col_b) as col_b_avg,
            stdev(tt.col_a) as col_a_stdev,
            stdev(tt.col_b) as col_b_stdev
        from my_table as tt
   ) as tt
```
The third option is a `WITH` clause:
```sql
WITH stats AS ( SELECT avg ( col_a ) a_avg, stddev ( col_a ) a_stddev,
                       avg ( col_b ) b_avg, stddev ( col_b ) b_stddev
                    FROM my_table 
              )
SELECT id, ( col_a - a_avg) / a_stddev col_d, 
           ( col_b - b_avg) / b_stddev col_e
    FROM my_table, stats
```
Lastly, you can also cross join with precalculated `avg` and `stdev`:
```sql
select
    t.id,
    (t.col_a - tt.col_a_avg) / tt.col_a_stdev as col_d,
    (t.col_b - tt.col_b_avg) / tt.col_b_stdev as col_e
from my_table as t
    cross join (
        select 
            avg(tt.col_a) as col_a_avg,
            avg(tt.col_b) as col_b_avg,
            stdev(tt.col_a) as col_a_stdev,
            stdev(tt.col_b) as col_b_stdev
        from my_table as tt
   ) as tt
```

[https://stackoverflow.com/questions/19279346/calculating-the-respective-z-score-of-several-columns](Stackoverflow reference)


