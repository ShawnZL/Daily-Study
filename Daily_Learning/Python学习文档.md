# Python学习文档

## day1数据类型和变量

转义字符 \ ，在有很多需要转义的时候可以使用 r’’，引号中间表示非转义

```python
print(r'\\\t\\n') #非转义
```

## day2

```
print(2**10) #表示2的10次方
```

## day3

print() 可以打印每一个字符，遇到逗号会输出 , 会输出一个空格

e 就是10^ 就是次方

单引号和双引号都是可以表示一个字符串，没有区别。单引号需要加 '\' 来让编译器判断目前是转义字符，而双引号方便了很多。反之，如果字符串中有双引号，为了避免使用转义符，可以使用单引号来定义这个字符串。

布尔值：True和False

空值是 python 里一个特殊的值，使用 None 表示，None不能理解为 0，0 有意义，None 是一个特殊控制

Python中用全部大写的字母表示常量

**//** 地板除只取结果的整数部分，所以Python还提供一个余数运算，可以得到两个整数相除的余数 **%**

## day4

如果函数没有使用 return 语句，默认返回一个None。要返回一个None，可以只写一个 return，但是返回具体的数值，就需要return 加上返回内容。对于函数的定义来说，使用return语句可以向外提供该函数执行的一下结果；对于函数的调用者来说，是否可以使用函数中执行的一些操作结果，就在于函数是否使用return语句返回了对应的执行结果。

有的函数会产生结果（如数学函数），我们称这种函数为有返回值函数（fruitful function）;有的函数执行一些动作后不返回任何值，我们称这类函数为无返回值函数。**当我们调用有返回值函数时，可以使用返回的结果做相关操作；当我们使用无返回值函数或返回None的函数时，只能得到一个None值。**

## 2021-11-03

计算机统一存储使用 Unicode 编码，在 python3 中字符串也是使用Unicode编码的

```python
ord('A') #字符 -> Unicode编码
chr(123123) #Unicode编码 -> 字符
```

python 对于bytes类型的数据用带 b 前缀的单引号或双引号表示

%运算符就是用来格式化字符串的。在字符串内部，%s表示用字符串替换

```python
print('%2d-%02d' % (3, 1)) #3-01
print('%.2f' % 3.1415926)  #3.14
```

### list

python list功能，nums[-1] 直接最后一个元素 -n就是倒数第 n 个元素

```python
list.append() 
list.insert(location, 元素)
list.pop() #删除最后一个元素
list.pop(i) #删除位置 i 的元素
```

**一个完整的切片表达式包含两个“:”，用于分隔三个参数（start_index、end_index、step），当只有一个“:”时，默认第三个参数step=1。**

```
>>>a[1:6:-1] # step=-1，决定了从右往左取值，而start_index=1到end_index=6决定了从左往右取值，两者矛盾。
>>> [] # 输出为空列表，说明没取到数据。

>>>a[6:1] # step=1，决定了从左往右取值，而start_index=6到end_index=1决定了从右往左取值，两者矛盾。
>>> [] # 同样输出为空列表。
```



### tuple

tuple 元组：tuple一旦初始化就无法更改 tuple ->() 而list -> []

只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义

```python
t = (1,)
```

可变tuple例子

```python
t = ('a', 'b', ['A', 'B']) #tuple 中挂载一个list
```

input() 类型返回 str 类型，不能直接处理为 int 

range() 函数返回的是一个可迭代对象（类型是对象），而不是列表类型， 所以打印的时候不会打印列表。

```python
range(stop) #默认从 0 开始
range(start, stop[, step]) #到 stop 结束，但是不包括stop，step默认为 1
```

range 可以转化为list 

```python
list(range(5))
```

continue 直接跳过本次循环，直接开始下一次循环

### dict与set

dict -> 其他语言 map {}   key-value 使用hash，key必须为不可变对象

```python
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Michael'] # 95
```

判断key存在不存在使用 in 或者方法 get，不存在返回None或者自己指定数字，存在直接返回value

```python
>>> 'Thomas' in d
>>> False
>>> d.get('M')
```

删除使用pop

```
d.pop('m')
```

set -> 其他语言set key不重复而且按照顺序输出

要创建一个set，需要提供一个list作为输入集合

```python
s = set([1, 2, 3])
```

而显示的`{1, 2, 3}`只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的

重复元素在set中自动被过滤

增删使用

```
add(key)
remove(key)
```

str是不变对象，list是可变对象

### 补充可变和不可变对象

不可变对象：int，string，float，tuple  --可理解为C中，该参数为值传递

由于 Python 中的变量存放的是对象引用，所以对于不可变对象而言，尽管对象本身不可变，但变量的对象引用是可变的。运用这样的机制，有时候会让人产生糊涂，似乎可变对象变化了。所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

可变对象：list，dic                                   -- 可理解为C中，该参数为指针传递

其对象的内容是可以变化的。当对象的内容发生变化时，变量的对象引用是不会变化的

## 2021-11-11

函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于起了一个别名。

```
>>> a = abs # 变量a指向abs函数
>>> a(-1) # 所以也可以通过a调用abs函数
1
```

