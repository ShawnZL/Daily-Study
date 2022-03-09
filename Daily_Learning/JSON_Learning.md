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

```
{"flag":true}
```

## JSON 使用 JAVAScript 语法

因为JSON使用了JavaScript语法

```
var sites = [
    { "name":"runoob" , "url":"www.runoob.com" }, 
    { "name":"google" , "url":"www.google.com" }, 
    { "name":"微博" , "url":"www.weibo.com" }
];
```

这样可以访问JavaScript对象数组中的第一项

```
sites[0].name; // runoob
sites[0].name = "菜鸟教程"; //修改数据
```

# JSON vs XML

相似之处

- SON 和 XML 数据都是 "自我描述" ，都易于理解。
- JSON 和 XML 数据都是有层次的结构
- JSON 和 XML 数据可以被大多数编程语言使用

JSON与XML的不同之处

- JSON 不需要结束标签
- JSON 更加简短
- JSON 读写速度更快
- JSON 可以使用数组

```
JSON.parse(); #将一个JSON字符串转换为JavaScript对象
JSON.stringify(); #将JavaScript转换为JSON字符串
```

# JSON对象

```
{ "name":"runoob", "alexa":10000, "site":null }
```

JSON对象使用在大括号中书写，对象可以包含`key/value(键/值)`对。

key 必须是字符串，value 可以是合法的 JSON 数据类型（字符串, 数字, 对象, 数组, 布尔值或 null）。

key 和 value 中使用冒号(:)分割。

每个 key/value 对使用逗号(,)分割。

## 访问对象值

使用点号`.`来访问对象的值，也可以使用`[]`来访问对象的值

```
var myObj, x, y;
myObj = { "name":"runoob", "alexa":10000, "site":null };
x = myObj.name;
y = myObj["name"]
```

## 循环对象

使用 `for-in` 来循环对象的属性值

```
var myObj, x, y;
myObj = { "name":"runoob", "alexa":10000, "site":null };
for (x in myObj) {
		document.getElementById("demo").innerHTML += x + "<br>";
}
```

可以使用中括号`[]`来访问属性的值

```
for (x in myObj) {
    document.getElementById("demo").innerHTML += myObj[x] + "<br>";
}
```

## 嵌套JSON对象

```
myObj = {
    "name":"runoob",
    "alexa":10000,
    "sites": {
        "site1":"www.runoob.com",
        "site2":"m.runoob.com",
        "site3":"c.runoob.com"
    }
}
```

你可以使用点号`.`或者中括号`[]`来访问嵌套的JSON对象

```
x = myObj.sites["site1"]; //或者
x = myObj.sites.site1;
```

## 修改值

可以使用`.`和`[]`来修改JSON对象的值

```
myObj.sites.site1 = "www.google.com";
myObj.sites["site1"] = "www.google.com";
```

## 删除对象

使用delete关键字来删除JSON对象的属性

```
delete myObj.sites.site1;
delete myObj.sites["site1"]
```

# JSON数组

```
["Google", "Runoob", "Taobao"]
```

数组必须中括号`[]`书写。

## JSON对象中的数组

对象属性的值可以是一个数组，使用索引值访问数组

```
{
"name":"网站",
"num":3,
"sites":[ "Google", "Runoob", "Taobao" ]
}

x = myObj.sites[0];
```

### 循环数组

使用for-in来访问数组，或者for循环

```
for (i in myObj.sites) {
    x += myObj.sites[i] + "<br>";
}

for (i = 0; i < myObj.sites.length; i++) {
    x += myObj.sites[i] + "<br>";
}
```

## 嵌套JSON对象的数组

JSON对象中数组可以包含另一个数组，或者另一个JSON对象

```
myObj = {
    "name":"网站",
    "num":3,
    "sites": [
        { "name":"Google", "info":[ "Android", "Google 搜索", "Google 翻译" ] },
        { "name":"Runoob", "info":[ "菜鸟教程", "菜鸟工具", "菜鸟微信" ] },
        { "name":"Taobao", "info":[ "淘宝", "网购" ] }
    ]
}
```

使用for-in来循环访问每一个数组

```
for (i in myObj.sites) {
    x += "<h1>" + myObj.sites[i].name + "</h1>";
    for (j in myObj.sites[i].info) {
        x += myObj.sites[i].info[j] + "<br>";
    }
}
```

## 修改数组值

```
myObj.sites[1] = "Github";
```

# JSON.parse()

JSON 通常用于与服务器交换数据

在接收服务器数据时一般是字符串。我们使用`JSON.parse()` 将数据转换为JavaScript对象

```
JSON.parse(text[,reviver])
```

- **text:**必需， 一个有效的 JSON 字符串。
- **reviver:** 可选，一个转换结果的函数， 将为对象的每个成员调用此函数。

## 从服务端接收JSON数据

我们可以使用AJAX从服务器请求JSON数据，并解析为JavaScript对象

```
var xmlhttp = new XMLHttpRequest();
xmlhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
        myObj = JSON.parse(this.responseText);
        document.getElementById("demo").innerHTML = myObj.name;
    }
};
xmlhttp.open("GET", "/try/ajax/json_demo.txt", true);
xmlhttp.send();
```

# JSON.stringify()

JSON 通常用于与服务端交换数据。

在向服务器发送数据时一般是字符串。

我们可以使用JSON.stringify()方法将JavaScript对象转换为字符串。

```
JSON.stringify(value[, replacer[, space]])
```

**参数说明：**

- value:

  必需， 要转换的 JavaScript 值（通常为对象或数组）。

- replacer:

  可选。用于转换结果的函数或数组。

  如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。

  如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。当 value 参数也为数组时，将忽略 replacer 数组。

- space:

  可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 也可以使用非数字，如：\t。

  ## JavaScript 对象转换

  ```
  var obj = { "name":"runoob", "alexa":10000, "site":"www.runoob.com"}; //向服务器发送以下数据
  var myJSON = JSON.stringify(obj); //转化为字符串
  document.getElementById("demo").innerHTML = myJSON; //将myJSON发送到服务器
  ```

  
