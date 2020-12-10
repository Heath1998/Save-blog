#   webpack的optimization优化属性





##   optimization

webpack主要的优化选项



###   runtimeChunk运行时代码额外打包

`runtimeChunk`可以理解为运行时代码。可以理解为运行异步的代码，他可以分离出运行时代码为一个新文件，那有什么用呢？     

先来看个例子

```javascript
const path = require('path');

module.exports = {
  entry: './es6.js',
  output: {
    filename: '[name].[chunkhash].js',
    path: path.resolve(__dirname) + '/dist'
  },
  module: {
    rules:[
      {
        test: '/\.js$/',
        loader: 'babel-loader',
        exclude: /node_modules/
      }
    ]
  },
  optimization: {
    runtimeChunk: true
  }
}
```

这是一个webpack的配置采取`runtimeChunk`优化。并将输出命名带上chunkhash。

```javascript
// es6.js
import('./utils/utils').then()
console.log(count)

// util.js
console.log('2312321')
export const sum = function(a, b) { return a + b}

```

当执行打包会生成三个文件

```javascript
0.01e47fe5.js
main.XXX.js
runtime~main.XXXX.js
// XXXX是chunkhash值
```

`0.01e47fe5.js`代表打包被分出来的动态加载的代码。     

`main.XXX.js`就是默认输出文件，名字默认为main。     

`runtime~main.XXX.js`是一个用来管理代码模块的代码，保存着每一个`chunkid`和`chunkhash`的对应列表。



1. 当改变运行时文件，即改变`util.js`，再继续打包。则`main`的`名称(hash)`不会改变，`runtime`与 (动态加载的代码) `0.01e47fe5.js`的名称(hash)会改变。        

2. 当改变`es6.js`代码，`runtime`与（被分出的动态加载的代码）`0.01e47fe5.js`的`名称(hash)`不会改变，main的`名称(hash)`会改变。



为什么要这样用呢？直接打包成一个再变化当前`chunkhash`不好吗？     

这样用是为了维持浏览器缓存持久化。现在的静态文件主要存在`cdn`上，比如说动态引入一个组件，即用`import()`的形式引入。会生成运行时文件和runtime文件和main主文件。当组件修改，并不会改变main文件名的hash，但会改变动态加载代码文件和runtime文件名的`chunkhash` ,这样就可以长时间使用浏览器缓存，而只会重新请求动态代码文件和runtime文件。它们相对主文件大小来说还是较小的。         
避免重新请求较大的主文件。





###   splitChunks代码分割

假设要打包两个文件，这两个文件引用了同一个文件。如果都分别打包引入文件，这会造成代码体积增大。

或者说打包第三方包用vendors。

```javascript
    
    splitChunks: {
    // 默认为all
      chunks: "all",
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,  // 匹配node_modules目录下的文件
          priority: 5  // 优先级配置项
        },
        // 打包重复出现的代码，minChunks: 2 && minSize: 0
        commons: {
          chunks: "initial",
          minChunks: 2,
          name: "commons",
          maxInitialRequests: 5,
          minSize: 0, // 默认是30kb,
          priority: 2,
        },
        // reactBase: {
        //   test: (module) => {
        //     return /react|redux|prop-types/.test(module.context);
        //   }, // 直接使用 test 来做路径匹配
        //   chunks: "initial",
        //   name: "reactBase",
        //   priority: 10,
        // }
      }
    },
```



这里主要有两部份。

1. `vendors`打包第三方包如`node_modules`下的。这样额外产出一个包的话，就可以保证浏览器器缓存不许需要再次请求当前包即包名hash不改变。
2. `commons`一般用来打包一些不会改变的工具类，理由同上。









