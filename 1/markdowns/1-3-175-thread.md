
# Post \#60279160 [Link](https://stackoverflow.com/questions/60279160/)

## Compare two dataframes Pyspark

**Vote**: 7 (449/702) **Views**: 68888 (127/702) 

**Internal ID** \#1-3-175

Created at 2020-02-18 10:50:32

Tags: `python` `dataframe` `apache-spark` `pyspark` `apache-spark-sql`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm trying to compare two data frames with have same number of columns i.e. 4 columns with id as key column in both data frames

```
df1 = spark.read.csv("/path/to/data1.csv")
df2 = spark.read.csv("/path/to/data2.csv")
```


Now I want to append new column to DF2 i.e. column_names which is the list of the columns with different values than df1

```
df2.withColumn("column_names",udf())
```




```
+------+---------+--------+------+
|   id | |name  | sal  | Address |
+------+---------+--------+------+
|     1|  ABC   | 5000 | US      |
|     2|  DEF   | 4000 | UK      |
|     3|  GHI   | 3000 | JPN     |
|     4|  JKL   | 4500 | CHN     |
+------+---------+--------+------+
```




```
+------+---------+--------+------+
|   id | |name  | sal  | Address |
+------+---------+--------+------+
|     1|  ABC   | 5000 | US      |
|     2|  DEF   | 4000 | CAN     |
|     3|  GHI   | 3500 | JPN     |
|     4|  JKL_M | 4800 | CHN     |
+------+---------+--------+------+
```


Now I want DF3



```
+------+---------+--------+------+--------------+
|   id | |name  | sal  | Address | column_names |
+------+---------+--------+------+--------------+
|     1|  ABC   | 5000 | US      |  []          |
|     2|  DEF   | 4000 | CAN     |  [address]   |
|     3|  GHI   | 3500 | JPN     |  [sal]       |
|     4|  JKL_M | 4800 | CHN     |  [name,sal]  |
+------+---------+--------+------+--------------+
```


