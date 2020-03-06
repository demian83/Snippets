## Z-Score Several Columns

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

***

## Select info from table where row has a max date

Select max date:

```sql
SELECT tipo_operacion, MAX(data_time_stamp) AS  max_date
FROM table
WHERE checks>0
GROUP BY tipo_operacion
```

Join it back to your data to get the other columns:

```sql
SELECT tipo_operacion, max_date,checks
FROM table t
INNER JOIN
(SELECT tipo_operacion, MAX(data_time_stamp) AS max_date
FROM table
WHERE checks>0
GROUP BY tipo_operacion) a
ON a.tipo_operacion = t.tipo_operacion AND a.max_date = data_time_stamp
```
Or restrict with a `WHERE`:

```sql
Select tipo_operacion, data_time_stamp, checks
FROM table t
WHERE data_time_stamp IN
(SELECT MAX(data_time_stamp) 
FROM table
WHERE checks>0
GROUP BY tipo_operacion) a
```

Or is available use a window `MAX()` (Impala does not allow window on MAX):

To get max dates per `group` alongside other data:

```sql
SELECT
  *, 
  max_date = MAX(date) OVER (PARTITION BY group)
FROM table

-- group  date      cash  checks  max_date
-- -----  --------  ----  ------  --------
-- 1      1/1/2013  0     0       1/3/2013
-- 2      1/1/2013  0     800     1/1/2013
-- 1      1/3/2013  0     700     1/3/2013
-- 3      1/1/2013  0     600     1/5/2013
-- 1      1/2/2013  0     400     1/3/2013
-- 3      1/5/2013  0     200     1/5/2013
```
Using the above output as a derived table, you can then get only rows where `date` matches `max_date`:

```sql
SELECT
  group,
  date,
  checks
FROM (
  SELECT
    *, 
    max_date = MAX(date) OVER (PARTITION BY group)
  FROM table
) AS s
WHERE date = max_date;
```

Another alternative that could work with a window `MAX()`:

```sql
SELECT distinct
  group, 
  max_date = MAX(date) OVER (PARTITION BY group), checks
FROM table
```
