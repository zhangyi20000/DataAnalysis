
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

>灵活的在文本中搜索或匹配字符串模式的方式--**Regex**,**import re**

1. 正则表达式基础
   1. 元字符
    `.`：匹配除换行符外的任意单个字符。
    `^`：匹配字符串的开头。
    `$`：匹配字符串的结尾。
    `*`：匹配前一个字符 0 次或多次。
    `+`：匹配前一个字符 1 次或多次。
    `?`：匹配前一个字符 0 次或 1 次。
    `{m}`：匹配前一个字符恰好 m 次。
    `{m,n}`：匹配前一个字符 m 到 n 次。
    `[]`：**定义字符集合**（如 [abc] 匹配 a、b 或 c）。
    `|`：逻辑或（如 a|b 匹配 a 或 b）。
    `()`：分组，捕获匹配的子字符串。

   2. 特殊序列
    `\d`：匹配任意数字（等价于 [0-9]）。
    `\D`：匹配非数字字符。
    `\w`：匹配字母、数字或下划线（等价于 [a-zA-Z0-9_]）。
    `\W`：匹配非单词字符。
    `\s`：匹配空白字符（如空格、制表符、换行符）。
    `\S`：匹配非空白字符。
    `\b`：匹配单词边界（如 \bword\b 匹配独立的单词 "word"）。

   3. 例子
      1. `s+`:表示匹配一个或多个空白字符
      2. '[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}'表示：第一段用@分割。第二段用.分割，但是得用转义字符\。第一段匹配A-Z的字母，0-9的数字，`.`、`_`、`%`、`+`、`-`。第二段同理分析。第三段限制为2-4格字母。

2. 常用方法，可以分为三大类：模式匹配、替换、拆分
   1. re.compile():预编译正则表达式，提高效率
      1. 参数：`flags=re.IGNORECASE`。表示忽略大小写。
      2. 例：`regex = re.compile('\s+')  regex.split(text)`
   2. re.split()：根据模式分割字符串
      1. 例：`parts = re.split(r'\d+', 'abc123def456ghi')`
      2. 例：`re.split('\s+',text)`。先编译正则表达式，再在text上调用其split方法。
   3. re.findall()： 返回非重叠匹配项的列表
      1. 例子：

        ```python
          text = "苹果 12元，香蕉 5元，葡萄 8元"
          pattern = re.compile(r'\d+元')  # 匹配"数字+元"
          matches = pattern.findall(text)
        ```

   4. .search():只返回第一个匹配项
   5. re.match():只匹配出现在字符串**开头**的模式
   6. .sub(): 将匹配到的模式替换为制定字符串
      1. 还可以用特殊符号访问匹配项中的分组。`\1`：表示第一个匹配的组
   7. .group()：返回所有分组的匹配结果，以元组保存

#### pandas矢量化字符串函数

对于含有字符串的列有缺失数据；

1. Series.str.contains()函数：用于检查字符串内是否包含特定子串或正则表达式。
   1. 例子：data.str.contains('gmail')
2. 使用正则表达式
   1. 例子：
      `pattern = r'([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})'
      data.str.findall(pattern, flags=re.IGNORECASE)`
      结果返回每个字符串非冲得匹配的列表。
   2. 例子  ：
      `matches = data.str.match(pattern, flags=re.IGNORECASE)`
      检查字符串是否从开头匹配正则表达式，如果成功返回`True`，匹配失败返回`False`。对于缺失值返回`NaN`

## 数据规整：聚合、合并和重塑

### 重要工具：层次化索引

在一个轴上拥有多个（两个以上）索引级别。  
可作用对象Series、DataFrame。

- 初始化：由列表或数组组成的列表作为索引
  - Series：data = pd.Series(np.random.randn(7), index=[['a',  'a', 'a', 'b', 'b'], [1, 2, 3, 1, 2]])
  - DataFrame：
- 索引
  - 外层索引：df.loc["A"]
  - 内层索引
  - 多层索引：df.loc[("B", 1)]
  - 每一层都可以设置名字：frame.index.names = ['key1', 'key2']；frame.columns.names = ['state', 'color']
- 数据重塑：.**unstack**() <span id="unstack"></span>
  - 就是将其转换为矩阵形式，不存在的标签为NaN
  - 语法：`DataFrame.unstack(level=-1, fill_value=None)`
    - level=0:表示第一层（最外层）
  - 逆运算：**stack**()。可以将多行多列转换为带有标签的一列Series
- 重排与分级排序
  - 交换各级别顺序：frame.swaplevel('key1', 'key2')
  - 排序：sort_index(level=1)，根据单个级别中的值对数据进行排序
- 聚合操作:根据几倍汇总统计
  - frame.sum(level='key2')：按照'key2'聚合
- 列做索引
  - 初始化：类似字典{'列名1':[元组],'列名2':range(7),,,,}
  - set_index函数重新设置行索引：frame2 = frame.set_index(['c', 'd'])
  - reset_index函数的功能和set_index相反。层次化索引会被移到列里。

### 合并数据集

