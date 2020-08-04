## webpack4+入门笔记2
---
### 提取css到单独的文件中
1. import相关的css文件到index.js中
2. 下载好需要的插件
```
cnpm i mini-css-exstact-plugin -D
```
3. 在webpack.config.js中进行配置
```
//引入插件
const MiniCssExtractPlugin=require('mini-css-extract-plugin');

//修改module
{
    test:/\.css$/,
    use:[
        // 'style-loader',
        //取代style-loader，将js中的css提取到单独的文件中
        MiniCssExtractPlugin.loader,
        'css-loader'
    ]
}

//添加plugin
new MiniCssExtractPlugin({
    filename:'css/bundle.css'
})
```
4. 终端中使用webpack进行打包

***
### css兼容性处理
1. 在终端中下载好需要的loader
```
cnpm i postcss-loader postcss-preset-env -D
```
post-preset-env 帮助post-loader找到package.json中的browserslist的相关配置

2. 配置loader
```
{
    test:/\.css$/,
    use:[
        MiniCssExtractPlugin.loader,
        'css-loader',
        {
            loader:'postcss-loader',
            options:{
                ident:'postcss',
                plugins:()=>[
                    require('postcss-preset-env')()
                ]
            }
        }

    ]
}
```
3. 修改package.json
```
"browserslist":{
    "development":[
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production":[
      ">0.2%",
      "not dead",
      "not op_mini all"
    ]
  }
```
> 分为生产环境和开发环境，默认看的是“production”，如果要更改为“development”，需要在webpack.config.js中修改nodejs的环境变量
```
process.env.NODE_ENV='development'
```
> 有看到另一种不用postcss-preset-env的方法，是在项目的根目录中添加postcss的配置文件postcss.config.js
```
module.exports = {
  "plugins": {
    "autoprefixer": {
      "browsers": [
        "ie >= 8",
        "ff >= 30",
        "chrome >= 34",
        "safari >= 8",
        "opera >= 23"
      ]
    }
  }
}
```
4. 终端中使用webpack进行打包

***
### css的压缩
1. 在终端中下载好需要的插件
```
cnpm i optimize-css-assets-webpack-plugin -D
```
2. 修改webpack.config.js
```
//引入插件
const OptimizeCssCssetsWebpackPlugin=require("optimize-css-assets-webpack-plugin");

//plugin中添加插件
new OptimizeCssCssetsWebpackPlugin()
```
3. 终端中使用webpack进行打包
   
***
### js语法检查--eslint和airbnb
1. 下载需要的loader
```
//eslint-loader 依赖于eslint
cnpm i eslint-loader eslint -D

//这里我们使用airbnb标准来处理,eslint不重复下载
cnpm i eslint-config-airbnb-base eslint-plugin-import -D
```
2. 配置loader
```
{
    test: /\.js$/,
    exclude: /node_modules/,
    loader: 'eslint-loader',
    options: {
        fix:true
    },
}
```  
3. 在package.json中添加配置,设置检查规则
```
"eslintConfig":{
    "extends":"airbnb-base"
  }
``` 
4. 终端中使用webpack进行打包

***
### js兼容性处理
> 基本兼容性处理
1. 下载好需要的loader
```
cnpm i babel-loader @babel/core @babel/preset-env -D
```
2. 配置module
```
{
    test:/\.js$/,
    exclude:/node_modules/,
    loader:'babel-loader',
    options:{
        presets:['@babel/preset-env']
    }
}
```
> 全部兼容性处理
1. 下载好需要的loader
```
cnpm i @babel/polyfill -D
```
2. 直接在index.js中引入
```
import '@babel/polyfill';
```
> 部分兼容性处理--按需下载
1. 下载需要的库
```
cnpm i core-js -D
```
2. 配置modules
```
{
  test:/\.js$/,
  exclude:/node_modules/,
  loader:'babel-loader',
  options:{
      presets:[
          [
              '@babel/preset-env',
              {
                  useBuiltIns:'usage',
                  corejs:{
                      version:3
                  },
                  targets:{
                      chrome:"60",
                      firefox:'60', 
                      ie:'9',
                      safari:'10',
                      edge:'17'
                  }
              }
          ]
      ]
  }
}
```
要注意，不能和@babel/polyfill同时使用


***
### 压缩html和js
1. 压缩js很简单，在生产模式下会自动进行压缩
```
mode:"production"
```
2. 压缩html，增加HtmlWebpackPlugin的配置
```
new HtmlWebpackPlugin ({
  template:'./src/index.html',
  minify:{
    collapseWhitespace:true,
    removeComments:true,
  }
})
```
这里只删除了空格和注释，还支持很多其他的需求，可以自己再去查找。
***
### 生产环境配置--总结之前的内容
```
const {resolve}=require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");

//复用loader
const commonCssLoader=[
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
        //还需要在package.json中定义browserslist
        loader:'post-loader',
        options:{
            ident:'postcss',
            plugin:()=>{
                require('postcss-preset-env')()
            }
        }
    }
];

module.exports={
    entry:'./src/js/index.js',
    output:{
        filename:'js/bundle.js',
        path:resolve(__dirname,'dist')
    },
    modules:{
        rules:[
            {
                test:/\.css$/,
                use:[...commonCssLoader]
            },
            {
                test:/\.less$/,
                use:[
                    ...commonCssLoader,
                    'less-loader'
                ]
            },
            {
                //在package.json中eslintConfig 使用airbnb
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'eslint-loader',
                //优先执行
                enforce:'pre',
                options:{
                    fix:true
                }
            },
            {
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'babel-loader',
                options:{
                    preset:[
                        [
                            '@babel/preset-env',
                            {
                                useBuiltIns:'usage',
                                corejs:{version:3},
                                targets:{
                                    chrome:"60",
                                    firefox:'60', 
                                    ie:'9',
                                    safari:'10',
                                    edge:'17'
                                }
                            }
                        ]
                    ]
                }
            },
            {
                test:/\.(jpg|png|gif)/,
                loader:'url-loader',
                options:{
                    limit:8*1024,
                    name:'[hash:10].[ext]',
                    outputPath:'imgs',
                    esModule:false
                }
            },
            {
                test:/\.html$/,
                loader:'html-loader'
            },
            {
                exclude:/\.(js|css|less|html|jpg|png|gif)/,
                loader:'file-loader',
                options:{
                    outputPath:'media' 
                }
            }
        ]
    },
    plugins:[
        new MiniCssExtractPlugin({
            filename:'css/bundle.css'
        }),
        new OptimizeCssAssetsPlugin(),
        new HtmlWebpackPlugin({
            template:'./src/index.html',
            minify:{
                collapseWhitespace:true,
                removeComments:true,
            }
        })
    ],
    mode:'production'
};
```
***
### 参考资料
<a href='https://segmentfault.com/a/1190000018534625'>从零开始的Webpack4教程</a>

<a href='https://www.bilibili.com/video/BV1e7411j7T5'>
尚硅谷2020最新版Webpack5实战教程(从入门到精通)</a>



