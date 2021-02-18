# **图解面试题：如何查找第 N 高的数据？**

<img src="https://mmbiz.qpic.cn/mmbiz_png/PnRVMhXvfFK6FAk3qhUZicz0ibMJnXgDOea8B3ibzELniaEvPph6gibQRAPiaZictQOBDetsq9L6louXaArYjwwzVMlow/640?wx_fmt=png" width=680px />

**【题目】**

现在有 “课程表”，记录了学生选修课程的名称以及成绩。

现在需要找出语文课中成绩第二高的学生成绩。如果不存在第二高成绩的学生，那么查询应返回 null。

<img src="https://mmbiz.qpic.cn/mmbiz_png/PnRVMhXvfFK6FAk3qhUZicz0ibMJnXgDOeVlXteKwPmhfmIZPyH0tcrBG1XYcjeicxJfqH1ic7UWD41yShlv3AbbaQ/640?wx_fmt=png" width=680px />


**【解题思路】**

**1. 找出所有选修了 “语文” 课的学生成绩**

```MYSQL
select * 
from 成绩表
where 课程='语文';

```

**2. 查找语文课程成绩的第二名**

考虑到成绩可能有一样的值，所以使用 distinct 成绩进行成绩去重。

**思路 1：**

使用子查询找出语文成绩查询最大的成绩记为 a，然后再找出小于 a 的最大值就是课程成绩的第二高值。

max(列名) 可以返回该列的最大值

可以用下面的 sql 语句得到语文课的最大值

```MYSQL
select max(distinct 成绩) 
from 成绩表
where 课程='语文';

```

然后再找出小于 a 的最大值就是课程成绩的第二高值。

```MYSQL
select max(distinct 成绩) 
from 成绩表
where 课程='语文' and
      成绩 < (select max(distinct 成绩) 
              from 成绩表 
              where 课程='语文');

```

**思路 2：** 使用 limit 和 offset  

在《猴子 从零学会 sql》中讲过：

limit n 子句表示查询结果返回前 n 条数据      

offset n 表示跳过 x 条语句

limit y offset x 分句表示查询结果跳过 x 条数据，读取前 y 条数据

使用 limit 和 offset，降序排列再返回第二条记录可以得到第二大的值。

```MYSQL
select distinct 成绩  
from 成绩表
where 课程='语文'
order by 课程,成绩 desc
limit 1,1;

```

**3. 考虑特殊情况**

题目要求，如果没有第二高的成绩，返回空值，所以这里用判断空值的函数（ifnull）函数来处理特殊情况。

ifnull(a,b) 函数解释：

> 如果 value1 不是空，结果返回 a
> 
> 如果 value1 是空，结果返回 b

对于本题的 sql 就是：  

```MYSQL
select ifnull(第2步的sql,null) as '语文课第二名成绩';

```

我们把第 2 步的 sql 语句套入上面的 sql 语句，本题最终 sql 如下：

```MYSQL
select ifnull(
(select max(distinct 成绩) from 成绩表
where 成绩<(select max(成绩) from 成绩表 where 课程='语文')
and 课程='语文')
,null) as '语文课第二名成绩';

```

查询结果：

![](https://mmbiz.qpic.cn/mmbiz_png/PnRVMhXvfFLxxpcjBgyMbgZjBBV9qwpibXZIlxVBiboWurV6XyOb4vpWXWwE5puictZsL8C5NOZictqtrrR2ONZW7g/640?wx_fmt=png)

**【本题考点】**

1）第二高的查询思路，利用本题的解决办法可以解决这类问题：查询第 N 高的数据

2） limit 子句的用法

3） ifnull 的用法

**【举一反三】**

查找 Employee 表中第二高的薪水（Salary）。查询结果返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。

<img src="https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLxxpcjBgyMbgZjBBV9qwpibEicyHa8qZEKlyo3ic0ribrwFV71k0ib8OAlYeVc3kicqR6oT7T18E36wRZw/640?wx_fmt=jpeg" width=680px />


【参考答案】

```MYSQL
select ifNull(
(select distinct salary
from Employee 
order by Salary Desc
limit 1,1),null
) as SecondHighestSalary;

```

推荐：[如何如何从零学会 sql？](http://mp.weixin.qq.com/s?__biz=MzAxMTMwNTMxMQ==&mid=2649247566&idx=2&sn=5af748b677eb72028764dde0577675fb&chksm=835fc77eb4284e68e8cfe3f08c5a671b9e080b2651f20b40b1c793ffda4042ae43ad8f35a755&scene=21#wechat_redirect)

<img src="https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLxIWAcpH8WkJcASQH4ndhfSBQdupDEEcrxt9GKsU4nKKMQ4ZRVesnGwDT0jUbsRXt5ywrfmE8pqw/640?wx_fmt=jpeg" width=680px />
