# 利用webpack建立一个Vue脚手架


--------
## 1用webpack打包文件

	npm init -y初始化
	npm i webpack@3.6.0 -D

接着配置webpack.config.js文件

	// webpack.config.js
	const path = require("path")
	module.exports = {
	  entry:  "./main.js",  // 入口
	  output: {
	    path: path.resolve(__dirname, 'dist'), // 出口。必须为绝对路径，所以借助node的path模块
	    filename: 'bundle.js' // 打包后文件名
	  },
	}
	// 意思就是：我根目录有个main.js文件，里面导入了好多东西，帮我打包到我目录里面的dist文件下bundle.js文件里

之后就可以执行webpack命令。

## 2关于loader的使用css和图片的打包

之后像vue脚手架一样建立文件夹，并且用loader来识别文件

###2.1引入css的loader

首先按照npm包

	npm i style-loader css-loader less-loader less -D

之后就可以在webpack.config.js的rules条件匹配来进行书写所使用的loader

	// webpack.config.js
	module.exports = {
	  ...
	  rules: [
	      {
	        test: /\.css$/,
	        // css-loader 只负责解析css，并不负责插入样式到页面
	        // style-loader 负责将样式插入页面中
	        // 使用多个loader时，从右往左调用
	        use: ['style-loader','css-loader']
	      },
	      {
	        test: /\.less$/,
	        use: [{ // use中如需配置其他options 可以使用Object形式， 否则不配置可直接用Array形式，同上css-loader
	          loader: "style-loader"
	        }, {
	          loader: "css-loader"
	        }, {
	          loader: "less-loader"
	        }]
	      },
	  ]
	 ...
	}


接着是图片的loader要下载url-loader

	// webpack.config.js
	module.exports = {
	  ...
	  rules: [
	    ...
	    {
	        test: /\.(png|jpg|gif)$/,
	        use: [{
	          loader: 'url-loader',
	          options: {
	            // 当加载图片，小于limit时，会将图片编译成base64字符串形式
	            // 当加载图片，大于limit时，需要安装file-loader进行加载，加载图片会放入你打包输出的文件目录
	            limit: 8192, // 8192 / 1024 = xKB
            // 当不希望它打包直接放入dist文件中时，可以添加name属性，
            // 当需要保存原名时可以添加[name]
            // 当需要防止重名又不需要太长的hash值时可以添加[hash: x] x为你需要hash值位数
            name: 'img/[name].[hash:8].[ext]'
	          }
	        }]
	      }
	    ...
	  ]
	 ...
	}

当图片大于limit设定值时的打包后页面引入路径更改为dist/xxxxx，好的，进入webpack.config.js，在其中output属性中添加publicPath属性

因为之前有publicPath配置，所以打包运行index.html页面图片引入路径仍然为dist/img/xxxx.xxx



## 3用bebel把Es6转为Es5

下载包

	npm install --save-dev babel-loader @babel/core @babel/preset-env

此时，babel-loader已经可以将ES6语法识别，但是打包将ES6转ES5还需要@babel/preset-env插件，所以我们要新建一个名为.babelrc的babel配置文件使用该转译插件：

	// .babelrc
	{
	  "presets": ["@babel/preset-env"]
	}

## 4使用Vue

	npm i vue -S

下载完后更换别名

	// webpack.config.js
	module.exports = {
	  ...
	  // 设置模块如何被解析
	  resolve: {
	    // 当安装vue时，默认使用的是runtime-only版本，此版本只含有vue运行的代码，不包含编译template的代码
	    // 需要重新更换含有runtime-compiler的版本，因为runtime-complier含有complier代码可以用于编译template
	    // alias（别名）: 用别名代替前面的路径，不是省略，而是用别名代替前面的长路径
	    // 如下，当main.js中import Vue from "vue"时，因为vue是别名，所以实际为import Vue from "vue/dist/vue.esm.js"
	    // 别名好处是webpack直接会去别名对应的目录去查找模块，减少了webpack自己去按目录查找模块的时间
	    alias: {
	      'vue$': 'vue/dist/vue.esm.js'
	    }
	  },
	 ...
	}


识别.vue文件

	npm i vue-loader vue-template-compiler -D


修改

	// webpack.config.js
	const VueLoaderPlugin = require('vue-loader/lib/plugin');
	module.exports = {
	  ...
	  module: {
	    rules: [
	      ...
	        {
	          test: /\.vue$/,
	          loader: 'vue-loader'
	        }
	      ...
	    ]
	  },
	  plugins: [
	      new VueLoaderPlugin() // 因为我安装的"vue-loader"版本为"^15.7.2",需要配置此插件
	    ]
	  }
	 ...


## 4插件的使用


#### 4.1热更新webpack-dev-server

先安装npm i webpack-dev-server -D，

	// webpack.config.js
	...
	module.exports = {
	  ...
	  output: {...},
	  devServer: {
	    contentBase: './dist', // 为哪一个文件夹提供服务，默认根目录
	    inline: true // 页面实时刷新
	    // port: 8080, // 端口号，默认8080
	    // 其他配置自行看文档
	  },
	 ...

	} 

若没有html-webpack-plugin就是用contentBase下的index.html作返回。若有html-webpack-plugin就是返回内存中的index.html文件。

#### 4.2 html-webpack-plugin

先来安装该插件npm i html-webpack-plugin -D，然后再到webpack.config.js中继续配置


	// webpack.config.js
	...
	const HtmlWebpackPlugin = require('html-webpack-plugin');
	module.exports = {
	  ...
	  output: {
	    ...
	    // publicPath: 'dist/'
	  },
	  plugins: [
	    ...
	    new HtmlWebpackPlugin({
	      template: 'index.html' // 指定生成html的模板，如果不知道只会默认生成html文件插入打包后js,并没有<div di="app"></div>节点
	    })
	  ]
	 ...
	}

#### 4.3其它插件

uglifyjs-webpack-plugin插件丑化，，，，BannerPlugin插件打包前的声明


####4.4 webpack.config.js抽离 和 webpack-merge
base.config.js为通用配置文件   
dev.config.js为开发配置文件    
prod.config.js为生产配置文件     