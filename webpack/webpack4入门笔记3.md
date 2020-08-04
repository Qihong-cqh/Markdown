## webpack4+入门笔记3
性能优化
---
### HMR
HMR:热模更新，可以更快的更新打包，提高打包的效率。<br>
配置也很简单，还是在webpack.config.js中更改：
```
//devSever中开启HMR
devServer:{
    contentBase:path.resolve(__dirname,'dist'),
    compress:true,
    port:3000,
    open:true,
    //
    hot:true
   }
```
但是开启之后发现，HMR只支持了样式文件，因为在style-loader中有实现，
但是对js文件和html文件没有效果，而且使得html不能热更新,可以这样解决
```
//在entry中引入html文件
entry:['./src/index.js','./src/index.html'],
```

对于js文件，需要在index.js中加入一段代码来支持
```
if(module.hot){
    //一旦module.hot为true，说明开启了HMR功能
    module.hot.accept(目标js文件,function(){
        //监听目标js文件的变化，一旦变化，其他模块不动，而执行后面的回调函数
        //回调函数中可以执行目标js文件的函数
    })
}
```

### source-map
source-map：提供源代码到构建后代码映射的技术，可以更加方便的进行调试，找出
错误。
```
//只需要在webpack.config.js中加入
devtool:'source-map'

一部分可选参数（供了解）：
    [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map

    source-map：外部
    inline-source-map:内联(只生成一个大的)，不生成文件，构建的速度更快
    hidden-source-map:外部
    eval-source-map:内联(每一个文件都生成对应的source-map，都在eval)
    nosources-source-map:外部
    cheap-source-map:外部
    cheap-module-source-map:外部

其中：
    inline-/eval-/cheap-或者source-map都可以提示到源代码的准确位置
    和错误信息，适合开发环境，推荐eval-source-map
    而hidden-/nosources-则只能提示错误信息，适合生产环境，推荐source-map
```

### oneOf
在选择loader时需要遍历全部的loader，oneof可以减少次数，只会匹配一个
```
//配置很简单，webpack.config.js将所有的rules用oneOf数组包起来
module:{
    rules:[
        {
            oneOf:[
                {
                    //loader规则
                },
                ...
            ]
        }
    ]
}
```
> 需要注意的是：oneof中不能有两个规则处理同一个文件，如果有，需要把其中一个放到外面

### 缓存