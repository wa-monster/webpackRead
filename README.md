## webpack
+ 安装
    `npm init`
    `npm i webapck webpack-cli -D`

+ 基本使用
    新建src目录 ，里面新建index.js 写入 js语法
    执行命令 `npx webpack`
    输出一个新文件夹dist，里面有打包好的main.js
    + 目前可以使用require()语法引入其他js文件到index.js里面,也就是支持cmd语法
    最终都会被打包
+ 自我配置输入输出文件和文件夹
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
+ 配置npm命令
    ```
     "scripts": {
        "build": "webpack --config webpack.config.js",
    },
    ```    
    这样可以npm run 运行 配置文件可以自己命名