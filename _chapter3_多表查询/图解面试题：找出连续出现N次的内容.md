# **图解面试题：找出连续出现 N 次的内容**

![](https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLSwbx3echq5ZWvxucmHc3nnLkxaiarK9zCl2LmYFQBwVPbEDA233cdd4LUX5BrLQ4FU0341WXogaw/640?wx_fmt=jpeg)

**【题目】**

下面是学生的成绩表（表名 score，列名：学号、成绩），使用 SQL 查找所有至少连续出现 3 次的成绩。  

![](https://mmbiz.qpic.cn/mmbiz_png/PnRVMhXvfFLSwbx3echq5ZWvxucmHc3nAOOk3K2Y4uuic5fQFbvhpEmmtjibPT2V3Of2IVnuXDGbG7sEJ3ldMfeg/640?wx_fmt=png)

例如，“成绩” 这一列里 84 是连续出现 3 次的成绩。

**【解题思路】**

1. 条件 1：什么是连续出现 3 次？

假设 “学号” 是按顺序排列的（如果不是，可以增加一列，这一列是按序号顺序排列的），所以每一学号与上一学号相差 1。例如下图的 3 个学号是连续学号，他们之间的关系是：

某一学号（0002）= 下一位的学号（0003）-1

下一位学号（0003）= 下下位学号（0004）-1

![](https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLSwbx3echq5ZWvxucmHc3ndrkfy4ocJM3sXDKWbd5QIvh1qKy1Srm7pROyCv6CT4piak3j1YPAuGg/640?wx_fmt=jpeg)

2. 条件 2：成绩相等

如果这 3 个连续学号的**成绩相等**，就是题目要求的 “至少连续出现 3 次的成绩”。  

3. 利用 “自关联 “的思路

自连接（自身连接）的本质是把一张表复制出多张一模一样的表来使用。SQL 语法：

```MYSQL
select 列名
from 表名 as 别名1,表名 as 别名2;

```

步骤 1）将成绩表（score）复制 3 个一样的表，分别命名为 a、b、c  

```MYSQL
select *
from score as a,
   score as b,
   score as c;

```

![](https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFKZGXNvhob4H74WibyKaYzmPChADHS8OXTKPC4XSkZUrdJvYCp5ukfFywIrb15jZIicdmuGFK8icdH7Q/640?wx_fmt=jpeg)

步骤 2）我们需要找到这 3 个表中 3 个**连续**的学号，这个条件如下  

a. 学号 = b. 学号 - 1 and b. 学号 = c. 学号 - 1

![](https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLSwbx3echq5ZWvxucmHc3nUncvibzsicPOlYdOqUnwdQfv50QYp7q0OfeRzbJ8rb89E7tECNT4yP9g/640?wx_fmt=jpeg)

步骤 3）还要让这 3 个学号连续的人 “**成绩相等**”，这个条件如下

a. 成绩 = b. 成绩 and b. 成绩 = c. 成绩 

将步骤 2 和步骤 3 的条件合并起来就是下面 SQL 里的 where 字句：  

```MYSQL
select *
from score as a,
   score as b,
   score as c;
 where a.学号 = b.学号 - 1
   and b.学号 = c.学号 - 1
   and a.成绩 = b.成绩
   and b.成绩 = c.成绩;

```

步骤 4）前面步骤已经将连续 3 人相等的成绩找出，现在用 distinct 去掉自连接产生的重复数。最终 SQL 如下：

```MYSQL
select distinct a.成绩 as 最终答案
from score as a,
   score as b,
   score as c;
 where a.学号 = b.学号 - 1
   and b.学号 = c.学号 - 1
   and a.成绩 = b.成绩
   and b.成绩 = c.成绩;

```

最终结果：  

![](https://mmbiz.qpic.cn/mmbiz_png/PnRVMhXvfFLSwbx3echq5ZWvxucmHc3neXOr4u70JlbGkmYEpKTTibBhEgPeqgCUtGdLrcBmU0npoonmgxKDanQ/640?wx_fmt=png)

**【本题考点】**

• 本题考察的是连续出现，会有同学忽略 “连续” 二字

• 考察对自关联的灵活应用

• 从题目连续 3 次成绩相等，判断出 “成绩相等” 和“学号连续”这 2 个条件。考察构建 “连续学号成绩相等” 的思维构建能力

**【举一反三】**  

遇到类似 “连续出 N 次的问题” 可以回想本题的解答思路，如：查询至少连续 3 天没有出勤的员工。

推荐：[如何提升你的分析技能，实现升职加薪？](http://mp.weixin.qq.com/s?__biz=MzAxMTMwNTMxMQ==&mid=2649246542&idx=2&sn=522f75638ff0e49fb23c1c74b89e3db9&chksm=835fc37eb4284a68e2f1d31d53323e37f36f2637070aaf594edabbb376a0416d09315b9d0bee&scene=21#wechat_redirect)

![](https://mmbiz.qpic.cn/mmbiz_jpg/PnRVMhXvfFLxIWAcpH8WkJcASQH4ndhfSBQdupDEEcrxt9GKsU4nKKMQ4ZRVesnGwDT0jUbsRXt5ywrfmE8pqw/640?wx_fmt=jpeg)
