---
layout: post
title:  "Pandas v.s PySpark"
date:   2020-11-17 10:57:30 +0800
categories: Data_Science
tags : Python
mathjax: true
---
* content 
{:toc}
Pandas 和 PySpark的比較





## Basis

|                          | Pandas                   | PySpark                                                                        |
| ------------------------ | ------------------------ | ------------------------------------------------------------------------------ |
| 工作方式                 | Single Machine tool      | Distributed Parallel Computing                                                 |
| Parallelism              | 有                       | 無                                                                             |
| Hadoop                   | 無                       | 有                                                                             |
| 處理大數據能力           | 有瓶頸                   | 可以                                                                           |
| 延遲機制                 | 有                       | 無                                                                             |
| 內存緩存                 | 單機                     | 利用persist()或cache()將轉換的RDDs保存在內存                                   |

## DataFrame

|                          | Pandas                   | PySpark                                                                        |
| ------------------------ | ------------------------ | ------------------------------------------------------------------------------ |
| DataFrame可變性          | 可變                     | 因spark不可變，所以不可變                                                      |
| index索引                | 自動創建                 | 無，需另外創建                                                                 |
| row架構                   | Series, Pandas DataFrame | Row, Spark DataFrame                                                           |
| col架構                   | Series, Pandas DataFrame | Column, Spark DataFrame                                                        |
| col名稱                   | 不允許重名               | 允許重名, 修改列名採用alias方法                                                |
| col添加                   | df['xx']=0               | from pyspark.sql import functions df.withColumn(“xx”, functions.lit(0)).show() |
| col修改(原来有df['xx']列) | df['xx']=1               | df.withColumn(“xx”, 1).show()|
| 顯示 |df輸出具體內容|df不輸出具體內容, 輸出具體內容用show方法|

### 選擇或切片 

||Pandas|PySpark|
|---|---|---|
|First|df[0], df.ix[0]|df.first()|
|Head|df.head(2)|df.head(2), df.take(2)|
|Tail|df.tail(2)||
|Slice|df.ix[:3], df.ix[:'xx'], df[:'xx']|df.select()|
|Selected by label|df.loc[ ]||
|Selected by location|df.iloc[ ]||

## Extract Transform Load Instructions

|  |Pandas| PySpark |
| -------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------ |
| 按col排序 | df.sort()| df.sort()|
| 按軸進行排序 | df.sort_index()| 無|
| 過濾| df[df['colname_1']>threshold]| df.filter(df['colname_1']>threshold) df.where(df['colname_1']>threshold) |
| 分組聚合| df.groupby('colname_1')| df.groupBy('colname_1')|
| df.count()| 輸出每一列非空行數 | 輸出總行數|
| df.describe()  | 描述某些列的count, mean, std, min, 25%, 50%, 75%, max 描述某些列的count, mean, stddev, min, , max|
| 合併|註一|df.join()|
| 遺失值處理|自動添加NaNs|不自動添加NaNs, 且不拋出錯誤|
| SQL語句|import sqlite3 pd.read_sql("SELECT name, age FROM people WHERE age >= 13 AND age <= 19")| 註二 |
| 兩者相互轉換   |pandas_df = spark_df.toPandas()|spark_df = spark.createDataFrame(pandas_df)|
| 函數應用 | df.apply(f)|df.foreach(f) or df.foreachPartition(f)|
| map-reduce操作 |map(func, list), reduce(func, list)|df.map(func), df.reduce(func)|
|map-reduce返回類型|seq|seqRDDs|
| diff操作| 有(處理時間序列數據)| 無|

* 註一
    * pd.concat() : 軸向合併 
    * df.join() : 多列合併 
    * pd.merge() : 多列合併  
    * df.append() : 多行合併

* 註二
    1. 表格註冊：把DataFrame結構註冊成SQL語句使用類型
        * df.registerTempTable('people') 
        * sqlContext.registerDataFrameAsTable(df, 'people')
        * spark.sql('SELECT name, age FROM people WHERE age >= 13 AND age <= 19')
    2. 功能註冊：把函数註冊成SQL語句使用類 
        * spark.registerFunction('stringLengthString', lambda x: len(x))
        * spark.sql("SELECT stringLengthString('test')")

## Reference

[pyspark系列--pandas与pyspark对比](https://zhuanlan.zhihu.com/p/34901585)

[Pandas - 一文看懂透视表pivot_table](https://zhuanlan.zhihu.com/p/31952948)