---
title: 浅克隆与深克隆
date: 2017-09-13
tags: [原生js]
categories: js
---

# 浅克隆与深克隆

## 一. js中的对象

谈到对象的克隆，必定要说一下对象的概念。

js中的数据类型分为两大类：原始类型和对象类型。

原始类型包括：数值number、字符串string、布尔值boolean、null、undefined.
基本数据类型保存在栈中，

对象类型包括：对象即是属性的集合，当然这里又有两个特殊的对象—-函数Array、数组（键值的有序集合）。
引用数据类型的值是对象，保存在堆中。

这两种类型在复制克隆的时候是有很大区别的。原始类型存储的是对象的实际数据，而对象类型存储的是对象的引用地址（对象的实际内容单独存放，为了减少数据开销通常存放在内存中）

这两种数据类型存储方式有很大区别，尤其是在参数传递上。可以看看下面的例子。

参数的传递

```js
    function setName(obj)
    { 
      obj.name="我是传递的"; 
      obj=new Object(); 
      obj.name="我是new出来的"; 
    } 
    var person=new Object(); 
    setName(person); 
    console.log(person.name);
```

以上代码的弹出值是:我是传递的，很多人可能会以为将会弹出“我是new出来的”，下面进行一下简单的分析:

在函数外面创建一个对象，并将对象的引用赋值给变量person，person中存储的是对象在内存中的存储地址，当为函数传递参数时，就是传递的在函数外面创建的对象的地址。
在函数中，obj的值即为person的值，指向外面创建的对象，并为其创建一个自定义属性name，然后又创建一个新的对象，并将新对象的地址赋值给obj(person一直没有变化，始终指向原来的对象)，这个时候obj指向的并不是函数外面创建的对象，所以外面对象name属性不会被改变。

