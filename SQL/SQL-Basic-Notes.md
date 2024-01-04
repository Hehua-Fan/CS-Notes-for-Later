# SQL Basic Notes

## Table of Contents
### Basis SQL
* [Order of SQL execution](#orderofsqlexecution)
* [WHERE & HAVING](#wherehaving)
* [LIKE模糊匹配](#like)
* [GROUP BY](#groupby)
* [最大值问题的三种思路](#max)
* [最小值问题的三种思路](#min)
* [DISTINCT( )](#distinct)

### Advanced SQL
* [window_fuction](#window_fuction)
* [SUM IF & COUNT IF](#sumif-countif)
* [DATEDIFF( )](#datediff)
* [IFNULL( )](#ifnull)
* [ROUND( )](#round)

## Order of SQL execution<a name="orderofsqlexecution"></a>
- `FROM(JOIN)`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Choose and join tables to get base data
- `WHERE`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Filters the base data (by rows)
- `GROUP BY`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aggregates the base data
- `HAVING`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Filters the aggregated data
- `SELECT`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Returns the final data
- `ORDER BY`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sorts the final data
- `LIMIT`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Limits the returned data to a row count

## WHERE & HAVING<a name="wherehaving"></a>
- `WHERE`是筛选分组前的数据，`HAVING`是筛选分组后的数据
- `WHERE`和`HAVING`后必须是原始列名，不能是列的别名（`AS`后的别名）
- `WHERE`和`HAVING`后跟的是condition（condition是`=`,`!=`,`>`,`>=`,`<`,`<=`,`LIKE`,`NOT LIKE`,`IS NULL`,`IS NOT NULL`与`OR`,`AND`的组合）
- `WHERE`的condition不能包含`Aggregate_function()`
- `HAVING`的condition只能包含`Aggregate_function()` 或 `GROUP BY`后的字段

## LIKE模糊匹配<a name="like"></a>
- `LIKE 'c%'`: 以c开头的
- `LIKE '%c'`: 以c结尾的
- `LIKE '%c%'`: 包含c的（不限位置）
- `LIKE '%%'`: 包含''的

## GROUP BY<a name="groupby"></a>
- `GROUP BY`按column_name 或 (column_name_1, ... , column_name_N)对数据进行分组，假想压缩成一行数据
- `GROUP BY`搭配`SELECT` `Aggregate_fuction(column_name)`，根据`Aggregate_fuction()`输出一条记录
- `GROUP BY`不搭配`SELECT` `Aggregate_fuction(column_name)`，默认取分组结果的第一条记录
- `GROUP BY`筛选的不是完整的一行记录，`WHERE`筛选的是完整的一行记录

## 最大值问题的三种思路<a name="max"></a>
**Solution1: Subquery（`WHERE` is not required）**
```sql
SELECT ...
FROM ...
(WHERE) ...
GROUP BY 关键字段
HAVING Aggregate_function(字段) >= ALL(SELECT Aggregate_function(字段)
                                      FROM ...
                                      (WHERE) ...
                                      GROUP BY 关键字段)
)
```
**Solution2: Windows_function overall rank(do not use `PARTITION BY`)**
```sql
SELECT ...
FROM (SELECT *,
          <窗口排名函数> OVER(ORDER BY 字段 DESC) AS ranking
      FROM ...

)cte
WHERE ranking = 1
```
**Solution3: `ORDER BY` (only for the simple question)**
```sql
ORDER BY 字段 DESC，LIMIT 1
```

## 最小值问题的三种思路<a name="min"></a>
**Solution1: Subquery（`WHERE` is not required）**
```sql
SELECT ...
FROM ...
(WHERE) ...
GROUP BY 关键字段
HAVING Aggregate_function(字段) <= ALL(SELECT Aggregate_function(字段)
                                      FROM ...
                                      (WHERE) ...
                                      GROUP BY 关键字段)
)
```
**Solution2: Windows function overall rank(do not use `PARTITION BY`)**
```sql
SELECT ...
FROM (SELECT *,
          <窗口排名函数> OVER(ORDER BY 字段 ASC) AS ranking
      FROM ...
)cte
WHERE ranking = 1
```
**Solution3: ORDER BY (only for the simple question)**
```sql
ORDER BY 字段 ASC，LIMIT 1
```

## DISTINCT<a name="distinct"></a>
```
使用范围：SELECT clause

用法一：SELECT DISTINCT column_name -- 返回一列不同名称
用法二：SELECT COUNT(DISTINCT column_name) -- 统计该列不同的名称个数
用法三：SELECT COUNT(DISTINCT column_name1,column_name2) -- 统计该两列不同的组合名称个数
```

## Window_function<a name="window_fuction"></a>

**1.窗口函数的基本语法框架**
```sql
<window_function> OVER(PARTITION BY expr_list ORDER BY order_list frame_clause)
````
注1：frame_clause不适用于窗口排名函数，适用于窗口聚合函数。

注2：`PARTITION BY`在窗口排名函数和窗口聚合函数中都可省略。

**2.窗口排名函数中`PARTITION BY`的细节**
- 窗口排名函数，不可省略`ORDER BY`。
- 窗口排名函数不使用`PARTITION BY`进行不分组的总体排名，并打印排名。
- 窗口排名函数使用`PARTITION BY`进行分组的组内排名，并打印排名。
- 外部`ORDER BY`和窗口排名函数`DENSE_RANK()`中不使用`PARTITION BY`的`ORDER BY`输出的总体数据排序相同，后者多了一列排名。
- 使用窗口函数后如需继续使用`WHERE`或`HAVING`筛选行，必须外层嵌套，因为窗口函数的操作顺序在`WHERE`和`GROUP BY`之后。

**3.窗口聚合函数中`ORDER BY`的细节**
- 无`ORDER BY`，无frame_clause：窗口聚合范围 默认组内所有行
- 有`ORDER BY`，无frame_clause：窗口聚合范围 默认从第一行到当前行
- 有`ORDER BY`，有frame_clause：窗口聚合范围 frame_clause

i.窗口聚合范围：从第一行到当前行
```
“累计”：累计求和、累计计数、累计平均数、累计最小值、累计最大值
<窗口聚合函数> OVER(ORDER BY order_list)
<窗口聚合函数> OVER(ORDER BY order_list ROWS UNBOUNDED PRECEDING AND CURRENT ROW）
```
ii.窗口聚合范围：组内所有行
```
<窗口聚合函数> OVER()
<窗口聚合函数> OVER(ORDER BY order_list ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)
```
iii.窗口聚合范围：frame_clause
```
<窗口聚合函数> OVER(ORDER BY order_list *frame_clause*)
```

**4.窗口排名函数的使用**
- `row_number()`  不考虑列中的相同字段，生成自然数
- `rank()`  遇到相同字段排名相同，skip排名，e.g. 1 2 2 4 5
- `dense_rank()`  遇到相同字段排名相同，不skip排名，e.g. 1 2 2 3 4

## SUM IF & COUNT IF<a name="sumif-countif"></a>
```
对条件进行计数：SUM(条件)等价于SUM(if(条件,1,0))
对条件进行计数：COUNT(IF(条件,1,null))

注：COUNT(条件)等价于COUNT(条件,true,null)，条件满足返回true，否则返回null
```

## DATEDIFF()<a name="datediff"></a>
```
DATEDIFF(x1,x2) -- 返回x1-x2的时间差值，其中x1,x2为column_name或时间字符串
```
## IFNULL()<a name="ifnull"></a>
```
IFNULL(x,value) -- 如果x为NULL，返回value

注：指标计算中，需要外部加IFNULL()。例如IFNULL(COUNT()/COUNT(),0)
```
## ROUND()<a name="round"></a>
```
ROUND(x,d)：四舍五入保留x的d位小数
```

> 1141. 查询近30天活跃用户数
> 597. 好友申请 I：总体通过率
