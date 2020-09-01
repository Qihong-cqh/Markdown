## javascript 总结

框架参考：
//https://juejin.im/post/6844904116339261447
1. 数据类型以及转换、判断
2. 执行上下文--闭包、作用域链
3. this指向--call、apply、bind
4. 原型/对象--创建、继承
5. 事件循环--eventloop
6. es6 增强语法、模块化
7. 异步流程：promise、settimeout、settimeinterval
8. 深浅拷贝
9. dom事件、bom事件
10. 正则
11. 手写代码
12. 各种api总结

### 1.数据类型、转换、隐式转换、判断

#### 数据类型

一共8种数据类型:Undefined、Null、Boolean、Number、String、Object、Symbol、BigInt

基本数据类型：Undefined、Null、Boolean、Number、String、Symbol、BigInt

引用数据类型：Object

##### Undefined
一个变量声明后没有初始化，值就是 undefined。没有声明就是用变量会报错，但是使用 typeof 判断时还是 undefined。

```
var msg
alert(msg)  //undefined
alert(a)    //报错
alert(typeof msg)   //undefined
alert(typeof a)   //undefined
```

##### Null
一个空对象指针，表示这个地方没有值。null 值的主要作用是如果定义的变量在将来用于保存对象，那么最好将该变量初始化为null，可以方便的知道是否已经保存了对象。

> 注意：使用typeof null 值为 “Object” ，是js存在的一个bug

> null 和 undefined
> 
> 实际上，undefined是派生自null值的，所以 alert(null == undefined)  为 true。
> 但是它们用途不同，没有必要将一个变量显式初始化为undefined，但是对保存对象的变量而言，有必要初始化为null

##### Boolean
只有 false 和 true（只有小写），可以使用Boolean()将其他数据类型转换为boolean值。

调用Boolean()转换规则：
|数据类型|结果|
|----|----|
|Number|除了0 和 NaN 都为true|
|String|除了""(空字符串)都为true|
|Object|true|
|undefined 和 null|false|

##### Number
Number 可以表示整数、浮点数、科学计数法和各种进制。JavaScript能够表示的最小数值保存在 Number.MIN_VALUE 中——在大多数浏览器中，这个值是 5e-324；能够表示的最大数值保存在 Number.MAX_VALUE 中——在大多数浏览器中，这个值是 1.7976931348623157e+308。如果超出数值范围，则会自动转化成有正负的Infinity值，且无法参加下一次的计算，可以用isFinite()函数来判断是否处于有效范围内。


```
var intNum=6
var floatNum1=1.1
var floatNum2=.1    //有效、但是不推荐
var floatNum3=3.125e7   //31250000
//八进制的第一位必须为0，后面是0~7，如果超出范围，则按照十进制解析
var octalNum1=070    //八进制的56
var octalNum2=079   //十进制的79
//十六进制的前两位必须为0x，后面跟0~9|A~F|a~f
var hexNum1=0xA     //十六进制的10
var hexNum2=0x1f    //十六进制的31
```

> 0.1+0.2!=0.3  结果为false
> 
> 这是基于IEEE754数值的浮点计算的通病，

javascript提供了三个函数可以把非数值转换为数值
* Number() 可以用于任何数据类型
* parseInt() 和 parseFloat() 专门用于字符串装转换为数值

1. 调用 Number()转换规则：

|数据类型|结果|
|----|----|
|Boolean|true为1，false为0|
|null|0|
|undefined|NaN|
|String|1.只含数字，变为数字（前置的0会被忽略,'011'=>'11'）<br>2.正确格式的浮点数，变为浮点数<br>3.正确格式的十六进制数，变为十进制<br>4.字符串为空，则变为0<br>5.包含除了以上格式的字符，则转换为 NaN|
|Object|1.调用对象的valueOf()方法，按照前面的规则转换<br>2.如果转换的结果为NaN，则调用对象的toString()方法，然后再按照前面的规则转换|

> 一元+操作符的操作与Number()相同，在隐性转换中介绍

2. parseInt()转换规则：

parseInt()函数在转换字符串时，更多的是看其是否符合数值模式。

