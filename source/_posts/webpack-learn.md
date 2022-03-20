---
title: webpack 初步学习
date: 2017-06-23
tags: 
- webpack
- webpack学习
- webpack文档
---
## 什么是webpack

个人的简单理解：早期对于grunt，gulp有一点的了解，对于这两者来说，更合适的是称之为工具，通过开发者的角度创建各类 Task，使用插件来构建一系列的自动化构建流程（值得注意的是这边指的是人工流水线），将其作为构建工具的角色来优化前端开发流程。而对于webpack来说，可以简单理解为自动化，也可以说是智能的模块化解决方案（意味着全自动化流水线），处理模块依赖分析的打包工具，自动计算分析所有的依赖关系以及加载顺序，而不是让开发者分析以及编写配置打包的依赖。

**在此盗图两张**

![image](https://llp0574.github.io/img/1.png)

![image](https://llp0574.github.io/img/2.png)

受益于 Webpack，使用JS来取代开发者的脑力劳动，来进行依赖与加载的管理。

**上图镇楼！ 万物皆模块**

![image](http://image.freefe.cc/20170518152116.png)

## webpack 特点
- 智能的分块打包（bundle）,几乎可以处理所有的第三方库，无论是CommonJS，AMD还是基础的JS文件。
- webpack本身只支持原生的JS模块，但是其loader转换器可以使其支持各类静态资源的依赖打包
    - JS (jsx或es6等 -> js)
    - CSS (less或scss等 -> css)
    - 各类图片
    - 各类模板
    - ...

## webpack CLI 和 webpack-dev-server

**webpack CLI：** 默认的交互方式，随webpack本身安装到本地，可以从命令行获取参数或者从配置文件获取参数传入webpack来进行打包。

```
$ npm install webpack
$ webpack  // 使用 webpack.config.js 配置文件构建打包

// 添加package.json的scripts配置项
"scripts": {
    "build": "webpack --config webpack.config.prod.js -p"
}

$ npm run build // 构建打包
```

**webpack-dev-server：** Nodejs的服务器，需要使用npm进行安装。
```
$ npm install webpack-dev-server
// 终端输入启动本地服务
// inline 选项会为入口页面添加热加载功能
// hot 选项则开启热替换（Hot Module Reloading）
$ webpack-dev-server --inline --hot  

// 添加package.json的scripts配置项
"scripts": {
    "start": "webpack-dev-server --inline --hot"
}

$ npm run start // 构建打包
```

## 最简单的配置实例

```
var webpack = require('webpack');

module.exports = {
    // 程序入口，打包依赖计算的起点
    entry: './index.js',
    // 输出的配置对象
    output: {
        path: __dirname,
        filrname: 'bundle.js'
    },
    // 模块配置
    module: {
        // 依赖规则 配置loader以及解析器等选项
        rules: [
            {
                test: /\.css$/,
                loader: 'style-loader!css-loader'
            }
        ]
    }
}

```

## 配置介绍

### 入口与输出

**`entry`**

> 作为程序入口，标识应用的起点，打包依赖的起点。可为字符串，数组或对象。

```
// 单入口
entry: './index.js',

// 数组，参数配置 webpack 提供的一个静态资源服务器，webpack-dev-server,监控项目中每一个文件的变化，实时的进行构建
entry: [
    'webpack/hot/only-dev-server',
    './main.js'
]

// 多入口bundle
entry: {
    home: './home.js',
    list: './list.js',
    ...
}
```

**`context`**

> 推荐在配置中传递一个值,使配置独立于当前的执行路径

```
context: path.resolve(__dirname, 'app')
```

**`output`**

> 配置wenpack的如何输出以及输出位置，形式等，定义构建后的输出。
> path 仅仅告诉Webpack结果存储在哪里，然而 publicPath 项则被许多Webpack的插件用于在生产模式下更新内嵌到css、html文件里的url值。

```
output: {
    // 配置输出bundle的文件名
    // 可以使用多个entry point，
    // 还可使用标识符
    // [hash],[chunkhash],[name],[id]等
    filename: '[hash:8].bundle.js',
    
    // 产出对应的绝对路径
    path: path.resolve(__dirname, 'dist')
    
    // publicPath: "https://cdn.example.com/assets/", // CDN（总是 HTTPS 协议）
    // publicPath: "//cdn.example.com/assets/", // CDN (协议相同)
    // publicPath: "/assets/", // 相对于服务(server-relative)
    // publicPath: "assets/", // 相对于 HTML 页面
    // publicPath: "../assets/", // 相对于 HTML 页面
    // publicPath: "", // 相对于 HTML 页面（目录相同）
}
```

**`module`**

> 配置处理项目中不同类型的模块，配置各个类型的加载器，实现真正的万物皆模块形式。
> noParse：忽略所匹配文件的依赖处理 确定某模块没有依赖 忽略的文件中不应该含有 import，require， define
> rules：一个匹配loaders所处理的文件的拓展名的正则表达式（必须），规则将模块应用对应的loader，或者修改parser
> Rule：每个规则由三部分组成，条件，结果和嵌套规则。

**Rule 条件** 条件的两种输入值
- resource：请求文件的绝对路径，属性 test, include, exclude 和 resource 
- issuer: 被请求资源(requested the resource)的模块文件的绝对路径，属性 issuer

例如: 从 app.js 导入 "./style.css"，resource 是 /path/style.css. issuer 是 /path/app.js

**Rule 结果** 规则只有在条件匹配时使用
- 应用的loader：应用在 resource 上的loader，属性：loader，options，use，兼容query，loaders
- Parser：为模块创建解析器的选项对象，熟悉：parser

**嵌套的Rule**


```
module: {
    // 
    noParse: /jquery|lodash/,
    
    // 加载器配置
    use: [
        {   
            loader: 'style-loader'
        },
        {
            loader: 'css-loader',
            options: {
                importLoaders: 1
            }
        }
    ]
}
```

对于
```
{   
    test: /\.css$/,
    loader: 'style!css'
}
```
多个loader可以用在同一个文件上并且被链式调用，链式调用时从右到左执行且loader之间用“!”来分割。

- Webpack在模块内部搜索在css的依赖项，即Webpack检查js文件是否有“require('myCssFile.css')”的引用，如果它发现有css的依赖，Webpack将css文件交给“css-loader”去处理
- css-loader加载所有的css文件以及css自身的依赖（如@import xxx）到JSON对象里，Webpack然后将处理结果传给“style-loader”
- style-loader接受JSON值然后添加一个style标签并将其内嵌到html文件里


**`plugins`**

> 用于自定义webpack的多种构建方式。

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var DashboardPlugin = require('webpack-dashboard/plugin');

// 配置添加插件
plugins: [
    // 构建时优化插件
    new webpack.optimize.CommonsChunkPlugin({
        name: 'vendor',
        filename: 'vendor-[hash].min.js',
    }),
    new webpack.optimize.UglifyJsPlugin({
        compress: {
            warnings: false,
            drop_console: false,
        }
    }),
    new ExtractTextPlugin({
        filename: 'build.min.css',
        allChunks: true,
    }),
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
    // 编译时(compile time)插件
    new webpack.DefinePlugin({
        'process.env.NODE_ENV': '"production"',
    }),
    // webpack-dev-server 强化插件
    new DashboardPlugin(),
    new webpack.HotModuleReplacementPlugin(),
]
```

**`devServer`**

> 启动开发服务的配置, webpack-dev-server传入参数的配置形式。
> 等效于在使用CLI形式启动直接传参

## 经典相关阅读

- [webpack中文文档](https://doc.webpack-china.org/configuration/)
- [Webpack - 令人困惑的地方](https://github.com/chemdemo/chemdemo.github.io/issues/13)
- [基于webpack搭建前端工程解决方案探索](https://github.com/chemdemo/chemdemo.github.io/issues/10)
- [webpack 使用总结](http://www.ferecord.com/webpack-summary.html)


## 文档的综合实例

官网一个详细的实例

```
const path = require('path');

module.exports = {
    
    entry: "./app/entry", // string | object | array
    entry: ["./app/entry1", "./app/entry2"],
    entry: {
        a: "./app/entry-a",
        b: ["./app/entry-b1", "./app/entry-b2"]
    },
    // 这里应用程序开始执行
    // webpack 开始打包

    output: {
        // webpack 如何输出结果的相关选项

        path: path.resolve(__dirname, "dist"), // string
        // 所有输出文件的目标路径
        // 必须是绝对路径（使用 Node.js 的 path 模块）

        filename: "bundle.js", // string
        filename: "[name].js", // 用于多个入口点(entry point)（出口点？）
        filename: "[chunkhash].js", // 用于长效缓存
        // 「入口分块(entry chunk)」的文件名模板（出口分块？）

        publicPath: "/assets/", // string
        publicPath: "",
        publicPath: "https://cdn.example.com/",
        // 输出解析文件的目录，url 相对于 HTML 页面

        library: "MyLibrary", // string,
        // 导出库(exported library)的名称

        libraryTarget: "umd", // 通用模块定义
        libraryTarget: "umd2", // 通用模块定义
        libraryTarget: "commonjs2", // exported with module.exports
        libraryTarget: "commonjs-module", // 使用 module.exports 导出
        libraryTarget: "commonjs", // 作为 exports 的属性导出
        libraryTarget: "amd", // 使用 AMD 定义方法来定义
        libraryTarget: "this", // 在 this 上设置属性
        libraryTarget: "var", // 变量定义于根作用域下
        libraryTarget: "assign", // 盲分配(blind assignment)
        libraryTarget: "window", // 在 window 对象上设置属性
        libraryTarget: "global", // property set to global object
        libraryTarget: "jsonp", // jsonp wrapper
        // 导出库(exported library)的类型

        /* 高级输出配置（点击显示） */
        pathinfo: true, // boolean
        // 在生成代码时，引入相关的模块、导出、请求等有帮助的路径信息。
    
        chunkFilename: "[id].js",
        chunkFilename: "[chunkhash].js", // 长效缓存(/guides/caching)
        // 「附加分块(additional chunk)」的文件名模板
    
        jsonpFunction: "myWebpackJsonp", // string
        // 用于加载分块的 JSONP 函数名
    
        sourceMapFilename: "[file].map", // string
        sourceMapFilename: "sourcemaps/[file].map", // string
        // 「source map 位置」的文件名模板
    
        devtoolModuleFilenameTemplate: "webpack:///[resource-path]", // string
        // 「devtool 中模块」的文件名模板
    
        devtoolFallbackModuleFilenameTemplate: "webpack:///[resource-path]?[hash]", // string
        // 「devtool 中模块」的文件名模板（用于冲突）
    
        umdNamedDefine: true, // boolean
        // 在 UMD 库中使用命名的 AMD 模块
    
        crossOriginLoading: "use-credentials", // 枚举
        crossOriginLoading: "anonymous",
        crossOriginLoading: false,
        // 指定运行时如何发出跨域请求问题
    
        /* 专家级输出配置（自行承担风险） */
    
        devtoolLineToLine: {
          test: /\.jsx$/
        },
        // 为这些模块使用 1:1 映射 SourceMaps（快速）
    
        hotUpdateMainFilename: "[hash].hot-update.json", // string
        // 「HMR 清单」的文件名模板
    
        hotUpdateChunkFilename: "[id].[hash].hot-update.js", // string
        // 「HMR 分块」的文件名模板
    
        sourcePrefix: "\t", // string
        // 包内前置式模块资源具有更好可读性
    },

    module: {
        // 关于模块配置

        rules: [
            // 模块规则（配置 loader、解析器等选项）

        {
            test: /\.jsx?$/,
            include: [
                path.resolve(__dirname, "app")
            ],
            exclude: [
                path.resolve(__dirname, "app/demo-files")
            ],
            // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
            // test 和 include 具有相同的作用，都是必须匹配选项
            // exclude 是必不匹配选项（优先于 test 和 include）
            // 最佳实践：
            // - 只在 test 和 文件名匹配 中使用正则表达式
            // - 在 include 和 exclude 中使用绝对路径数组
            // - 尽量避免 exclude，更倾向于使用 include
    
            issuer: { test, include, exclude },
            // issuer 条件（导入源）

            enforce: "pre",
            enforce: "post",
            // 标识应用这些规则，即使规则覆盖（高级选项）

            loader: "babel-loader",
            // 应该应用的 loader，它相对上下文解析
            // 为了更清晰，`-loader` 后缀在 webpack 2 中不再是可选的
            // 查看 webpack 1 升级指南。
    
            options: {
              presets: ["es2015"]
            },
            // loader 的可选项
        },

        {
            test: "\.html$"

            use: [
                // 应用多个 loader 和选项
                "htmllint-loader",
                {
                    loader: "html-loader",
                    options: {
                      /* ... */
                    }
                }
            ]
        },

        { oneOf: [ /* rules */ ] },
        // 只使用这些嵌套规则之一
        
        { rules: [ /* rules */ ] },
        // 使用所有这些嵌套规则（合并可用条件）
        
        { resource: { and: [ /* 条件 */ ] } },
        // 仅当所有条件都匹配时才匹配
        
        { resource: { or: [ /* 条件 */ ] } },
        { resource: [ /* 条件 */ ] },
        // 任意条件匹配时匹配（默认为数组）
        
        { resource: { not: /* 条件 */ } }
        // 条件不匹配时匹配
        ],

        /* 高级模块配置（点击展示） */
        noParse: [
          /special-library\.js$/
        ],
        // 不解析这里的模块
    
        unknownContextRequest: ".",
        unknownContextRecursive: true,
        unknownContextRegExp: /^\.\/.*$/,
        unknownContextCritical: true,
        exprContextRequest: ".",
        exprContextRegExp: /^\.\/.*$/,
        exprContextRecursive: true,
        exprContextCritical: true,
        wrappedContextRegExp: /.*/,
        wrappedContextRecursive: true,
        wrappedContextCritical: false,
        // 配置
    },

    resolve: {
        // 解析模块请求的选项
        // （不适用于对 loader 解析）
    
        modules: [
            "node_modules",
            path.resolve(__dirname, "app")
        ],
        // 用于查找模块的目录
    
        extensions: [".js", ".json", ".jsx", ".css"],
        // 使用的扩展名

        alias: {
            // 模块别名列表
            
            "module": "new-module",
            // 起别名："module" -> "new-module" 和 "module/path/file" -> "new-module/path/file"
            
            "only-module$": "new-module",
            // 起别名 "only-module" -> "new-module"，但不匹配 "module/path/file" -> "new-module/path/file"
            
            "module": path.resolve(__dirname, "app/third/module.js"),
            // 起别名 "module" -> "./app/third/module.js" 和 "module/file" 会导致错误
            // 模块别名相对于当前上下文导入
        },
        /* 可供选择的别名语法（点击展示） */
        alias: [
            {
                name: "module",
                // 旧的请求
                
                alias: "new-module",
                // 新的请求
                
                onlyModule: true
                // 如果为 true，只有 "module" 是别名
                // 如果为 false，"module/inner/path" 也是别名
            }
        ],
        /* 高级解析选项（点击展示） */
        symlinks: true,
        // 遵循符号链接(symlinks)到新位置
        
        descriptionFiles: ["package.json"],
        // 从 package 描述中读取的文件
        
        mainFields: ["main"],
        // 从描述文件中读取的属性
        // 当请求文件夹时
        
        aliasFields: ["browser"],
        // 从描述文件中读取的属性
        // 以对此 package 的请求起别名
        
        enforceExtension: false,
        // 如果为 true，请求必不包括扩展名
        // 如果为 false，请求可以包括扩展名
        
        moduleExtensions: ["-module"],
        enforceModuleExtension: false,
        // 类似 extensions/enforceExtension，但是用模块名替换文件
        
        unsafeCache: true,
        unsafeCache: {},
        // 为解析的请求启用缓存
        // 这是不安全，因为文件夹结构可能会改动
        // 但是性能改善是很大的
        
        cachePredicate: (path, request) => true,
        // predicate function which selects requests for caching
        
        plugins: [
        // ...
        ]
    },

    performance: {
        hints: "warning", // 枚举
        hints: "error", // 性能提示中抛出错误
        hints: false, // 关闭性能提示
        maxAssetSize: 200000, // 整数类型（以字节为单位）
        maxEntrypointSize: 400000, // 整数类型（以字节为单位）
        assetFilter: function(assetFilename) {
            // 提供资源文件名的断言函数
            return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
        }
    },

    devtool: "source-map", // enum
    devtool: "inline-source-map", // 嵌入到源文件中
    devtool: "eval-source-map", // 将 SourceMap 嵌入到每个模块中
    devtool: "hidden-source-map", // SourceMap 不在源文件中引用
    devtool: "cheap-source-map", // 没有模块映射(module mappings)的 SourceMap 低级变体(cheap-variant)
    devtool: "cheap-module-source-map", // 有模块映射(module mappings)的 SourceMap 低级变体
    devtool: "eval", // 没有模块映射，而是命名模块。以牺牲细节达到最快。
    // 通过在浏览器调试工具(browser devtools)中添加元信息(meta info)增强调试
    // 牺牲了构建速度的 `source-map' 是最详细的。

    context: __dirname, // string（绝对路径！）
    // webpack 的主目录
    // entry 和 module.rules.loader 选项
    // 相对于此目录解析

    target: "web", // 枚举
    target: "webworker", // WebWorker
    target: "node", // node.js 通过 require
    target: "async-node", // Node.js 通过 fs and vm
    target: "node-webkit", // nw.js
    target: "electron-main", // electron，主进程(main process)
    target: "electron-renderer", // electron，渲染进程(renderer process)
    target: (compiler) => { /* ... */ }, // 自定义
    // 包(bundle)应该运行的环境
    // 更改 块加载行为(chunk loading behavior) 和 可用模块(available module)

    externals: ["react", /^@angular\//],
    externals: "react", // string（精确匹配）
    externals: /^[a-z\-]+($|\/)/, // 正则
    externals: { // 对象
        angular: "this angular", // this["angular"]
        react: { // UMD
            commonjs: "react",
            commonjs2: "react",
            amd: "react",
            root: "React"
        }
    },
    externals: (request) => { /* ... */ return "commonjs " + request }
    // 不要遵循/打包这些模块，而是在运行时从环境中请求他们

    stats: "errors-only",
    stats: { //object
        assets: true,
        colors: true,
        errors: true,
        errorDetails: true,
        hash: true,
        // ...
    },
    // 精确控制要显示的 bundle 信息

    devServer: {
        proxy: { // proxy URLs to backend development server
          '/api': 'http://localhost:3000'
        },
        contentBase: path.join(__dirname, 'public'), // boolean | string | array, static file location
        compress: true, // enable gzip compression
        historyApiFallback: true, // true for index.html upon 404, object for multiple paths
        hot: true, // hot module replacement. Depends on HotModuleReplacementPlugin
        https: false, // true for self-signed, object for cert authority
        noInfo: true, // only errors & warns on hot reload
        // ...
    },

    plugins: [
        // ...
    ],
    // 附加插件列表
    
    
    /* 高级配置（点击展示） */
    
    resolveLoader: { /* 等同于 resolve */ }
    // 独立解析选项的 loader
    
    profile: true, // boolean
    // 捕获时机信息
    
    bail: true, //boolean
    // 在第一个错误出错时抛出，而不是无视错误。
    
    cache: false, // boolean
    // 禁用/启用缓存
    
    watch: true, // boolean
    // 启用观察
    
    watchOptions: {
        aggregateTimeout: 1000, // in ms
        // 将多个更改聚合到单个重构建(rebuild)
        
        poll: true,
        poll: 500, // 间隔单位 ms
        // 启用轮询观察模式
        // 必须用在不通知更改的文件系统中
        // 即 nfs shares（译者注：Network FileSystem，最大的功能就是可以透過網路，讓不同的機器、不同的作業系統、可以彼此分享個別的檔案 ( share file )）
    },
    
    node: {
    /* TODO */
    },
    
    recordsPath: path.resolve(__dirname, "build/records.json"),
    recordsInputPath: path.resolve(__dirname, "build/records.json"),
    recordsOutputPath: path.resolve(__dirname, "build/records.json"),
    // TODO

}
```