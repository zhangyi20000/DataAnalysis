# python笔记

  [TOC]

## 学习资料

- [廖雪峰的 Python 教程](https://www.liaoxuefeng.com/wiki/1016959663602400)
- 书籍类
  - [利用Python进行数据分析](https://github.com/iamseancheney/python_for_data_analysis_2nd_chinese_version)

## 前置配置

### Anaconda

- [安装教程](https://blog.csdn.net/qq_44000789/article/details/142214660)
- 配置环境  
  - 为什么用Anaconda配置
    - 不同的项目依赖不同版本的python或第三方库，用Anaconda为每个项目创建独立的虚拟环境。
    - Anaconda 提供了 conda 包管理工具，可以轻松安装、更新和删除依赖库，并且会自动解决依赖关系。
    - [配置教程](https://blog.csdn.net/u011385476/article/details/105277426) 或 使用**deepseek**询问
    - 现有项目：将虚拟环境配置为项目的解释器!
  [alt text](image.png)
- jupyter
  - 打开其他盘的文件
    1. cd /d D:
    2. jupyter notebook
  - 快捷键
    - tab 可以补全函数的关键词
    - ?
      - 变量后使用，可以显示对象的信息。
      - 函数名后使用会显示说明文档。??会显示函数源码
      - 搜索命名空间，字符与通配符结合可以匹配所有的名字。如：np.*load*?，搜索np中所有包含load

## 基础知识

### 书写习惯

- 使用tab和空格取代括号；冒号标志着缩进代码块的开始。
- 不需要分号结尾。
- #为注释
- 副作用：函数或方法在执行过程中，除了返回一个值之外，还对程序的其他部分产生了影响
  - 修改全局变量
  - 修改传入的可变对象(list,dict,set)
  - 执行 I/O 操作（如打印到控制台、写入文件）
  - 修改对象的内部状态。
  - :point_right: 避免副作用
    - 不修改传入的可变对象,使用副本（如 list.copy() 或 copy.deepcopy()）。或 返回新对象而不是修改原对象。
    - 尽量使用不可变对象（如元组、字符串）来避免意外修改。
    - 在函数中避免修改全局参数。
- Python的字符串是不可变的，不能修改字符串

- None是Python的空值类型。如果一个函数没有明确的返回值，就会默认返回None。也常常作为函数的默认参数。

### 错误和异常处理

在try/except中调用函数。如果出现异常，则执行except中内容。
无论try部分的代码是否成功，都执行一段代码。可以使用finally
用else让只在try部分成功的情况下，才执行代码

```python
f = open(path, 'w')

try:
    write_to_file(f)
except:
    print('Failed')
else:
    print('Succeeded')
finally:
    f.close()
```

### 文件管理

#### 读取文件

- with:确保文件在使用完毕后被正确关闭。
- read(size=-1)：指定要读取的字符数（文本模式）或字节数（二进制模式）
- seek(offset, whence=0):用于移动文件指针到指定位置，返回新的文件指针位置。offset	移动的偏移量（字节数）；whence	可选参数，指定偏移量的参考位置
- tell():方法用于获取当前文件指针的位置

```python
# 打开文件
with open('example.txt', 'r') as file:
    # 读取前 10 个字符
    content = file.read(10)
    print(f"Read content: {content}")

    # 获取当前文件指针位置
    position = file.tell()
    print(f"Current position: {position}")

    # 移动文件指针到第 5 个字节
    file.seek(5)
    print(f"Position after seek: {file.tell()}")

    # 读取从第 5 个字节开始的内容
    content = file.read()
    print(f"Content after seek: {content}")
```

#### 字节和Unicode

- Unicode: 每个字符都有一个唯一的码点
  - **UTF-8**:长度可变的Unicode编码（1 到 4 个字节表示一个字符）；兼容 ASCII
- ASCII字符：固定长度；码点范围是 0 到 127
- 二进制模式：处理二进制文件（如图片、音频、视频等），读入写入时加符号b

### 赋值与引用

- 将参数传递给函数，新局域变量创建了原始对象的引用

### 鸭子类型

关注的是对象的 **行为**，而不是对象的 **类型**。不需要显式地继承或实现接口，代码更加简洁和灵活。

- 例子:判断对象是否为可迭代的

  ```python
  def isiterable(obj):
    try:
        iter(obj)  # 尝试调用 iter() 函数
        return True  # 如果成功，说明对象是可迭代的
    except TypeError: # not iterable
        return False
  ```

  - 说明:
    - iter() 是 Python 的内置函数，如果是可迭代的，返回一个迭代器。如果对象不可迭代，会抛出 TypeError 异常。
    - try() 尝试执行函数

### 常见函数

#### 时间函数

datetime模块提供了datetime、date和time类型

例子：

```python
dt = datetime(2011, 10, 29, 20, 30, 21)
dt.day
dt.minute
dt.date()
dt.time()
dt.strftime('%m/%d/%Y %H:%M') #转化格式
datetime.strptime('20091031', '%Y%m%d')
dt.replace(minute=0, second=0) #将分钟和秒钟替换为0
```

#### range函数

- 语法： range（起点，终点，步进）
- 产生的整数不包括终点

#### 三元表达式

- 语法：value = true-expr **if** condition **else** alse-expr

#### 序列函数

- enumerate函数：同时跟踪序列的值和序号
  - 用法：for i, value in enumerate(collection):
  - 映射到字典
- sorted函数可以从任意序列的元素返回一个新的排好序的列表
- zip函数（看做是拉链），将多个列表、元组或其它序列成对组合成一个元组列表
  - 可用于同时迭代多个序列：

    ```python
    for i, (a, b) in enumerate(zip(seq1, seq2)):
        print('{0}: {1}, {2}'.format(i, a, b))
    ```

  - 用于解压被压缩序列

    ```python
    first_names, last_names = zip(*pitchers)
    ```

### 数据结构

#### 元组

- 初始化:直接逗号分割；方括号定义
- 固定长度，不可改变。如果内某个对象可改变，可以在原位置进行修改。
- tuple()函数将任意序列或迭代器转换成元组
- 元组可拆分
  - 高级用法：
    values = 1, 2, 3, 4, 5  
    a, b, *_ = values

#### 列表

- 初始化：[1,2,3,'A'];list(**)
- 长度可变、内容可以被修改；
- 常用方法：
  - append:末尾添加元素
  - insert(位置，插入元素) #计算量大
  - pop(n)：移除并返回指定位置的元素
  - remove(去除元素):寻找第一个值并除去
  - in / not in 插值元素是否包含 #慢
  - extend(多个元素)：方法可以追加多个元素
  - sort(): 原地排序
- 切片
  - [start:stop]，不包含终点
  - 元素个数：start:stop
  - [::step]：间隔步长step取数。当step为-1时，颠倒

#### 字典（本质上是2元元组的集合）

- 初始化：{key1 : value1，key2 : value2,,,}。值可以是python中的任意对象，但是键是不可变的标量类型或元组（元组中对象不可变）
- 访问、插入 类似与列表或元组。
- 是否包含某个值： in
- 删除：del关键字 或 pop方（返回值的同时删除键）删除值
- 融合：update({另一字典})
- 获取指定键的值，如果键不存在，则会插入该键并设置默认值
  - dict.setdefault(key, default=None)

#### 集合

- 无序的不可重复的元素的集合
- 初始化：set()或{v1,v2,,,}
- 支持集合的操作
- 支持检测一个集合是否是另一个集合的子集或父集

#### 列表/集合/集合 推导式

- 列表推导式
  - 用于快速创建列表；过滤数据；转换数据；嵌套循环
  - 语法：
  [expression for item in iterable if condition]
  [表达式 for 迭代变量 in 可迭代对象 if 可选条件，用于过滤元素]
  - 例子:  
  [x**2 for x in range(10) if x % 2 == 0] 得到：  [0, 4, 16, 36, 64]
  [(x, y) for x in range(3) for y in range(3)]  得到： [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
- 字典推导式
  - 例子：{x: x**2 for x in range(5)} 得到 {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
- 集合推导式
  - 例子：{x**2 for x in range(-2, 3)} 得到 {0, 1, 4}

### 函数

#### 函数也是对象

- 可以将函数做成一个列表
如对字符串的操作： clean_ops = [str.strip, str.title]。通过遍历clean_ops中元素，作用在字符串类型的元素
- 函数作为参数传入另一个函数
  高阶函数是指能够接受函数作为参数或返回函数作为结果的函数。
  - 例子：

  ```python
  # 自定义一个高阶函数
  def apply_function(func, value):
      return func(value)  # 调用传入的函数

  # 定义一个普通函数
  def square(x):
      return x ** 2

  # 将 square 函数作为参数传递给 apply_function
  result = apply_function(square, 5)
  print(result)  # 输出: 25
  ```

  - 常见的高阶函数
    - map()：将一个函数应用于可迭代对象的每个元素，并返回一个迭代器。
    - filter()：根据条件过滤可迭代对象中的元素，并返回一个迭代器。
    - reduce()：对可迭代对象中的元素进行累积计算
    - 自定义

- 函数作为返回值

```python
      # 定义一个高阶函数，返回一个函数
      def create_multiplier(n):
          def multiplier(x):
              return x * n
          return multiplier
      # 创建一个乘以 2 的函数
      double = create_multiplier(2)

      # 调用返回的函数
      result = double(5)
      print(result)  # 输出: 10
```

#### 匿名函数  lambda

- 仅由单条语句组成，该语句的结果就是返回值
- 语法: lambda 参数1, 参数2, ...: 表达式
add = lambda x, y: x + y
result = add(3, 5)
- 作为参数传递给高阶函数，例如 map()、filter() 和 sorted()。
  - list(map(lambda x: x**2, numbers))
  解释：将 lambda x: x**2 应用到 numbers 列表的每个元素上，再转化为list
  - list(filter(lambda x: x % 2 == 0, numbers))
  解释：过滤出列表中的偶数
  - sorted(words, key=lambda x: len(x))
  解释： 按字符串长度排序

#### 柯里化：部分参数应用

通过“部分参数应用”（partial argument application）从现有函数派生出新函数的技术
例：

```Python
def add_numbers(x, y):
    return x + y
add_five = lambda y: add_numbers(5, y)
```

#### 生成器

生成器只能遍历一次，遍历完后需要重新创建

- 作用：特殊的迭代器，允许你按需生成值，而不是一次性生成所有值。在处理大量数据时，节省内存 和 提高性能
- 创建：
  - 生成器函数：使用 yield 关键字。返回一个生成器对象，不会立即执行函数体。
  
    ```python
    def simple_generator():
      yield 1
      yield 2
      yield 3
    # 创建生成器对象
    gen = simple_generator()

    # 使用 next() 获取值
    print(next(gen))  # 输出: 1
    print(next(gen))  # 输出: 2
    print(next(gen))  # 输出: 3
    ```

    每次调用,函数会从上次暂停的位置继续执行，直到遇到下一个 yield。

  - 生成器表达式：类似于列表推导式，但使用圆括号。

    ```python
    # 生成器表达式
    gen = (x**2 for x in range(5))

    # 使用 for 循环遍历
    for value in gen:
        print(value)
    ```

  - 使用场景
    - 处理大规模数据
    - 可以表示无限序列，而不会耗尽内存
    - 可以用于构建数据处理管道，将多个生成器串联起来

## 常用模块 import **

### itertools模块

提供了许多用于操作迭代器的工具函数。该模块中的函数返回的都是**迭代器**。

#### 常用函数

- 无限迭代器：
  - itertools.count(start=m, step=n):生成一个无限的数字序列
    - 调用： ```for _ in range(5): print(next(counter))```
  - itertools.cycle(可迭代对象): 无限循环遍历一个可迭代对象
  - itertools.repeat('Hello', times=3):  重复生成一个值
- 有限迭代器：
  - itertools.groupby(iterable, key=None)
    - iterable：需要分组的目标可迭代对象；key:用于指定分组依据的函数。返回的是迭代器。
    - eg.

      ```Python
      for key, group in itertools.groupby(data):
        print(f"Key: {key}, Group: {list(group)}")
      ```

### NumPy

#### 初始化

- data1=[1,2,3]
- data2=np.array(data1) 或 data2=np.array(data1,dtype=np.float64)

#### NumPy常用函数

- data.dtype :输出data的类型；data.asatype(np.float64)：转换数组类型
- data.shape
- np.arange(n);生成1~(n-1)

#### NumPy常见用法

- 切片
  - 标量值赋值给切片，该值会自动传播到整个选区。
  - :exclamation: 切片是视图，在视图上的修改会改变源数组
- 布尔索引
  - 对数组进行比较运算，得到一个布尔类型的数组（创建的副本）

    ```Python
    names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
    names == 'Bob'
    ```

  得到：array([ True, False, False,  True, False, False, False], dtype=bool)
  
  - 布尔数组可以用于索引，但是长度得保持一致

    ```Python
    data[names == 'Bob'] ##找到Bob对应的行
    ```

  - ~操作符用来反转条件； 布尔运算符： &、|、!=
- 常见函数（一元或多元）：
  - np.dot();arr.T;np.sqrt(arr);np.exp(arr);np.maximum(x, y);np.modf(arr)(返回浮点数的整数部分和小数部分)
  - np.sqrt(arr, arr):可以直接原地操作
  - numpy.where(condition,x,y)：接收三个参数. 通常用于根据另一个数组而产生一个新的数组。第二三个参数可以是标量也可是是数组。
    - 例子:np.where(arr > 0, 2, -2)
  - .sum()函数可以用于对布尔类型数组计数。 (arr > 0).sum()
  - .sort()对数组就地排序。多维数组可以指定轴排序。（取分位数的方法可以是对数组排序之后，取对应位置上的值）
  - np.unique()：找出数组中的唯一值并返回已排序的结果
  - 文件输入与输出：np.save();np.load()
  - any()：检查可迭代对象中是否存在至少一个为true的元素

- 矩阵运算：numpy.linalg中有标准的矩阵运算
- 生成随机数：numpy.random里有生成不同分布的函数
  - numpy.random.randint(最小值, high=None, size=None, dtype=int)。size为输出数组的形状

### pandas

#### pandas数据结构

- Series（定长的有序字典）
  - 初始化：
    - obj = pd.Series([4, 7, -5, 3])
    - pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])
      可以通过索引的方式获取Series中的单个或者一组值obj2[['c', 'a', 'd']]
    - pd.Series(sdata【,index=数组】) 直接将字典转化为series，可以按照index来改变排列顺序。
    - 索引可以通过赋值的方式修改
  - 根据索引自动对齐数据。
  - 索引和对象本身都有名字：obj.name；obj.index.name
- DataFrame：表格型数据结构
  - 初始化：
    - 直接传入等长的列表或者NumPy数组组成的字典，然后用np.DataFram()函数初始化
    - 指定列序列。pd.DataFrame(data, columns=['year', 'state', 'pop'])
  - loc()属性获取

- 基本功能
  - .reindex()：用于重新索引一个 Series 或 DataFrame
    - 语法：DataFrame.reindex(index=None, columns=None, fill_value=None, method=None, ...)  
    - fill_value：用于填充缺失值的标量值；limit：填充的最大连续缺失值数量；method：填充缺失值的方法。
  - .drop()：删除指定的行或列
    - 语法：DataFrame.drop(labels=None, axis=0, index=None, columns=None, level=None, inplace=False, errors='raise')  
    - inplace=True可以原地修改对象，不返回新的对象
  - 利用标签的切片是包含末端的
  - 索引
    - loc()轴标签：通过标签选择行或列
    - iloc()整数索引：通过位置选择行或列
    - 当索引为整数时，为避免歧义，使用loc（标签）或iloc（整数）
    - 轴索引可以是重复标签,重复的会返回series

#### pandas运算

- df1+df2:对相同索引的对象进行运算，不同索引获得结果并集（不重叠处引入NA）
- 算术方法：df1.add(df2, fill_value=0):没有重叠的部分填充为0
- DataFrame和Series之间可以运算  
需要注意的是，当两边索引不相同的时候，只对相同索引进行计算，不同的索引保留，但是值为NAN。
- apply():将函数应用到各行或各列形成的一维数组上
  - 语法：  
    Series.apply(func, convert_dtype=True, args=(), **kwds)  
    DataFrame.apply(func, axis=0, raw=False, result_type=None, args=(), \*\*kwds)
  - 不止可以返回一个标量
  - 对每个元素应用函数：applymap
- sort_index(): 对索引进行排序
  - 语法：  
        DataFrame.sort_index(axis=0, level=None, ascending=True, inplace=False,  na_position='last', sort_remaining=True, ignore_index=False)
- sort_values()：按照值进行排序
- rank()：计算排名
  - 参数method可以选择计算排名的方法
- .unique()：得到唯一值数组
- .value_counts()：计算出现的频率，按照降序排列；pd.value_counts
- .isin()判断元素是否出现，返回布尔类型。用于过滤Series中或DataFrame列中数据的子集。  
例：obj[obj.isin(['b', 'c'])]

### matplotlib

### SciPy  

解决**科学计算**中各种标准问题域的包的集合,**NumPy**和**SciPy**结合使用可以处理多种传统的科学计算问题。

- 数值积分例程和微分方程求解器
- 线性代数例程和矩阵分解功能
- 稀疏矩阵和稀疏线性系统求解器
- 标准连续和离散概率分布（如密度函数、采样器、连续分布函数等）、各种统计检验方法，以及更好的描述统计法。

### scikit-learn

通用**机器学习**工具包，注重预测。

- 分类：SVM、近邻、随机森林、逻辑回归等等。
- 回归：Lasso、岭回归等等。
- 聚类：k-均值、谱聚类等等。
- 降维：PCA、特征选择、矩阵分解等等。
- 选型：网格搜索、交叉验证、度量。
- 预处理：特征提取、标准化。

### statsmodels

统计分析包,与scikit-learn比较，statsmodels包含经典统计学和经济计量学的算法。更关注与统计推断。

- 回归模型：线性回归，广义线性模型，健壮线性模型，线性混合效应模型等等。
- 方差分析（ANOVA）。
- 时间序列分析：AR，ARMA，ARIMA，VAR和其它模型。
- 非参数方法： 核密度估计，核回归。
- 统计模型结果可视化。

### 其他

- bisect 二分查找，和向已排序的列表插入值
  - bisect.bisect_left()：返回插入点的索引，如果元素已存在，则返回其左侧位置。
  - bisect.bisect_right()（或 bisect.bisect()）：返回插入点的索引，如果元素已存在，则返回其右侧位置。
  - 参数：(已排序的序列（列表）,要插入的元素[,查找范围的起始索引,查找范围的结束索引] )

## 数据分析

### 数据加载、存储与文件格式化

- pd.read_csv()
  - na_values：指定哪些值应被视为缺失值（NaN）​
  - sep=：应用正则表达式非正常格式数据进行处理
  - nrows=；只读取部分行
  - chunksize=；逐块读取，对返回对象进行迭代，得到每一块的数据

### 数据可视化

#### 直方图

```python
import pandas as pd
import matplotlib.pyplot as plt

# 读取 Excel 文件
file_path = 'your_file.xlsx'  # 替换为你的文件路径
sheet_name = 'Sheet1'  # 替换为你的工作表名称
column_name = 'YourColumnName'  # 替换为你要绘制直方图的列名

# 使用 pandas 读取数据
data = pd.read_excel(file_path, sheet_name=sheet_name)

# 检查数据
print(data.head())  # 打印前几行数据，确保读取正确

# 创建画布和子图
plt.figure(figsize=(8, 6))  # 设置画布大小

# 绘制直方图
plt.hist(data[column_name], bins=30, color='skyblue', edgecolor='black', alpha=0.7)

# 添加标题和标签
plt.title(f'Distribution of {column_name}', fontsize=16)
plt.xlabel(column_name, fontsize=14)
plt.ylabel('Frequency', fontsize=14)

# 添加网格线
plt.grid(axis='y', linestyle='--', alpha=0.7)

# 显示图形
plt.show()
```