```
var num1 = parseInt("1234blue"); // 1234 
var num2 = parseInt(""); // NaN 
var num3 = parseInt("0xA"); // 10（十六进制数）
var num4 = parseInt(22.5); // 22 
var num5 = parseInt("070"); // 56（八进制数）
var num6 = parseInt("70"); // 70（十进制数）
var num7 = parseInt("0xf"); // 15（十六进制数）
```

为了解决ecmascript 3 和 5 版本的分歧，parseInt()引入第二个参数：设置转换时的基数

```
var num1 = parseInt("10", 2); //2 （按二进制解析）
var num2 = parseInt("10", 8); //8 （按八进制解析）
var num3 = parseInt("10", 10); //10 （按十进制解析）
var num4 = parseInt("10", 16); //16 （按十六进制解析）
```

3. parseFloat()
   
parseFloat()的解析与parseInt()类似

```
var num1 = parseFloat("1234blue"); //1234 （整数）
var num2 = parseFloat("0xA"); //0 
var num3 = parseFloat("22.5"); //22.5 
var num4 = parseFloat("22.34.5"); //22.34 第二个小数点无效
var num5 = parseFloat("0908.5"); //908.5 
var num6 = parseFloat("3.125e7"); //31250000
```

##### Number补充：NaN

即：Not a Number。是一个特殊的值。有两个特点：

1.任何涉及NaN的操作(例如NaN/10)都会返回NaN；

2.NaN与任何值都不相等，包括NaN本身。

javascript提供了isNaN()函数来判断是不是 NaN 值

```
alert(isNaN(NaN));      //true 
alert(isNaN(10));       //false（10 是一个数值）
alert(isNaN("10"));     //false（可以被转换成数值 10）
alert(isNaN("blue"));   //true（不能转换成数值）
alert(isNaN(true));     //false（可以被转换成数值 1）
```

##### String
JavaScript中的字符串是不可变的，也就是说，字符串一旦创建，它们的值就不能改变。

字符串字面量：

<img src='./images/字面量.jpg'>

1. javascript提供了toString()方法来转换字符串，除了null 和 undefined不具有这个方法。toString()可以传入一个参数，作为输出数值的基数。

```
var num = 10; 
alert(num.toString()); // "10" 
alert(num.toString(2)); // "1010" 
alert(num.toString(8)); // "12" 
alert(num.toString(10)); // "10" 
alert(num.toString(16)); // "a"
```

2. String()

* 如果值有toString()方法，则调用该方法(无参数)并返回相应的结果。
* 如果是 null，则返回“null”
* 如果是 undefined，则返回“undefined”

3. 使用 +"" ,即可以通过要转换的值 + 空字符串("")，也可以实现转换。

##### Object
在ECMAScirpt中，Object类型是所有它的实例的基础。换句话说，Object类型所具有的任何属性和方法也同样存在于更具体的对象中。

Object的每个实例都具有下列属性和方法
* constructor：保存着用于创建当前对象的函数。
* hasOwnProperty(propertyName):用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在。
* isPrototypeOf(object)：用于检查传入的对象是否是传入对象的原型。
* propertyIsEnumerable(propertyName)：用于检查给定的属性是否能够使用 for-in 语句来枚举。
* toLocaleString()：返回对象的字符串表示，该字符串与执行环境的地区对应。
* toString()：返回对象的字符串表示
* valueOf()：返回对象的字符串、数值或布尔值表示。通常与 toString()方法的返回值相同。

<img src='./images/tostring和valueof转换.jpg'>

##### Symbol 和 BigInt
占坑

阮一峰老师：

<a href='https://es6.ruanyifeng.com/#docs/symbol'>es6入门教程-Symbol</a><br>
<a href='https://es6.ruanyifeng.com/#docs/number'>es6入门教程-BigInt</a><br>

##### 基本数据类型 vs 引用数据类型

> 基本数据类型
> 
> 1. 值是不可变的
> 
> 2. 存放在栈区：按值访问，可以直接操作保存在变量中实际的值，占据空间小，大小固定
> 
> 3. 值的比较：
> * == ：只进行值的比较，会进行数据类型转换
> * === ：不会转换数据类型

