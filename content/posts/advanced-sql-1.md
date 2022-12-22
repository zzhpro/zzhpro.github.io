---
title: "Advanced SQL 1: Window function and With Clause"
date: 2022-12-22T23:15:34+08:00
draft: false
categories:
- 编程语言
- SQL
tags:
- window function
- Recursive CTE
- With Clause
summary: "Window function and with clause in SQL"
---

### Window function
Window function能在一系列与当前行相关的多行组成的集合上进行计算，并把计算结果赋予当前行。与SUM，AVG，MAX等聚合函数类似，
都是在多行上进行计算，其区别在于聚合函数会把参与计算的多行在结果中合并为一行，而窗口函数会为每行生成一个结果。其区别的示意图如下：
![window](https://www.sqlitetutorial.net/wp-content/uploads/2018/11/SQLite-window-function-vs-aggregate-function.png)
如下图所示，SQLite中的window function被分为3类;其中的Aggregate类其实就相当于聚合函数的窗口函数版本。
![window-cat](https://www.sqlitetutorial.net/wp-content/uploads/2018/11/SQLite-Window-Functions-1.png)
下面以NTILE为例，解释window function的使用方法；NTILE主要用于将数据划分、排序并分桶，并将桶号作为新的一列输出;
{{<codeblock "" "SQL">}}
select num, 
ntile(3) over (partition by num % 2 ORDER BY num desc)
from x;
{{</codeblock>}}
若我们的table仅有一列int，其内容为[1,10]，上述代码的输出效果为：
```
10|1
8|1
6|2
4|2
2|3
9|1
7|1
5|2
3|2
1|3
```
可以看到，NITLE函数的执行遵循：划分（partition）、排序（sort）、计算（NTILE）的顺序；这也是大部分window function的处理方式。
由于不能整除，NTILE分出的桶的大小可能至多相差1；若桶的数量大于元素数量，则前面的桶中只有一个元素，后面的桶中无元素。
### With Clause
![with](https://github.com/zzhpro/zzhpro.github.io/raw/main/static/images/with-clause.png)
`With`clause类似普通变成语言中的临时变量定义与赋值，它允许你在进行主要的SQL语句之间创建一系列临时表，避免重复计算，也使得`With`clause后的
主要的计算步骤更易读。一个`With`clause后的可接一个或多个Commen Table Expression(CTE)，每个CTE都能定义一个临时表；而CTE又分为两种：普通
CTE(Ordinary CTE)和递归CTE（Recursive CTE）。其中后者比较有趣：
#### Recursive Common Table Expressions
![recursiveCTE](https://github.com/zzhpro/zzhpro.github.io/raw/main/static/images/recursive-CTE.png)
递归CTE的特殊性质有：
1. CTE体由多个select语句由union、union all、except、intersect复合而成；（注：union会对结果排序去重，而union all不会）；
2. select语句中至少有一个是递归的；递归是指select语句的from clause中包含了至少一次对当前CTE table的引用；
3. select语句中至少有一个是非递归的；
4. 非递归语句出现在递归语句之前；
5. 递归语句与非递归语句必须由union或union all符合；且递归语句之间的复合符必须与递归语句与非递归语句之间的复合符相同；
6. 不能使用窗口函数或聚合函数；


递归CTE的执行算法为：
1. 运行initial-select，将结果加入队列中；
2. while队列非空：
   1. 从队列中取出一行；
   2. 加入表中；
   3. 装作表中只有这一行，调用递归select，并将结果加入队列；


下面的示例生成了Window function一节使用的x表：
{{<codeblock "" "SQL">}}
WITH tmp as (
    select 1 as num
    union all
    select num + 1
    from tmp
    limit 10
)
    insert into x
    select * from tmp;
{{</codeblock>}}
### References
1. [SQLite窗口函数](https://www.sqlitetutorial.net/sqlite-window-functions/)
2. [SQLite NTILE](https://www.sqlitetutorial.net/sqlite-window-functions/sqlite-ntile/)
3. [SQLite With Clause](https://sqlite.org/lang_with.html)