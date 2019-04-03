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
    
##css样式处理
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