> 引用数据类型
> 
> 1. 值是可变的：引用数据类型具有一个或多个属性和方法，是可以动态修改的
> 
> 2. 同时存放在栈内存和堆内存：应用数据类型在栈中存放了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。
> 
> 3. 值的复制：当从一个变量向另一个变量赋引用类型的值时，只是存储了一个指向堆内存中相同对象的指针。

##### Javascript的内置对象

<a href='https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects'>JavaScript 标准内置对象</a>

#### 数据类型的转换
javascript中类型转换(显性)只有三种情况：
##### 1. 转换为Boolean--Boolean()

调用Boolean()转换规则：

|数据类型|结果|
|----|----|
|Number|除了0 和 NaN 都为true|
|String|除了""(空字符串)都为true|
|Object|true|
|undefined 和 null|false|

##### 2. 转换为Number--Number()/parseInt()/parseFloat()

调用 Number()转换规则：

|数据类型|结果|
|----|----|
|Boolean|true为1，false为0|
|null|0|
|undefined|NaN|
|String|1.只含数字，变为数字（前置的0会被忽略,'011'=>'11'）<br>2.正确格式的浮点数，变为浮点数<br>3.正确格式的十六进制数，变为十进制<br>4.字符串为空，则变为0<br>5.包含除了以上格式的字符，则转换为 NaN|
|Object|1.调用对象的valueOf()方法，按照前面的规则转换<br>2.如果转换的结果为NaN，则调用对象的toString()方法，然后再按照前面的规则转换|

##### 3. 转换为String--toString()/String()

调用 String()转换规则：

|数据类型|结果|
|----|----|
|Number|调用toString()|
|Boolean|调用toString()|
|Function|调用toString()|
|Symbol|调用toString()|
|Object|"[Object Object]"|
|undefined |"undefined"|
|null|"null"|

#### 数据类型的隐式转换（+、-、==...）

##### 1. 递增递减操作符

|操作对象|结果|
|---|---|
|包含有效数字字符的字符串|先转换成数字值，再执行+/- 1的操作|
|不包含有效数字字符的字符串|NaN|
|false|先转换成0，再执行+/- 1的操作|
|true|先转换成1，再执行+/- 1的操作|
|Object|1. 先调用对象的valueOf()方法，来取得可以操作的数值。再应用前面的规则<br>2.如果结果是NaN，则再调用toString()方法，再应用前述规则。|

```
var s1='2'
var s2='z'
var b=false
var f=1.1
var o={
    valueOf:function(){return -1}
}

s1++    //3
s2++    //NaN
b++     //1
f--     //0.10000000000000009 由于浮点数舍入错误
o--     //-2
```

##### 2. 一元 +/- 操作符
在对非数值应用一元 +/- 操作符时，该操作符会像Number()一样对这个值进行转换。一元 - 操作符主要用于表示负数，会在转换后将数值变为负值。

为了方便理解，这里直接将Number()的规则搬来：

调用 Number()转换规则：

|数据类型|结果|
|----|----|
|Boolean|true为1，false为0|
|null|0|
|undefined|NaN|
|String|1.只含数字，变为数字（前置的0会被忽略,'011'=>'11'）<br>2.正确格式的浮点数，变为浮点数<br>3.正确格式的十六进制数，变为十进制<br>4.字符串为空，则变为0<br>5.包含除了以上格式的字符，则转换为 NaN|
|Object|1.调用对象的valueOf()方法，按照前面的规则转换<br>2.如果转换的结果为NaN，则调用对象的toString()方法，然后再按照前面的规则转换|

```
var s1='01'
var s2='1.1'
var s3='z'
var b=false
var f=1.1
var o={
    valueOf:function(){return -1}
}
//运用一元 + 操作符
s1 = +s1    //1
s2 = +s2    //1.1
s2 = +s3    //NaN
b = +b      //0
f = +f      //1.1
o = +o      //-1
//运用一元 - 操作符
s1 = -s1    //-1
s2 = -s2    //-1.1
s2 = -s3    //NaN
b = -b      //0
f = -f      //-1.1
o = -o      //1
```

