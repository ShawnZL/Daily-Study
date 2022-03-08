# JSON语法

## JSON语法规则

大括号`{}` 保存对象

中括号`[]` 保存数组，数组可以包含多个对象

## JSON 名称/值对

```
key : value
"name" : "Shawn"
```

## JSON 值

数字，字符串(在双引号中)，逻辑值，数组(在中括号)，对象(在大括号)，null

### JSON 对象

```
{key1:value1, key2:value2, ... keyN:valueN}
```

### JSON 数组

JSON数组写在 `[]` 中，数组可包含多个对象

```
[
    { key1 : value1-1 , key2:value1-2 }, 
    { key1 : value2-1 , key2:value2-2 }, 
    { key1 : value3-1 , key2:value3-2 }, 
    ...
    { key1 : valueN-1 , key2:valueN-2 }, 
]

{
    "sites": [
        { "name":"菜鸟教程" , "url":"www.runoob.com" }, 
        { "name":"google" , "url":"www.google.com" }, 
        { "name":"微博" , "url":"www.weibo.com" }
    ]
}
```

在上面的例子中，对象 **sites** 是包含三个对象的数组。每个对象代表一条关于某个网站（name、url）的记录。

### JSON 布尔值

