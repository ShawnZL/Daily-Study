# Pandas_learning

主要数据结构是 Series（一维数据）与 DataFrame（二维数据）

Series是一种类似于一维数组的对象，它由一组数据（各种Numpy数据类型）以及一组与之相关的数据标签（即索引）组成。

**DataFrame** 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。

## Series

Pandas Series 类似表格中的一个列（column），类似于一维数组，可以保存任何数据类型。

Series 由索引（index）和列组成，函数如下

```
pandas.Series( data, index, dtype, name, copy)
```

- **data**：一组数据(ndarray 类型)。
- **index**：数据索引标签，如果不指定，默认从 0 开始。
- **dtype**：数据类型，默认会自己判断。
- **name**：设置名称。
- **copy**：拷贝数据，默认为 False。

```
import pandas as pd
a = [1, 2, 3]
myvar = pd.Series(a)
print(myvar)
print(myvar[1])
```

如果没有指定索引值，索引值就从0开始，我们可以根据索引值读取数据。

我们可以指定索引值，如下实例：

```
import pandas as pd
a = ["Google", "Runoob", "Wiki"]
myvar = pd.Series(a, index = ["x", "y", "z"])
print(myvar)
print(myvar["y"]) #根据索引值读取
```

我们也可以使用 key/value 对象，类似字典来创建 Series：

```
import pandas as pd
sites = {1: "Google", 2: "Runoob", 3: "Wiki"}
myvar = pd.Series(sites)
print(myvar)
```

### dtype: object

函数 dtype()

结构化数组类型并加以使用

语法：numpy.dtype(object, align, copy)

| 参数   | 含义                                                         |
| ------ | :----------------------------------------------------------- |
| object | 被转换为数据类型的对象                                       |
| align  | 如果为true，则向字段添加间隔，使其类似 C 的结构体            |
| copy   | 是否生成dtype对象的新副本，如果为false，结果是内建数据类型对象的引用。 |

`object` 是一个类型最终所有的类

## DataFrame

DataFrame 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。

<img src="https://www.runoob.com/wp-content/uploads/2021/04/pandas-DataStructure.png" style="zoom:150%;" />