##### 3. ！操作符/!!操作符
！操作符会将它的操作数转换为一个布尔值，然后再取反

|操作对象|结果|
|---|---|
|对象|false|
|空字符串|true|
|非空字符串|false|
|0|true|
|任意非0数值（包括Infinity）|false|
|null|true|
|NaN|true|
|undefined|true|

!!操作符可以将一个值转换为与之对应的布尔值，实际上就是模拟了Boolean()。

```
alert(!false)   //true
alert(!'blue')  //false
alert(!0)       //true
alert(!NaN)     //true
alert(!"")      //true
alert(!1234)    //false

alert(!!"blue") //true
alert(!!0)      //false
alert(!!NaN)    //false
alert(!!"")     //false
alert(!!1234)   //true
```

##### 4. 加法操作符 +

|操作对象|结果|
|---|---|
|有一个操作数是字符串|将另一个操作数也转换成字符串，再拼接起来|
|两个操作数都是字符串|直接拼接起来|
|有一个操作数是对象、数值或布尔值|调用toString()方法取得字符串值，然后应用前面规则|
|undefined/null|"undefined"/"null"|

```
var result=5+'5'
alert(result)   //'55'

var num1=5
var num2=10
var msg="result is "+num1+num2
alert(msg)      //result is 510
var msg="result is "+(num1+num2)
alert(msg)      //result is 15
```

##### 5. 减法操作符 -

|操作对象|结果|
|---|---|
|如果有一个操作数为字符串、布尔值、null或undefined|1.先调用Number()转换为数值，再计算<br>2.如果转换的结果为NaN，则结果为NaN|
|如果有一个操作数为对象|1.先调用对象的valueOf()方法取得数值，再计算<br>
2.如果转换的结果为NaN，则结果为NaN<br>3.如果对象没有valueOf()方法，则调用toString()方法并将得到的字符串转换为数值|

```
var res1=5-true     //4
var res2=NaN-1      //NaN
var res3=5-3        //2
var res4=5-''       //5
var res5=5-'2'      //3
var res6=5-null     //5
```

##### 6. 相等和不相等 全等和不全等

1. 相等和不相等

|操作对象|结果|
|---|---|
|有一个操作数为布尔值|则转换为数值进行比较|
|有一个操作数为字符串，另一个为数值|则把字符串转换为数值再比较|
|有一个操作数为对象，另一个不是|调用对象的valueOf(),用得到的基本类型按照前面的规则进行判断|

> 特殊要点：
> * null 和 undefined，判断为相等
> * 一个为NaN，另一个不是，判断为不相等
> * 两个NaN，判断为不相等
> * 两个都为对象，则需要比较它们是否为同一个对象

```
null == undefined   //true
"NaN" == NaN        //false
5 == NaN            //false
NaN == NaN          //false
false == 0          //true
ture == 1           //true
true == 2           //false
undefined == 0      //false
null == 0           //false
'5' == 5            //true
```

2. 全等和不全等

严格比较两个操作数，需要完全相等（数据类型也相等），不进行数据类型的转换。

##### 7. 其他隐式转换

* 小于、大于、小于等于、大于等于
* 三元运算符
* if 条件判断

#### 数据类型的判断
typeof、instanceof、constructor、Object.prototype.toString.call() 是比较常用的四种方法

##### 1. typeof
对于原始类型来说，除了null都可以显示正确的类型

```
console.log(typeof 2)           //number
console.log(typeof true)        //boolean
console.log(typeof 'str')       //string
console.log(typeof [])          //object
console.log(typeof function(){})       //function
console.log(typeof undefined)   //undefined
console.log(typeof null)        //object 
```

##### 2. instanceof
typeof 对于对象来首，除了函数都会显示object，所以说typeof并不能准备判断对象的类型。这时可以考虑使用instanceof，它的内部机制是通过判断对象的原型链中能否找到类型的prototype。

```
console.log(2 instanceof Number);               // false，2不是Number的实例
console.log(true instanceof Boolean);           // false，true不是Boolean的实例
console.log('str' instanceof String);           // false，'str'不是String的实例 
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true    
console.log(undefined instanceof Undefined);         //报错
console.log(null instanceof Null);                   //报错
//可以看出，基本的数据类型不能被instanceof 准确判断
```