### 定义函数

在python中，定义函数要使用 def，依次写出函数名，括号，括号中的参数和冒号：

如果没有`return`语句，函数执行完毕后也会返回结果，只是结果为`None`。`return None`可以简写为`return`。

如果你已经把`my_abs()`的函数定义保存为`abstest.py`文件了，那么，可以在该文件的当前目录下启动Python解释器，用`from abstest import my_abs`来导入`my_abs()`函数，注意`abstest`是文件名（不含`.py`扩展名）：

```
>>> from abstest import my_abs
>>> my_abs(-9)
```

`pass` 语句，什么都不做，直接通过，表示占位符！

`isinstance()` 用来判断一个对象是否是一个已知的类型，**考虑父类类型，考虑继承关系**，type不考虑！

```
isinstance(object, classinfo) #classinfo 可以是直接或者间接类名，基本类型或者元组
```

### 返回多个数值

实际返回的是一个 tuple！但是在语法上，返回的tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

这里还有一个问题就是输入多个数据怎么办

```
a, b = input('输入a,b:').split()  #此时a,b为str类型
a, b = map(int, input('输入a,b:').split()) #此时a,b为int类型
```

## 函数的参数

### 位置参数

### 默认参数

```
def power(x, n = 2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

调用 power(5) 相当于调用 power(5, 2)，而对于 n > 2 的其他情况，必须明确传入 n, 比如power(5, 3)

一是必选参数在前，默认参数在后，否则Python的解释器会报错（思考一下为什么默认参数不能放在必选参数前面）；

二是如何设置默认参数。

当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。

使用默认参数有什么好处？最大的好处是能降低调用函数的难度。

```
def enroll(name, genderm, age = 6, city = 'Shanghai')
```

有多个默认参数时，调用的时候，既可以按顺序提供默认参数，比如调用`enroll('Bob', 'M', 7)`，意思是，除了`name`，`gender`这两个参数外，最后1个参数应用在参数`age`上，`city`参数由于没有提供，仍然使用默认值。

也可以不按顺序提供部分默认参数。当不按顺序提供部分默认参数时，需要把参数名写上。比如调用`enroll('Adam', 'M', city='Tianjin')`，意思是，`city`参数用传进去的值，其他默认参数继续使用默认值。

### 可变参数

个数可变，将参数组成一个list 或者一个 tuple 传输进来。在函数内部，参数`numbers`接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数

```
def calc(*number):
    sum = 0
    for n in number:
        sum += n
    return sum
```

`*number`表示把`number`这个list的所有元素作为可变参数传进去。这种写法相当有用，而且很常见。

### 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。

```
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

关键字参数有什么用？它可以扩展函数的功能。比如，在`person`函数里，我们保证能接收到`name`和`age`这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。和可变参数类似，也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去

如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收`city`和`job`作为关键字参数。这种方式定义的函数如下：

```
def person(name, age, *, city, job):
    print(name, age, city, job)
```

命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错

### 参数组合

在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

```
def f1(a, b, c = 0, *args, **kw)
```

在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去。

## 2021-11-17

解决递归调用栈溢出的方法使用过尾递归优化。*（如果一个函数中所有递归形式的调用都出现在函数的末尾，我们称这个递归函数是尾递归的。当递归调用是整个函数体中最后执行的语句且它的返回值不属于表达式的一部分时，这个递归调用就是尾递归）* 简单一句话：在尾部调用自己！

汉诺塔形式

```python
def move(n, a, b, c):
    if n == 1:
        print(a, '-->', c)
    else:
        move(n - 1, a, c, b)
        move(1, a, b, c)
        move(n - 1, b, a, c)
```

### 切片

L[0: 3] 表示从索引 0 开始取，一直到索引到 3 为止，不包括三。第一个索引为 0 可以忽视。L[-1] 选择倒数第一个元素。选择后几个数字

```
L[-10:]  #选择后10个数字
L[start location: ending location: step]
```

tuple 也是一种list，唯一区别就是tuple不可变。

### 迭代

dict 迭代格式

```
for key in d: # 迭代 key
for value in d.values()  #迭代 value
for k, v in d.items()   #迭代 item
```

判断一个对象是否可以迭代

```
from collections.abc import Iterable
isinstance(对象, Iterable)  # 
```

### 列表生成式

List Comprehensions，内置生成 list 的生成式

```
[x * x for x in range(1, 11)]
[x * x for x in range(1, 11) if x % 2 == 0]  #增加判断语句
[m + n for m in 'ABC' for n in 'XYZ']  #使用两层循环，形成全排列
[k + '=' + v for k, v in d.items()]   #调用多个变量！
[x if x % 2 == 0 else -x for x in range(1, 11)]
```

在[ for ] for 之前为对于遍历以后的 x 进行处理的。for 之后的为对于遍历中需要满足条件。在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。

### 生成器

要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的`[]`改成`()`，就创建了一个generator。创建`L`和`g`的区别仅在于最外层的`[]`和`()`，`L`是一个list，而`g`是一个generator。

