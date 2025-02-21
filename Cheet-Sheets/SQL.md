# SQL 复习

[TOC]

## 常见函数

- round(x,y): 四舍五入函数

### 字符串函数

- contact(s1,s2,...): 连接字符串
- replace(s,s1,s2): 替换函数。使用s2替换s中所有的s1
- left(s,n)：返回字符串s最左边n个字符
- right(s,n):返回字符串s最右边n个字符
- substring(s,n,len): 返回从第n个位置起len长度个字符

### 日期函数

- year()、month()、day()：获取年月日
- date_add(date,interval expr type)或  date_sub(date,interval expr type)：日期加减
例子：date_add('2021-08-03 23:59:59',interval 1 second)
- datediff(date1,date2)：两日期之间间隔的天数，date1-date2
- dateformat(date,format): 按照制定格式显示date的值  
format: %Y %m %d

### 条件判断函数

- if(expr,v1,v2)：如果expr为真，返回v1，如果为假，返回v2
- case when
  - case expr when v1 then r1 [when v2 then v2]...[else rn] end
  - case when v1 then r1 [when v2 then r2]...[else rn] end

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

#### 常见窗口函数

##### 排名函数

- **ROW_NUMBER()**：返回当前行的**序号**（唯一）

- **RANK()**：返回当前行的**排名**（并列时跳过后续排名）

- **DENSE_RANK()**:返回当前行的排名（并列时不跳过后续排名）

- 语法

```SQL
SELECT 
    column1,
    ROW_NUMBER()/RANK()/DENSE_RANK() OVER (ORDER BY column2) AS row_num
FROM table_name;
```

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
  
## 常见问题