##### 3. constructor

```
console.log((2).constructor === Number); // true
console.log((true).constructor === Boolean); // true
console.log(('str').constructor === String); // true
console.log(([]).constructor === Array); // true
console.log((function() {}).constructor === Function); // true
console.log(({}).constructor === Object); // true
```

> 如果更改了对象的原型，constructor就不能准确判断

##### 4. Object.prototype.toString().call()
借用 Object 的 toString 方法

```
var a = Object.prototype.toString;
 
console.log(a.call(2));
console.log(a.call(true));
console.log(a.call('str'));
console.log(a.call([]));
console.log(a.call(function(){}));
console.log(a.call({}));
console.log(a.call(undefined));
console.log(a.call(null));
```

##### 5. 其他判断方式
* Object.is(a,b) 判断a，b 是否完全相等，与 === 基本相同，但是会判断+0不等于-0，NaN等于NaN
* isPrototypeOf() 判断原型
* isNaN() 是否为NaN

#### 参考

<a href='https://juejin.im/post/6844903990904225805'>「前端料包」可能是最透彻的JavaScript数据类型详解</a><br>


### 4.原型、对象的创建和继承

#### 原型和原型链

> <img src='./images/原型链.png'>
> 
> * 原型：
> 可以简单理解为每一个对象在创建时会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型“继承”属性。
> 
> * 为什么要有原型链？它的作用？
> 
> 简单来说就是为了javascript继承实现的一种机制，所有实例对象需要共享的属性和方法，都可以放在prototype对象中，减少资源的浪费。
> 具体可以参考：阮一峰老师的<a href='https://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html'>Javascript继承机制的设计思想</a>
> 
> 1. prototype: 
> 函数的prototype属性指向了一个对象，这个对象正式调用该构造函数而创建的实例的原型
> 
> 2. ```__prototype__``` :
> 浏览器实现了一个__proto__属性来使得我们可以访问到对象的原型，使用Object.getPrototypeOf()更为规范。
> 
> 3. constructor:
> 每一个原型都有一个constructor属性指向关联的构造函数。
> 
> 4. 原型链：
> 原型对象也是通过Object构造函数生成的，所以原型对象的__proto__指向Object.prototype。而Object.prototype的原型不存在，即为null。图中蓝色的线，就是由相互关联的原型组成的原型链。
> 当查找对象的属性时，如果实例对象自身不存在该属性，则沿着原型链往上一级查找，直至最顶级的原型对象Object.prototype，如还是没找到，则输出undefined
> 参考：<a href='https://github.com/mqyqingfeng/Blog/issues/2'>JavaScript深入之从原型到原型链</a>
> * 补充：function构造函数本身也是function类型的实例吗？
> 在js的定义中：Function instanceof Function 为true，即Object.getPrototypeOf(Function) === Function.prototype 符合定义。
> 但是实际上，Function是js内置的函数，不存在“Function对象由Function构造函数生成”这样的情况。可以简单认为这只是符合某种关系。

#### 对象的创建

##### 1. 工厂模式
```
function createPerson(name){
    var o=new Object()
    o.name=name
    o.getName=function(){
        console.log(this.name)
    }
    return o
}
var person1=createPerson('kevin')
//缺点：对象无法识别，因为所有的实例都指向一个原型。
```

##### 2. 构造函数模式

```
function Person(name){
    this.name=name
    this.getName=function(){
        console.log(name)
    }
}
var person1=new Person('kevin')
//优点：实例可以识别为一个特定的类型
//缺点：每次创建实例时，每个方法都要被创建一次，也就是每个实例中的方法其实不是相等的（不指向同一个内存地址）。
```

> 构造函数模式优化
> 让实例指向同一个方法

```
function Person(name){
    this.name=name
    this.getName=getName
}
function getName(){
    console.log(this.name)
}
var person1=new Person('kevin')
//优点：解决了上面所讲的，每个方法都要被重新创建的问题
//没有封装性可言，当对象需要很多方法时，需要定义多个全局对象
```