![](https://www.runoob.com/wp-content/uploads/2021/04/df-dp.png)

构造方法

```
pandas.DataFrame( data, index, columns, dtype, copy)
```

参数说明：

- **data**：一组数据(ndarray、series, map, lists, dict 等类型)。
- **index**：索引值，或者可以称为行标签。
- **columns**：列标签，默认为 RangeIndex (0, 1, 2, …, n) 。
- **dtype**：数据类型。
- **copy**：拷贝数据，默认为 False。

```
import pandas as pd
data = [['Google',10],['Runoob',12],['Wiki',13]]
df = pd.DataFrame(data,columns=['Site','Age'],dtype=float)
print(df)
```

以下实例使用 ndarrays 创建，ndarray 的长度必须相同， 如果传递了 index，则索引的长度应等于数组的长度。如果没有传递索引，则默认情况下，索引将是range(n)，其中n是数组长度。

```
import pandas as pd
data = {'Site':['Google', 'Runoob', 'Wiki'], 'Age':[10, 12, 13]}
df = pd.DataFrame(data)
print(df)
```

从以上输出结果可以知道， DataFrame 数据类型一个表格，包含 rows（行） 和 columns（列）：

![](https://www.runoob.com/wp-content/uploads/2021/04/rows-cloumns.png)

还可以使用字典创建

```
import pandas as pd
data = [{'a': 1, 'b': 2},{'a': 5, 'b': 10, 'c': 20}]
df = pd.DataFrame(data)
print (df)
```

无数据的部分数据为NaN

Pandas 可以使用 **loc** 属性返回指定行的数据，如果没有设置索引，第一行索引为 **0**，第二行索引为 **1**。

也可以返回多行数据，使用`[[ ... ]]` 格式，`...` 为各行的索引

```
print(df.loc[[0, 1]])
```

Pandas 可以使用 `loc` 属性返回指定索引对应到某一行

```
data = {
  "calories": [420, 380, 390],
  "duration": [50, 40, 45]
}

df = pd.DataFrame(data, index = ["day1", "day2", "day3"])

# 指定索引
print(df.loc["day2"])
```

## CSV文件

CSV（Comma-Separated Values，逗号分隔值，有时也称为字符分隔值，因为分隔字符也可以不是逗号），其文件以纯文本形式存储表格数据（数字和文本）。

```
df = pd.read_csv('nba.csv')
print(df.to_string())
```

输出结果为右靠齐

**to_string()** 用于返回 DataFrame 类型的数据，如果不使用该函数，则输出结果为数据的前面 5 行和末尾 5 行，中间部分以 **...** 代替。

我们也可以使用 **to_csv()** 方法将 DataFrame 存储为 csv 文件：

```
import pandas as pd
   
# 三个字段 name, site, age
nme = ["Google", "Runoob", "Taobao", "Wiki"]
st = ["www.google.com", "www.runoob.com", "www.taobao.com", "www.wikipedia.org"]
ag = [90, 40, 80, 98]
   
# 字典
dict = {'name': nme, 'site': st, 'age': ag}
     
df = pd.DataFrame(dict)
 
# 保存 dataframe
df.to_csv('site.csv')
```

### 数据处理

#### head()

`head(n)` 方法用于读取前面的 n 行，如果不填参数 n ，默认返回 5 行。

#### tail()

`tail(n)` 方法用于读取尾部的 n 行， 如果不填写参数 n ，默认返回 5 行。空行各个字段的值返回 **NaN**。

#### info()

`info` 方法返回表格的一些基本信息：

```
import pandas as pd
df = pd.read_csv('nba.csv')
print(df.info())
```

## JSON

JSON（**J**ava**S**cript **O**bject **N**otation，JavaScript 对象表示法），是存储和交换文本信息的语法，类似 XML。

```
import pandas as pd

df = pd.read_json('sites.json')
   
print(df.to_string())
```

JSON 对象与 Python 字典具有相同的格式，所以我们可以直接将 Python 字典转化为 DataFrame 数据：

```
# 字典格式的 JSON                                            
s = {
    "col1":{"row1":1,"row2":2,"row3":3},
    "col2":{"row1":"x","row2":"y","row3":"z"}
}
# 读取 JSON 转为 DataFrame
df = pd.DataFrame(s)
print(df)
```

从 URL 中读取 JSON 数据：

```
URL = 'https://static.runoob.com/download/sites.json'
df = pd.read_json(URL)
print(df)
```

### 内嵌的 JSON 数据

假设有一组内嵌的 JSON 数据文件 nested_list.json

这时候我们就需要使用到 `json_normalize()` 方法将内嵌的数据完整的解析出来

```
import pandas as pd
import json

# 使用 Python JSON 模块载入数据
with open('nested_list.json','r') as f:
    data = json.loads(f.read())

# 展平数据
df_nested_list = pd.json_normalize(data, record_path =['students'])
print(df_nested_list)
```

**data = json.loads(f.read())** 使用 Python JSON 模块载入数据。

**json_normalize()** 使用了参数 **record_path** 并设置为 **['students']** 用于展开内嵌的 JSON 数据 **students**。

```
{
    "school_name": "ABC primary school",
    "class": "Year 1",
    "students": [
    {
        "id": "A001",
        "name": "Tom",
        "math": 60,
        "physics": 66,
        "chemistry": 61
    },
    {
        "id": "A002",
        "name": "James",
        "math": 89,
        "physics": 76,
        "chemistry": 51
    },
    {
        "id": "A003",
        "name": "Jenny",
        "math": 79,
        "physics": 90,
        "chemistry": 78
    }]
}
```

显示结果还没有包含 school_name 和 class 元素，如果需要展示出来可以使用 meta 参数来显示这些元数据：

```
#展平数据
df_nested_list = pd.json_normalize(
    data,
    record_path =['students'],
    meta=['school_name', 'class']
)
```

当嵌套了列表和字典

```
{
    "school_name": "local primary school",
    "class": "Year 1",
    "info": {
      "president": "John Kasich",
      "address": "ABC road, London, UK",
      "contacts": {
        "email": "admin@e.com",
        "tel": "123456789"
      }
    },
    "students": [
    {
        "id": "A001",
        "name": "Tom",
        "math": 60,
        "physics": 66,
        "chemistry": 61
    },
    {
        "id": "A002",
        "name": "James",
        "math": 89,
        "physics": 76,
        "chemistry": 51
    },
    {
        "id": "A003",
        "name": "Jenny",
        "math": 79,
        "physics": 90,
        "chemistry": 78
    }]
}
```

```
df = pd.json_normalize(
    data,
    record_path=['students'],
    meta=[
        'class',
        ['info', 'president'],
        ['info', 'contacts', 'tel']
    ]
)
```

students 是信息列表，{}是字典

#### 读取嵌套数据中的一组数据

这里我们需要使用到 **glom** 模块来处理数据套嵌，**glom** 模块允许我们使用 **.** 来访问内嵌对象的属性。

```
df = pd.read_json('nested_deep.json')
data = df['students'].apply(lambda row: glom(row, 'grade.math'))
```

## Pandas 数据清洗

数据清洗是对一些没有用的数据进行处理的过程。

很多数据集存在数据缺失、数据格式错误、错误数据或重复数据的情况，如果要对使数据分析更加准确，就需要对这些没有用的数据进行处理。

在这个教程中，我们将利用 Pandas包来进行数据清洗。

四种空数据：

​	n/a (Not applicable)没有东西可填写，但空格也不允许此项留白的时候，可以写N/A

​	NAN(Not a Number，非数) 表示未定义或不可表示的值
​	--

​	NA

### Pandas 清洗空值

如果我们要删除包含空字段的行，可以使用dropna() 方法

```
DaraFrame.dropna(axis=0, how='any', thresh=None, subset=None, inplace=False)
```

**参数说明：**

- axis：默认为 **0**，表示逢空值剔除整行，如果设置参数 **axis＝1** 表示逢空值去掉整列。
- how：默认为 **'any'** 如果一行（或一列）里任何一个数据有出现 NA 就去掉整行，如果设置 **how='all'** 一行（或列）都是 NA 才去掉这整行。
- thresh：设置需要多少非空值的数据才可以保留下来的。
- subset：设置想要检查的列。如果是多个列，可以使用列名的 list 作为参数。
- inplace：如果设置 True，将计算得到的值直接覆盖之前的值并返回 None，修改的是源数据。

我们可以通过 **isnull()** 判断各个单元格是否为空

```
print (df['NUM_BEDROOMS'])
print (df['NUM_BEDROOMS'].isnull())
```

检查 `NUM_BEDROOMS` 这一列是否有空值。

我们可以指定空数据类型：

```
missing_values = ["n/a", "na", "--"]
df = pd.read_csv('asdasd.csv', na_values = missing_values)
```

删除空值所在的行，只要有一个是空，就会删除整行

```
df.dropna()
df.dropna(subset=['ST_NUM']) #删除ST_NUM 行的空值元素
```

可以使用`fillna()` 方法来替换一些空字段

```
df.fillna(12345, inplace = True) #表示直接写入原文件
```



Pandas使用 **mean()**、**median()** 和 **mode()** 方法计算列的均值（所有值加起来的平均值）、中位数值（排序后排在中间的数）和众数（出现频率最高的数）

```
x = df["ST_NUM"].median()
y = df['ST_NUM'].mean()
z = df['ST_NUM'].mode()
```

### Pandas清洗错误数据

数据错误是很常见的现象，我们可以对错误的数据进行替换或移除

修改数据

```
df = pd.DataFrame(person)

df.loc[2, 'age'] = 30 # 修改数据
```

也可以设置条件语句

```
df = pd.DataFrame(person)

for x in df.index:
  if df.loc[x, "age"] > 120:
    df.loc[x, "age"] = 120
	#df.loc[x, ["age"]] = 120 #第2种写法
```

将错误数据的行删除

```
df = pd.DataFrame(person)

for x in df.index:
  if df.loc[x, "age"] > 120:
    df.drop(x, inplace = True)
```

### Pandas清洗重复数据

如果我们要清洗重复数据，可以使用 **duplicated()** 和 **drop_duplicates()** 方法。

如果对应的数据是重复的，**duplicated()** 会返回 True，否则返回 False。

但是这个会保留第一个数据，删除之后存在的数据