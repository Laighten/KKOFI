#### 一、Webpack的常见配置项有哪些？

1. Entry:

   入口(Entry)指示 webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图。

2. Output：

   输出(Output)指示 webpack 打包后的资源 bundles 输出到哪里去，以及如何命名。

3. Loader:

   Loader 让 webpack 能 够 去 处 理 那 些 非 JavaScript 文 件 (webpack 自 身 只 理 解JavaScript)

4. Plugins：

   插件(Plugins)可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等。

5. Mode：

   模式(Mode)指示 webpack 使用相应模式的配置。在DefinePlugin中的process.env.NODE_ENV中设置

   1. development 开发模式

      能让代码本地调试

   2. production 生产模式

      能让代码优化上线

```js
//webpack.config.js webpack的配置文件
/*
    作用：指示webpack干哪些活（当你运行webpack指令时，会加载里面的配置）
    所有构建工具都是基于node.js平台运行的~模块化采用commonjs
*/
const {resolve} = require('path') //用来拼接绝对路径
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry:'./src/index.js',
    output:{
        filename:'built.js'
        path:'resolve(__dirname,'build')'//__dirname 代表当前文件目录的绝对路径
    },
    //loader的配置
    module:{
        rules:[
            {
                //匹配哪些文件
                test:/\.css$/,
                //使用哪些loader进行处理
                use:[
                    //创建style标签，将js中的样式资源插入进行，添加到head中生效
                    'style-loader',
                    //将css文件变成commonjs模块加载js中，里面内容是样式字符串
                    'css-loader'
                ]
            },
            {
                test:/\.less$/,
                use:[
                     'style-loader',
                     'css-loader',
                     'less-loader'
                ]
            },
            {
                //处理不了html中的img
                test:/\.(jpg|png|gif)$/,
                loader:'url-loader',
                option:{
                    //图片大小为8kb,就会被base64处理
                    //优点：减少请求数量（减轻服务器压力）
                    //缺点：图片体积会更大（文件请求更慢）
                    limit:8*1024,
                    //问题：因为url-loader默认使用es6模块解析，而html-loader引入图片是
                    //commonjs,解析时会出问题
                    esModule:false
                }
            },
            {
                test:/\.html$/,
                //处理HTML文件的img图片（负责引入img,从而能被url-loader进行处理）
                loader:'html-loader',
            }
        ]
    },
    plugins:[
        //默认会创建一个空的HTML,引入打包输出的所有资源（JS/CSS）
        new HtmlWebpackPlugin({
            //复制‘./src/index.html’文件，并自动引入打包输出的所有资源（JS/CSS）
            template:'./src/index.html'
        })
    ],
    mode:'development',
    devServer:{ //开发模式时使用，打包上线就不需要了
      port:3000,
      contentBase:path.join(__dirname,'dist')  
    },
}
```

### 二、babel-loader、bable-core和bable-preset-env有什么区别？

- @babel/core是babel的核心库，所有的核心Api都在这个库里，这些Api供babel-loader调用。

- @babel/preset-env，是一个预设的插件集合，包含了一组相关的插件，Bable中是通过各种插件来指导如何进行代码转换。只能转换基本的语法如箭头函数转为普通函数、const转换为var等。

- @babel/polyfill，可以做全部js兼容性处理，且不用在config.js中配置，只需要下载之后在相应的页面用import引入即可。但存在的问题是引入所有兼容性代码会导致打包后体积过大。

- @babel-loader，作为一个中间桥梁，通过调用babel/core中的api来告诉webpack要如何处理js。在webpack中使用@babel/core、@babel/preset-env 、@babel/polyfill时都需要先在loader中配置babel-loader才行。

### 三、loader与plugin的区别

​	**loader：**由于webpack 本身只能打包**commonjs规范的js文件**，所以，针对css，图片等格式的文件没法打包，就需要引入第三方的模块进行打包。loader只专注于转化文件（transform）这一个领域，完成压缩，打包，语言翻译。

​	**plugin：**plugin也是为了扩展webpack的功能，但是 plugin 是作用于webpack本身上的。而且plugin不仅只局限在打包，资源的加载上，它的功能要更加丰富。从打包优化和压缩，到重新定义环境变量，功能强大到可以用来处理各种各样的任务。