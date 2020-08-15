# mongoDB基本使用

## 概念解析
RDBMS:关联式数据库，例如；mysql、sql<br>
SQL:Not only SQL ，例如：MongoDB

|  SQL        | MongoDB     |  解释说明 |
| ----------- |-----------  |  ----    |
|  database   | database    |  数据库   | 
| table       | collection  |  表/集合 |
| row         | document    |  行/对象 |
| column      | field       |   列/域 |
| index       | index       |  索引 |
| primary key | primary key | 主键 |

***

## 基本操作
### 1. 数据库操作
查看：show databases<br>
创建：一般使用use隐式创建<br>
选择：use 数据库名<br>
删除：先用use选中，再使用db.dropdatabase()删除<br>

### 2. 集合操作
查看：show collections<br>
创建：db.createCollection('CollectionName') 或者 通过插入数据创建<br>
删除：db.CollectionName.drop()<br>

### 3. 增加语法
db.CollectionName.insert(json)<br>
例如:
```
use test
db.student.insert({name:"qiong",age:18})
```
注意：mongodb会自动创建一个_id的值，这个值是唯一的，可以在插入数据时进行覆盖，但是一般不推荐

> 一次性插入多条数据：
```
1.使用数组：
  db.student.insert([
   {name:'john',age:19},
   {name:'alliy',age:18},
   {name:'jack',age:19}
 ])
2.使用js语法，for循环：
  for(var i=0;i<10;i++){
      db.student.insert(name:'student'+i,age:i)
  }
```
### 4.查询语法
db.CollectionName.find(条件,[,查询的列])
```
条件：
    查询所有数据 {}或者不写
    查询age=18的数据 {age:18}
    查询age=18且sex为'男'的数据 {age:18，sex:'男'}
查询的列(可选)：
    不写  默认查询所有列
    {age:1} 只显示age字段
    {age:0} 除了age字段都显示
    而_id字段一直都会在
Example:
1.查询所有数据：
    db.student.find()
2.查询age为18的数据：
        db.student.find({age:18})
3.只显示age字段：
db.student.fing(,{age:1})
```

补充语法，使得条件更准确：
```
db.CollectionName.find({key:{运算符:value}})
```

|运算符|含义|
|---|---|
|&gt|大于|
|&gte|大于等于|
|&lt|小于|
|&lte|小于等于|
|&ne|不等于|
|&in|in|
|&nin|not in|
这里列出最常用的一部分，详细列表可以查询
<a href='https://mongodb.net.cn/manual/reference/operator/query/#query-selectors'>mongodb中文官网</a>

```
Example:
1.查询age大于18的数据：
    db.student.find({age:{&gt:18}})
2.查询a,b,c三人的数据：
    db.student.find({name:{&in:['a','b','c']}})
3.数据太多时，使用格式化：
    db.student.find({}).pretty()
```
### 5.修改语法
db.CollectionName.update(条件，新数据[，是否新增，是否修改多条]) 
```
是否新增：指如果条件匹配不到则新增数据（true：插入，false：否，默认）
是否修改多条：值将匹配成功的数据都修改（true：是，false：否，默认）
```
补充语法：
```
db.Collection.update(条件，{修改器：{key：value}})
```
|修改器|作用|
|---|---|
|$inc|递增|
|$rename|重命名列|
|$set|修改列值|
|$unset|删除列|
```
Example:
1.修改名字
    db.student.update({name:'jom'},{name:'jack'})
发现这条语句起到的是替换的作用，原来语句的其他信息都被覆盖了
要想正确修改需要使用修改器
    db.student.update({name:'jom',{$set:{name:'jack'}}})
2.增加age
    db.student.update({name:'jom',{$inc:2}})
3.修改多个field
    db.student.update({name:'jom',{
        $set:{name:'jack'},
        $rename:{age:'how_old'},
        $unset:{grade:true}
    }})
4.匹配不到则新增数据
    db.student.update({name:'amy'}，{name:'james'},true)
5.修改全部学生的age为18
    db.student.update({},{age:18},false,true)
```

