# SQL 复习

[TOC]

## 常见函数和关键字

- round(x,y): 四舍五入函数
- limit:
  - LIMIT offset, row_count
  - offset跳过的行数；row_count返回的行的数量
  - :exclamation: limit 不支持运算！
- offset
  - 通常与 LIMIT 一起使用,跳过指定数量的行，然后返回后续的行。
  - 用法LIMIT row_count OFFSET offset_value; 先跳过前offset_value行,返回row_count行
- declare
  - 用于处理变量，通常用于存储过程、函数和触发器中，用于定义和操作局部变量
  - 语法：DECLARE variable_name data_type
  - declare 必须放在存储过程、函数或触发器的 BEGIN 和 END 块中，且必须放在其他语句之前
- set
  - 为变量赋值
  - 语法：SET variable_name = value;

### 字符串函数

- contact(s1,s2,...): 连接字符串
- replace(s,s1,s2): 替换函数。使用s2替换s中所有的s1
- left(s,n):返回字符串s最左边n个字符
- right(s,n):返回字符串s最右边n个字符
- substring(s,n,len): 返回从第n个位置起len长度个字符

### 日期函数

- year()、month()、day()：获取年月日
- date_add(date,interval expr type)或  date_sub(date,interval expr type)：日期加减
例子:date_add('2021-08-03 23:59:59',interval 1 second)
- datediff(date1,date2):两日期之间间隔的天数, date1-date2
- dateformat(date,format): 按照制定格式显示date的值  
format: %Y %m %d

### 条件判断函数

- if(expr,v1,v2)：如果expr为真，返回v1，如果为假，返回v2
- case when
  - case expr when v1 then r1 [when v2 then v2]...[else rn] end
  - case when v1 then r1 [when v2 then r2]...[else rn] end

- ifnull():如果第一个参数是 NULL，则返回第二个参数；否则返回第一个参数。
  - 语法:IFNULL(expression, replacement_value)
  
### 窗口函数

#### 窗口函数语法

对一组进行计算，并返回一个值。和聚合函数不同，窗口函数不会将多行合并为一行

``` SQL
function_name (expression) OVER (
  [PARTITION BY partition_expression]
  [ORDER BY sort_expression]
  [frame_clause]
)
```

解释：

- PARTITION BY:将数据分组，窗口函数在每个分组内独立计算
- :exclamation:注意PARTITION BY要在ORDER BY之前！

#### 常见窗口函数

##### 排名函数

- **ROW_NUMBER()**：返回当前行的**序号**（唯一）

- **RANK()**：返回当前行的**排名**（并列时跳过后续排名）得到不连续的排序结果

- **DENSE_RANK()**:返回当前行的排名（并列时不跳过后续排名）得到连续的排序结果

- 语法

```SQL
SELECT 
    column1,
    ROW_NUMBER()/RANK()/DENSE_RANK() OVER (ORDER BY column2) AS row_num
FROM table_name;
```
- 区分：1,2,2,2,3
  - ROW_NUMBER()得到排序结果：1,2,3,4,5
  - RANK()得到排序结果：1,2,2,2,5
  - DENSE_RANK()得到排序结果：1,2,2,2,3

##### 分布函数

- **PERCENT_RANK()**：返回当前行的百分比排名

- **CUME_DIST()**：返回当前行的累积分布

##### 前后行函数

- **LAG()**：返回前一行数据

- **LEAD()**：返回后一行数据

- 语法：

```SQL
SELECT 
    column1,
    LAG(column2, 1)/LEAD(column2, 1) OVER (ORDER BY column2) AS prev_value
FROM table_name;
```

##### 聚合函数

- SUM(); AVG()
  - 语法

    ```SQL
    SELECT 
        column1,
        AVG(column2) OVER (ORDER BY column1 ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
    FROM table_name;
    ```

  - ROWS: 基于行数定义窗口范围
    - 语法: ROWS BETWEEN *frame_start* AND *frame_end*  
    其中frame_start 和 frame_end 可以是：  
    UNBOUNDED PRECEDING: 从分区的第一行开始  
    UNBOUNDED FOLLOWING: 到分区的最后一行结束  
    CURRENT ROW: 当前行  
    n PRECEDING: 当前行之前的第 n 行  
    n FOLLOWING: 当前行之后的第 n 行

## SQL技巧

- 查询优化  
  - 只选择需要的列，减少数据传输量。
  - 避免在 WHERE 子句中使用函数或计算。
  - 使用 EXISTS 代替 IN
- 连接优化  
  使用inner join替代子查询
  避免使用笛卡尔积

### 存储函数命令

**CREATE FUNCTION**用于创建自定义的存储函数，类似于内置函数。

- 基本语法

```SQL
CREATE [DEFINER = { user | CURRENT_USER }]  
FUNCTION 函数名称 (参数列表) 
RETURNS 返回值类型
[DETERMINISTIC | NOT DETERMINISTIC]
[CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA]
[SQL SECURITY { DEFINER | INVOKER }]
BEGIN
    -- 函数体，包含 SQL 语句和 RETURN 语句
END;
```

- 传入参数到函数体，在BEGIN 之后，声明（DECLARE）变量和类型，之后设置（SET）值
获取第N高的薪水

```SQL
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT; 
    SET M = N-1; 
  RETURN (
      SELECT DISTINCT salary
      FROM Employee
      ORDER BY salary DESC
      LIMIT M, 1
  );
END
```

- 例子

```SQL
DELIMITER //

CREATE FUNCTION add_numbers(a INT, b INT) RETURNS INT
BEGIN
    RETURN a + b;
END //

DELIMITER ;
# 调用：
SELECT add_numbers(5, 3); -- 返回 8
```

- 题目：[存储命令的例子](https://leetcode.cn/problems/nth-highest-salary/)

## 常见题目

- 查询第N高的数据，如果不存在返回null  

  [leetcode题目](https://leetcode.cn/problems/second-highest-salary/)

  - 使用:limit + offset
  - 先对数据进行排序去重，offset N-1，limit 1，ifnull判断是偶为空。
