# C++学习文档

## Chp2

### 2.1.5 名称空间 P17

名称空间支持是一项C++特性，名称空间让厂商能够将其产品封装在一个叫名称空间的单元中，可以使用名称空间来指出想使用哪个厂商的产品。

### 2.2.3 cout的新花样 P23

## Chp3

### 3.1.1 变量名 P38

名称中只能使用字母字符、数字、下划线。名称的第一个字符不能是数字。

### 3.1.3 整形short、int、long和long long P41

#### 1. 运算符sizeof和头文件limits

可以对类型名或变量名使用sizeof运算符。**对类型名（如int）使用 sizeof 运算符时，应将名字放在括号里；但对变量名如（n_short）使用该运算符，括号可以选**

```
cout << "int is " << sizeof(int) << " bytes.\n"
cout << "n_short is " << sizeof n_short << " bytes.\n"
```

| 符号常量   | 表示                        |
| :--------- | --------------------------- |
| CHAR_MAX   | char的最大值                |
| UCHAR_MAX  | Unsigned char 的最大值      |
| LLONG_MAX  | long long 的最大值          |
| ULLONG_MAX | Unsigned long long 的最大值 |

**还可以进行预处理的方法**

```
#define INT_MAX 32767
//但是经常使用const
```

将变量声明和赋值分开，可能会带来瞬间悬而未决的问题

#### 3. C++11初始化方式

```
int hamburgers = {24}; //set hamburgers to 24
//大括号用于初始化单值变量的情形还不多。但是C++11这种情形变多
int emus{7};
int rheas = {12}; //
//大括号可以不包含任何东西，这样变量将初始化为零
int rocs = {};
int psychics{};
```

#### 6. 整形字面值

第一位为0，第二位为1-7，则基数为8（8进制）

如果前两位为 `0x` 或者 `0X` ，则基数为16（16进制）

针对于这种特性，为`cout` 提供了控制符 `dex` ,`hex` 和 `oct` 分别用于表示 cout 以十进制，十六进制和八进制格式表示整数。

默认是十进制，如果没有修改，原来格式一直存在且有效

```
int waits = 14;
cout << waits << endl;
cout << hex;
cout << waits << endl;
```

#### 7. C++如何确定常量的类型

除非有理由存储为其它类型（例如使用了特殊的后缀来表示特定的类型，或者值太大，不能存储为int）都将保存为int

**放置于数字常量后面的字母，用于表示类型，大小写都可以**

| 后缀            | 含义               |
| --------------- | :----------------- |
| l L             | long               |
| u U             | unsigned int       |
| ul UL lu        | unsigned long      |
| ll LL           | long long          |
| ull ULL uLL Ull | unsigned long long |

cout.put() 函数，该函数显示一个字符，如果放入多个字符，则只输出最后一个字符。cout.put('abc') => c

### 3.2 const 限定符

相比于#define，const首先可以指明类型，其次可以使用C++的作用域将定义限制在特定的函数或文件中，第三就是const可以使用于更加复杂类型。

### 3.3 浮点数

第二种表示方法，E表示方法，`3.14E6 = 3140000` 6被称为指数，3.14称为尾数。

`d.dddE+n` 表示将小数点右移n位，`d.dddE-n`表示将小数点左移n位。

## Chp4 复合类型