##### 3. 原型模式
```
function Person(name){}
Person.prototype.name='kevin'
Person.prototype.getName=function(){
    console.log(this.name)
}
var person1=new Person()
//优点：方法不会重新创建
//缺点：1.所有的属性和方法都共享 2.不能初始化参数
```
> 原型模式优化

```
方法1：
function Person(name){}
person.prototype={
    name:'kevin',
    getName:function(){
        console.log(this.name)
    }
}
var person1=new Person()
//优点：封装性好一点
//缺点：重写了原型，丢失了constructor属性

方法2：
function Person(name){}
Person.prototype={
    constructor:Person,
    name:'kevin',
    getName:function(){
        console.log(this.name)
    }
}
var person1=new Person()
//优点：实例可以通过constructor属性找到对应的构造函数
//缺点：没有解决原型模式的缺点
```

##### 4. 组合模式
结合构造函数模式和原型模式
```
function Person(name){
    this.name=name
}
Person.prototype={
    constructor:Person,
    getName:function(){
        console.log(this.name)
    }
}
var person1=new Person()
//优点：方法可以共享，属性可以私有，使用最广泛。
//缺点：希望可以有更好的封装性
```

##### 4.1 动态原型模式
可以通过检查某个应该存在的方法是否有效，来决定是否初始化原型。

```
function Person(name){
    this.name=name
    if(typeof this.getName!='function'){
        Person.prototype.getName=function(){
            console.log(this.name)
        }
    }
}
var person1=new Person()
//优点：保持了组合模式的优点的同时，封装性更好，使得属性和方法可以写在一起。
```
> 注意：不能用对象字面量重写原型，如下：
```
var person1=Person('a')
var person2=Person('b')
person1.getName()   //报错，没有该方法
person2.getName()   //注释掉上面的代码的话，可以执行
```
> 解释：调用构造函数时会为实例添加一个指向最初原型的prototype指针，而把原型修改为另一个对象就切断了构造函数和最初原型之间的联系。
> 
> <img src='./images/重写原型对象.jpg'>

```
解决方案：
function Person(name) {
    this.name = name;
    if (typeof this.getName != "function") {
        Person.prototype = {
            constructor: Person,
            getName: function () {
                console.log(this.name);
            }
        }

        return new Person(name);
    }
}
var person1 = new Person('a');
var person2 = new Person('b');

person1.getName(); // a
person2.getName();  // b
```

##### 5. 寄生-构造函数-模式
创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后在返回新创建的对象。

```
function Person(name){
    var o=new Object()
    o.name=name
    o.getName=function(){
        console.log(this.name)
    }
    return o
}
var person1=new Person('kevin')
console.log(person1 instanceof Person) //false
console.log(person1 instanceof Object) //true
//跟工厂模式一样，无法识别对象
```

> 这个模式可以在特殊的情况下用来为对象创建构造函数。
> 假设我们想创建一个具有额外方法的特殊数组，由于不能直接修改Array构造函数，因此可以使用这个模式

```
function SpecialArray(){
    var values=new Array()      //创建数组
    values.push.apply(values,arguments)     //添加值
    values.toPipedString=function(){        //添加方法
        return this.join('|')
    }
    return values               //返回数组
}
var colors=new SpecialArray('red','blue','green')
console.log(colors.toPipedString())     //red|blue|green
```

##### 6. 稳妥构造函数模式
所谓稳妥对象，指的是没有公共属性，而且其方法也不引用 this 的对象。
```
function Person(name){
    var o=new Object()
    o.sayName=function(){
        console.log(name)
    }
    return o
}
var person1= Person('A')
person1.name='B'
person1.sayName()   //A
console.log(person1.name)   //B

//除了sayName方法外，没有其他方法可以访问name的值。
//适合在一些安全的环境中（禁止this和new）使用，或者在防止数据被应用程序修改时使用

//跟工厂模式，寄生构造函数模式一样，无法识别对象。不能依赖instanceof操作符来确定对象类型。
```
> 跟寄生构造函数模式的不同：
> 1. 新创建对象的实例方法不引用this
> 2. 不实用new操作符调用构造函数

#### 对象的继承