### 6.删除语法
db.CollectionName.remove(条件，[,是否删除一条])
```
是否删除一条：true：是，false：否，默认

Example：
1.删除全部数据：
    db.student.remove({})
2.删除一条数据：
    db.student.remove({},true)
```
### 7.排序
db.CollectionName.find().sort({key:value})
```
key:要排序的列
value:1表示升序，-1表示降序
Example：
1.按成绩升序排序
    db.student.find().sort({grade:1})
2.按成绩降序排序
    db.student.find().sort({grade:-1})
```

### 8.limit方法和skip方法
limit方法：限制查询的数量<br>
db.CollectionName.find().limit(Number)<br>
skip方法：跳过多少条数据<br>
db.CollectionName.find().skip(Number)<br>
```
Example:
1.查找排名前十的学生
    db.student.find().sort({age:1}).limit(10)
2.跳过排名前十的学生
    db.student.find().sort({age:1}).skip(10)
```

### 9.聚合查询
普通查询：<br>
使用count()查询总数量<br>
如：db.student.find().count()<br>
<br>
聚合查询：<br>
db.CollectionName.aggregate([{管道:{表达式}}])

```
常用管道：
$group  将collection中的document分组，用于统计结果
$match  过滤数据，只输出符合条件的文档
$sort   聚合数据进一步过滤
$skip   跳过指定的文档数
$limit  限制集合数据返回文档数

常用表达式：
$sum    总和,其中$sum:1跟count()一样表示统计
$avg    平均
$min    最小值
$max    最大值

Example：
1.分别统计男女生数量：
    //_id:固定，用来标识分类的列
    //result:可重命名，用来展示分类后统计的数量
    db.student.aggregate([
        {
            $group:{
                _id:'$sex',      
                result:{$sum:1}        
            }
        }
    ])
2.分别统计男女生的年龄：
    db.student.aggregate([
        {
            $group:{
                _id:'$sex',
                result:{$sum:'$age'}
            }
        }
    ])
3.求学生总人数和平均成绩
    db.student.aggregate([
        {
            $group:{
                _id:null,
                num:{$sum:1},
                avg:{$avg:'$grade'}
            }
        }
    ])
4.分别查询男女生人数并按照人数升序排序：
    db.student.aggregate([
        {
            $group:{
                _id:'$sex',
                sum:{'$sum':1}
            }
        },
        {
            $sort:{sum:1}
        }
    ])
```

### 10.索引
索引是一种排序好的便于高效查询的数据结构<br>

```
创建：db.CollectionName.createIndex(key,options)
key:待创建的列，1位指定按升序创建，-1位降序
options:可选，有background后台创建，unique创建唯一索引等。

删除：
全部删除：db.CollectionName.dropIndexs()
指定删除：db.CollectionName.fropIndex(索引名)

查看：db.CollectionName.getIndexs()

Example：
1.创建索引：
    db.student.createIndex({age:1})
2.利用多个字段创建索引：
    db.student.createIndex({age:1,grade:-1}) 
3.给索引重命名：
    db.student.createIndex({age:1},{name:'newName'})
4.创建唯一索引：
    db.student.createIndex({age:1},{unique:'name'})
```
索引选择规则：<br>
* 为经常做条件、排序、分组的字段建立索引，更有效率
* 选择唯一性索引，即相同值较少的字段
* 选择较小的数据列作为索引，可以减少索引文件占用的空间

### 权限机制
因为我们可以直接在命令行窗口进入查看到所有的数据库，这是不安全的，权限机制使得每个用户使用账号密码进入数据库，更加安全可靠。<br>
暂时用不到，先搁置，有兴趣的可以查看<a href='https://mongodb.net.cn/manual/security/'>MongoDB 中文手册-安全部分</a>

