# Python问题解答汇总

# local variable referenced before assignment 和global声明的问题

问题起因就是在函数内部更改全局变量就会出现如此错误

```python
a = 3
def temp():
	print(a)
	a+=1
temp()#会报错, UnboundLocalError: local variable 'a' referenced before assignment
```

原因是我们定义的函数temp内部第一次打印a是调用的全局变量，而打印后却令a=a+1，**在函数内部试图更改全局变量a导致错误**。如果我们确实希望通过函数temp打印全局变量a，并且将a的值加1，那么就要在函数内部声明全局变量：

```
a= 3
def temp():
    global a #声明我们在函数内部使用的是在函数外部定义的全局变量a
    print(a)
    a+=1
temp() #调用temp函数，不会再报错了
a #此时就会发现a的值确实变成4
```

**不要在函数内部改变全局变量的值，如果确实想改变全局变量的值（以a为例），那么需要在函数内部首先声明，即加上`global a`这一行代码。**

### global全局变量定义

1.函数间gobal定义

```
def out():
    ## global关键字作用
    global en
    en = 1
    ## 调用other可以打印en，去掉global会报错。
    other()
    print(out.__globals__)
def other():
    print(en)
out()
```

2.在类中也是一样，不多说，怕越多越乱，类中可以用self。

3.其他文件导入，如果定义好了之后从其他文件导入了呢？会发生什么，看一下栗子,a文件仍为上面栗子中代码

4.面好多地方都用到了，func.__globals__，这个运行一下，看个实例

```
# a.py
def out():
    ## global关键字作用
    global en
    en = 1
    # other()
    # print(out.__globals__['en'])
    ## 调用other可以打印en
 
# def other():
#     print(en)
 
# b.py
from a import *
##  b 文件没执行过a文件的out函数，也就是global还没定义
# print('这个是out的：',out.__globals__['en'])    # KeyError: 'en'
def b():
    ## 执行out，global被定义
    out()
    ## 打印out函数的globals
    print('这个是out的：',out.__globals__['en'])
    ## 打印b函数的globals
    # print('这个是b的：',b.__globals__['en'])    # KeyError: 'en'
b()
print('这里是分割线'.center(150,'-'))
## out函数执行过后，global已经被定义
print('这个是out的：',out.__globals__['en'])
# print(b.__globals__['en'])        # KeyError: 'en'
```

 可以得知，如果执行了对应函数，global定义的函数内会存入对应变量，其他函数内则不会存入