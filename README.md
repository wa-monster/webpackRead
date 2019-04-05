# webpack
## 安装
    `npm init`
    `npm i webapck webpack-cli -D`

##基本使用
    新建src目录 ，里面新建index.js 写入 js语法
    执行命令 `npx webpack`
    输出一个新文件夹dist，里面有打包好的main.js
    目前可以使用require()语法引入其他js文件到index.js里面,也就是支持cmd语法
    最终都会被打包
## 自我配置输入输出文件和文件夹
    在package.json同级下，新建webpack.config.js
    在webpack.config.js里面可以进行配置
    输入
    ```
    let path = require('path')//解析路径，获取绝对路径
    module.exports={//cmd语法
        mode:'developer'//webpack有两种模式，production,development
        entry:'./src/index.js',
        output:{
            filename:"aa.js",
            path:path.resolve(__dirname,'dist')//必须为绝对路径
            // __dirname表示当前目录 所以(__dirname,'dist')表示当前目录下的dist目录
        }
    }
    ```
## 配置npm命令
    "scripts": {
        "build": "webpack --config webpack.config.js",
    }, 
    这样可以npm run 运行 配置文件可以自己命名
## 配置热更新
    npm i webpack-dev-server -D

    运行命令 npx webpack-dev-server 可以热更新

    配置npm命令
    "scripts": {
        "dev": "webpack-dev-server"
    },
    可以`npm run dev`

    修改端口号
    module.exports={//cmd语法

        devServer:{//开发服务器配置
            port:3000,//端口号
            progress:true,//显示打包进度
            contentBase:'./dist',//dist目录下的作为服务器加载的东西
        },

        mode:'developer'//webpack有两种模式，production,development
        entry:'./src/index.js',
        output:{
            filename:"aa.js",
            path:path.resolve(__dirname,'dist')//必须为绝对路径
            // __dirname表示当前目录 所以(__dirname,'dist')表示当前目录下的dist目录
        }
    }
## html打包
    到目前为止，都是打包js，如果把dist。目录下的index.js删除，那么热更新服务找不到html，所以我们需要能够把src的html也打包
    安装
    npm i html-webpack-plugin

    let HtmlWebpackPlugin = require('html-webpack-plugin)
    module.exports = {
        plugins:[//放所有的webpack插件
			new HtmlWebpackPlugin({
				template:'./src/index.html',//模板
				filename:'index.html',
				minify:{
					removeAttributeQuotes:true,//删除部分双引号
					collapseWhitespace:true,//压缩html代码
				},
				hash:true,//给js文件加上哈希戳
			})
        ]
    }

    js要有哈希 需要给
        output下的filename作如下修改
        output:{
            filename:'aa.[hash].js'
        }
    如果希望哈希值短一点，可以 [hash:8] => 只显示前8位
    
## css样式处理

    安装
    npm i css-loader style-loader -D
    //css-loader 解析@import规则 =》达成多个css一起调用
    //style-loader把css插入到head标签中
    //loader的特点，希望功能单一

    webpack.config.js中的配置
    module.exports={
        module：{
            rules:[
                //用法：
                //字符串只用一个loader
                //多个loader需要[]
                //loader的顺序，默认从右到左，从下到上
                //loader还可以写成对象形式 => 好处可以传参
                //例如 
                //{
                //  loader:'style-loader',
                //  options:{
                //      insertAt:'top'//实现把插入到head标签时放在所有css的最上面
                //  }       
                //}
                {test:/\.css$/,use:[
                    'css-loader'，
                    ‘style-loader
                ]}
            ]
        }
    }

    使用预处理语言less
    npm i less less-loader
    rules:[
        {test:/\.less$/,use:[
                'style-loader',
                'css-loader',
                'less-loader',//把less => css
            ]
        }
    ]

    抽离css样式
    安装 插件
    npm i mini-css-extract-plugin -D

    因为是插件，所以引入
    let MiniCssExtractPlugin = require('mini-css-extract-plugin')
    插件放在plugins里面
    plugins:[
        new MiniCssExtractPlugin({
            filename:'css/main.css'
        })
    ]
    
    如此我们可以把css全部提出来，作为一个单独的css文件存在

    实现css添加前缀（实现一些css的兼容）

    安装 postcss-loader 和 autoprefixer
     npm i postcss-loader autoprefixer 

    给css加上这个loader
    module:{
        rules:[
            {
                test:/\.css$/,use:[
                    'css-loader',
                    'postcss-loader',//需要放在css-loader之前先解析
                ]
            },
            {//如果有用到sass,less等，也需要在里面添加postcss-loader
                test:/\.less$/,use:[
                    'css-loader',
                    'postcss-loader',//需要放在css-loader之前先解析
                    'less-loader',
                ]
            }
        ]
    }
    然后还需要添加新的配置文件 => postcss.config.js
    放在和webpack.config.js同目录下
    写入
    module.exports={
	    plugins:[require('autoprefixer')]
    }

    如此可以加入前缀 

    压缩css
    在使用MiniCssExtractPlugin 的时候，生产环节不会压缩css. MiniCssExtractPlugin提供了压缩css的办法

    安装
    optimize-css-assets-webpack-plugin （ css压缩）
    uglifyjs-webpack-plugin （再用css压缩那个插件的时候，js无法在自主压缩了，所以需要新的js压缩）

    npm i optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin -D

    引入插件
    let OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')

    let UglifyJsPlugin = require("uglifyjs-webpack-plugin");

    plugins里面引入

        new OptimizeCSSAssetsPlugin({}),//css压缩
	    new UglifyJsPlugin({//js压缩
		        cache: true,
		        parallel: true,
		        sourceMap: true 
		    }),
    实现css和js压缩        

## 高级js语法转化
    安装 babel-loader @babel/core @babel/preset-env

    npm i babel-loader @babel/core @babel/preset-env -D

    webpack.config.js 的module下添加

        {
            test:/\.js$/,
            use:{
                loader:'babel-loader',
                options:{ //用babel-loader 把es6转换为es5
                    presets:[
                    '@babel/preset-env'
                    ],
                    
                }
            }
        }


    上面只能转换es6如果要转化es7 的class 类 

    需要下载 @babel/plugin-proposal-class-properties
    npm i @babel/plugin-proposal-class-properties -D

    在 use对象里面的options做额外配置

        options:{ //用babel-loader 把es6转换为es5
            presets:[
            '@babel/preset-env'
            ],
            plugins:[
                ["@babel/plugin-proposal-class-properties", { "loose" : true }],
            ]   
        }

    同理，如果要用装饰器
    则
    npm i @babel/plugin-proposal-decorators -D
    代码添加
    plugins:[
        ["@babel/plugin-proposal-decorators", { "legacy": true }],
        ["@babel/plugin-proposal-class-properties", { "loose" : true }],
    ]   

 



