#Vue全家桶之Vue-cli学习笔记
##一、安装vue-cli
首先检查是否下载node了,然后用npm -v检查是否安装  
npm没有问题，接下来我们可以用npm 命令安装vue-cli了，在命令行输入下面的命令：  
		  
     npm n install vue-cli -g

-g :代表全局安装。

我们用vue init命令来初始化项目，具体看一下这条命令的使用方法。


    vue init <template-name> <project-name>

如果用webpack打包，一般直接在目录下打vue init webpack就可以了。  
npm install  安装我们的项目依赖包，也就是安装package.json里的包，如果你网速不好，你也可以使用cnpm来安装。  
npm run dev 开发模式下运行我们的程序。给我们自动构建了开发用的服务器环境和在浏览器中打开，并实时监视我们的代码更改，即时呈现给我们。  

##第二节Vue-cli项目结构
vue-cli脚手架工具就是为我们搭建了开发所需要的环境，为我们省去了很多精力。有必要对这个环境进行熟悉，我们就从项目的结构讲起。  


		.
		|-- build                            // 项目构建(webpack)相关代码
		|   |-- build.js                     // 生产环境构建代码
		|   |-- check-version.js             // 检查node、npm等版本
		|   |-- dev-client.js                // 热重载相关
		|   |-- dev-server.js                // 构建本地服务器
		|   |-- utils.js                     // 构建工具相关
		|   |-- webpack.base.conf.js         // webpack基础配置
		|   |-- webpack.dev.conf.js          // webpack开发环境配置
		|   |-- webpack.prod.conf.js         // webpack生产环境配置
		|-- config                           // 项目开发环境配置
		|   |-- dev.env.js                   // 开发环境变量
		|   |-- index.js                     // 项目一些配置变量
		|   |-- prod.env.js                  // 生产环境变量
		|   |-- test.env.js                  // 测试环境变量
		|-- src                              // 源码目录
		|   |-- components                     // vue公共组件
		|   |-- store                          // vuex的状态管理
		|   |-- App.vue                        // 页面入口文件
		|   |-- main.js                        // 程序入口文件，加载各种公共组件
		|-- static                           // 静态文件，比如一些图片，json数据等
		|   |-- data                           // 群聊分析得到的数据用于数据可视化
		|-- .babelrc                         // ES6语法编译配置
		|-- .editorconfig                    // 定义代码格式
		|-- .gitignore                       // git上传需要忽略的文件格式
		|-- README.md                        // 项目说明
		|-- favicon.ico 
		|-- index.html                       // 入口页面
		|-- package.json                     // 项目基本信息
		.

dependencies字段和devDependencies字段

dependencies字段指项目运行时所依赖的模块；  
devDependencies字段指定了项目开发时所依赖的模块；  

webpack.base.confg.js   webpack的基础配置文件

...
...

	module.export = {
	    // 编译入口文件
	    entry: {},
	    // 编译输出路径
	    output: {},
	    // 一些解决方案配置
	    resolve: {},
	    resolveLoader: {},
	    module: {
	        // 各种不同类型文件加载器配置
	        loaders: {
	        ...
	        ...
	        // js文件用babel转码
	        {
	            test: /\.js$/,
	            loader: 'babel',
	            include: projectRoot,
	            // 哪些文件不需要转码
	            exclude: /node_modules/
	        },
	        ...
	        ...
	        }
	    },
	    // vue文件一些相关配置
	    vue: {}
	}

...
...

	module.export = {
	    // 编译入口文件
	    entry: {},
	    // 编译输出路径
	    output: {},
	    // 一些解决方案配置
	    resolve: {},
	    resolveLoader: {},
	    module: {
	        // 各种不同类型文件加载器配置
	        loaders: {
	        ...
	        ...
	        // js文件用babel转码
	        {
	            test: /\.js$/,
	            loader: 'babel',
	            include: projectRoot,
	            // 哪些文件不需要转码
	            exclude: /node_modules/
	        },
	        ...
	        ...
	        }
	    },
	    // vue文件一些相关配置
	    vue: {}
	}

##第3节：解读Vue-cli的模板
一、npm run build 命令
如何把写好的Vue网页放到服务器上，那我就在这里讲解一下，主要的命令就是要用到npm run build 命令。我们在命令行中输入npm run build命令后，vue-cli会自动进行项目发布打包。你在package.json文件的scripts字段中可以看出，你执行的npm run build命令就相对执行的 node build/build.js 。  
打包时，记得将config目录下的index.js下的 assetsPublicPath: '/'改为 assetsPublicPath: './'，
这样就是相对路径。
