---
title: JSON（JavaScript Object Notation）是用于存储和传输数据的格式
date: 2019-10-11 15:07:23
tags: 
- JSON
categories:
- js
---
####  JSON语法规则

- 数据为键/值对
- 数据由逗号分隔
- 大括号保存对象
- 方括号保存数组
***
<!--more-->
#### JSON 对象方法
> JSON字符串转换为JavaScript对象（JSON.parse）
 
```
let text = '{ "sites" : [' +
           '{ "name":"Runoob" , "url":"www.runoob.com" },' +
           '{ "name":"Google" , "url":"www.google.com" },' +
           '{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
let obj=JSON.parse(text);
console.log(obj);
```
![RUNOOB console](../img/1570778333(1).png)

> JSON.parse 进阶用法  


> JavaScript对象序列化为JSON字符串 （JSON.stringify()）
```
   let book = {
            title: 'Profess',
            authors: [
                "Nicholas"
            ],
            edition: 3,
            year: 2001        
       };
   let jsonText = JSON.stringify(book);
   // 默认情况虾，JSON.stingify() 输出的字符串不包含任何空格字符或缩进
   // '{"title":"Profess","authors":["Nicholas "],"edition":3,"year":2011}'

   tips:
   - 在序列化JavaScript对象时，所有函数及原型成员都会被有意忽略，不体现在结果中。
   - 值为undefined的任何属性都会被跳过。结果最终都是值为有效JSON数据类型的实例属性。
```
实际上 JSON.stringify()除了要序列化的JavaScript对象外，还可以接收两个参数，这两个参数用于
指定以不同的方式序列化JavaScript对象。第一个参数是过滤器，可以是一个数组，也可以是一个函数；
第二个参数是一个选项，表示是否在JSON字符串中保留缩进。

- 过滤器  

如果过滤器参数是数组，那么JSON.stringify()的结果中将只包含数组中列出的属性  W

```flow js
let book = {
    "title":"book",
    "name":"JavaScript高级程序设计",
    "year":2019,
}
let jsonText = JSON.stringify(book,["title","year"]);
// {"title":"book","year":2019}
```
如果过滤器参数是函数。传入的函数会接收两个属性【属性名】和【属性值】。跟据属性名可以知道如何处理
要序列化的对象中的属性。属性姓名只能是 字符串。

```flow js
var book = {
    "title": "Professional JavaScript",
    "authors": [
        "Nicholas C. Zakas"
    ],
    "edition": 3,
    "year": 2011
};
var jsonText = JSON.stringify(book, function(key, value){
    switch(key){
        case "authors":
            return value.join(",")
        case "year":
            return 5000;
        case "edition":
            return undefined;
        default:
            return value;
    }
});
console.log(jsonText);
// {"title":"Professional JavaScript","authors":"Nicholas C. Zakas","year":5000}
```
- 选项（字符串缩进）
这个参数是一个数值，那它表示的是每个级别缩进的空格树
```flow js
JSON.stringify(obj,null,4);
每个级别缩进4个空格  
```