![image.png](http://upload-images.jianshu.io/upload_images/4869616-3d98896d160e73b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总的来说，就是函数内创建object对象不会改变外面的属性值。最好画个内存图，马老师教我的，碰到值传递和引用传递一图足矣。当然，这个小例子只是为了让大家复习一下克隆的前置知识。

## 克隆的概念

* 浅度克隆：原始类型为值传递，对象类型仍为引用传递，克隆对象修改引用类型的属性时会影响到原始对象。

* 深度克隆：所有元素或属性均完全复制，与原对象完全脱离，也就是说所有对于新对象的修改都不会反映到原对象中。

### 浅克隆

#### 浅克隆与赋值的区别

```js
    var obj1 = {
        'name' : 'zhangsan',
        'age' :  '18',
        'language' : [1,[2,3],[4,5]],
    };

    var obj2 = obj1;


    var obj3 = shallowCopy(obj1);
    function shallowCopy(src) {
        var dst = {};
        for (var prop in src) {
            if (src.hasOwnProperty(prop)) {
                dst[prop] = src[prop];
            }
        }
        return dst;
    }

    obj2.name = "lisi";
    obj3.age = "20";

    obj2.language[1] = ["二","三"];
    obj3.language[2] = ["四","五"];

    console.log(obj1);  
    //obj1 = {
    //    'name' : 'lisi',
    //    'age' :  '18',
    //    'language' : [1,[4,5]],
    //};

    console.log(obj2);
    //obj2 = {
    //    'name' : 'lisi',
    //    'age' :  '18',
    //    'language' : [1,[4,5]],
    //};

    console.log(obj3);
    //obj3 = {
    //    'name' : 'zhangsan',
    //    'age' :  '20',
    //    'language' : [1,[4,5]],
    //};
```

先定义个一个原始的对象 obj1，然后使用赋值得到第二个对象 obj2，然后通过浅拷贝，将 obj1 里面的属性都赋值到 obj3 中。也就是说：

* obj1：原始数据
* obj2：赋值操作得到
* obj3：浅拷贝得到

然后我们改变 obj2 的 name 属性和 obj3 的 name 属性，可以看到，改变赋值得到的对象  obj2 同时也会改变原始值 obj1，而改变浅拷贝得到的的 obj3 则不会改变原始对象 obj1。这就可以说明赋值得到的对象 obj2 只是将指针改变，其引用的仍然是同一个对象，而浅拷贝得到的的 obj3 则是** 重新创建**了新对象。

然而，我们接下来来看一下改变引用类型会是什么情况呢，我又改变了赋值得到的对象 obj2 和浅拷贝得到的 obj3 中的 language 属性的第二个值和第三个值（language 是一个数组，也就是引用类型）。结果见输出，可以看出来，无论是修改赋值得到的对象 obj2 和浅拷贝得到的 obj3 都会改变原始数据。

这是因为浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据。所以就会出现改变浅拷贝得到的 obj3 中的引用类型时，会使原始数据得到改变。

深拷贝：将 B 对象拷贝到 A 对象中，包括 B 里面的子对象，

浅拷贝：将 B 对象拷贝到 A 对象中，但不包括 B 里面的子对象

#### 深克隆的实现

为了保证对象的所有属性都被复制到，我们必须知道如果for循环以后，得到的元素仍是Object或者Array，那么需要再次循环，直到元素是原始类型或者函数为止。为了得到元素的类型，我们定义一个通用函数，用来返回传入对象的类型。

```js
    //返回传递给他的任意对象的类
    function isClass(o){
        if(o===null) return "Null";
        if(o===undefined) return "Undefined";
        return Object.prototype.toString.call(o).slice(8,-1);//[Object Array] =>Array
    }
```

1. 为什么不直接用toString方法？这是为了防止对象中的toString方法被重写，为了正确的调用toString()版本，必须间接的调用Function.call()方法

2. 为什么不使用typeof来直接判断类型？因为对于Array而言，使用typeof（Array）返回的是object，所以不能得到正确的Array，这里对于后续的数组克隆将产生致命的问题。

3. 确定是那种基本数据类型用typeof,确定是哪种引用数据类型用instanceof

```js
    //深度克隆
    function deepClone(obj){
        var result,oClass=isClass(obj);
            //确定result的类型
        if(oClass==="Object"){
            result={};
        }else if(oClass==="Array"){
            result=[];
        }else{
            return obj;
        }
        for(key in obj){
            var copy=obj[key]; 
            if(isClass(copy)=="Object" || isClass(copy)=="Array"){
                result[key]=arguments.callee(copy);//递归调用
            }else{ //如果为基本数据类型
                result[key]=obj[key];
            }
        }
        return result;
    }
    //返回传递给他的任意对象的类
    function isClass(o){
        if(o===null) return "Null";
        if(o===undefined) return "Undefined";
        return Object.prototype.toString.call(o).slice(8,-1);
    }
    var oPerson={
        oName:"rookiebob",
        oAge:"18",
        oAddress:{
            province:"beijing"
        },    
        ofavorite:[
            "swimming",
            {reading:"history book"}
        ],
        skill:function(){
            console.log("bob is coding");
        }
    };
    //深度克隆一个对象
    var oNew=deepClone(oPerson);
     
    oNew.ofavorite[1].reading="picture";
    console.log(oNew.ofavorite[1].reading);//picture
    console.log(oPerson.ofavorite[1].reading);//history book
     
    oNew.oAddress.province="shanghai";
    console.log(oPerson.oAddress.province);//beijing
    console.log(oNew.oAddress.province);//shanghai
```


>整理自：[浅拷贝VS深拷贝](http://www.jianshu.com/p/ddc2bc57be55)
[Javascript深克隆原理与实现](http://www.zyy1217.com/2017/01/05/Javascript%E6%B7%B1%E5%85%8B%E9%9A%86%E5%8E%9F%E7%90%86%E4%B8%8E%E5%AE%9E%E7%8E%B0/)