##### 1.原型链继承
```
function Parent(){
    this.name='nameA'
}
Parent.prototype.getName=function(){
    console.log(this.name)
}

function Child(){}
Child.prototype=new Parent()

var child1=new Child()
console.log(child1.getName())   //  nameA
//问题：1.引用类型的属性被所有实例共享
//2.在创建child的实例时，不能向Parent传参
```

##### 2.借用构造函数（经典继承）

```
function Parent(){
    this.names=['nameA','nameB']
}
function Child(){
    Parent.call(this)
}
var child1=new Child()
child1.names.push('nameC')
console.log(child1.names)   //['nameA','nameB','nameC']
var child2=new Child()
console.log(child2.names)   //['nameA','nameB']

//优点：1.避免了引用类型的属性被所有实例共享
//2. 可以在Child中向Parent传参
//缺点：方法都在构造函数中定义，每次创建实例都会创建一遍方法
```

##### 3.组合继承
结合 原型链继承 和 经典继承

```
function Parent(name){
    this.name=name
    this.colors=['red','blue','green']
}
Parent.prototype.getName=function(){
    console.log(this.name)
}

function Child(name,age){
    Parent.call(this,name)
    this.age=age
}
Child.prototype=new Parent()

var child1=new Child('nameA','18')
child1.colors.push('black')

console.log(child1.name) //nameA
console.log(child1.age) //18
console.log(child1.colors) //['red','blue','green','black']

//优点：融合 原型链继承 和 构造继承 的优点，是javascript中最常用的继承模式。
```

##### 4.原型式继承
就是es5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型。本质上讲，createObj（）对传入其中的对象执行了一次浅复制。

```
function createObj(o){
    function F(){}
    F.prototype=o
    return new F()
}
//优点：在没有必要创建构造函数，而只想让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的。
//缺点：包含应用类型值的属性始终会共享相应的值，就像使用原型模式一样
```

##### 5. 寄生式继承
创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来做增强对象，最后返回对象。
```
function createObj(o){
    var clone=object.create(o)
    clone.sayName=function(){
        console.log("hi")
    }
    return clone
}
//缺点：跟 借用构造函数模式 一样，每次创建对象都会创建一边方法，造成资源的浪费
```

##### 6. 寄生组合式继承
在组合继承的基础上进行改进，可以先看看前面写的组合式继承，最大缺点是会调用两次父构造函数。
1. 设置子类型实例的原型的时候：Child.prototype=new Parnet()
2. 创建子类型实例的时候：var child1=new Child('nameA','18')。回想new的模拟实现，其实在：Parent.call(this,name) 中又调用了一次Parent构造函数

如图：<br>
<img src='./images/寄生组合式继承缺点.jpg'>

所以在这个例子中，我们会发现Child.prototype 和 child1 中都有一个属性colors，值为：['red','blue','green']。所以我们想办法避免这一次重复调用

```
function Parent(name){
    this.name=name
    this.colors=['red','blue','green']
}
Parent.prototype.getName=function(){
    console.log(this.name)
}
function Child(name,age){
    Parent.call(this,name)
    this.age=age
}

//关键的三步
var F=function(){}
F.prototype=Parent.prototype
Child.prototype=new F()

var child1=new Child('nameA','19')
console.log(child1)
```

> 最后我们封装一下这个继承方法

```
function object(o){
    function f(){}
    F.prototype=o
    return new F()
}
function prototype(child,parent){
    var prototype=object(parent.prototype)
    prototype.constructor=child
    child.prototype=prototype
}
//使用时：
prototype(Child,Parent)
```
> 优点：只调用了一次Parent构造函数，因此避免了在Parent.prototype上创造不必要的、多余的属性。同时，原型链还能保持不变，可以正常使用instanceof 和 isPrototypeOf。

#### 参考
<a href='https://github.com/mqyqingfeng/Blog/issues/2'>JavaScript深入之从原型到原型链</a><br>
<a href='https://github.com/mqyqingfeng/Blog/issues/15'>JavaScript深入之创建对象的多种方式以及优缺点</a><br>
<a href='https://github.com/mqyqingfeng/Blog/issues/16'>JavaScript深入之继承的多种方式和优缺点</a><br>

### 