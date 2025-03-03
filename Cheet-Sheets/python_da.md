
# 用python数据分析

## 数据加载、存储与文件格式化

- pd.read_csv()
  - na_values：指定哪些值应被视为缺失值（NaN）​
  - sep=：应用正则表达式非正常格式数据进行处理
  - nrows=；只读取部分行
  - chunksize=；逐块读取，对返回对象进行迭代，得到每一块的数据
- pd.to_csv()
  - sep=:指定分割符
  - na_rep=：缺失值处理
  - columns=：只输出指定的列
- 处理不常见格式  
  1. import csv
  2. csv.reader为每一行产生一个元组
      with open('examples/ex7.csv') as f:
        lines = list(csv.reader(f))
  3. 自定义格式：
    class my_dialect(csv.Dialect):
      lineterminator = '\n'
      delimiter = ';'
      quotechar = '"'
      quoting = csv.QUOTE_MINIMAL
    reader = csv.reader(f, dialect=my_dialect)
- 数据库交互：从SQL加载到DataFrame
  - 常用库：SQLAlchemy
  - 例子：连接并读取数据
  
  ```python
  import sqlalchemy as sqla
  db = sqla.create_engine('sqlite:///mydata.sqlite')
  pd.read_sql('select * from test', db)
  ```

- 可以处理JSON数据
- 通过HTTP请求在Web浏览器和其他应用程序之间发送数据的标准格式之一
- HTML和XML格式
- 二进制数据格式
- HDF5格式
- Microsoft Excel文件
- Web APIs交互

## 数据预处理

### 缺失数据

pandas使用NaN表示缺失数据

- 处理缺失数据的函数
  - isnull()函数判断是否为缺失数据，返回布尔类型数据。
  - notnull()为isnull的否定式
  - fillna()：指定值或插值填充数据
  - dropna()：对缺失数据进行过滤
- 删掉缺失数据
  - dropna()：
    - Series对象：返回一个仅含非空数据和索引值的Series
    - DataFrame对象：默认丢弃任何含有缺失值的行
      - 只丢弃全为NA的**行**：how='all'
      - 只丢弃全为NA的**列**：axis=1, how='all'
      - 保留至少有 thresh 个非 NaN 值的行/列
  - isnull()和notnull()通过索引方式定位缺失值并处理
- 缺失数据填充
  - DataFrame.fillna(value=None, method=None, axis=None, inplace=False, limit=None)
    - 实现不同列填充不同值：通过字典表示，如{1: 0.5, 2: 0}
    - 可以填充平均值或中位数
    - method：'ffill'（向前填充）、'bfill'（向后填充）
    - inplace：是否直接修改原对象（默认为False返回新对象）
    - limit：填充的最大连续缺失值数量

### 数据转换

#### 数据去重

- duplicated()
  - 返回布尔类型，如果之前行出现过，本行返回true。
  - 可以指定某列进行判断
  - 默认保留第一个出现的，传入keep='last'则保留最后一个
- drop_duplicates()：返回去重后的结果

#### 建立映射

- 首先建立映射关系--用字典表示
- .map(字典映射关系)或者用函数表示

#### 替换值

- .replace([被替换值],[替换值])或者传入字典.replace({被替换值:替换值})

#### 重命名轴

- .index.map(转换关系/函数)， 直接原地修改  
  如，

  ```python
  data.index = data.index.map(lambda x: x[:4].upper())
  ```

- rename()：创建新的数据，
  - 也可以结合字典部分更新
  - 就地修改某个数据集，传入inplace=True

#### 离散化和面元划分

- 划分面元（分组）
  - cut函数
    - 语法：
      > pandas.cut(x, bins, right=True, labels=None, retbins=False, precision=3, include_lowest=False, duplicates='raise')
      - bins 可以为列表，整数
      - 默认为左开右闭，可通过right=False进行修改
      - labels可以传递列表或数组设置面元的名称
      - precision=n：分箱边界的小数精度
    - 返回Categorical对象
      - pd.value_counts(cats)是pandas.cut结果的面元计数
  - qcut函数：根据样本分位数进行面元划分

#### 检测和过滤异常值

通常运用数组运算找到找到异常值

#### 排列和随机采样

- 对列重新排列（洗牌）
  - 语法：numpy.random.permutation(x)
  - x为整数：将生成一个从 0 到 x-1 的随机排列的数组
  - x为数组：将返回该数组的一个随机排列的副本。
- 采样sample()
  - 语法：  
  
    ```python
    DataFrame.sample(n=None, frac=None, replace=False, weights=None, random_state=None, axis=None)
    ```

    - n:采样数量
    - replace=True：允许重复

#### 计算指标/哑变量

>**独热编码**（One-Hot Encoding）是将分类变量转换为可以被机器学习算法使用的数值形式的一种常用方法，**get_dummies函数**将数据转化为二进制指示符，用于模型处理

例如：对于['red', 'blue', 'green', 'blue', 'red']而言，每个变量的独热编码为：  

 |  |   color_blue | color_green |  color_red |
 | -----------| ----------- | ----------- | ----------- |
|0    |       0     |       0      |    1|
|1    |       1      |      0       |   0|
|2     |      0     |       1      |    0|
|3      |     1      |      0      |   0|
|4      |     0       |     0        |  1|

- 语法：

    ```python
    pandas.get_dummies(data, prefix=None, prefix_sep='_', dummy_na=False, columns=None, sparse=False, drop_first=False, dtype=None)
    ```

### 字符串操作

#### 字符串内置函数

- .split(',')：拆分
- .strip()：去除空白符
- +：连接字符串
- .join()：用separator作为分隔符，iterable为可迭代对象
  - 语法：separator.join(iterable)
- in：判断子串是否出现
- .index('')：返回第一个字符所在位置，如果找不到，会返回一个异常
- .find('')：如果没找到，返回-1
- .count():返回指定子串的次数
- .replace('a','b')：用a替换b

#### 正则表达式

>灵活的在文本中搜索或匹配字符串模式的方式--**Regex**

**import re**

- 模式匹配
- 替换
- 拆分
  - re.split('\s+',text)
    - 描述一个或多个空白符的regex是\s+
    - 用于拆分text中数量不一定的空白符
    - 先编译正则表达式，再在text上调用其split方法