每一次调用 next(g), 就计算出`g`的下一个元素的值，直到没有元素，抛出StopIteration错误。但基本这个方法不适用

**这就是定义generator的另一种方法。如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator函数，调用一个generator函数将返回一个generator**

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

而变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

但是用`for`循环调用generator时，发现拿不到generator的`return`语句的返回值。如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中。

```python
while True:
	try:
		x = next(g)
		print('g', x)
	except StopIteration as e:
		print('Gnerator return value:', e.value)
		break
```

### 迭代器

可以直接作用于`for`循环的数据类型有以下几种：

一类是集合数据类型，如`list`、`tuple`、`dict`、`set`、`str`等；

一类是`generator`，包括生成器和带`yield`的generator function。

这些可以直接作用于`for`循环的对象统称为可迭代对象：`Iterable`。

可以使用`isinstance()`判断一个对象是否是`Iterable`对象：



而生成器不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到最后抛出`StopIteration`错误表示无法继续返回下一个值了。

可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。

可以使用`isinstance()`判断一个对象是否是`Iterator`对象：

生成器都是`Iterator 对象`，但`list, dict, str` 虽然都是`Iterable`，但是却不是`Iterator`

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```
isinstance(iter(物体), Iterator)
```

这是因为Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

## 函数式编程

特点。允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

函数本身也可以赋值给变量，即变量可以指向函数。

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就可以称为高阶函数

**调用另一个文件中另一个函数，A.py 中的 add 函数, from A import add**

#### map/reduce

我们先看map。`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

```python
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

map() 传入的第一个参数是 f，即函数对象本身。由于结果 r 是一个Iterator，Iterator是惰性序列，因此可以通过 list() 函数将整个序列都计算出来。

```pyhon
>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```

reduce 用法`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：

```python
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```

##### 字符串大小写转换

```python
str = "www.runoob.com"
str.upper() 		#将所有字符中的小写字母转换为大写字母
str.lower() 		#将所有字符中的大写字母转换为小写字母
str.capitalize() 	#把第一个字母转化为大写字母，其余小写
str.title()			#把每个单词的第一个字母转化为大写，其余小写
```

```
def str2float(s) -> float:
    s = s.split('.')
    if s[0]=='0':
        return 0 + reduce(lambda x, y: x / 10 + y, map(lambda x:DIGITS[x], s[1][::-1]))/10
    #[::-1]表示为反转
    else:
        return reduce(lambda x, y: x * 10 + y, map(lambda x:DIGITS[x], s[0])) + reduce(lambda x, y: x / 10 + y, map(lambda x:DIGITS[x], s[1][::-1]))/10

```

#### filter

`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。`filter()`这个高阶函数，关键在于正确实现一个筛选函数。

其中`filter()`函数返回的是一个`Iterator`，也是一个惰性序列，所以要强迫`filter()`完成计算结果，需要用`list()`函数获得所有结果并返回list

```python
def _odd_iter():
    n = 1
    while True:
        n = n + 2
        yield n

def _not_divisible(n):
    return lambda x: x % n > 0

def primes():
	yield 2
	it = _odd_iter()
	while True:
		n = next(it)
		yield n
		it = filter(_no_divisible(n), it) #构造新序列
```

#### 排序算法sort

```
sort([36,5,-13,9,-21])
sort([36,5,-13,9,-21], key=abs)
sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
```

key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。对比原始的list和经过`key=abs`处理过的list，要进行反向排序，不必改动key函数，可以传入第三个参数`reverse=True`。

```python
def by_name(t):
	return t[0].lower()
	
def by_score():
	return -t[1]
```

#### 返回函数

```
def lazy_sum(*args):
	def sum():
		ax = 0
		for n in args:
			ax = ax + n
		return ax
	return sum
```

我们在函数`lazy_sum`中又定义了函数`sum`，并且内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量，当`lazy_sum`返回函数`sum`时，相关参数和变量都保存在返回的函数中。这称为闭包。

##### 闭包

注意到返回的函数在其定义内部引用了局部变量`args`，所以，当一个函数返回了一个函数后，其内部的局部变量还被新函数引用，所以，闭包用起来简单，实现起来可不容易。

另一个需要注意的问题是，返回的函数并没有立刻执行，而是直到调用了`f()`才执行。我们来看一个例子：

```
def count():
	fs = []
	for i in range(1, 4):
		def f():
			return i * i
		fs.append(f)
	return fs
f1, f2, f3 = count()
```

你可能认为调用`f1()`，`f2()`和`f3()`结果应该是`1`，`4`，`9`，但实际结果全是 9，原因就在于返回的函数引用了变量`i`，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量`i`已经变成了`3`，因此最终结果为`9`

```
def count():
	def f(j):
		def g():
			return j * j
		return g
	fs = []
	for i in range(1, 4):
		fs.append(f(i))
	return fs
f1, f2, f3 = count()
```

使用闭包时，对外层变量赋值前，需要先使用nonlocal声明该变量不是当前函数的局部变量。

#### 匿名函数

我们在传入函数时，不需要显式定义函数，直接传入匿名函数更方便

关键字`lambd`表示匿名函数