- pandas.merge()：根据一个或多个键将不同的DataFrame中的行连接起来。
  - 语法：`pd.merge(left, right, how='inner', on=None, left_on=None, right_on=None, left_index=False, right_index=False, suffixes=('_x', '_y'))`
    - how=left;right;outer;inner
    - (左侧dataframe，右侧dataframe，how=合并方式（默认内连接），on=用于合并的列名（共用），left_on=左表合并列名，suffixes=重叠列名加后缀)
- np.concatenate()：轴向连接
  - 要连接的数组维度相同
  - 语法：`np.concatenate((a1, a2, ...), axis=0)`
- pd.concat()：
  - 语法：

    ```python
    pd.concat(
    objs,                # 要合并的对象列表（如 [df1, df2]）
    axis=0,              # 合并方向：0 按行（纵向），1 按列（横向）
    join='outer',        # 合并方式：outer（并集），inner（交集）
    ignore_index=False,  # 是否忽略原索引，重新生成新索引
    keys=None,           # 添加层次化索引，区分合并对象
    sort=False,          # 是否对列排序
    copy=True            # 是否复制数据
    )
    ```
  
  - 既可以横向也可以纵向拼接。默认在axis=0上工作的（增加行）
  - 可以增加层次化索引，参数增加keys

- 合并重叠数据集
  - combine_first方法：（打补丁）用于合并两个 DataFrame 或 Series 的方法，用第二个对象（参数）的非空值填补第一个对象中的缺失值（NaN）
    - 语法：`df1.combine_first(df2)`

### 重塑（reshape）和轴向旋转（pivot）

