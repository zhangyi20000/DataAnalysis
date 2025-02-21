# python笔记

## 学习资料

- [廖雪峰的 Python 教程](https://www.liaoxuefeng.com/wiki/1016959663602400)
- 书籍类
  - [利用Python进行数据分析](https://github.com/iamseancheney/python_for_data_analysis_2nd_chinese_version)
  
## 统计图表

### 直方图

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