### 备份和还原
备份：
```
mongodump -h -port -u -p -d -o
说明：
-h      host    服务器ip地址，一般默认本机
-port           端口号，一般默认27017
-u      user    账号
-p      pwd     密码
-d      database    数据库，如果不写则全局导出进行备份
-o      open    备份到指定目录下

```
还原：
```
mongorestore -h -port -u -p -d --drop 地址
说明：
-d：不写则全部还原
-drop：先删除数据库在导入
```
*** 

## Mongoose 入门
### 简介
Mongoose是在node.js异步环境下对mongodb进行便捷操作的一个对象模型工具，可以更加轻松的在node中实现对mongodb的管理，实现增删改查的操作。<br>
<a href='http://mongoosejs.net/docs/guide.html'>Mongoose 中文文档</a>

```
核心概念：
schema：
    用来约束MongoDB文档数据，规定哪些字段必须，哪些字段可选。可以理解为表结构的定义，不可以操作数据库
Model：
    schema生成的模型，用来管理Collection中的数据，一个model对应一个collection

安装：
    npm i mongoose
```

### 基本使用
在基本使用之前先了解一下mongoose使用的基本流程：<br>
1. 连接上数据库
2. 使用schema规定字段
3. schema生成model，使用model对应的方法可以实现对数据库的增删改查
<br>
> Schema Types:<br>
> 
> 定义一个Schema很简单，指定字段名和类型即可。<br>
> 内置的字段名有：<br>
> String<br>
> Number<br>
> Boolean/Bool<br>
> Array<br>
> Buffer<br>
> Date<br>
> ObjectId/Oid<br>
> Mixed



```
var mongoose = require('mongoose'),
    DB_URL = 'mongodb://localhost:27017/mongoosesample';

//连接
mongoose.connect(DB_URL);
//连接成功
mongoose.connection.on('connected', function () {    
    console.log('Mongoose connection open to ' + DB_URL);  
});    
//连接异常
mongoose.connection.on('error',function (err) {    
    console.log('Mongoose connection error: ' + err);  
});    
//连接断开
mongoose.connection.on('disconnected', function () {    
    console.log('Mongoose connection disconnected');  
});  

//使用Schema，映射到数据库中的一个collection，不具备操作数据库的能力
Schema = mongoose.Schema;

var UserSchema = new Schema({          
    username : { type: String },     //用户账号
    userpwd: {type: String},         //密码
    userage: {type: Number},         //年龄
    logindate : { type: Date}        //最近登录时间
});

//生成Model，可以实现对数据库的操作
var User=mongoose.model('User',UserSchema);

//插入，使用Model.prototype.save()
function insert() {
    var user = new User({
        username : 'Tracy McGrady',                 //用户账号
        userpwd: 'abcd',                            //密码
        userage: 37,                                //年龄
        logindate : new Date()                      //最近登录时间
    });
    user.save(function (err, res) {
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    });
}

insert();

//更新，使用Model.update()
function update(){
    var wherestr = {'username' : 'Tracy McGrady'};
    var updatestr = {'userpwd': 'zzzz'};
    User.update(wherestr, updatestr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}

update();

//删除，使用Model.remove()
function del(){
    var wherestr = {'username' : 'Tracy McGrady'};
    User.remove(wherestr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}

del();

//查找，使用Model.find()
function getByConditions(){
    var wherestr = {'username' : 'Tracy McGrady'};
    User.find(wherestr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}

getByConditions();
```
>  更多的一些查询条件：<br>
>  * $or    或
>  * $nor   或取反
>  * $gt    大于
>  * $gte   大于等于
>  * $lt    小于
>  * $lte   小于等于
> 
> Example：<br>
> User.find({userage: {$gte: 21, $lte: 65}}, callback);    //这表示查询年龄大于等21而且小于等于65岁<br>
> 还有很多，具体可以查询api

### 参考资料：
Mongoose部分：
<a href='https://www.cnblogs.com/zhongweiv/p/mongoose.html'>Nodejs学习笔记（十四）— Mongoose介绍和入门</a>
Mongodb教程资料：
<a href='https://www.bilibili.com/video/BV1xz4y1X7cE'>
MongoDB入门</a>