I saw this SO question, [How to compare two dataframe and print columns that are different in scala](https://stackoverflow.com/questions/44338412/how-to-compare-two-dataframe-and-print-columns-that-are-different-in-scala). Tried that, however the result is different.

I'm thinking of going with a UDF function by passing row from each dataframe to udf and compare column by column and return column list. However for that both the data frames should be in sorted order so that same id rows will be sent to udf. Sorting is costly operation here. Any solution?


----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2020-02-18 15:54:37

------------

Assuming that we can use id to join these two datasets I don't think that there is a need for UDF. This could be solved just by using inner join, [array](https://spark.apache.org/docs/latest/api/sql/index.html#array) and [array_remove](https://spark.apache.org/docs/latest/api/sql/index.html#array_remove) functions among others.
First let's create the two datasets:
```
df1 = spark.createDataFrame([
  [1, "ABC", 5000, "US"],
  [2, "DEF", 4000, "UK"],
  [3, "GHI", 3000, "JPN"],
  [4, "JKL", 4500, "CHN"]
], ["id", "name", "sal", "Address"])

df2 = spark.createDataFrame([
  [1, "ABC", 5000, "US"],
  [2, "DEF", 4000, "CAN"],
  [3, "GHI", 3500, "JPN"],
  [4, "JKL_M", 4800, "CHN"]
], ["id", "name", "sal", "Address"])
```

First we do an inner join between the two datasets then we generate the condition `df1[col] != df2[col]` for each column except `id`. When the columns aren't equal we return the column name otherwise an empty string. The list of conditions will consist the items of an array from which finally we remove the empty items:
```
from pyspark.sql.functions import col, array, when, array_remove

# get conditions for all columns except id
conditions_ = [when(df1[c]!=df2[c], lit(c)).otherwise("") for c in df1.columns if c != 'id']

select_expr =[
                col("id"), 
                *[df2[c] for c in df2.columns if c != 'id'], 
                array_remove(array(*conditions_), "").alias("column_names")
]

df1.join(df2, "id").select(*select_expr).show()

# +---+-----+----+-------+------------+
# | id| name| sal|Address|column_names|
# +---+-----+----+-------+------------+
# |  1|  ABC|5000|     US|          []|
# |  3|  GHI|3500|    JPN|       [sal]|
# |  2|  DEF|4000|    CAN|   [Address]|
# |  4|JKL_M|4800|    CHN| [name, sal]|
# +---+-----+----+-------+------------+
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-02-18 13:07:33

------------

Here is your solution with `UDF`, I have changed first `dataframe` name dynamically so that it will be not ambiguous during check. Go through below code and let me know in case any concerns.

```
>>> from pyspark.sql.functions import *
>>> df.show()
+---+----+----+-------+
| id|name| sal|Address|
+---+----+----+-------+
|  1| ABC|5000|     US|
|  2| DEF|4000|     UK|
|  3| GHI|3000|    JPN|
|  4| JKL|4500|    CHN|
+---+----+----+-------+

>>> df1.show()
+---+----+----+-------+
| id|name| sal|Address|
+---+----+----+-------+
|  1| ABC|5000|     US|
|  2| DEF|4000|    CAN|
|  3| GHI|3500|    JPN|
|  4|JKLM|4800|    CHN|
+---+----+----+-------+

>>> df2 = df.select([col(c).alias("x_"+c) for c in df.columns])
>>> df3 = df1.join(df2, col("id") == col("x_id"), "left")

 //udf declaration 

>>> def CheckMatch(Column,r):
...     check=''
...     ColList=Column.split(",")
...     for cc in ColList:
...             if(r[cc] != r["x_" + cc]):
...                     check=check + "," + cc
...     return check.replace(',','',1).split(",")

>>> CheckMatchUDF = udf(CheckMatch)

//final column that required to select
>>> finalCol = df1.columns
>>> finalCol.insert(len(finalCol), "column_names")

>>> df3.withColumn("column_names", CheckMatchUDF(lit(','.join(df1.columns)),struct([df3[x] for x in df3.columns])))
       .select(finalCol)
       .show()
+---+----+----+-------+------------+
| id|name| sal|Address|column_names|
+---+----+----+-------+------------+
|  1| ABC|5000|     US|          []|
|  2| DEF|4000|    CAN|   [Address]|
|  3| GHI|3500|    JPN|       [sal]|
|  4|JKLM|4800|    CHN| [name, sal]|
+---+----+----+-------+------------+
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-02-18 12:37:23

------------

 PySpark version of my previous scala code.

```
import pyspark.sql.functions as f

df1 = spark.read.option("header", "true").csv("test1.csv")
df2 = spark.read.option("header", "true").csv("test2.csv")

columns = df1.columns
df3 = df1.alias("d1").join(df2.alias("d2"), f.col("d1.id") == f.col("d2.id"), "left")

for name in columns:
    df3 = df3.withColumn(name + "_temp", f.when(f.col("d1." + name) != f.col("d2." + name), f.lit(name)))


df3.withColumn("column_names", f.concat_ws(",", *map(lambda name: f.col(name + "_temp"), columns))).select("d1.*", "column_names").show()
```



---



 Here is my best approach for your problem.

```
val df1 = spark.read.option("header", "true").csv("test1.csv")
val df2 = spark.read.option("header", "true").csv("test2.csv")

val columns = df1.columns
val df3 = df1.alias("d1").join(df2.alias("d2"), col("d1.id") === col("d2.id"), "left")

columns.foldLeft(df3) {(df, name) => df.withColumn(name + "_temp", when(col("d1." + name) =!= col("d2." + name), lit(name)))}
  .withColumn("column_names", concat_ws(",", columns.map(name => col(name + "_temp")): _*))
  .show(false)
```


First, I join two dataframe into `df3` and used the columns from `df1`. By folding left to the `df3` with temp columns that have the value for column name when `df1` and `df2` has the same `id` and other column values.

After that, `concat_ws` for those column names and the null's are gone away and only the column names are left.

```
+---+----+----+-------+------------+
|id |name|sal |Address|column_names|
+---+----+----+-------+------------+
|1  |ABC |5000|US     |            |
|2  |DEF |4000|UK     |Address     |
|3  |GHI |3000|JPN    |sal         |
|4  |JKL |4500|CHN    |name,sal    |
+---+----+----+-------+------------+
```


The only thing different from your expected result is that the output is not a list but string.

p.s. I forgot to use PySpark but this is the normal spark, sorry.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-08-28 18:33:13

------------

You can get that query build for you in PySpark and Scala by the [spark-extension](https://github.com/g-research/spark-extension) package.
It provides the `diff` transformation that does exactly that.
```
from gresearch.spark.diff import *

options = DiffOptions().with_change_column('changes')
df1.diff_with_options(df2, options, 'id').show()
+----+-----------+---+---------+----------+--------+---------+------------+-------------+
|diff|    changes| id|left_name|right_name|left_sal|right_sal|left_Address|right_Address|
+----+-----------+---+---------+----------+--------+---------+------------+-------------+
|   N|         []|  1|      ABC|       ABC|    5000|     5000|          US|           US|
|   C|  [Address]|  2|      DEF|       DEF|    4000|     4000|          UK|          CAN|
|   C|      [sal]|  3|      GHI|       GHI|    3000|     3500|         JPN|          JPN|
|   C|[name, sal]|  4|      JKL|     JKL_M|    4500|     4800|         CHN|          CHN|
+----+-----------+---+---------+----------+--------+---------+------------+-------------+
```

While this is a simple example, diffing DataFrames can become complicated when wide schemas, insertions, deletions and null values are involved. That package is well-tested, so you don't have to worry about getting that query right yourself.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-28 15:30:56

------------

There is a wonderful package for pyspark that compares two dataframes. The name of the package is 
[https://capitalone.github.io/datacompy/](https://capitalone.github.io/datacompy/)
example code:
```
import datacompy as dc

comparison = dc.SparkCompare(spark, base_df=df1, compare_df=df2, join_columns=common_keys, match_rates=True)
comparison.report()
```

The above code will generate a summary report, and the one below it will give you the mismatches.
```
comparison.rows_both_mismatch.display()
```

There are also more fearures that you can explore.


------------
    
    