- [stack和unstack](#unstack)

- 长格式旋转为宽格式
  - pivot() 函数
    - 语法：`DataFrame.pivot(index=None, columns=None, values=None)`
    - index为行索引，columns为新的列名，values为填充新表数据的列
- 宽格式旋转为长格式
  - pandas.melt()函数
    - 语法：`DataFrame.melt(id_vars=None,value_vars=None,var_name=None,value_name='value',ignore_index=True )`
    - id_vars为不参与融合的列；value_vars为要融合的列，var_name融合后的“变量名”列的标题，value_name融合后的“值”列的标题。

- 其他函数
  - .reset_index()将行索引转换为普通的列

## 数据可视化

### matplotlib

1. 创建子图  plt.subplots
   - 语法：fig, ax =plt.subplots(行数，列数，sharex,sharey)
     - fig：返回的图形对象
     - ax：返回的子图对象
     - sharex（sharey）：指定子图有相同的坐标轴
   - 例子：fig, axes = plt.subplots(2, 3)  
   - 绘制子图：axes[0,0].plot()
   - 具体的子图绘制见 [数据观察](#数据观察)

2. 调整子图间距 subplots_adjust
   - 语法：`subplots_adjust(left=None, bottom=None, right=None, top=None, wspace=None, hspace=None)`
     - wspace和hspace用于控制宽度和高度的百分比
3. 颜色、标记、线型
   - `ax.plot(x, y, linestyle='--', color='g')`
4. 设置标题、轴标签、刻度以及刻度标签
   - 改变刻度：`ax.set_xticks([0, 250, 500, 750, 1000])`
   - 改变刻度标签：`ax.set_xticklabels(['one', 'two', 'three', 'four', 'five'],rotation=30, fontsize='small')`
   - **设置标题**：`ax.set_title('My first matplotlib plot')`
5. 添加图例
   - 首先ax.plot时，传入参数label=。`ax.plot(randn(1000).cumsum(), 'k', label='one')`
   - 之后，使用ax.legend()添加图例
6. 在图上进行注释
   - ax.annotate() 详情看[绘图](https://matplotlib.org/stable/gallery/index.html)
7. 绘制图形
   - 可以画矩形、三角形、圆形
8. 图片保存
   - plt.savefig('figpath.svg', dpi=400, bbox_inches='tight')
     - dpi：控制分辨率
     - bbox_inches：剪除图表周围空白的部分

### seaborn绘图

`import seaborn as sns`

1. 线形图
   - 对于pd.Series类型的对象，直接.plot()就可以画。pd.DataFrame类型类似
2. 柱状图
   - data.plot.bar()水平柱状图
   - data.plot.barh()绘制垂直柱状图
   - 参数：color='k'，alpha=0.7 设置颜色和不透明度
   - stacked=True 生成堆积柱状图

3. 直方图和密度图
   - .plot.hist
   - .plot.density()
4. 散点图
   - sns.regplot
5. 探索性数据分析时，观察一组变量的散布图，绘制散布图矩阵。
   - sns.pairplot
6. 分面图：可视化一个或多个分类变量与数值变量之间的关系。探究分类变量和数值变量之间的关系。
   - `sns.catplot(x=None, y=None, hue=None, data=None, row=None, col=None, kind='strip', height=5)`
     - x:x轴上分类变量。y:y轴上数值变量
     - hue：用于分组的分类变量
     - row、col：分别为分面的行变量和列变量
     - kind：绘图类型。
7. 箱线图
   - sns.factorplot

- 其他函数
  - 交叉表（类似数据透视表）pd.crosstab
    - `pd.crosstab(index, columns, values=None, rownames=None, colnames=None, aggfunc=None, margins=False, margins_name='All', dropna=True, normalize=False)`
    - index:行索引；columns：列索引；aggfunc：聚合函数（默认为计数）
  - 对数据归一化：.div
    - 例子：data.div(data.sum(1), axis=0)。对每行归一化

## 数据聚合与分组运算

隐函数定义分组规则->groupby应用分组->.apply(函数)将函数应用到分组后的结果。

### groupby

### cut

将连续数据分箱，根据数值划分为离散的区间，并返回每个值所属的区间。

语法：

```Python
pd.cut(x, bins, right=True, labels=None, retbins=False, precision=3, include_lowest=False, duplicates='raise')
```

x:需要分箱的数据；  
bins：分箱的规则，可以是整数（表示**等宽**分箱的数量）或列表（表示自定义的区间边界）；  
labels：为每个区间指定标签

例子：

```python
df = pd.DataFrame({'Value': [1, 7, 5, 4, 6, 3]})
df['Category'] = pd.cut(df['Value'], bins=[0, 3, 6, 9], labels=['Low', 'Medium', 'High'])
```

groupby 和 cut 连用：对分箱之后的数据分组，得到一些结果。（自定义分组）

### 应用

#### 缺失数据处理

- dropna()
- fillna()
- 对不同的分组填充不同的数值

  ```python
  fill_mean = lambda g: g.fillna(g.mean())
  data.groupby(group_key).apply(fill_mean)
  ```

#### 随机采样

#### 分组加权平均和相关系数

```python
grouped = df.groupby('category')
get_wavg = lambda g: np.average(g['data'], weights=g['weights'])
grouped.apply(get_wavg)
```

### 交叉表

用于计算分组频率的透视表

- pandas.crosstab

```python
pandas.crosstab(index, columns, values=None, rownames=None, colnames=None, aggfunc=None, margins=False, margins_name='All', dropna=True, normalize=False)
```

index:交叉表的行；columns：交叉表的列；values：要聚合的值；aggfunc：指定的聚合函数（sum，mean等）；margins：是否在交叉表中添加行和列的总计。

## 分析全流程

拿到一个数据该怎么办？

## 数据处理

1. 加载数据集  
data=`pd.read_csv("path")`
2. 查看数据分布  
`data.describu()` ：每列的平均值、分位数、最大值、最小值等，看数据是否有异常。  
`data.info()`：可以看到数据是否有缺失。
3. 缺失数据处理
   1. 是否有缺失：`data.isna().sum()`
   2. 找到缺失值所在行：`data[data.isna().any(axis=1)]`
   3. 缺失值处理
      - 保留：
        - `data['A']=data['A'].fillna(value)`其中value可以是具体的数值，或者是函数
      - 去除：
4. 重复数据
   1. 是否有重复：
      1. 快速判断`data.duplicated().any()`，某列：`data.duplicated()`返回 bool值序列。
      1. 找到所在位置：`data[data['A'].duplicated(keep=False)]`
   2. 重复数据处理
      - 删除重复： `data_deduplicated = data.drop_duplicates()`

## 数据观察

[绘制子图](#matplotlib)  

对于连续型变量可以用直方图查看分布状况，对于离散型变量，使用条形图等查看分布。

0. 对分类数据快速画图,使用plot方法

  ```python
  sex_count=data['sex'].value_counts()
  sex_count.plot(kind='bar')
  ```

1. 离散数据的分布情况
   - 单列取值分布： `data['A'].value_counts()`，结果不包括缺失数据。
   - 所有列：`data.apply(lambda col: col.value_counts())`
2. 连续性直方图  
  plt.hist()返回参数：n（柱子的频数）,bins（柱子的边界）,patches（柱子的图形对象）。

    ```python
    plt.tight_layout() #自动调整大小
    n, bins, patches = plt.hist(data['Age'],bins=10,edgecolor='black')
    total=len(data['Age'])
    for i in range(len(n)):
        plt.text(
            bins[i] + (bins[i+1] - bins[i]) / 2,  # x 位置：柱子中心
            n[i] + 0.1,                            # y 位置：柱子高度上方
            f'{n[i]/total:.2%}',                         # 显示的文本（频率）
            ha='center',                            # 水平对齐方式
            va='bottom'                             # 垂直对齐方式
        )
    plt.title('年龄的分布')
    plt.xlabel('年龄')
    plt.ylabel('频数')
    plt.show()
    ```

1. 饼图

  ```python
  labels=['男','女']
  sizes=data['Sex'].value_counts()
  colors = [ 'lightskyblue','lightcoral']
  plt.pie(
      sizes,
      labels=labels,
      colors=colors,
      autopct='%1.1f%%',  # 显示百分比
      shadow=True,  # 添加阴影
      startangle=90  # 旋转角度
  )
  # 设置标题
  plt.title('性别分布饼图')

  # 显示图形
  plt.show()
  ```

4. 条形图