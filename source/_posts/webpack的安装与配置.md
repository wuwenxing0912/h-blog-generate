---
title: webpack的安装与配置
date: 2018-07-02 01:05:28
tags: webpack
categories: Webpack
---

# 一、安装
## 1、首先进行初始化，得到合法的package.json；
    ````
    npm init -y
    ````
## 2、进入webpack官方网站，查看安装要点，这里选择的webpack版本是3.12.0；
    ````
    npm instasll -save-dev webpack@3
    ````
## 3、在当前目录下创建webpack.config.js文件，文件内容如下：
<!-- more -->
    ````
    const path = require('path');

    module.exports = {
        entry: './src/index.js',
        output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist')
        }
    };
    ````
## 4、保存文件后，在当前的src目录下创建一个index.js文件，然后执行如下命令：
    ````
    npx webpack
    ````
或者还可以执行 ./node-modules/.bin/webpack  命令执行后（未报错）查看当前的dist目录下是否有main.js,
如果有，则表示成功。

# 二、安装babel
## 1、使用babel把ES6或者更高版本的代码转换为兼容IE浏览器的JS代码
这里使用的版本是：webpack 3.12.0 、babel-loader 7.1.4 、 babel 6.26.3 ，所以安装命令是：
    ````
    npm install babel-loader babel-core babel-preset-env webpack
    ````
## 2、在webpack.config.js文件中加入如下代码：
    ````
    module: {
        rules: [{
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env']
                     }
                }
        }]
    }
    ````
## 3、在index.js文件中写入如下代码：
    ````
    let a = 1
    alert(a)
    ````
## 4、执行 ./node-modules/.bin/webpack  命令，查看dist目录下的main.js文件发现代码已经转换：
    ````
    var a = 1
    alert(a)
    ````

# 三、存在多个模块时的处理
## 1、在src目录下新建js目录并且一次创建module_1.js、module_2.js 以及app.js文件；
module_1.js代码如下：
    ````
    function f1() {
        let a = 1
        console.log(a)
    }

    export default f1 //当被引用时，默认就把f2传给他
    ````
module_2.js代码如下：
    ````
    function f2() {
        let b = 2
        console.log(b)
    }

    export default f2 
    ````
## 2、在app.js文件中引入module_1.js与module_2.js：
    ````
    import module1 from './module_1'
    import module2 from './module_2'

    module1()
    module2() 
    ````
## 3、修改webpack.config.js文件部分代码如下：
    ````
    entry: './src/js/app.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist/js')
    }
    ````
## 4、执行 ./node-modules/.bin/webpack  命令，查看dist目录下的main.js文件，
module_1.js与module_2.js中的代码已经被引用。

# 四、引入scss并转换为浏览器可以识别的css
## 1、在src目录下新建css目录并创建main.scss文件，且代码如下：
    ````
    body {
        background: gray;
        >div {
            padding: 20px;
        }
    }
    ````
## 2、在src目录下创建index.html文件并且复制到dist目录下，在index.html中引入main.js文件；
## 3、执行安装命令：
    ````
    npm install sass-loader node-sass webpack --save-dev
    npm install style-loader css-loader --save-dev
    ````
## 4、修改webpack.config.js部分代码如下：
    ````
    {
        test: /\.scss$/,
        use: [{
                loader: "style-loader" // creates style nodes from JS strings
                }, {
                    loader: "css-loader" // translates CSS into CommonJS
                }, {
                    loader: "sass-loader" // compiles Sass to CSS
                }]
    }
    ````
## 5、在app.js中引入main.scss文件：
    ````
    import '../css/main.scss'
    ````
## 6、执行 ./node-modules/.bin/webpack  命令，打开服务 http-server -c-1，
在浏览器中输入127.0.0.1:8080，打开控制台。在Elements下发现多出了一个style标签，内容为：
    ````
    body{
        background:grey;}
    body > div{
        padding:20px;}
    ````
scss文件成功传唤为浏览器可识别的css。

# 五、使用postcss-loader自动使css兼容
## 1、执行安装命令：
    ````
    npm i -D postcss-loader
    ````
## 2、在安装过程中可能会出错，这里遇到的问题是缺少了 postcss-cssnext，所以再安装这部分：
    ````
    npm i postcss-cssnext
    ````
## 3、在根目录下创建postcss.config.js文件，内容为：
    ````
    module.exports = {
        plugins: {
            'postcss-cssnext': {}
        }
    }
    ````
## 4、修改main.scss文件内容为：
    ````
    body{
        display:flex;
    }
    ````
## 5、修改webpack.config.js部分代码如下：
    ````
    {
        test: /\.scss$/,
        use: [{
                loader: "style-loader" // creates style nodes from JS strings
                }, {
                    loader: "css-loader", // translates CSS into CommonJS
                    options: { importLoaders: 1 }
                }, {
                    loader: 'postcss-loader'
                }, {
                    loader: "sass-loader" // compiles Sass to CSS
                }]
    }
    ````
## 6、执行 ./node-modules/.bin/webpack  命令，打开服务 http-server -c-1，
在浏览器中输入127.0.0.1:8080，打开控制台。style标签的内容为：
    ````
    body{
        display:-webkit-box;
        display:-ms-flexbox;
        display:flex;}
    ````
说明在打包过程中postcss-loader已经自动地将scss转换为兼容的css。